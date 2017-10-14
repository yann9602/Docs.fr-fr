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
ms.openlocfilehash: 000b14ddc1adb56c029d3da8ab0754215403ba79
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="10501-103">Créer une application ASP.NET Core et de données utilisateur protégées par l’autorisation</span><span class="sxs-lookup"><span data-stu-id="10501-103">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="10501-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="10501-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="10501-105">Ce didacticiel montre comment créer une application web et de données utilisateur protégées par l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="10501-105">This tutorial shows how to create a web app with user data protected by authorization.</span></span> <span data-ttu-id="10501-106">Il affiche une liste de contacts (inscrits) les utilisateurs authentifiés ont créés.</span><span class="sxs-lookup"><span data-stu-id="10501-106">It  displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="10501-107">Il existe trois groupes de sécurité :</span><span class="sxs-lookup"><span data-stu-id="10501-107">There are three security groups:</span></span>

* <span data-ttu-id="10501-108">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="10501-108">Registered users can view all the approved contact data.</span></span>
* <span data-ttu-id="10501-109">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="10501-109">Registered users can edit/delete their own data.</span></span> 
* <span data-ttu-id="10501-110">Gestionnaires peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="10501-110">Managers can approve or reject contact data.</span></span> <span data-ttu-id="10501-111">Seuls les contacts approuvés sont visibles par les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="10501-111">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="10501-112">Les administrateurs peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="10501-112">Administrators can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="10501-113">Dans l’image suivante, l’utilisateur Rick (`rick@example.com`) n’est connecté.</span><span class="sxs-lookup"><span data-stu-id="10501-113">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="10501-114">Utilisateur Rick peut uniquement afficher contacts approuvés et modifier/supprimer ses contacts.</span><span class="sxs-lookup"><span data-stu-id="10501-114">User Rick can only view approved contacts and edit/delete his contacts.</span></span> <span data-ttu-id="10501-115">Seul le dernier enregistrement, créé par Rick, affiche les modifier et supprimer les liens</span><span class="sxs-lookup"><span data-stu-id="10501-115">Only the last record, created by Rick, displays edit and delete links</span></span>

![image décrite ci-dessus](secure-data/_static/rick.png)

<span data-ttu-id="10501-117">Dans l’image suivante, `manager@contoso.com` est signé dans et dans le rôle gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="10501-117">In the following image, `manager@contoso.com` is signed in and in the managers role.</span></span> 

![image décrite ci-dessus](secure-data/_static/manager1.png)

<span data-ttu-id="10501-119">L’illustration suivante montre les gestionnaires de vue des détails d’un contact.</span><span class="sxs-lookup"><span data-stu-id="10501-119">The following image shows the  managers details view of a contact.</span></span>

![image décrite ci-dessus](secure-data/_static/manager.png)

<span data-ttu-id="10501-121">Seuls les responsables et les administrateurs ont l’approuver et rejettent les boutons.</span><span class="sxs-lookup"><span data-stu-id="10501-121">Only managers and administrators have the approve and reject buttons.</span></span>

<span data-ttu-id="10501-122">Dans l’image suivante, `admin@contoso.com` est signé dans et dans le rôle de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="10501-122">In the following image, `admin@contoso.com` is signed in and in the administrator’s role.</span></span> 

![image décrite ci-dessus](secure-data/_static/admin.png)

<span data-ttu-id="10501-124">L’administrateur a tous les privilèges.</span><span class="sxs-lookup"><span data-stu-id="10501-124">The administrator has all privileges.</span></span> <span data-ttu-id="10501-125">Elle peut en lecture/modification/suppression de contact et modifier l’état de contacts.</span><span class="sxs-lookup"><span data-stu-id="10501-125">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="10501-126">L’application a été créée par [la structure](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller) suit `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="10501-126">The app was created by [scaffolding](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller)  the following `Contact` model:</span></span>

[!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="10501-127">A `ContactIsOwnerAuthorizationHandler` Gestionnaire d’autorisation garantit qu’un utilisateur peut modifier uniquement leurs données.</span><span class="sxs-lookup"><span data-stu-id="10501-127">A `ContactIsOwnerAuthorizationHandler` authorization handler ensures that a user can only edit their data.</span></span> <span data-ttu-id="10501-128">A `ContactManagerAuthorizationHandler` Gestionnaire d’autorisation permet aux gestionnaires d’approuver ou rejeter des contacts.</span><span class="sxs-lookup"><span data-stu-id="10501-128">A `ContactManagerAuthorizationHandler` authorization handler allows managers to approve or reject contacts.</span></span>  <span data-ttu-id="10501-129">A `ContactAdministratorsAuthorizationHandler` Gestionnaire d’autorisation permet aux administrateurs pour approuver ou rejeter des contacts et à modifier/supprimer des contacts.</span><span class="sxs-lookup"><span data-stu-id="10501-129">A `ContactAdministratorsAuthorizationHandler` authorization handler allows administrators to approve or reject contacts and to edit/delete contacts.</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="10501-130">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="10501-130">Prerequisites</span></span>

<span data-ttu-id="10501-131">Ce n’est pas un didacticiel de début.</span><span class="sxs-lookup"><span data-stu-id="10501-131">This is not a beginning tutorial.</span></span> <span data-ttu-id="10501-132">Vous devez être familiarisé avec :</span><span class="sxs-lookup"><span data-stu-id="10501-132">You should be familiar with:</span></span>

* [<span data-ttu-id="10501-133">Base d’ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="10501-133">ASP.NET Core MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="10501-134">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="10501-134">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="10501-135">Le démarrage et l’application terminée</span><span class="sxs-lookup"><span data-stu-id="10501-135">The starter and completed app</span></span>

<span data-ttu-id="10501-136">[Télécharger](xref:tutorials/index#how-to-download-a-sample) le [terminé](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final) application.</span><span class="sxs-lookup"><span data-stu-id="10501-136">[Download](xref:tutorials/index#how-to-download-a-sample) the [completed](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final) app.</span></span> <span data-ttu-id="10501-137">[Test](#test-the-completed-app) application terminée afin de vous familiariser avec ses fonctionnalités de sécurité.</span><span class="sxs-lookup"><span data-stu-id="10501-137">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span> 

### <a name="the-starter-app"></a><span data-ttu-id="10501-138">L’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="10501-138">The starter app</span></span>

<span data-ttu-id="10501-139">Il est utile comparer votre code avec l’exemple terminé.</span><span class="sxs-lookup"><span data-stu-id="10501-139">It's helpful to compare your code with the completed sample.</span></span>

<span data-ttu-id="10501-140">[Télécharger](xref:tutorials/index#how-to-download-a-sample) le [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter) application.</span><span class="sxs-lookup"><span data-stu-id="10501-140">[Download](xref:tutorials/index#how-to-download-a-sample) the [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter) app.</span></span> 

<span data-ttu-id="10501-141">Consultez [créer l’application starter](#create-the-starter-app) si vous voulez créer à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="10501-141">See [Create the starter app](#create-the-starter-app) if you'd like to create it from scratch.</span></span>

<span data-ttu-id="10501-142">Mettre à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="10501-142">Update the database:</span></span>

```none
   dotnet ef database update
