# <a name="key-vault-configuration-provider-sample-application-aspnet-core-2x"></a>Application d’exemple de fournisseur de Configuration de coffre de clés (ASP.NET Core 2.x)

Cet exemple illustre l’utilisation du fournisseur de Configuration Azure Key Vault pour ASP.NET Core 2.x. Pour l’exemple 1.x ASP.NET Core, consultez [application d’exemple de fournisseur de Configuration de coffre de clés (ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/basic-sample/1.x).

Pour plus d’informations sur le fonctionne de l’exemple, consultez la [fournisseur de configuration d’Azure Key Vault](xref:security/key-vault-configuration) rubrique.

## <a name="using-the-sample"></a>Utilisation de l'exemple
1. Créer un coffre de clés et configurer Azure Active Directory (Azure AD) pour l’application en suivant les instructions de [prise en main d’Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-get-started/).
  * Ajouter les clés secrètes au coffre de clés à l’aide la [PowerShell Module d’Azure Resource Manager clé de coffre](/powershell/module/azurerm.keyvault) disponible à partir de la [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM.KeyVault), le [API REST de coffre de clés Azure](/rest/api/keyvault/), ou le [Portail azure](https://portal.azure.com/). Les secrets sont créés en tant que *manuel* ou *certificat* secrets. *Certificat* secrets sont des certificats pour les applications et services, mais ne sont pas pris en charge par le fournisseur de configuration. Vous devez utiliser le *manuel* option permettant de créer des clés secrètes de paire nom-valeur pour une utilisation avec le fournisseur de configuration.
    * Les secrets simples sont créées en tant que paires nom-valeur. Les noms de secret de coffre de clés Azure sont limités à des caractères alphanumériques et des tirets.
    * Utilisent des valeurs hiérarchiques (sections de configuration) `--` (deux tirets) comme séparateur dans l’exemple. Signes deux-points, qui sont normalement utilisés pour délimiter une section à partir d’une sous-clé dans [configuration d’ASP.NET Core](xref:fundamentals/configuration/index), ne sont pas autorisés dans les noms de secret principal. Par conséquent, les deux tirets sont utilisés et échangés avec un signe deux-points, lorsque les clés secrètes sont chargés dans la configuration de l’application.
    * Créez deux *manuel* secrets avec les paires nom-valeur suivantes. La question secrète du premier est une valeur et le nom simple, et la question secrète du deuxième crée une valeur de secret principal avec une section et le sous-clé dans le nom de clé secrète :
      * `SecretName`: `secret_value_1`
      * `Section--SecretName`: `secret_value_2`
  * Inscrire l’exemple d’application avec Azure Active Directory.
  * Autoriser l’application à accéder au coffre de clés. Lorsque vous utilisez la `Set-AzureRmKeyVaultAccessPolicy` fournissent de l’applet de commande PowerShell pour autoriser l’application à accéder au coffre de clés, `List` et `Get` accès aux clés secrètes avec `-PermissionsToSecrets list,get`.
2. Mettre à jour de l’application *appsettings.json* fichier avec les valeurs de `Vault`, `ClientId`, et `ClientSecret`.
3. Exécutez l’exemple d’application, qui obtient ses valeurs de configuration à partir de `IConfigurationRoot` avec le même nom que le nom secret.
  * Valeurs non hiérarchique : la valeur de `SecretName` est obtenu avec `config["SecretName"]`.
  * Valeurs hiérarchiques (sections) : utilisez `:` la notation (deux-points) ou le `GetSection` méthode d’extension. Pour obtenir la valeur de configuration, utilisez une des ces approches :
    * `config["Section:SecretName"]`
    * `config.GetSection("Section")["SecretName"]`
