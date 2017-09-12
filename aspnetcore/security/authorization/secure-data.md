---
title: "Créer une application ASP.NET Core et de données utilisateur protégées par l’autorisation"
author: rick-anderson
keywords: "ASP.NET Core, MVC, l’autorisation, rôles, sécurité, administrateur"
ms.author: riande
manager: wpickett
ms.date: 05/22/2017
ms.topic: article
ms.assetid: abeb2f8e-dfbf-4398-a04c-338a613a65bc
ms.technology: aspnet
ms.prod: aspnet-core
uid: security/authorization/secure-data
ms.openlocfilehash: db05ffb585022c3d9512d32da28c54788f97129c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Créer une application ASP.NET Core et de données utilisateur protégées par l’autorisation

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)

Ce didacticiel montre comment créer une application web et de données utilisateur protégées par l’autorisation. Il affiche une liste de contacts (inscrits) les utilisateurs authentifiés ont créés. Il existe trois groupes de sécurité :

* Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.
* Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données. 
* Gestionnaires peuvent approuver ou rejeter les données de contact. Seuls les contacts approuvés sont visibles par les utilisateurs.
* Les administrateurs peuvent approuver/refuser et modifier/supprimer des données.

Dans l’image suivante, l’utilisateur Rick (`rick@example.com`) n’est connecté. Utilisateur Rick peut uniquement afficher contacts approuvés et modifier/supprimer ses contacts. Seul le dernier enregistrement, créé par Rick, affiche les modifier et supprimer les liens

![image décrite ci-dessus](secure-data/_static/rick.png)

Dans l’image suivante, `manager@contoso.com` est signé dans et dans le rôle gestionnaires. 

![image décrite ci-dessus](secure-data/_static/manager1.png)

L’illustration suivante montre les gestionnaires de vue des détails d’un contact.

![image décrite ci-dessus](secure-data/_static/manager.png)

Seuls les responsables et les administrateurs ont l’approuver et rejettent les boutons.

Dans l’image suivante, `admin@contoso.com` est signé dans et dans le rôle de l’administrateur. 

![image décrite ci-dessus](secure-data/_static/admin.png)

L’administrateur a tous les privilèges. Elle peut en lecture/modification/suppression de contact et modifier l’état de contacts.

L’application a été créée par [la structure](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller) suit `Contact` modèle :