```

<span data-ttu-id="10501-143">Exécuter l’application, appuyez sur la **ContactManager** la liaison et vérifiez que vous pouvez créer, modifier et supprimer un contact.</span><span class="sxs-lookup"><span data-stu-id="10501-143">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

<span data-ttu-id="10501-144">Ce didacticiel comporte les principales étapes pour créer l’application de données utilisateur sécurisée.</span><span class="sxs-lookup"><span data-stu-id="10501-144">This tutorial has all the major steps to create the secure user data app.</span></span> <span data-ttu-id="10501-145">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="10501-145">You may find it helpful to refer to the completed project.</span></span>

## <a name="modify-the-app-to-secure-user-data"></a><span data-ttu-id="10501-146">Modifier l’application pour sécuriser les données utilisateur</span><span class="sxs-lookup"><span data-stu-id="10501-146">Modify the app to secure user data</span></span>

<span data-ttu-id="10501-147">Les sections suivantes ont les principales étapes pour créer l’application de données utilisateur sécurisée.</span><span class="sxs-lookup"><span data-stu-id="10501-147">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="10501-148">Il peut s’avérer utile de faire référence au projet terminé.</span><span class="sxs-lookup"><span data-stu-id="10501-148">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="10501-149">Lier les données de contact à l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="10501-149">Tie the contact data to the user</span></span>

<span data-ttu-id="10501-150">Utilisez ASP.NET [identité](xref:security/authentication/identity) ID d’utilisateur pour vérifier les utilisateurs permettre modifier leurs données, mais pas les autres données utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="10501-150">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="10501-151">Ajouter `OwnerID` pour la `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="10501-151">Add `OwnerID` to the `Contact` model:</span></span>

[!code-csharp[Main](secure-data/samples/final/Models/Contact.cs?name=snippet1&highlight=5-6,16-)]

<span data-ttu-id="10501-152">`OwnerID`est l’ID de l’utilisateur à partir de la `AspNetUser` de table dans le [identité](xref:security/authentication/identity) base de données.</span><span class="sxs-lookup"><span data-stu-id="10501-152">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="10501-153">Le `Status` champ détermine si un contact est visible par les utilisateurs standard.</span><span class="sxs-lookup"><span data-stu-id="10501-153">The `Status` field determines if a contact is viewable by general users.</span></span> 

