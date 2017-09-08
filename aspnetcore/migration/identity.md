---
title: "Identité et authentification de migration"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0db145cb-41a5-448a-b889-72e2d789ad7f
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/identity
ms.openlocfilehash: 41682244cb9faa9a109661c09b6d7e67bca72534
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="migrating-authentication-and-identity"></a><span data-ttu-id="5bcfb-103">Identité et authentification de migration</span><span class="sxs-lookup"><span data-stu-id="5bcfb-103">Migrating Authentication and Identity</span></span>

<a name=migration-identity></a>

<span data-ttu-id="5bcfb-104">Par [Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="5bcfb-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="5bcfb-105">Dans le précédent article nous [migré la configuration à partir d’un projet ASP.NET MVC à ASP.NET MVC de base](configuration.md).</span><span class="sxs-lookup"><span data-stu-id="5bcfb-105">In the previous article we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](configuration.md).</span></span> <span data-ttu-id="5bcfb-106">Dans cet article, nous migrer les fonctionnalités de gestion d’inscription et connexion utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="5bcfb-107">Configurez l’identité et l’appartenance</span><span class="sxs-lookup"><span data-stu-id="5bcfb-107">Configure Identity and Membership</span></span>

<span data-ttu-id="5bcfb-108">Dans ASP.NET MVC, les fonctionnalités d’authentification et identité sont configurées à l’aide d’ASP.NET Identity dans Startup.Auth.cs et IdentityConfig.cs, situé dans le dossier App_Start.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in Startup.Auth.cs and IdentityConfig.cs, located in the App_Start folder.</span></span> <span data-ttu-id="5bcfb-109">Dans ASP.NET MVC de base, ces fonctionnalités sont configurées dans *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="5bcfb-110">Installer le `Microsoft.AspNetCore.Identity.EntityFrameworkCore` et `Microsoft.AspNetCore.Authentication.Cookies` les packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="5bcfb-111">Ensuite, ouvrez Startup.cs et mettre à jour le `ConfigureServices()` méthode à utiliser les services d’Entity Framework et de l’identité :</span><span class="sxs-lookup"><span data-stu-id="5bcfb-111">Then, open Startup.cs and update the `ConfigureServices()` method to use Entity Framework and Identity services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
  // Add EF services to the services container.
  services.AddEntityFramework(Configuration)
    .AddSqlServer()
    .AddDbContext<ApplicationDbContext>();

  // Add Identity services to the services container.
  services.AddIdentity<ApplicationUser, IdentityRole>(Configuration)
    .AddEntityFrameworkStores<ApplicationDbContext>();

  services.AddMvc();
}
```

<span data-ttu-id="5bcfb-112">À ce stade, il existe des deux types référencés dans le code ci-dessus, nous n’avons pas encore été migrés à partir du projet ASP.NET MVC : `ApplicationDbContext` et `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="5bcfb-113">Créer un nouveau *modèles* dossier dans le noyau ASP.NET le projet, puis ajoutez les deux classes lui correspondant à ces types.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="5bcfb-114">Vous trouverez le ASP.NET MVC versions de ces classes dans `/Models/IdentityModels.cs`, mais nous allons utiliser un fichier par la classe dans le projet migré car il s’agit plus claire.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-114">You will find the ASP.NET MVC versions of these classes in `/Models/IdentityModels.cs`, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="5bcfb-115">ApplicationUser.cs :</span><span class="sxs-lookup"><span data-stu-id="5bcfb-115">ApplicationUser.cs:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvc6Project.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="5bcfb-116">ApplicationDbContext.cs :</span><span class="sxs-lookup"><span data-stu-id="5bcfb-116">ApplicationDbContext.cs:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
using Microsoft.AspNetCore.Identity.EntityFramework;
using Microsoft.Data.Entity;

