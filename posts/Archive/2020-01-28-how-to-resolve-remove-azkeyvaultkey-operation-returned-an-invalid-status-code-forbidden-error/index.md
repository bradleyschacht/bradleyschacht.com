---
title: "How to Resolve Remove-AzKeyVaultKey : Operation returned an invalid status code 'Forbidden' Error"
date: "2020-01-28"
categories: 
  - "azure"
  - "powershell"
  - "syndication"
---

I've come across another error message in my seemingly never-ending battle with Azure Key Vault. A while back I couldn't delete a resource group because of Key Vault soft-delete. Then I couldn't recreate a Key Vault with the same name again because of soft-delete. Which brings us to today's post where I had to go to PowerShell and purge a key that but ran into an issue because of soft-delete.

Maybe my issue isn't with Key Vault so much as it is with soft-delete.

While doing some testing for [TDE protectors on Managed Instance](https://bradleyschacht.com/bring-your-own-key-to-azure-sql-database-managed-instance-tde) I had to remove a key and recreate it for part of my test setup. The key I deleted earlier in the day was named ManagedInstance. So when I went to create a new key called ManagedInstance in the Azure Portal I received an error message. No big deal, I had this one in the bag. Go to PowerShell, find the key, remove the key, go back to the portal and try again. Unfortunately, I hit a snag. Let's walk through the code and see where I went wrong.

To remove a soft-deleted key you must first locate the key's name or Id. To do this we will use the Get-AzKeyVaultKey cmdlet with the parameter -InRemovedState to show all the deleted, but not really deleted (aka soft-deleted) keys.

```powershell
Get-AzKeyVaultKey -VaultName "bschacht-kv" -InRemovedState
```

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/how-to-resolve-remove-azkeyvaultkey-operation-returned-an-invalid-status-code-forbidden-error-001.png)

Make note of the name or Id and move on to the next cmdlet and we will be done...or we should have been done anyway. We will use Remove-AzKeyVaultKey, specify the key name to be removed, make note that this key is in a removed state and run the cmdlet. Except here is where I ran into the somewhat unhelpful error message Remove-AzKeyVaultKey : Operation returned an invalid status code 'Forbidden'.

```powershell
Remove-AzKeyVaultKey -VaultName "bschacht-kv" -Name "ManagedInstance" -InRemovedState
```

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/how-to-resolve-remove-azkeyvaultkey-operation-returned-an-invalid-status-code-forbidden-error-002.png)

It turns out this error message stems from the user running the command (myself in this case) doesn't not have purge permissions explicitly granted in the Key Vault. Running this cmdlet requires the purge permission, no inherited RBAC or explicitly granted RBAC roles no matter how high up the food chain (Contributor, Owner, etc.) will give you the ability to remove a soft-deleted key. You must go to the Azure Portal, navigate to your Key Vault, go to Access Policies, and grant the user running this cmdlet the Purge permission for keys which is in a special section on the dropdown menu called "Privileged Key Operations".

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/how-to-resolve-remove-azkeyvaultkey-operation-returned-an-invalid-status-code-forbidden-error-003.png)

Now, let's run the Remove-AzKeyVaultKey cmdlet again, verify that we are sure we want to remove the key permanently, and no response means success!

![](https://images.bradleyschacht.com/wp-content/uploads/2020/01/how-to-resolve-remove-azkeyvaultkey-operation-returned-an-invalid-status-code-forbidden-error-004.png)

There you have it. In the end it was a very simple fix. Unfortunately that error message seems to be less than well documented. Thankfully, if you go look at the [documentation for the cmdlet](https://docs.microsoft.com/en-us/powershell/module/az.keyvault/remove-azkeyvaultkey) under example 3 (which, let's be honest, who does that unless they run into a problem and have to go looking for an answer) you will in fact see the purge permission noted. Too bad I don't read instructions. I just go run things and see what happens. It's a great way to learn...as long as you aren't doing it in production.