<span data-ttu-id="10501-154">Structurer une nouvelle migration et mise à jour de la base de données :</span><span class="sxs-lookup"><span data-stu-id="10501-154">Scaffold a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
 ```

### <a name="require-ssl-and-authenticated-users"></a><span data-ttu-id="10501-155">Exiger SSL et les utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="10501-155">Require SSL and authenticated users</span></span>

<span data-ttu-id="10501-156">Dans le `ConfigureServices` méthode de la *Startup.cs* , ajoutez le [RequireHttpsAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) filtre d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="10501-156">In the `ConfigureServices` method of the *Startup.cs* file, add the [RequireHttpsAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) authorization filter:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_SSL&highlight=1)]

<span data-ttu-id="10501-157">Pour rediriger les demandes HTTP vers HTTPS, consultez [intergiciel (middleware) réécriture d’URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="10501-157">To redirect HTTP requests to HTTPS, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span> <span data-ttu-id="10501-158">Si vous êtes à l’aide de Visual Studio Code ou de test sur une plateforme locale qui n’inclut pas un certificat de test pour le protocole SSL :</span><span class="sxs-lookup"><span data-stu-id="10501-158">If you are using Visual Studio Code or testing on local platform that doesn't include a test certificate for SSL:</span></span>

- <span data-ttu-id="10501-159">Définissez `"LocalTest:skipSSL": true` dans les *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="10501-159">Set `"LocalTest:skipSSL": true` in the *appsettings.json* file.</span></span>

### <a name="require-authenticated-users"></a><span data-ttu-id="10501-160">Demander aux utilisateurs authentifiés</span><span class="sxs-lookup"><span data-stu-id="10501-160">Require authenticated users</span></span>

<span data-ttu-id="10501-161">Définir la stratégie d’authentification par défaut pour les utilisateurs doivent être authentifiés.</span><span class="sxs-lookup"><span data-stu-id="10501-161">Set the default authentication policy to require users to be authenticated.</span></span> <span data-ttu-id="10501-162">Vous pouvez désactiver l’authentification au niveau de la méthode de contrôleur ou d’action avec le `[AllowAnonymous]` attribut.</span><span class="sxs-lookup"><span data-stu-id="10501-162">You can opt out of authentication at the controller or action method with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="10501-163">Avec cette approche, les contrôleurs de nouveau ajoutés seront automatiquement requièrent l’authentification, qui est plus sûre que la partie de confiance sur les contrôleurs de nouveau pour inclure le `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="10501-163">With this approach, any new controllers added will automatically require authentication, which is safer than relying on new controllers to include the `[Authorize]` attribute.</span></span> <span data-ttu-id="10501-164">Ajoutez le code suivant à la `ConfigureServices` méthode de la *Startup.cs* fichier :</span><span class="sxs-lookup"><span data-stu-id="10501-164">Add the following to  the `ConfigureServices` method of the *Startup.cs* file:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=snippet_defaultPolicy)]

<span data-ttu-id="10501-165">Ajouter `[AllowAnonymous]` au contrôleur home pour permettre aux utilisateurs anonymes d’obtenir plus d’informations sur le site avant qu’ils s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="10501-165">Add `[AllowAnonymous]` to the home controller so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/HomeController.cs?name=snippet1&highlight=2,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="10501-166">Configurer le compte de test</span><span class="sxs-lookup"><span data-stu-id="10501-166">Configure the test account</span></span>

<span data-ttu-id="10501-167">La `SeedData` classe crée deux comptes, administrateur et le gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="10501-167">The `SeedData` class creates two accounts,  administrator and manager.</span></span> <span data-ttu-id="10501-168">Utilisez le [Secret gestionnaire](xref:security/app-secrets) pour définir un mot de passe pour ces comptes.</span><span class="sxs-lookup"><span data-stu-id="10501-168">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="10501-169">Cela à partir du répertoire du projet (le répertoire contenant *Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="10501-169">Do this from the project directory (the directory containing *Program.cs*).</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="10501-170">Mise à jour `Configure` à utiliser le mot de passe de test :</span><span class="sxs-lookup"><span data-stu-id="10501-170">Update `Configure` to use the test password:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=Configure&highlight=19-21)]

<span data-ttu-id="10501-171">Ajouter l’ID d’utilisateur administrateur et `Status = ContactStatus.Approved` aux contacts.</span><span class="sxs-lookup"><span data-stu-id="10501-171">Add the administrator user ID and `Status = ContactStatus.Approved` to the contacts.</span></span> <span data-ttu-id="10501-172">Un seul contact est indiqué, d’ajouter l’ID d’utilisateur pour tous les contacts :</span><span class="sxs-lookup"><span data-stu-id="10501-172">Only one contact is shown, add the user ID to all contacts:</span></span>

