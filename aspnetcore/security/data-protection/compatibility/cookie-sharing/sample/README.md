# <a name="cookie-sharing-sample-app"></a>Exemple d’application de cookie de partage

L’exemple illustre le partage de cookie entre trois applications qui utilisent l’authentification de cookie :

| Projet                             | Description |
| ----------------------------------- | ----------- |
| CookieAuth.Core                     | Principales 2.0 Razor Pages d’application ASP.NET sans utiliser ASP.NET Core Identity |
| CookieAuthWithIdentity.Core         | Application MVC de base d’ASP.NET 2.0 avec l’identité de ASP.NET Core |
| CookieAuthWithIdentity.NETFramework | Application avec l’identité d’ASP.NET MVC est ASP.NET Framework 4.6.1 |

Instructions :

1. Exécutez l’application CookieAuth.Core. Inscrire un utilisateur. L’application authentifie l’utilisateur lorsque l’utilisateur est inscrit. Déconnecter l’utilisateur.
1. Dans la même session de navigateur, exécutez l’application CookieAuthWithIdentity.Core. Inscrire le même utilisateur que celles utilisées avec l’application de base. L’application authentifie l’utilisateur lorsque l’utilisateur est inscrit. Déconnecter l’utilisateur.
1. Dans la même session de navigateur, exécutez l’application CookieAuthWithIdentity.NETFramework. Inscrire le même utilisateur que celles utilisées avec les autres applications. L’application authentifie l’utilisateur lorsque l’utilisateur est inscrit. Déconnecter l’utilisateur.
1. Connectez-vous à l’utilisateur à un des trois applications. Le cookie d’authentification est partagé entre les applications. Notez que l’utilisateur est automatiquement connecté à deux autres applications.
1. Se déconnecter l’utilisateur à partir des applications. Notez que l’utilisateur est automatiquement déconnecté deux autres applications.

Cet exemple illustre les fonctionnalités décrites dans le [les cookies entre les applications de partage](https://docs.microsoft.com/aspnet/core/security/data-protection/compatibility/cookie-sharing) rubrique.