[!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

A `ContactIsOwnerAuthorizationHandler` Gestionnaire d’autorisation garantit qu’un utilisateur peut modifier uniquement leurs données. A `ContactManagerAuthorizationHandler` Gestionnaire d’autorisation permet aux gestionnaires d’approuver ou rejeter des contacts.  A `ContactAdministratorsAuthorizationHandler` Gestionnaire d’autorisation permet aux administrateurs pour approuver ou rejeter des contacts et à modifier/supprimer des contacts. 

## <a name="prerequisites"></a>Conditions préalables

Ce n’est pas un didacticiel de début. Vous devez être familiarisé avec :

* [Base d’ASP.NET MVC](xref:tutorials/first-mvc-app/start-mvc)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Le démarrage et l’application terminée

[Télécharger](xref:tutorials/index#how-to-download-a-sample) le [terminé](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final) application. [Test](#test-the-completed-app) application terminée afin de vous familiariser avec ses fonctionnalités de sécurité. 

### <a name="the-starter-app"></a>L’application de démarrage

Il est utile comparer votre code avec l’exemple terminé.

[Télécharger](xref:tutorials/index#how-to-download-a-sample) le [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter) application. 

Consultez [créer l’application starter](#create-the-starter-app) si vous voulez créer à partir de zéro.

Mettre à jour la base de données :

```none
   dotnet ef database update
```

Exécuter l’application, appuyez sur la **ContactManager** la liaison et vérifiez que vous pouvez créer, modifier et supprimer un contact.

Ce didacticiel comporte les principales étapes pour créer l’application de données utilisateur sécurisée. Il peut s’avérer utile de faire référence au projet terminé.

## <a name="modify-the-app-to-secure-user-data"></a>Modifier l’application pour sécuriser les données utilisateur

Les sections suivantes ont les principales étapes pour créer l’application de données utilisateur sécurisée. Il peut s’avérer utile de faire référence au projet terminé.

### <a name="tie-the-contact-data-to-the-user"></a>Lier les données de contact à l’utilisateur

Utilisez ASP.NET [identité](xref:security/authentication/identity) ID d’utilisateur pour vérifier les utilisateurs permettre modifier leurs données, mais pas les autres données utilisateurs. Ajouter `OwnerID` pour la `Contact` modèle :

[!code-csharp[Main](secure-data/samples/final/Models/Contact.cs?name=snippet1&highlight=5-6,16-)]

`OwnerID`est l’ID de l’utilisateur à partir de la `AspNetUser` de table dans le [identité](xref:security/authentication/identity) base de données. Le `Status` champ détermine si un contact est visible par les utilisateurs standard. 

Structurer une nouvelle migration et mise à jour de la base de données :

```console
dotnet ef migrations add userID_Status
dotnet ef database update
 ```

### <a name="require-ssl-and-authenticated-users"></a>Exiger SSL et les utilisateurs authentifiés

Dans le `ConfigureServices` méthode de la *Startup.cs* , ajoutez le [RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api) filtre d’autorisation :

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_SSL&highlight=1)]

Si vous utilisez Visual Studio, consultez [configurer IIS Express pour SSL/HTTPS](xref:security/enforcing-ssl#set-up-iis-express-for-sslhttps). Pour rediriger les demandes HTTP vers HTTPS, consultez [intergiciel (middleware) réécriture d’URL](xref:fundamentals/url-rewriting). Si vous êtes à l’aide de Visual Studio Code ou de test sur une plateforme locale qui n’inclut pas un certificat de test pour le protocole SSL :

- Définissez `"LocalTest:skipSSL": true` dans les *appsettings.json* fichier.

### <a name="require-authenticated-users"></a>Demander aux utilisateurs authentifiés

Définir la stratégie d’authentification par défaut pour les utilisateurs doivent être authentifiés. Vous pouvez désactiver l’authentification au niveau de la méthode de contrôleur ou d’action avec le `[AllowAnonymous]` attribut. Avec cette approche, les contrôleurs de nouveau ajoutés seront automatiquement requièrent l’authentification, qui est plus sûre que la partie de confiance sur les contrôleurs de nouveau pour inclure le `[Authorize]` attribut. Ajoutez le code suivant à la `ConfigureServices` méthode de la *Startup.cs* fichier :

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_defaultPolicy)]

Ajouter `[AllowAnonymous]` au contrôleur home pour permettre aux utilisateurs anonymes d’obtenir plus d’informations sur le site avant qu’ils s’inscrire.

[!code-csharp[Main](secure-data/samples/final/Controllers/HomeController.cs?name=snippet1&highlight=2,6)]

### <a name="configure-the-test-account"></a>Configurer le compte de test

La `SeedData` classe crée deux comptes, administrateur et le gestionnaire. Utilisez le [Secret gestionnaire](xref:security/app-secrets) pour définir un mot de passe pour ces comptes. Cela à partir du répertoire du projet (le répertoire contenant *Program.cs*).

```console
dotnet user-secrets set SeedUserPW <PW>
```

Mise à jour `Configure` à utiliser le mot de passe de test :

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=Configure&highlight=19-21)]

Ajouter l’ID d’utilisateur administrateur et `Status = ContactStatus.Approved` aux contacts. Un seul contact est indiqué, d’ajouter l’ID d’utilisateur pour tous les contacts :

[!code-csharp[Main](secure-data/samples/final/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Créer le propriétaire et Gestionnaire de gestionnaires d’autorisations d’administrateur

Créer un `ContactIsOwnerAuthorizationHandler` classe dans le *autorisation* dossier. Le `ContactIsOwnerAuthorizationHandler` vérifiera le propriétaire de l’utilisateur sur la ressource de la ressource.

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

Le `ContactIsOwnerAuthorizationHandler` appelle `context.Succeed` si l’utilisateur authentifié actuel est le propriétaire du contact. Les gestionnaires d’autorisation retournent généralement `context.Succeed` lorsque les conditions sont remplies. Elles retournent `Task.FromResult(0)` lorsque les conditions ne sont pas remplies. `Task.FromResult(0)`est une réussite ou l’échec, il permet l’autre gestionnaire d’autorisation Exécuter. Si vous devez explicitement échouer, retourner `context.Fail()`.

Nous autoriser les propriétaires de contact à modifier/supprimer leurs propres données, nous n’avez pas besoin de vérifier le fonctionnement passé dans le paramètre de condition.

### <a name="create-a-manager-authorization-handler"></a>Créer un gestionnaire d’autorisation de gestionnaire

Créer un `ContactManagerAuthorizationHandler` classe dans le *autorisation* dossier. Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur qui agit sur la ressource est un gestionnaire. Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouvelles ou modifiées).

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Créer un gestionnaire d’autorisations d’administrateur

Créer un `ContactAdministratorsAuthorizationHandler` classe dans le *autorisation* dossier. Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur qui agit sur la ressource est un administrateur. Administrateur peut effectuer toutes les opérations.

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Inscrire les gestionnaires d’autorisation

Services à l’aide d’Entity Framework Core doivent être inscrit pour [injection de dépendance](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](https://docs.microsoft.com/aspnet/core/api). Le `ContactIsOwnerAuthorizationHandler` utilise ASP.NET Core [identité](xref:security/authentication/identity), qui est basé sur Entity Framework Core. Enregistrez les gestionnaires avec la collection de service afin qu’ils soient disponibles pour le `ContactsController` via [injection de dépendance](xref:fundamentals/dependency-injection). Ajoutez le code suivant à la fin de `ConfigureServices`:

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=AuthorizationHandlers)]