[!code-csharp[Main](secure-data/samples/final/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="10501-173">Créer le propriétaire et Gestionnaire de gestionnaires d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="10501-173">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="10501-174">Créer un `ContactIsOwnerAuthorizationHandler` classe dans le *autorisation* dossier.</span><span class="sxs-lookup"><span data-stu-id="10501-174">Create a `ContactIsOwnerAuthorizationHandler` class in the  *Authorization* folder.</span></span> <span data-ttu-id="10501-175">Le `ContactIsOwnerAuthorizationHandler` vérifiera le propriétaire de l’utilisateur sur la ressource de la ressource.</span><span class="sxs-lookup"><span data-stu-id="10501-175">The `ContactIsOwnerAuthorizationHandler` will verify the user acting on the resource owns the resource.</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="10501-176">Le `ContactIsOwnerAuthorizationHandler` appelle `context.Succeed` si l’utilisateur authentifié actuel est le propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="10501-176">The `ContactIsOwnerAuthorizationHandler` calls `context.Succeed` if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="10501-177">Les gestionnaires d’autorisation retournent généralement `context.Succeed` lorsque les conditions sont remplies.</span><span class="sxs-lookup"><span data-stu-id="10501-177">Authorization handlers generally return `context.Succeed` when the requirements are met.</span></span> <span data-ttu-id="10501-178">Elles retournent `Task.FromResult(0)` lorsque les conditions ne sont pas remplies.</span><span class="sxs-lookup"><span data-stu-id="10501-178">They return `Task.FromResult(0)` when requirements are not met.</span></span> <span data-ttu-id="10501-179">`Task.FromResult(0)`est une réussite ou l’échec, il permet l’autre gestionnaire d’autorisation Exécuter.</span><span class="sxs-lookup"><span data-stu-id="10501-179">`Task.FromResult(0)` is neither success or failure, it allows other authorization handler to run.</span></span> <span data-ttu-id="10501-180">Si vous devez explicitement échouer, retourner `context.Fail()`.</span><span class="sxs-lookup"><span data-stu-id="10501-180">If you need to explicitly fail, return `context.Fail()`.</span></span>

<span data-ttu-id="10501-181">Nous autoriser les propriétaires de contact à modifier/supprimer leurs propres données, nous n’avez pas besoin de vérifier le fonctionnement passé dans le paramètre de condition.</span><span class="sxs-lookup"><span data-stu-id="10501-181">We allow contact owners to edit/delete their own data, so we don't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="10501-182">Créer un gestionnaire d’autorisation de gestionnaire</span><span class="sxs-lookup"><span data-stu-id="10501-182">Create a manager authorization handler</span></span>

<span data-ttu-id="10501-183">Créer un `ContactManagerAuthorizationHandler` classe dans le *autorisation* dossier.</span><span class="sxs-lookup"><span data-stu-id="10501-183">Create a `ContactManagerAuthorizationHandler` class in the  *Authorization* folder.</span></span> <span data-ttu-id="10501-184">Le `ContactManagerAuthorizationHandler` vérifie que l’utilisateur qui agit sur la ressource est un gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="10501-184">The `ContactManagerAuthorizationHandler` will verify the user acting on the resource is a manager.</span></span> <span data-ttu-id="10501-185">Seuls les responsables peuvent approuver ou rejeter les modifications de contenu (nouvelles ou modifiées).</span><span class="sxs-lookup"><span data-stu-id="10501-185">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="10501-186">Créer un gestionnaire d’autorisations d’administrateur</span><span class="sxs-lookup"><span data-stu-id="10501-186">Create an administrator authorization handler</span></span>

<span data-ttu-id="10501-187">Créer un `ContactAdministratorsAuthorizationHandler` classe dans le *autorisation* dossier.</span><span class="sxs-lookup"><span data-stu-id="10501-187">Create a `ContactAdministratorsAuthorizationHandler` class in the  *Authorization* folder.</span></span> <span data-ttu-id="10501-188">Le `ContactAdministratorsAuthorizationHandler` vérifie que l’utilisateur qui agit sur la ressource est un administrateur.</span><span class="sxs-lookup"><span data-stu-id="10501-188">The `ContactAdministratorsAuthorizationHandler` will verify the user acting on the resource is a administrator.</span></span> <span data-ttu-id="10501-189">Administrateur peut effectuer toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="10501-189">Administrator can do all operations.</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="10501-190">Inscrire les gestionnaires d’autorisation</span><span class="sxs-lookup"><span data-stu-id="10501-190">Register the authorization handlers</span></span>

<span data-ttu-id="10501-191">Services à l’aide d’Entity Framework Core doivent être inscrit pour [injection de dépendance](xref:fundamentals/dependency-injection) à l’aide de [AddScoped](/aspnet/core/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="10501-191">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/aspnet/core/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="10501-192">Le `ContactIsOwnerAuthorizationHandler` utilise ASP.NET Core [identité](xref:security/authentication/identity), qui est basé sur Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="10501-192">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="10501-193">Enregistrez les gestionnaires avec la collection de service afin qu’ils soient disponibles pour le `ContactsController` via [injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="10501-193">Register the handlers with the service collection so they will be available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="10501-194">Ajoutez le code suivant à la fin de `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="10501-194">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=AuthorizationHandlers)]

<span data-ttu-id="10501-195">`ContactAdministratorsAuthorizationHandler`et `ContactManagerAuthorizationHandler` sont ajoutés en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="10501-195">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="10501-196">Elles sont singletons, car ils n’utilisent pas EF et toutes les informations nécessitées sont dans le `Context` paramètre de la `HandleRequirementAsync` (méthode).</span><span class="sxs-lookup"><span data-stu-id="10501-196">They are singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

<span data-ttu-id="10501-197">Le texte complet `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="10501-197">The complete `ConfigureServices`:</span></span>

[!code-csharp[Main](secure-data/samples/final/Startup.cs?name=ConfigureServices)]

## <a name="update-the-code-to-support-authorization"></a><span data-ttu-id="10501-198">Mettre à jour le code pour prendre en charge d’autorisation</span><span class="sxs-lookup"><span data-stu-id="10501-198">Update the code to support authorization</span></span>

<span data-ttu-id="10501-199">Dans cette section, vous mettre à jour le contrôleur et les vues et ajoutez une classe de configuration requise des opérations.</span><span class="sxs-lookup"><span data-stu-id="10501-199">In this section, you update the controller and views and add an operations requirements class.</span></span>

### <a name="update-the-contacts-controller"></a><span data-ttu-id="10501-200">Mettre à jour le contrôleur de Contacts</span><span class="sxs-lookup"><span data-stu-id="10501-200">Update the Contacts controller</span></span>

<span data-ttu-id="10501-201">Mise à jour le `ContactsController` constructeur :</span><span class="sxs-lookup"><span data-stu-id="10501-201">Update the `ContactsController` constructor:</span></span>

* <span data-ttu-id="10501-202">Ajouter la `IAuthorizationService` service pour accéder aux gestionnaires d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="10501-202">Add the `IAuthorizationService` service to  access to the authorization handlers.</span></span> 
* <span data-ttu-id="10501-203">Ajouter le `Identity` `UserManager` service :</span><span class="sxs-lookup"><span data-stu-id="10501-203">Add the `Identity` `UserManager` service:</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_ContactsControllerCtor)]

### <a name="add-a-contact-operations-requirements-class"></a><span data-ttu-id="10501-204">Ajouter une classe de la configuration requise operations contact</span><span class="sxs-lookup"><span data-stu-id="10501-204">Add a contact operations requirements class</span></span>

<span data-ttu-id="10501-205">Ajouter le `ContactOperationsRequirements` classe le *autorisation* dossier.</span><span class="sxs-lookup"><span data-stu-id="10501-205">Add the `ContactOperationsRequirements` class to the *Authorization* folder.</span></span> <span data-ttu-id="10501-206">Cette classe contient les exigences notre application prend en charge :</span><span class="sxs-lookup"><span data-stu-id="10501-206">This class  contain the requirements our app supports:</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

### <a name="update-create"></a><span data-ttu-id="10501-207">Création de la mise à jour</span><span class="sxs-lookup"><span data-stu-id="10501-207">Update Create</span></span>

<span data-ttu-id="10501-208">Mise à jour le `HTTP POST Create` méthode :</span><span class="sxs-lookup"><span data-stu-id="10501-208">Update the `HTTP POST Create` method to:</span></span>

* <span data-ttu-id="10501-209">Ajouter l’ID utilisateur à la `Contact` modèle.</span><span class="sxs-lookup"><span data-stu-id="10501-209">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="10501-210">Appelle le Gestionnaire d’autorisation pour vérifier que l’utilisateur propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="10501-210">Call the authorization handler to verify the user owns the contact.</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Create)]

