---
title: "Bring Your Own Key to Azure SQL Database TDE"
date: "2018-05-22"
categories: 
  - "azure"
  - "sql-server"
  - "syndication"
---

I have previously written about using [Transparent Data Encryption (TDE) with Azure Key Vaule](https://bradleyschacht.com/transparent-data-encryption-with-azure-key-vault/) as a great way to store and manage encryption keys for SQL Server. With Azure SQL Database there has long been an option to enable TDE with just the click of a button. A lot of users I talk to say that is great, but they need to be able to manage the keys themselves. That could be for a variety of reasons, and with my government customers that tends to be for some sort of regulatory compliance. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-001.png) Great news, now you can bring your own key to Azure SQL Database TDE! The process to bring your own key (BYOK) is a little tricky at the moment since there is no portal option for doing this. So break out the PowerShell and follow along below. For my sample, I am going to be taking an existing server and database that are currently deployed with TDE enabled, UtilityMeters. You can see the encryption\_state = 3 meaning the database is encrypted and the encryptor\_thumbprint is 0x55ED837D5D49B0AF6C21F37C39E05FAD6D8A417D. The process is only slightly different on the first step if you are creating a new server, but you can always create the server and then follow these steps.

```
SELECT
	db_name(database_id) AS database_name,
	*
FROM sys.dm_database_encryption_keys
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-002.png) As previously mentioned you will need to use PowerShell to complete the setup. You'll need to use Azure PowerShell version 4.2.0 or newer. First, we need to create an Azure Active Directory identity for our SQL Server. If you recall from the previous TDE/Key Vault post, Azure Key Vault uses Azure AD for authenticating into the service and managing permissions on key access. Our first command will accomplish this. We will need the Principal ID later, so we'll want to hold on to the output in a variable. The optional parameter "AssignIdentity" will generate and assign an Azure Active Directory Identity for this server for use with Azure Key Vault.

```
$server = Set-AzureRmSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
$server = Set-AzureRmSqlServer -ResourceGroupName "PaaS" -ServerName "bradschacht" -AssignIdentity
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-006.png) Next, we need to grant this new identity, our server, the appropriate rights on our Key Vault service. It will need to be able to get, wrap and unwrap keys.

```
Set-AzureRmKeyVaultAccessPolicy -VaultName <KeyVaultName> -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
Set-AzureRmKeyVaultAccessPolicy -VaultName "bradschacht" -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-007.png) Now it is time to associate your key with your SQL Server. First, a key will need to exist in the Key Vault. If one does not exist, create it now. Navigate to your Key Vault in the [Azure Portal](http://portal.azure.com). In my example this is my vault called bradschacht. Locate the key to be used for TDE and copy the **Key Identifier**. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-008.png) Run the following PowerShell command to add key to the SQL Server.

```
Add-AzureRmSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>
Add-AzureRmSqlServerKeyVaultKey -ResourceGroupName "PaaS" -ServerName "bradschacht" -KeyId "https://bradschacht.vault.azure.net/keys/AzureSQLDB/0f9dcdaa2e874b7a8168ec9f4892d1c4"
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-009.png) Run the following PowerShell command to set the key as the TDE key for the server resources.

```
Set-AzureRmSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -Type "AzureKeyVault" -KeyId <KeyVaultKeyId>
Set-AzureRmSqlServerTransparentDataEncryptionProtector -ResourceGroupName "Paas" -ServerName "bradschacht" -Type "AzureKeyVault" -KeyId "https://bradschacht.vault.azure.net/keys/AzureSQLDB/0f9dcdaa2e874b7a8168ec9f4892d1c4"
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-011.png) To verify the TDE key on the server has switched from being managed by the service over to the Key Vault key you can run Get-AzureRmSqlServerTransparentDataEncryptionProtector. I know exactly what you are thinking. That is a nice, short, sweet, to the point command.

```
Get-AzureRmSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
Get-AzureRmSqlServerTransparentDataEncryptionProtector -ResourceGroupName "PaaS" -ServerName "bradschacht"
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-012.png) Notice the Key ID matches the key that was generated in Key Vault. The next screenshot is what the output will be when the TDE key is managed by the service rather than the key you provided in Azure Key Vault. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-013-1.png) If you had TDE enabled at the database level then you are done! That is all, the key change will be reflected at the database level through the same system DMV in the beginning of this post. If you still need to enable TDE on the database that can be done through the portal or by running

```
Set-AzureRmSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
Set-AzureRmSqlDatabaseTransparentDataEncryption -ResourceGroupName "Paas" -ServerName "bradschacht" -DatabaseName "UtilityMeters" -State "Enabled"

```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-014.png) Back in SSMS you can see the key has been changed as the thumbprint has changed. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-015.png)

# Revert Back to Service Managed Key

Should you need to change from the bring your own key model back to having the service manage your TDE keys that can be done pretty easily.

```
Set-AzureRmSqlServerTransparentDataEncryptionProtector ` -ResourceGroupName <SQLDatabaseResourceGroupName> ` -ServerName <LogicalServerName> ` -Type "ServiceManaged"
Set-AzureRmSqlServerTransparentDataEncryptionProtector -ResourceGroupName "PaaS" -ServerName "bradschacht" -Type "ServiceManaged"
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-016.png) And now the thumbprint is back to what it was before I moved to Azure Key Vault keys. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-017.png)

# Troubleshooting

My Key Vault has been provisioned for quite some time now, close to a year as I wiped my entire Azure account in May last year and rebuilt from scratch. Soft-deleting was introduced to Key Vault sometime last year after my vault was created. As a result this property was not set correctly. Also, the default setting for soft-delete is disabled at this time.As a result, when I ran the Add-AzureRmSqlServerKeyVaultKey command I received the following error message: Add-AzureRMSqlServerKeyVaultKey : SecurityInvalidAzureKeyVaultRecoveryLevel: The provided Key Vault uri 'https://bradschacht.vault.azure.net/keys/AzureSQLDB/0f9dcdaa2e874b7a8168ec9f4892d1c4' is not valid. Please ensure the vault has the right Recovery Level other than 'Purgeable'. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-003.png) It was time to do some digging. By running Get-AzureRmKeyVault -VaultName "bradschacht" I was able to see the property called **Soft Delete Enabled?** was blank. Interesting, and not exactly what I was looking for. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-004.png) If you run that command and don't even see a property listed for Soft Delete Enabled then you may have an outdated version of the PowerShell cmdlets. In that case run the following to see the property value:

```
$vault = Get-AzureRmKeyVault -VaultName "YourKeyVaultNameHere"
$vault.EnableSoftDelete
```

Maybe your property is like mine and it is blank. Maybe you have that set to disabled. Either way, we need that setting to be "true" which is a two command process.

```
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "YourKeyVaultNameHere").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

After which point running Get-AzureRmKeyVault again should give us the results we are looking for. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/04/bring-your-own-key-to-azure-sql-database-tde-005.png)
