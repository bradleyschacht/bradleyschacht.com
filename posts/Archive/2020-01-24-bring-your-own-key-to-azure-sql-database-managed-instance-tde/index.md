---
title: "Bring Your Own Key to Azure SQL Database Managed Instance TDE"
date: "2020-01-24"
categories: 
  - "azure"
  - "sql-server"
  - "syndication"
---

Last year Azure SQL Database Managed Instance saw the introduction of bring your own key (BYOK) functionality for transparent data encryption (TDE). This functionality has been in the singleton database version of Azure SQL Database for a while longer and you can [read about how to use that here](https://bradleyschacht.com/bring-your-own-key-to-azure-sql-database-tde-new-ui/). The experience between the two is very similar, but let's focus on the Managed Instance side of things today.

A few prerequisites for today:

- Azure SQL Managed Instance (any service tier, any size)
- A database (any user database will do, even an empty one)
- Azure Key Vault (with soft-delete enabled)
- Storage Account (optional, if you want to test the backup/restore process)

## Switching to Bring Your Own Key

Bringing your own key for TDE is actually not a requirement to use Managed Instance. By default, the service will manage its own TDE key. However, that comes with a few limitations. The main one being you cannot create user initiated backups when using the service-managed key. The built-in backup/restore functionality still works just fine with service-managed keys though.

The key settings can be found by navigating the [Azure Portal](http://portal.azure.com) to your Managed Instance and clicking on the **Transparent Data Encryption** option in the service navigation panel. Then change the

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-001.png)

The next step is to identify the key that will be used for encrypting the databases. This can be done in one of two ways: choosing a Key Vault and Key from inside the tenant or specifying a key identifier URL for a particular key. The second option is particularly useful if the user doing the configuration does not have access to the list of Key Vaults/Keys but is given a key identifier that should be used. The two configuration options can be seen in the following screenshot.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-002.png)

#### Important Notes

A few important pieces of information to note on from these settings.

First, the Managed Instance must be granted access through a Key Vault access policy. If that has not already been completed an attempt will be made to add the Managed Instance with the appropriate permissions (Get, Wrap Key, Unwrap Key). The access policy is defined and enforced at the vault level, not the individual key, secret, or certificate level. So an instance with Get access to one key in this vault will have Get access to all the keys in this vault.

The second piece of important information is the check box labeled **Make the selected key the default TDE protector.** By checking that box, all databases will be encrypted using the selected key. The situation may arise where you would like to simply restore a database protected with a particular key, but you do not wish to encrypt the databases on the server with that key. In that scenario, you will want to add the key to the Managed Instance, but NOT select the option to make it the default TDE protector.

Next, the Managed Instance and Key Vault must be in the same Active Directory tenant. At this time it is not supported to backup keys from one subscription and move them to another subscription, the keys must remain in the same subscription but can be moved to another vault someplace else in the same geography (moving between Key Vaults in North America that reside in the same subscription is supported, moving between a Key Vault in North America and one in Europe is not supported even if they are in the same subscription.

Finally, changing the default TDE protector does not encrypt existing backups with the new key. So it is important for business continuity that you retain the prior TDE key(s) in order to be able to restore backups. If you switch from customer-managed key back to service-managed key you must retain the copy of your key in Key Vault at least until the system's point-in-time restore window has rolled off for the customer-managed keys (i.e. retain the customer-managed key for 7-35 days depending on your configuration after switching back to service-managed key).

There is one and only one key that will protect the databases on an instance.

- Key marked as "Make the selected key the default TDE protector."
    - Can restore databases encrypted with this key.
    - Will be the key used to encrypt the user databases on the instance.
    - A database restored with this key will also be encrypted with this key.
- Key NOT marked as "Make the selected key the default TDE protector."
    - Can restored databases encrypted with this key.
    - A database restored with this key will be encrypted with the key marked as the protector after the restore process is completed.

## See it in Action - Changing to Customer-Managed Key

As a starting point I have a Managed Instance with a single database, TDETesting, that is encrypted with the service-managed key.

We can use a simple T-SQL query to get the encryptor thumbprint. This same query will help us identify when the key has changed.

```sql
SELECT
	DB_NAME(database_id) AS database_name,
	encryption_state,
	CASE encryption_state
		WHEN 0 THEN 'No Encryption Key Present. Database Not Encrypted.'
		WHEN 1 THEN 'Database Unencrypted'
		WHEN 2 THEN 'Database Encryption in Progress'
		WHEN 3 THEN 'Database Encrypted'
		WHEN 4 THEN 'Encryption Key Change in Progress'
		WHEN 5 THEN 'Database Decryption in Progress'
		WHEN 6 THEN 'Certificate or Key Change in Progress'
		ELSE 'Unknown Status'
		END AS encryption_state_descriptoin,
	encryptor_thumbprint
FROM sys.dm_database_encryption_keys
```

We can see that based on the query results the current thumbprint is 0x051082BA88D2882F551C530B74EB6F4380843029.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-003.png)![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-004.png)

