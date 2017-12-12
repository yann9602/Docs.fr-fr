# <a name="aspnet-core-authorization-sample"></a>Exemple d’autorisation ASP.NET Core

Cet exemple illustre l’utilisation de l’autorisation de Pages Razor par les conventions. Cet exemple illustre les fonctionnalités décrites dans le [conventions d’autorisation de Pages Razor](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) rubrique.

Autorisation de l’utilisateur dans cet exemple utilise l’authentification de cookie fonctionnalités décrites dans le [à l’aide de l’authentification de Cookie sans ASP.NET Core Identity](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) rubrique. Pour plus d’informations sur l’utilisation d’ASP.NET Core Identity, consultez les rubriques de la [authentification](https://docs.microsoft.com/aspnet/core/security/authentication/index) section de la documentation.

Lorsque vous exécutez l’exemple, utilisez l’adresse de messagerie  **maria.rodriguez@contoso.com**  pour authentifier l’utilisateur.

## <a name="examples-in-this-sample"></a>Exemples de cet exemple

| Fonctionnalité | Description |
| ------- | ----------- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | Ajoute un [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page avec le chemin d’accès spécifié. |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | Ajoute un [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à toutes les pages dans un dossier avec le chemin d’accès spécifié. |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | Ajoute un [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) à une page avec le chemin d’accès spécifié. |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | Ajoute un [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) à toutes les pages dans un dossier avec le chemin d’accès spécifié. |