### <a name="update-edit"></a><span data-ttu-id="10501-211">Modification de la mise à jour</span><span class="sxs-lookup"><span data-stu-id="10501-211">Update Edit</span></span>

<span data-ttu-id="10501-212">Mettre à jour les `Edit` méthodes à utiliser le Gestionnaire d’autorisation pour vérifier que l’utilisateur propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="10501-212">Update both `Edit` methods to use the authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="10501-213">Étant donné que nous exécutons d’autorisation de ressource, nous ne pouvons pas utiliser le `[Authorize]` attribut.</span><span class="sxs-lookup"><span data-stu-id="10501-213">Because we are performing resource authorization we cannot use the `[Authorize]` attribute.</span></span> <span data-ttu-id="10501-214">Nous n’avons pas accès à la ressource lorsque des attributs sont évaluées.</span><span class="sxs-lookup"><span data-stu-id="10501-214">We don't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="10501-215">L’autorisation de ressource en fonction doit être impérative.</span><span class="sxs-lookup"><span data-stu-id="10501-215">Resource based authorization must be imperative.</span></span> <span data-ttu-id="10501-216">Vérifications doivent être effectuées une fois que nous avons accès à la ressource, soit en le chargeant dans notre contrôleur, soit en le chargeant dans le Gestionnaire d’elle-même.</span><span class="sxs-lookup"><span data-stu-id="10501-216">Checks must be performed once we have access to the resource, either by loading it in our controller, or by loading it within the handler itself.</span></span> <span data-ttu-id="10501-217">Vous devez souvent accéder la ressource en passant la clé de ressource.</span><span class="sxs-lookup"><span data-stu-id="10501-217">Frequently you will access the resource by passing in the resource key.</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Edit)]

### <a name="update-the-delete-method"></a><span data-ttu-id="10501-218">Mise à jour de la méthode de suppression</span><span class="sxs-lookup"><span data-stu-id="10501-218">Update the Delete method</span></span>

<span data-ttu-id="10501-219">Mettre à jour les `Delete` méthodes à utiliser le Gestionnaire d’autorisation pour vérifier que l’utilisateur propriétaire du contact.</span><span class="sxs-lookup"><span data-stu-id="10501-219">Update both `Delete` methods to use the authorization handler to verify the user owns the contact.</span></span>