Before the switching to the customer-managed key, this is what the access policy settings look like on my Key Vault. The only user that has rights to do anything is myself.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-005.png)

From my Managed Instance I will switch to customer-managed key.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-006.png)

Select my Key Vault.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-007.png)

And on the select a key blade, I will choose create a new key as I do not already have a key to use.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-008.png)

I will keep the option on generate to create a key and provide a name. I'm going to use ManagedInstance, but there is not a specific name that is required. Then Click create.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-009.png)

With all the options configured click Save.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-010.png)

After the settings are applied we can see the change in access policy on the Key Vault has added my Managed Instance with Get, Wrap, and Unwrap permissions.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-011.png)

Additionally, running our T-SQL script we can see the encryptor thumbprint has now changed from 0x051082BA88D2882F551C530B74EB6F4380843029 to 0x71ABFFF1EAA10687BD43878C75E7F2D1744E285C.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-012.png)

With the customer-managed key in place, we can now run a create a user backup of the database.

```sql
BACKUP DATABASE TDETesting TO URL = 'https://bschachtstor.blob.core.windows.net/backup/TDETesting.bak' WITH COPY_ONLY
```

If we then look at the files in that backup, we will see that it is encrypted with the same thumbprint as our database showed after switching to customer-managed keys.

```sql
RESTORE FILELISTONLY FROM URL = 'https://bschachtstor.blob.core.windows.net/backup/TDETesting.bak'
```

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-013.png)

## See it in Action - Restoring a Backup

What happens when we need to restore a database that has been backed up under a different key? In this particular case I have a backup that was created under a different customer-managed key. This screenshot shows a different thumbprint than the system or current customer-managed key, 0x190256228610BBE409C0345597D49ABB5A40EFA6.

System key thumbprint: 0x051082BA88D2882F551C530B74EB6F4380843029  
Current key thumbprint: 0x71ABFFF1EAA10687BD43878C75E7F2D1744E285C  
Backup key thumbprint: 0x190256228610BBE409C0345597D49ABB5A40EFA6

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-014.png)

In the Azure Portal, I changed the key to the new ManagedInstance-AlternateKey that I added to my Key Vault. However, I did not choose the **Make the selected key the default TDE protector** option as I want to continue to encrypt my databases with the key used previously in this example but restore databases that are encrypted with the ManagedInstance-AlternateKey.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-015.png)

After saving the change I can now run the restore of my database that is encrypted with the ManagedInstance-AlternateKey (thumbprint 0x190256228610BBE409C0345597D49ABB5A40EFA6). After the restore completes we can see that the database encryptor has been switched to the thumbprint 0x71ABFFF1EAA10687BD43878C75E7F2D1744E285C that is used on my existing database. As mentioned previously, the current implementation is such that all databases are encrypted with the same key. So while the key ending in EFA6 is required for the restore process the database is then encrypted with the TDE protector key ending in 285C.

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-016.png)

## What Keys Are Associated with my Managed Instance?

We have seen how we can assign a customer-managed key to Managed Instance, add an additional key for restore purposes, how to see which key is encrypting the database, and how to restore backups encrypted with non-TDE Protector keys. There can be confusion as to what keys are available though because the Azure Portal only shows the last key assigned. In our demo walkthrough that wasn't even the TDE Protector key. How then do we find what keys are available to our managed instance for restore commands and what key is the current TDE Protector?

For that we go to PowerShell.

Get-AzSqlInstanceKeyVaultKey will give a list of all keys currently associated with your managed instance. In order for a backup file to be restored to your Managed Instance the key with which the backup was encrypted must be listed. In my example there is a service-managed key and two customer-managed keys with the thumbprints from earlier in this post.

```powershell
Get-AzSqlInstanceKeyVaultKey -ResourceGroupName "ManagedInstance-RG" -InstanceName "bschacht-sqlmi01"
```

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-017.png)

However, there is no indication of the current TDE Protector. For that we will go to a different PowerShell cmdlet, Get-AzSqlInstanceTransparentDataEncryptionProtector.

```powershell
Get-AzSqlInstanceTransparentDataEncryptionProtector -ResourceGroupName "ManagedInstance-RG" -InstanceName "bschacht-sqlmi01"
```

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-018.png)

Removing a key is also a simple operation with the cmdlet Remove-AzSqlInstanceKeyVaultKey.

```powershell
Remove-AzSqlInstanceKeyVaultKey -ResourceGroupName "ManagedInstance-RG" -InstanceName "bschacht-sqlmi01" -KeyId https://bschacht-kv.vault.azure.net/keys/ManagedInstance-AlternateKey/2603353efae04fbab840385dd43a590b
```

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/bring-your-own-key-to-azure-sql-database-managed-instance-tde-019.png)

## Wrapping Up

What other questions do you have about using TDE with Azure SQL Database Managed Instance? Let me know in the comments and I'll see if I can work through some examples with you.