`ContactAdministratorsAuthorizationHandler`et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons. Elles sont singletons, car ils n’utilisent pas EF et toutes les informations nécessitées sont dans le `Context` paramètre de la `HandleRequirementAsync` (méthode).

Le texte complet `ConfigureServices`:

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=ConfigureServices)]

## <a name="update-the-code-to-support-authorization"></a>Mettre à jour le code pour prendre en charge d’autorisation

Dans cette section, vous mettre à jour le contrôleur et les vues et ajoutez une classe de configuration requise des opérations.

### <a name="update-the-contacts-controller"></a>Mettre à jour le contrôleur de Contacts

Mise à jour le `ContactsController` constructeur :

* Ajouter la `IAuthorizationService` service pour accéder aux gestionnaires d’autorisation. 
* Ajouter le `Identity` `UserManager` service :

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_ContactsControllerCtor)]

### <a name="add-a-contact-operations-requirements-class"></a>Ajouter une classe de la configuration requise operations contact

Ajouter le `ContactOperationsRequirements` classe le *autorisation* dossier. Cette classe contient les exigences notre application prend en charge :

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

### <a name="update-create"></a>Création de la mise à jour

Mise à jour le `HTTP POST Create` méthode :

* Ajouter l’ID utilisateur à la `Contact` modèle.
* Appelle le Gestionnaire d’autorisation pour vérifier que l’utilisateur propriétaire du contact.

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Create)]

### <a name="update-edit"></a>Modification de la mise à jour

Mettre à jour les `Edit` méthodes à utiliser le Gestionnaire d’autorisation pour vérifier que l’utilisateur propriétaire du contact. Étant donné que nous exécutons d’autorisation de ressource, nous ne pouvons pas utiliser le `[Authorize]` attribut. Nous n’avons pas accès à la ressource lorsque des attributs sont évaluées. L’autorisation de ressource en fonction doit être impérative. Vérifications doivent être effectuées une fois que nous avons accès à la ressource, soit en le chargeant dans notre contrôleur, soit en le chargeant dans le Gestionnaire d’elle-même. Vous devez souvent accéder la ressource en passant la clé de ressource.

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Edit)]

### <a name="update-the-delete-method"></a>Mise à jour de la méthode de suppression

Mettre à jour les `Delete` méthodes à utiliser le Gestionnaire d’autorisation pour vérifier que l’utilisateur propriétaire du contact.

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Delete)]

## <a name="inject-the-authorization-service-into-the-views"></a>Le service d’autorisation d’injecter dans les vues

Actuellement le montre l’interface utilisateur modifier et supprimer des liens pour l’utilisateur ne peut pas modifier les données. Nous allons résoudre cela en appliquant le Gestionnaire d’autorisation pour les vues.

Injecter le service d’autorisation dans le *Views/_ViewImports.cshtml* afin qu’il sera disponible pour toutes les vues de fichiers :

[!code-html[Main](secure-data/samples/final/Views/_ViewImports.cshtml)]

Mise à jour la *Views/Contacts/Index.cshtml* vue Razor uniquement afficher la modification et supprimer les liens pour les utilisateurs peuvent modifier/supprimer le contact.

Ajouter`@using ContactManager.Authorization;`

Mise à jour la `Edit` et `Delete` liens afin qu’ils sont rendus uniquement pour les utilisateurs disposant de l’autorisation de modifier ou supprimer le contact.

[!code-html[Main](secure-data/samples/final/Views/Contacts/Index.cshtml?range=63-84)]

Avertissement : Le masquage des liens à partir des utilisateurs qui ne disposent pas d’autorisation pour modifier ou supprimer des données ne sécurise pas l’application. Le masquage des liens rend l’application utilisateur plus convivial en affichant des liens n’est valides. Les utilisateurs peuvent hack les URL générées pour appeler modifier et supprimer des opérations sur les données qu’ils ne possèdent pas.  Le contrôleur doit répéter vérifie l’accès sécurisé.

### <a name="update-the-details-view"></a>Mettre à jour l’affichage des détails

Mettre à jour l’affichage des détails pour les gestionnaires peuvent approuver ou rejeter des contacts :

[!code-html[Main](secure-data/samples/final/Views/Contacts/Details.cshtml?range=53-)]

## <a name="test-the-completed-app"></a>Tester l’application terminée

