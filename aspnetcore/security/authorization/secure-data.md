---
title: "Créer une application ASP.NET Core et de données utilisateur protégées par l’autorisation"
description: "Découvrez comment créer une application de Pages Razor avec les données utilisateur protégées par l’autorisation. Inclut le SSL, l’authentification, la sécurité, ASP.NET Core Identity."
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 01/24/2018
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: security/authorization/secure-data
ms.openlocfilehash: ff5c97feca58318d5f4e5b6a6a930c92469602ba
ms.sourcegitcommit: 18ff1fdaa3e1ae204ed6a2ba9351ce8cf1371c85
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Créer une application ASP.NET Core et de données utilisateur protégées par l’autorisation

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)

Ce didacticiel montre comment créer une application de web ASP.NET Core et de données utilisateur protégées par l’autorisation. Il affiche une liste de contacts (inscrits) les utilisateurs authentifiés ont créés. Il existe trois groupes de sécurité :

* **Utilisateurs inscrits** peuvent afficher toutes les données approuvées et peut modifier/supprimer leurs propres données.
* **Gestionnaires de** peuvent approuver ou rejeter les données de contact. Seuls les contacts approuvés sont visibles par les utilisateurs.
* **Les administrateurs** peut approuver/refuser et modifier/supprimer des données.

Dans l’image suivante, l’utilisateur Rick (`rick@example.com`) n’est connecté. Rick peut consulter uniquement les contacts approuvés et **modifier**/**supprimer**/**créer un nouveau** liens pour ses contacts. Seul le dernier enregistrement, créé par Rick, affiche **modifier** et **supprimer** des liens. Les autres utilisateurs ne verront pas le dernier enregistrement jusqu'à ce qu’un gestionnaire ou un administrateur change le statut « Approved ».

![image décrit précédent](secure-data/_static/rick.png)

Dans l’image suivante, `manager@contoso.com` est signé dans et dans le rôle gestionnaires :

![image décrit précédent](secure-data/_static/manager1.png)

L’illustration suivante montre les gestionnaires de vue des détails d’un contact :

![image décrit précédent](secure-data/_static/manager.png)

Le **approuver** et **rejeter** boutons sont affichés uniquement pour les responsables et les administrateurs.

Dans l’image suivante, `admin@contoso.com` est signé dans et dans le rôle Administrateurs :

![image décrit précédent](secure-data/_static/admin.png)

L’administrateur a tous les privilèges. Elle peut en lecture/modification/suppression de contact et modifier l’état de contacts.

L’application a été créée par [la structure](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller) suit `Contact` modèle :

[!code-csharp[Main](secure-data/samples/starter2/Models/Contact.cs?name=snippet1)]

L’exemple contient les gestionnaires d’autorisation suivants :

* `ContactIsOwnerAuthorizationHandler`: Permet de s’assurer qu’un utilisateur peut modifier uniquement leurs données.
* `ContactManagerAuthorizationHandler`: Permet aux responsables d’approuver ou rejeter des contacts.
* `ContactAdministratorsAuthorizationHandler`: Permet aux administrateurs pour approuver ou rejeter des contacts et à modifier/supprimer des contacts.

## <a name="prerequisites"></a>Prérequis

Ce didacticiel est avancé. Vous devez être familiarisé avec :

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentification](xref:security/authentication/index)
* [Confirmation de compte et récupération de mot de passe](xref:security/authentication/accconfirm)
* [Autorisation](xref:security/authorization/index)
* [Entity Framework Core](xref:data/ef-mvc/intro)

