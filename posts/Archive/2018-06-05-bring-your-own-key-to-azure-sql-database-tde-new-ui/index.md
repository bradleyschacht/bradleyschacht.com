---
title: "Bring Your Own Key to Azure SQL Database TDE New UI"
date: "2018-06-05"
categories: 
  - "azure"
  - "sql-server"
  - "syndication"
---

In a recent post I wrote about a new functionality for Azure SQL Database's TDE feature: [Bring Your Own Key](https://bradleyschacht.com/bring-your-own-key-to-azure-sql-database-tde/). At the time the only way to bring your own key was through PowerShell and T-SQL. Thankfully, that has changed. A recent update added a UI for managing your TDE keys using Azure Key Vault. Check out the quick, easy details below on setup and configuration. Before you start, you'll need to have a logical SQL Server and an Azure Key Vault provisioned. I'll be using the built-in sample database, AdventureWorksLT, since we don't really need any data for our exercise. You can see the encryption\_state = 3 for this database, meaning the database is encrypted and the encryptor\_thumbprint is 0x55E756B46BD747E7A44CA43878E0B5482B6AA28E.

```
SELECT
	db_name(database_id) AS database_name,
	*
FROM sys.dm_database_encryption_keys
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-002.png) In the Azure Portal navigate to your SQL Server. On the navigation go to **Transparent Data Encryption** in the Security section. Then change the toggle next to **Use your own key** to **Yes**. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-001.png) This exposes a series of options. If you have an existing key and want to simply enter the key identifier in the format of https://{keyvaultname}.vault.azure.net/keys/{keyname}/{versionguid} then select **Enter key identifier**. Otherwise, stay on **Select a key**. After selecting your Key Vault and either an existing key or generating a new key, click Save. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-003.png) Upon completion you can check the encryptor\_thumbprint has changed, previous it was 0x55E756B46BD747E7A44CA43878E0B5482B6AA28E and now it is 0xDA74B3129590A970EE1C8A66581450C80AD050D4. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-007.png) Checking in Key Vault will also show the TDE key that was created in the previous step (unless you used an existing key then there will obviously be no new keys in Key Vault). ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-008.png) Finally, reverting the SQL DB setting back to **No** will also revert the encryptor\_thumbprint back to the original, service managed, key. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-009.png) ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-010.png)

# Troubleshooting

The most common error message that will be encountered is no doubt the following: Failed to save Transparent Data Encryption settings for server: {serverName}. Error message: The provided Key Vault uri 'https://bradschacht-keyvault.vault.azure.net/keys/SQLTDE/261a05ca90e946f597c60d84a623f862' is not valid. Please ensure the vault has the right Recovery Level other than 'Purgeable'. ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-004.png) ![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-005.png) This same message is seen in the troubleshooting section of my post on [BYOK configuration using PowerShell](https://bradleyschacht.com/bring-your-own-key-to-azure-sql-database-tde/). Currently the Soft Delete Enabled property is not exposed in the Azure Portal, it is only accessible through PowerShell. So break out a couple quick commands and get that setting changed to True using the following two commands (after logging in with Azure PowerShell of course).

```
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "YourKeyVaultNameHere").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

Optionally, you can run Get-AzureRmKeyVault to see the properties and verify that Soft Delete Enabled is set to True.

```
$vault = Get-AzureRmKeyVault -VaultName "YourKeyVaultNameHere"
$vault.EnableSoftDelete
```

![](https://images.bradleyschacht.com/wp-content/uploads/2018/05/bring-your-own-key-to-azure-sql-database-tde-new-ui-006.png)