Si vous êtes à l’aide de Visual Studio Code ou de test sur une plateforme locale qui n’inclut pas un certificat de test pour le protocole SSL :

- Définissez `"LocalTest:skipSSL": true` dans les *appsettings.json* fichier.

Si vous avez exécuté l’application et que vous disposez des contacts, supprimez tous les enregistrements dans la `Contact` de table et de redémarrer l’application pour amorcer la base de données. Si vous utilisez Visual Studio, vous devez quitter et redémarrer IIS Express à la valeur initiale de la base de données.

Inscrire un utilisateur pour parcourir les contacts.

Un moyen simple pour tester l’application terminée consiste à lancer des trois différents navigateurs (ou les versions furtif/navigation InPrivate). Dans un navigateur, inscrire un nouvel utilisateur, par exemple, `test@example.com`. Connectez-vous à chaque navigateur avec un autre utilisateur. Vérifiez ce qui suit :

* Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.
* Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données. 
* Gestionnaires peuvent approuver ou rejeter les données de contact. Le `Details` vue présente **approuver** et **rejeter** boutons. 
* Les administrateurs peuvent approuver/refuser et modifier/supprimer des données.

| Utilisateur| Options |
| ------------ | ---------|
| test@example.com | Peut modifier/supprimer des données propres |
| manager@contoso.com | Peuvent approuver/refuser et modification/suppression posséder de données  |
| admin@contoso.com | Peut modifier/supprimer et approuver/refuser toutes les données|

Créer un contact dans le navigateur des administrateurs. Copier l’URL pour le supprimer et modifier du contact de l’administrateur. Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.

## <a name="create-the-starter-app"></a>Créer l’application de démarrage

Suivez ces instructions pour créer l’application de démarrage.

* Créer un **Application ASP.NET Core Web** à l’aide de [Visual Studio 2017](https://www.visualstudio.com/) nommé « ContactManager »

  * Créer l’application avec **comptes d’utilisateur individuels**.
  * Pour votre espace de noms correspond à l’utilisation de l’espace de noms dans l’exemple, nommez-Le « ContactManager ».

* Ajoutez le code suivant `Contact` modèle :

  [!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

* Une vue de structure le `Contact` de modèle à l’aide d’Entity Framework Core et `ApplicationDbContext` contexte de données. Acceptez toutes les valeurs par défaut de la structure. À l’aide de `ApplicationDbContext` pour le contexte de données classe place la table contact dans le [identité](xref:security/authentication/identity) base de données. Consultez [Ajout d’un modèle](xref:tutorials/first-mvc-app/adding-model) pour plus d’informations.

* Mise à jour la **ContactManager** d’ancrage dans le *Views/Shared/_Layout.cshtml* à partir du fichier `asp-controller="Home"` à `asp-controller="Contacts"` donc en appuyant sur la **ContactManager** lien appelle le contrôleur de Contacts. Le balisage d’origine :

```html
   <a asp-area="" asp-controller="Home" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

Le balisage mis à jour :

```html
   <a asp-area="" asp-controller="Contacts" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

* Structurez la migration initiale et de mettre à jour de la base de données

```none
   dotnet ef migrations add initial
   dotnet ef database update
   ```

* Tester l’application par la création, modification et suppression d’un contact

### <a name="seed-the-database"></a>Valeur initiale de la base de données

Ajouter le `SeedData` classe le *données* dossier. Si vous avez téléchargé l’exemple, vous pouvez copier le *SeedData.cs* de fichiers à la *données* dossier du projet de démarrage.

[!code-csharp[Main](secure-data/samples/starter/Data/SeedData.cs)]

Ajoutez le code en surbrillance à la fin de la `Configure` méthode dans le *Startup.cs* fichier :

[!code-csharp[Main](secure-data/samples/starter/Startup.cs?name=Configure&highlight=28-)]

Test de l’application d’amorçage la base de données. La méthode de la valeur de départ n’est pas exécuté s’il existe des lignes dans la base de données de contact.

### <a name="create-a-class-used-in-the-tutorial"></a>Créer une classe utilisée dans le didacticiel

* Créez un dossier nommé *autorisation*.
* Copie le *Authorization\ContactOperations.cs* de fichiers à partir du téléchargement du projet terminé, ou copiez le code suivant :

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

<a name=secure-data-add-resources-label></a>

### <a name="additional-resources"></a>Ressources supplémentaires

* [ASP.NET Core d’autorisation Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop). Ce laboratoire présente en détail sur les fonctionnalités de sécurité présentées dans ce didacticiel.
* [Autorisation dans ASP.NET Core : Simple, des rôles, basée sur les revendications et personnalisées](index.md)
* [Autorisation basée sur des stratégies personnalisées](policies.md)