namespace NewMvc6Project.Models
{
  public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
  {
    public ApplicationDbContext()
    {
      Database.EnsureCreated();
    }

    protected override void OnConfiguring(DbContextOptions options)
    {
      options.UseSqlServer();
    }
  }
}
```

<span data-ttu-id="5bcfb-117">Personnalisation des utilisateurs ou le ApplicationDbContext n’inclut pas le projet Web de Starter MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the ApplicationDbContext.</span></span> <span data-ttu-id="5bcfb-118">Lorsque vous migrez une application réelle, vous devrez également migrer toutes les propriétés personnalisées et les méthodes de l’utilisateur de votre application et les classes de DbContext, ainsi que d’autres classes de modèle, que votre application utilise (par exemple, si votre DbContext a un DbSet<Album>, vous devrez bien entendu migrer la classe Album).</span><span class="sxs-lookup"><span data-stu-id="5bcfb-118">When migrating a real application, you will also need to migrate all of the custom properties and methods of your application's user and DbContext classes, as well as any other Model classes your application utilizes (for example, if your DbContext has a DbSet<Album>, you will of course need to migrate the Album class).</span></span>

<span data-ttu-id="5bcfb-119">Ces fichiers en place, le fichier Startup.cs est possible pour compiler à la mise à jour à l’aide de ses instructions :</span><span class="sxs-lookup"><span data-stu-id="5bcfb-119">With these files in place, the Startup.cs file can be made to compile by updating its using statements:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
using Microsoft.Framework.ConfigurationModel;
using Microsoft.AspNetCore.Hosting;
using NewMvc6Project.Models;
using Microsoft.AspNetCore.Identity;
```

<span data-ttu-id="5bcfb-120">Notre application est maintenant prête à prendre en charge les services d’authentification et identité - il suffit d’activer ces fonctionnalités exposées aux utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-120">Our application is now ready to support authentication and identity services - it just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="5bcfb-121">Migrer l’inscription et la logique de connexion</span><span class="sxs-lookup"><span data-stu-id="5bcfb-121">Migrate Registration and Login Logic</span></span>

<span data-ttu-id="5bcfb-122">Avec les services d’identité configurés pour l’accès aux applications et données configuré à l’aide d’Entity Framework et SQL Server, nous êtes maintenant prêts à ajouter la prise en charge pour l’inscription et connexion à l’application.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-122">With identity services configured for the application and data access configured using Entity Framework and SQL Server, we are now ready to add support for registration and login to the application.</span></span> <span data-ttu-id="5bcfb-123">N’oubliez pas que [plus haut dans le processus de migration](mvc.md#migrate-layout-file) nous commenté une référence à _LoginPartial dans _Layout.cshtml.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-123">Recall that [earlier in the migration process](mvc.md#migrate-layout-file) we commented out a reference to _LoginPartial in _Layout.cshtml.</span></span> <span data-ttu-id="5bcfb-124">Il est maintenant temps pour revenir à ce code, supprimez les commentaires et l’ajouter dans les contrôleurs nécessaires et les vues pour prendre en charge les fonctionnalités de connexion.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-124">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="5bcfb-125">Mise à jour _Layout.cshtml ; ne pas commenter la @Html.Partial ligne :</span><span class="sxs-lookup"><span data-stu-id="5bcfb-125">Update _Layout.cshtml; uncomment the @Html.Partial line:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "none"} -->

```none
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="5bcfb-126">Maintenant, ajoutez une nouvelle Page de vue MVC appelé _LoginPartial dans le dossier Views/Shared :</span><span class="sxs-lookup"><span data-stu-id="5bcfb-126">Now, add a new MVC View Page called _LoginPartial to the Views/Shared folder:</span></span>

<span data-ttu-id="5bcfb-127">Mettre à jour de _LoginPartial.cshtml avec le code suivant (Remplacez tout son contenu) :</span><span class="sxs-lookup"><span data-stu-id="5bcfb-127">Update _LoginPartial.cshtml with the following code (replace all of its contents):</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
@inject SignInManager<User> SignInManager
@inject UserManager<User> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="LogOff" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log off</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

<span data-ttu-id="5bcfb-128">À ce stade, vous devez être en mesure d’actualiser le site dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-128">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="5bcfb-129">Résumé</span><span class="sxs-lookup"><span data-stu-id="5bcfb-129">Summary</span></span>

<span data-ttu-id="5bcfb-130">ASP.NET Core introduit des modifications pour les fonctionnalités d’identité ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-130">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="5bcfb-131">Dans cet article, vous avez vu comment migrer les fonctionnalités de gestion de l’authentification et l’utilisateur d’une identité ASP.NET vers ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5bcfb-131">In this article, you have seen how to migrate the authentication and user management features of an ASP.NET Identity to ASP.NET Core.</span></span>