[!code-csharp[Main](secure-data/samples/final/Controllers/ContactsController.cs?name=snippet_Delete)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="10501-220">Le service d’autorisation d’injecter dans les vues</span><span class="sxs-lookup"><span data-stu-id="10501-220">Inject the authorization service into the views</span></span>

<span data-ttu-id="10501-221">Actuellement le montre l’interface utilisateur modifier et supprimer des liens pour l’utilisateur ne peut pas modifier les données.</span><span class="sxs-lookup"><span data-stu-id="10501-221">Currently the UI shows edit and delete links for data the user cannot modify.</span></span> <span data-ttu-id="10501-222">Nous allons résoudre cela en appliquant le Gestionnaire d’autorisation pour les vues.</span><span class="sxs-lookup"><span data-stu-id="10501-222">We'll fix that by applying the authorization handler to the views.</span></span>

<span data-ttu-id="10501-223">Injecter le service d’autorisation dans le *Views/_ViewImports.cshtml* afin qu’il sera disponible pour toutes les vues de fichiers :</span><span class="sxs-lookup"><span data-stu-id="10501-223">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it will be available to all views:</span></span>

[!code-html[Main](secure-data/samples/final/Views/_ViewImports.cshtml)]

<span data-ttu-id="10501-224">Mise à jour la *Views/Contacts/Index.cshtml* vue Razor uniquement afficher la modification et supprimer les liens pour les utilisateurs peuvent modifier/supprimer le contact.</span><span class="sxs-lookup"><span data-stu-id="10501-224">Update the *Views/Contacts/Index.cshtml* Razor view to only display the edit and delete links for users who can edit/delete the contact.</span></span>

<span data-ttu-id="10501-225">Ajouter`@using ContactManager.Authorization;`</span><span class="sxs-lookup"><span data-stu-id="10501-225">Add `@using ContactManager.Authorization;`</span></span>

<span data-ttu-id="10501-226">Mise à jour la `Edit` et `Delete` liens afin qu’ils sont rendus uniquement pour les utilisateurs disposant de l’autorisation de modifier ou supprimer le contact.</span><span class="sxs-lookup"><span data-stu-id="10501-226">Update the `Edit` and `Delete` links so they are only rendered for users with permission to edit and delete the contact.</span></span>

[!code-html[Main](secure-data/samples/final/Views/Contacts/Index.cshtml?range=63-84)]

<span data-ttu-id="10501-227">Avertissement : Le masquage des liens à partir des utilisateurs qui ne disposent pas d’autorisation pour modifier ou supprimer des données ne sécurise pas l’application.</span><span class="sxs-lookup"><span data-stu-id="10501-227">Warning: Hiding links from users that do not have permission to edit or delete data does not secure the app.</span></span> <span data-ttu-id="10501-228">Le masquage des liens rend l’application utilisateur plus convivial en affichant des liens n’est valides.</span><span class="sxs-lookup"><span data-stu-id="10501-228">Hiding links makes the app more user friendly by displaying only valid links.</span></span> <span data-ttu-id="10501-229">Les utilisateurs peuvent hack les URL générées pour appeler modifier et supprimer des opérations sur les données qu’ils ne possèdent pas.</span><span class="sxs-lookup"><span data-stu-id="10501-229">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span>  <span data-ttu-id="10501-230">Le contrôleur doit répéter vérifie l’accès sécurisé.</span><span class="sxs-lookup"><span data-stu-id="10501-230">The controller must repeat the access checks to be secure.</span></span>

### <a name="update-the-details-view"></a><span data-ttu-id="10501-231">Mettre à jour l’affichage des détails</span><span class="sxs-lookup"><span data-stu-id="10501-231">Update the Details view</span></span>

<span data-ttu-id="10501-232">Mettre à jour l’affichage des détails pour les gestionnaires peuvent approuver ou rejeter des contacts :</span><span class="sxs-lookup"><span data-stu-id="10501-232">Update the details view so managers can approve or reject contacts:</span></span>

[!code-html[Main](secure-data/samples/final/Views/Contacts/Details.cshtml?range=53-)]

## <a name="test-the-completed-app"></a><span data-ttu-id="10501-233">Tester l’application terminée</span><span class="sxs-lookup"><span data-stu-id="10501-233">Test the completed app</span></span>

<span data-ttu-id="10501-234">Si vous êtes à l’aide de Visual Studio Code ou de test sur une plateforme locale qui n’inclut pas un certificat de test pour le protocole SSL :</span><span class="sxs-lookup"><span data-stu-id="10501-234">If you are using Visual Studio Code or testing on local platform that doesn't include a test certificate for SSL:</span></span>

- <span data-ttu-id="10501-235">Définissez `"LocalTest:skipSSL": true` dans les *appsettings.json* fichier.</span><span class="sxs-lookup"><span data-stu-id="10501-235">Set `"LocalTest:skipSSL": true` in the *appsettings.json* file.</span></span>

<span data-ttu-id="10501-236">Si vous avez exécuté l’application et que vous disposez des contacts, supprimez tous les enregistrements dans la `Contact` de table et de redémarrer l’application pour amorcer la base de données.</span><span class="sxs-lookup"><span data-stu-id="10501-236">If you have run the app and have contacts, delete all the records in the `Contact` table and restart the app to seed the database.</span></span> <span data-ttu-id="10501-237">Si vous utilisez Visual Studio, vous devez quitter et redémarrer IIS Express à la valeur initiale de la base de données.</span><span class="sxs-lookup"><span data-stu-id="10501-237">If you are using Visual Studio, you need to exit and restart IIS Express to seed the database.</span></span>

<span data-ttu-id="10501-238">Inscrire un utilisateur pour parcourir les contacts.</span><span class="sxs-lookup"><span data-stu-id="10501-238">Register a user to browse the contacts.</span></span>

<span data-ttu-id="10501-239">Un moyen simple pour tester l’application terminée consiste à lancer des trois différents navigateurs (ou les versions furtif/navigation InPrivate).</span><span class="sxs-lookup"><span data-stu-id="10501-239">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate versions).</span></span> <span data-ttu-id="10501-240">Dans un navigateur, inscrire un nouvel utilisateur, par exemple, `test@example.com`.</span><span class="sxs-lookup"><span data-stu-id="10501-240">In one browser, register a new user, for example, `test@example.com`.</span></span> <span data-ttu-id="10501-241">Connectez-vous à chaque navigateur avec un autre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="10501-241">Sign in to each browser with a different user.</span></span> <span data-ttu-id="10501-242">Vérifiez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="10501-242">Verify the following:</span></span>