La version de ASP.NET Core 1.1 de ce didacticiel est [cela](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data) dossier. La version 1.1 de ASP.NET Core exemple se trouve dans le [exemples](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

## <a name="the-starter-and-completed-app"></a>Le démarrage et l’application terminée

[Télécharger](xref:tutorials/index#how-to-download-a-sample) le [terminé](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2) application. [Test](#test-the-completed-app) application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.

### <a name="the-starter-app"></a>L’application de démarrage

[Télécharger](xref:tutorials/index#how-to-download-a-sample) le [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2) application.

Exécuter l’application, appuyez sur la **ContactManager** la liaison et vérifiez que vous pouvez créer, modifier et supprimer un contact.

## <a name="secure-user-data"></a>Sécuriser les données utilisateur

Les sections suivantes ont les principales étapes pour créer l’application de données utilisateur sécurisée. Il peut s’avérer utile de faire référence au projet terminé.

### <a name="tie-the-contact-data-to-the-user"></a>Lier les données de contact à l’utilisateur

Utilisez ASP.NET [identité](xref:security/authentication/identity) ID d’utilisateur pour vérifier les utilisateurs permettre modifier leurs données, mais pas les autres données utilisateurs. Ajouter `OwnerID` et `ContactStatus` pour la `Contact` modèle :

[!code-csharp[Main](secure-data/samples/final2/Models/Contact.cs?name=snippet1&highlight=5-6,16-)]

`OwnerID`est l’ID de l’utilisateur à partir de la `AspNetUser` de table dans le [identité](xref:security/authentication/identity) base de données. Le `Status` champ détermine si un contact est visible par les utilisateurs standard.

Créer une nouvelle migration et mise à jour de la base de données :

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="require-ssl-and-authenticated-users"></a>Exiger SSL et les utilisateurs authentifiés

Ajouter [IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) à `Startup`:

[!code-csharp[Main](secure-data/samples/final2/Startup.cs?name=snippet_env)]

Dans le `ConfigureServices` méthode de la *Startup.cs* , ajoutez le [RequireHttpsAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) filtre d’autorisation :

[!code-csharp[Main](secure-data/samples/final2/Startup.cs?name=snippet_SSL&highlight=19-)]

Si vous utilisez Visual Studio, activez SSL.

Pour rediriger les demandes HTTP vers HTTPS, consultez [intergiciel (middleware) réécriture d’URL](xref:fundamentals/url-rewriting). Si vous êtes à l’aide de Visual Studio Code ou de test sur une plateforme locale qui n’inclut pas un certificat de test pour le protocole SSL :

  Définissez `"LocalTest:skipSSL": true` dans le *appsettings. Developement.JSON* fichier.

### <a name="require-authenticated-users"></a>Demander aux utilisateurs authentifiés

Définir la stratégie d’authentification par défaut pour les utilisateurs doivent être authentifiés. Vous pouvez désactiver l’authentification au niveau de la méthode Page Razor, de contrôleur ou d’action avec le `[AllowAnonymous]` attribut. Définition de la stratégie d’authentification par défaut pour les utilisateurs doivent être authentifiés protège nouvellement ajouté les Pages Razor et tous les contrôleurs. Avec l’authentification requise par défaut est plus sûre que de s’appuyer sur nouveaux contrôleurs et les Pages Razor à inclure le `[Authorize]` attribut. Ajoutez le code suivant à la `ConfigureServices` méthode de la *Startup.cs* fichier :

[!code-csharp[Main](secure-data/samples/final2/Startup.cs?name=snippet_defaultPolicy&highlight=31-)]

Ajouter [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) à l’Index, des pages sur et de Contact pour les utilisateurs anonymes peuvent obtenir des informations sur le site avant qu’ils s’inscrire. 

[!code-csharp[Main](secure-data/samples/final2/Pages/Index.cshtml.cs?name=snippet&highlight=2)]

Ajouter `[AllowAnonymous]` à la [LoginModel et RegisterModel](https://github.com/aspnet/templating/issues/238).

### <a name="configure-the-test-account"></a>Configurer le compte de test

La `SeedData` classe crée deux comptes : administrateur et le gestionnaire. Utilisez le [Secret gestionnaire](xref:security/app-secrets) pour définir un mot de passe pour ces comptes. Définir le mot de passe à partir du répertoire de projet (le répertoire contenant *Program.cs*) :

```console
dotnet user-secrets set SeedUserPW <PW>
```

Mise à jour `Main` à utiliser le mot de passe de test :

[!code-csharp[Main](secure-data/samples/final2/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Créez les comptes de test et de mettre à jour les contacts

Mise à jour la `Initialize` méthode dans la `SeedData` classe pour créer les comptes de test :

[!code-csharp[Main](secure-data/samples/final2/Data/SeedData.cs?name=snippet_Initialize)]

Ajouter l’ID d’utilisateur administrateur et `ContactStatus` aux contacts. Apportez l’une des contacts « Envoyé » et un « Rejected ». Ajouter l’ID d’utilisateur et l’état de tous les contacts. Un seul contact s’affiche :

[!code-csharp[Main](secure-data/samples/final2/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Créer le propriétaire et Gestionnaire de gestionnaires d’autorisations d’administrateur

Créer un `ContactIsOwnerAuthorizationHandler` classe dans le *autorisation* dossier. Le `ContactIsOwnerAuthorizationHandler` vérifie que l’utilisateur qui agit sur une ressource de propriétaire de la ressource.

[!code-csharp[Main](secure-data/samples/final2/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

Le `ContactIsOwnerAuthorizationHandler` appelle [contexte. Réussir](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si l’utilisateur authentifié actuel est le propriétaire du contact. Les gestionnaires d’autorisation généralement :

* Retourner `context.Succeed` lorsque les conditions sont remplies.
* Retourner `Task.CompletedTask` lorsque les conditions ne sont pas remplies. `Task.CompletedTask`est une réussite ou l’échec&mdash;permet d’autres gestionnaires d’autorisation Exécuter.

Si vous devez explicitement échouer, retourner [contexte. Échec de](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

L’application permet les propriétaires de contact à modifier, supprimer ou créer leurs propres données. `ContactIsOwnerAuthorizationHandler`n’a pas besoin vérifier l’opération passée dans le paramètre de condition.

### <a name="create-a-manager-authorization-handler"></a>Créer un gestionnaire d’autorisation de gestionnaire

Créer un `ContactManagerAuthorizationHandler` classe dans le *autorisation* dossier. Le `ContactManagerAuthorizationHandler` vérifie l’utilisateur qui agit sur la ressource est un gestionnaire. Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouvelles ou modifiées).

[!code-csharp[Main](secure-data/samples/final2/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Créer un gestionnaire d’autorisations d’administrateur

Créer un `ContactAdministratorsAuthorizationHandler` classe dans le *autorisation* dossier. Le `ContactAdministratorsAuthorizationHandler` vérifie l’utilisateur qui agit sur la ressource est un administrateur. Administrateur peut effectuer toutes les opérations.

[!code-csharp[Main](secure-data/samples/final2/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Inscrire les gestionnaires d’autorisation

Services à l’aide d’Entity Framework Core doivent être inscrit pour [injection de dépendance](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/aspnet/core/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). Le `ContactIsOwnerAuthorizationHandler` utilise ASP.NET Core [identité](xref:security/authentication/identity), qui est basé sur Entity Framework Core. Enregistrez les gestionnaires avec la collection de service afin qu’elles soient disponibles pour le `ContactsController` via [injection de dépendance](xref:fundamentals/dependency-injection). Ajoutez le code suivant à la fin de `ConfigureServices`:

[!code-csharp[Main](secure-data/samples/final2/Startup.cs?name=ConfigureServices&highlight=41-)]

`ContactAdministratorsAuthorizationHandler`et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons. Ils sont singletons, car ils n’utilisent pas EF et toutes les informations nécessitées sont dans le `Context` paramètre de la `HandleRequirementAsync` (méthode).

## <a name="support-authorization"></a>Autorisation de la prise en charge

Dans cette section, vous mettez à jour les Pages Razor et ajoutez une classe de configuration requise des opérations.

### <a name="review-the-contact-operations-requirements-class"></a>Passez en revue la classe de la configuration requise operations contact

Examinez la `ContactOperations` classe. Cette classe contient les spécifications le prend en charge de l’application :

[!code-csharp[Main](secure-data/samples/final2/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-razor-pages"></a>Créer une classe de base pour les Pages Razor

Créer une classe de base qui contient les services utilisés dans les Pages Razor contacts. La classe de base place ce code d’initialisation dans un emplacement :

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/DI_BasePageModel.cs)]

Le code précédent :

* Ajoute la `IAuthorizationService` service pour accéder aux gestionnaires d’autorisation.
* Ajoute l’identité `UserManager` service.
* Ajoutez la `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Mettre à jour le CreateModel

Mettre à jour le constructeur de modèle de page de création à utiliser le `DI_BasePageModel` classe de base :

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Mise à jour le `CreateModel.OnPostAsync` méthode :

* Ajouter l’ID utilisateur à la `Contact` modèle.
* Appelle le Gestionnaire d’autorisation pour vérifier que l’utilisateur est autorisé à créer des contacts.

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Mettre à jour le IndexModel

Mise à jour le `OnGetAsync` méthode approuvées uniquement les contacts sont présentés aux utilisateurs généraux :

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Mettre à jour le EditModel

Ajoutez un gestionnaire d’autorisation pour vérifier que l’utilisateur propriétaire du contact. Étant donné que l’autorisation de ressource est en cours de validation, le `[Authorize]` attribut n’est pas suffisant. L’application n’a pas accès à la ressource lorsque des attributs sont évaluées. Autorisation basée sur les ressources doit être impérative. Vérifications doivent être effectuées une fois que l’application a accès à la ressource, en le chargeant dans le modèle de page ou en le chargeant dans le Gestionnaire d’elle-même. Vous accédez fréquemment à la ressource en passant la clé de ressource.

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Mettre à jour le DeleteModel

Mettre à jour le modèle de page de suppression pour utiliser le Gestionnaire d’autorisation pour vérifier que l’utilisateur a l’autorisation de suppression sur le contact.

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Le service d’autorisation d’injecter dans les vues

Actuellement, le montre l’interface utilisateur modifie et supprimer les liens pour l’utilisateur ne peut pas modifier les données. L’interface utilisateur est résolu en appliquant le Gestionnaire d’autorisation pour les vues.

Injecter le service d’autorisation dans le *Views/_ViewImports.cshtml* afin qu’il soit disponible pour toutes les vues de fichiers :

[!code-cshtml[Main](secure-data/samples/final2/Pages/_ViewImports.cshtml?highlight=6-9)]

Le balisage précédent ajoute plusieurs `using` instructions.

Mise à jour la **modifier** et **supprimer** des liaisons dans *Pages/Contacts/Index.cshtml* afin qu’ils sont rendus uniquement pour les utilisateurs disposant des autorisations appropriées :

[!code-cshtml[Main](secure-data/samples/final2/Pages/Contacts/Index.cshtml?highlight=34-36,64-)]

> [!WARNING]
> Le masquage des liens à partir des utilisateurs qui ne sont pas autorisés à modifier les données ne sécuriser l’application. Le masquage des liens rend l’application plus conviviale en affichant des liens n’est valides. Les utilisateurs peuvent hack les URL générées pour appeler modifier et supprimer des opérations sur les données qu’ils ne possèdent pas. Le contrôleur ou Page Razor doit appliquer les vérifications d’accès pour sécuriser les données.

### <a name="update-details"></a>Détails de la mise à jour

Mettre à jour l’affichage des détails pour les gestionnaires peuvent approuver ou rejeter des contacts :

[!code-cshtml[Main](secure-data/samples/final2/Pages/Contacts/Details.cshtml?range=48-)]

Mettre à jour le modèle de page de détails :

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="test-the-completed-app"></a>Tester l’application terminée

Si vous êtes à l’aide de Visual Studio Code ou de test sur une plateforme locale qui n’inclut pas un certificat de test pour le protocole SSL :

* Définissez `"LocalTest:skipSSL": true` dans le *appsettings. Developement.JSON* fichiers à ignorer la spécification SSL. Ignorer le protocole SSL uniquement sur un ordinateur de développement.

Si l’application contient des contacts :

* Supprimer tous les enregistrements dans la `Contact` table.
* Redémarrez l’application pour amorcer la base de données.

Inscrire un utilisateur pour parcourir les contacts.

Un moyen simple pour tester l’application terminée consiste à lancer des trois différents navigateurs (ou les versions furtif/navigation InPrivate). Dans un navigateur, inscrire un nouvel utilisateur (par exemple, `test@example.com`). Connectez-vous à chaque navigateur avec un autre utilisateur. Vérifiez les opérations suivantes :

* Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.
* Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.
* Gestionnaires peuvent approuver ou rejeter les données de contact. Le `Details` vue présente **approuver** et **rejeter** boutons.
* Les administrateurs peuvent approuver/refuser et modifier/supprimer des données.

| Utilisateur| Options |
| ------------ | ---------|
| test@example.com | Peut modifier/supprimer des données propres |
| manager@contoso.com | Peuvent approuver/refuser et modification/suppression posséder de données |
| admin@contoso.com | Peut modifier/supprimer et approuver/refuser toutes les données|

Créer un contact dans le navigateur de l’administrateur. Copier l’URL pour le supprimer et modifier du contact de l’administrateur. Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.

## <a name="create-the-starter-app"></a>Créer l’application de démarrage

* Créer une application de Pages Razor nommée « ContactManager »

  * Créer l’application avec **comptes d’utilisateur individuels**.
  * Pour votre espace de noms correspond à l’espace de noms utilisé dans l’exemple, nommez-Le « ContactManager ».

  ```console
  dotnet new razor -o ContactManager -au Individual -uld
  ```

  * `-uld`Spécifie la base de données locale au lieu de SQLite

* Ajoutez le code suivant `Contact` modèle :

  [!code-csharp[Main](secure-data/samples/starter2/Models/Contact.cs?name=snippet1)]

* Structure du `Contact` modèle :

```console
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
```

* Mise à jour la **ContactManager** d’ancrage dans le *Pages/_Layout.cshtml* fichier :

```cshtml
<a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
```

* Structurez la migration initiale et mettre à jour de la base de données :

```console
dotnet ef migrations add initial
dotnet ef database update
```

* Tester l’application par la création, modification et suppression d’un contact

### <a name="seed-the-database"></a>Amorcer la base de données

Ajouter le `SeedData` classe le *données* dossier. Si vous avez téléchargé l’exemple, vous pouvez copier le *SeedData.cs* de fichiers à la *données* dossier du projet de démarrage.

Appelez `SeedData.Initialize` de `Main`:

[!code-csharp[Main](secure-data/samples/starter2/Program.cs?name=snippet)]

Test de l’application d’amorçage la base de données. S’il existe des lignes dans la base de données de contact, la méthode de valeur initiale ne s’exécute pas.

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Ressources supplémentaires

* [ASP.NET Core d’autorisation Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop). Ce laboratoire présente en détail sur les fonctionnalités de sécurité présentées dans ce didacticiel.
* [Autorisation dans ASP.NET Core : Simple, des rôles, basée sur les revendications et personnalisées](xref:security/authorization/index)
* [Autorisation basée sur une stratégie personnalisée](xref:security/authorization/policies)