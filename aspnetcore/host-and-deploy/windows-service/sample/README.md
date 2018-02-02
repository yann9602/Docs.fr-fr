# <a name="custom-webhost-service-sample"></a>Exemple de Service WebHost personnalisé

Cet exemple montre la méthode recommandée pour héberger une application ASP.NET Core sur Windows sans utiliser IIS comme un Service Windows. Cet exemple illustre les fonctionnalités décrites dans [héberger une application ASP.NET Core dans un Service Windows](https://docs.microsoft.com/aspnet/core/host-and-deploy/windows-service).

## <a name="instructions"></a>Instructions

L’exemple d’application est une application web MVC simple modifiée en suivant les instructions de [héberger une application ASP.NET Core dans un Service Windows](https://docs.microsoft.com/aspnet/core/host-and-deploy/windows-service).

Pour exécuter l’application dans un service, procédez comme suit :

1. Créer un dossier au *c:\svc*.

1. Publier l’application dans le dossier avec `dotnet publish --configuration Release --output c:\\svc`. La commande déplacera les ressources de l’application dans le dossier, y compris les `appsettings.json` fichier et le `wwwroot` dossier avec son contenu.

1. Ouvrir un **administrateur** interface de commande.

1. Exécutez les commandes suivantes :

   ```console
   sc create MyService binPath="c:\svc\aspnetcoreservice.exe"
   sc start MyService
   ```

1. Dans un navigateur, accédez à `http://localhost:5000` pour vérifier que le service est en cours d’exécution.

1. Pour arrêter le service, utilisez la commande :

   ```console
   sc stop MyService
   ```

Si l’application ne démarre pas comme prévu lors de l’exécution dans un service, un moyen rapide pour rendre les messages d’erreur accessible est pour ajouter un fournisseur de journalisation, tels que les [fournisseur du journal des événements Windows](https://docs.microsoft.com/aspnet/core/fundamentals/logging/index#eventlog). Une autre option consiste à vérifier le journal des événements à l’aide de l’Observateur d’événements sur le système. Par exemple, voici une exception non prise en charge pour une erreur de fichier introuvable dans le journal des événements Application :

```console
Application: AspNetCoreService.exe
Framework Version: v4.0.30319
Description: The process was terminated due to an unhandled exception.
Exception Info: System.IO.FileNotFoundException
   at Microsoft.Extensions.Configuration.FileConfigurationProvider.Load(Boolean)
   at Microsoft.Extensions.Configuration.ConfigurationRoot..ctor(System.Collections.Generic.IList`1<Microsoft.Extensions.Configuration.IConfigurationProvider>)
   at Microsoft.Extensions.Configuration.ConfigurationBuilder.Build()
   ...
```