* <span data-ttu-id="10501-243">Les utilisateurs inscrits peuvent afficher toutes les données de contact approuvées.</span><span class="sxs-lookup"><span data-stu-id="10501-243">Registered users can view all the approved contact data.</span></span>
* <span data-ttu-id="10501-244">Les utilisateurs inscrits peuvent modifier/supprimer leurs propres données.</span><span class="sxs-lookup"><span data-stu-id="10501-244">Registered users can edit/delete their own data.</span></span> 
* <span data-ttu-id="10501-245">Gestionnaires peuvent approuver ou rejeter les données de contact.</span><span class="sxs-lookup"><span data-stu-id="10501-245">Managers can approve or reject contact data.</span></span> <span data-ttu-id="10501-246">Le `Details` vue présente **approuver** et **rejeter** boutons.</span><span class="sxs-lookup"><span data-stu-id="10501-246">The `Details` view shows **Approve** and **Reject** buttons.</span></span> 
* <span data-ttu-id="10501-247">Les administrateurs peuvent approuver/refuser et modifier/supprimer des données.</span><span class="sxs-lookup"><span data-stu-id="10501-247">Administrators can approve/reject and edit/delete any data.</span></span>

| <span data-ttu-id="10501-248">Utilisateur</span><span class="sxs-lookup"><span data-stu-id="10501-248">User</span></span>| <span data-ttu-id="10501-249">Options</span><span class="sxs-lookup"><span data-stu-id="10501-249">Options</span></span> |
| ------------ | ---------|
| test@example.com | <span data-ttu-id="10501-250">Peut modifier/supprimer des données propres</span><span class="sxs-lookup"><span data-stu-id="10501-250">Can edit/delete own data</span></span> |
| manager@contoso.com | <span data-ttu-id="10501-251">Peuvent approuver/refuser et modification/suppression posséder de données</span><span class="sxs-lookup"><span data-stu-id="10501-251">Can approve/reject and edit/delete own data</span></span>  |
| admin@contoso.com | <span data-ttu-id="10501-252">Peut modifier/supprimer et approuver/refuser toutes les données</span><span class="sxs-lookup"><span data-stu-id="10501-252">Can edit/delete and approve/reject all data</span></span>|

<span data-ttu-id="10501-253">Créer un contact dans le navigateur des administrateurs.</span><span class="sxs-lookup"><span data-stu-id="10501-253">Create a contact in the administrators browser.</span></span> <span data-ttu-id="10501-254">Copier l’URL pour le supprimer et modifier du contact de l’administrateur.</span><span class="sxs-lookup"><span data-stu-id="10501-254">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="10501-255">Collez ces liens dans le navigateur de l’utilisateur de test pour vérifier que l’utilisateur de test ne peut pas effectuer ces opérations.</span><span class="sxs-lookup"><span data-stu-id="10501-255">Paste these links into the test user's browser to verify the test user cannot perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="10501-256">Créer l’application de démarrage</span><span class="sxs-lookup"><span data-stu-id="10501-256">Create the starter app</span></span>

<span data-ttu-id="10501-257">Suivez ces instructions pour créer l’application de démarrage.</span><span class="sxs-lookup"><span data-stu-id="10501-257">Follow these instructions to create the starter app.</span></span>

* <span data-ttu-id="10501-258">Créer un **Application ASP.NET Core Web** à l’aide de [Visual Studio 2017](https://www.visualstudio.com/) nommé « ContactManager »</span><span class="sxs-lookup"><span data-stu-id="10501-258">Create an **ASP.NET Core Web Application** using [Visual Studio 2017](https://www.visualstudio.com/) named "ContactManager"</span></span>

  * <span data-ttu-id="10501-259">Créer l’application avec **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="10501-259">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="10501-260">Pour votre espace de noms correspond à l’utilisation de l’espace de noms dans l’exemple, nommez-Le « ContactManager ».</span><span class="sxs-lookup"><span data-stu-id="10501-260">Name it "ContactManager" so your namespace will match the namespace use in the sample.</span></span>

* <span data-ttu-id="10501-261">Ajoutez le code suivant `Contact` modèle :</span><span class="sxs-lookup"><span data-stu-id="10501-261">Add the following `Contact` model:</span></span>

  [!code-csharp[Main](secure-data/samples/starter/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="10501-262">Une vue de structure le `Contact` de modèle à l’aide d’Entity Framework Core et `ApplicationDbContext` contexte de données.</span><span class="sxs-lookup"><span data-stu-id="10501-262">Scaffold the `Contact` model using Entity Framework Core and the `ApplicationDbContext` data context.</span></span> <span data-ttu-id="10501-263">Acceptez toutes les valeurs par défaut de la structure.</span><span class="sxs-lookup"><span data-stu-id="10501-263">Accept all the scaffolding defaults.</span></span> <span data-ttu-id="10501-264">À l’aide de `ApplicationDbContext` pour le contexte de données classe place la table contact dans le [identité](xref:security/authentication/identity) base de données.</span><span class="sxs-lookup"><span data-stu-id="10501-264">Using `ApplicationDbContext` for the data context class  puts the contact table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="10501-265">Consultez [Ajout d’un modèle](xref:tutorials/first-mvc-app/adding-model) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="10501-265">See [Adding a model](xref:tutorials/first-mvc-app/adding-model) for more information.</span></span>

* <span data-ttu-id="10501-266">Mise à jour la **ContactManager** d’ancrage dans le *Views/Shared/_Layout.cshtml* à partir du fichier `asp-controller="Home"` à `asp-controller="Contacts"` donc en appuyant sur la **ContactManager** lien appelle le contrôleur de Contacts.</span><span class="sxs-lookup"><span data-stu-id="10501-266">Update the **ContactManager** anchor in the *Views/Shared/_Layout.cshtml* file from `asp-controller="Home"` to `asp-controller="Contacts"` so tapping the **ContactManager** link will invoke the Contacts controller.</span></span> <span data-ttu-id="10501-267">Le balisage d’origine :</span><span class="sxs-lookup"><span data-stu-id="10501-267">The original markup:</span></span>

```html
   <a asp-area="" asp-controller="Home" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

<span data-ttu-id="10501-268">Le balisage mis à jour :</span><span class="sxs-lookup"><span data-stu-id="10501-268">The updated markup:</span></span>

```html
   <a asp-area="" asp-controller="Contacts" asp-action="Index" class="navbar-brand">ContactManager</a>
   ```

* <span data-ttu-id="10501-269">Structurez la migration initiale et de mettre à jour de la base de données</span><span class="sxs-lookup"><span data-stu-id="10501-269">Scaffold the initial migration and update the database</span></span>

```none
   dotnet ef migrations add initial
   dotnet ef database update
   ```

* <span data-ttu-id="10501-270">Tester l’application par la création, modification et suppression d’un contact</span><span class="sxs-lookup"><span data-stu-id="10501-270">Test the app by creating, editing and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="10501-271">Amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="10501-271">Seed the database</span></span>

<span data-ttu-id="10501-272">Ajouter le `SeedData` classe le *données* dossier.</span><span class="sxs-lookup"><span data-stu-id="10501-272">Add the `SeedData` class to the *Data* folder.</span></span> <span data-ttu-id="10501-273">Si vous avez téléchargé l’exemple, vous pouvez copier le *SeedData.cs* de fichiers à la *données* dossier du projet de démarrage.</span><span class="sxs-lookup"><span data-stu-id="10501-273">If you've downloaded the sample, you can copy the *SeedData.cs* file to the *Data* folder of the starter project.</span></span>

[!code-csharp[Main](secure-data/samples/starter/Data/SeedData.cs)]

<span data-ttu-id="10501-274">Ajoutez le code en surbrillance à la fin de la `Configure` méthode dans le *Startup.cs* fichier :</span><span class="sxs-lookup"><span data-stu-id="10501-274">Add the highlighted code to the end of the `Configure` method in the *Startup.cs* file:</span></span>

[!code-csharp[Main](secure-data/samples/starter/Startup.cs?name=Configure&highlight=28-)]

<span data-ttu-id="10501-275">Test de l’application d’amorçage la base de données.</span><span class="sxs-lookup"><span data-stu-id="10501-275">Test that the app seeded the database.</span></span> <span data-ttu-id="10501-276">La méthode de la valeur de départ n’est pas exécuté s’il existe des lignes dans la base de données de contact.</span><span class="sxs-lookup"><span data-stu-id="10501-276">The seed method does not run if there are any rows in the contact DB.</span></span>

### <a name="create-a-class-used-in-the-tutorial"></a><span data-ttu-id="10501-277">Créer une classe utilisée dans le didacticiel</span><span class="sxs-lookup"><span data-stu-id="10501-277">Create a class used in the tutorial</span></span>

* <span data-ttu-id="10501-278">Créez un dossier nommé *autorisation*.</span><span class="sxs-lookup"><span data-stu-id="10501-278">Create a folder named *Authorization*.</span></span>
* <span data-ttu-id="10501-279">Copie le *Authorization\ContactOperations.cs* de fichiers à partir du téléchargement du projet terminé, ou copiez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="10501-279">Copy the *Authorization\ContactOperations.cs* file from the completed project download, or copy the following code:</span></span>

[!code-csharp[Main](secure-data/samples/final/Authorization/ContactOperations.cs)]

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="10501-280">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="10501-280">Additional resources</span></span>

* <span data-ttu-id="10501-281">[ASP.NET Core d’autorisation Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="10501-281">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="10501-282">Ce laboratoire présente en détail sur les fonctionnalités de sécurité présentées dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="10501-282">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* [<span data-ttu-id="10501-283">Autorisation dans ASP.NET Core : Simple, des rôles, basée sur les revendications et personnalisées</span><span class="sxs-lookup"><span data-stu-id="10501-283">Authorization in ASP.NET Core : Simple, role, claims-based and custom</span></span>](index.md)
* [<span data-ttu-id="10501-284">Autorisation basée sur une stratégie personnalisée</span><span class="sxs-lookup"><span data-stu-id="10501-284">Custom Policy-Based Authorization</span></span>](policies.md)
