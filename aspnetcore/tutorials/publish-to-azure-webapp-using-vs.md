---
title: "Publier une application ASP.NET Core sur Azure à l’aide de Visual Studio"
author: rick-anderson
description: "Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio."
ms.author: riande
manager: wpickett
ms.date: 12/16/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: d0e64c967ff332365981338809a47faf35d499ab
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a>Publier une application web ASP.NET Core sur Azure App Service à l’aide de Visual Studio

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs) et [Rachel Appel](https://twitter.com/rachelappel)

Consultez la page [Publier sur Azure à partir de Visual Studio pour Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) si vous travaillez sur un Mac.

## <a name="set-up"></a>Installer

* Ouvrez un [compte Azure gratuit](https://aka.ms/K5y5yh) si vous n’en avez pas. 

## <a name="create-a-web-app"></a>Créer une application web

Dans la page de démarrage de Visual Studio, sélectionnez **Fichier > Nouveau > Projet...**

![menu Fichier](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Renseignez la boîte de dialogue **Nouveau projet** :

* Dans le volet gauche, sélectionnez **.NET Core**.
* Dans le volet central, sélectionnez **Application web ASP.NET Core**.
* Sélectionnez **OK**.

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Dans la boîte de dialogue **Nouvelle application web ASP.NET Core** :

* Sélectionnez **Application web**.
* Sélectionnez **Modifier l’authentification**.

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

La boîte de dialogue **Modifier l’authentification** s’affiche. 

* Sélectionnez **Comptes d’utilisateur individuels**.
* Sélectionnez **OK** pour revenir à la **nouvelle application web ASP.NET Core**, puis sélectionnez à nouveau **OK**.

![Boîte de dialogue Nouvelle application web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio crée la solution.

## <a name="run-the-app"></a>Exécuter l'application

* Appuyez sur CTRL+F5 pour exécuter le projet.
* Testez les liens **À propos de** et **Contact**.

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Inscrire un utilisateur

* Sélectionnez **S’inscrire**, puis inscrivez un nouvel utilisateur. Vous pouvez utiliser une adresse e-mail fictive. Quand vous effectuez l’envoi, la page affiche l’erreur suivante :

    *« Erreur de serveur interne : Une opération de base de données a échoué lors du traitement de la requête. Exception SQL : Impossible d’ouvrir la base de données. Appliquer des migrations existantes pour le contexte de base de données d’application peut résoudre ce problème. »*
* Sélectionnez **Appliquer les migrations**, puis, une fois la page mise à jour, actualisez-la.

![Erreur de serveur interne : Une opération de base de données a échoué lors du traitement de la requête. Exception SQL : Impossible d’ouvrir la base de données. Appliquer des migrations existantes pour le contexte de base de données d’application peut résoudre ce problème.](publish-to-azure-webapp-using-vs/_static/mig.png)

L’application affiche l’adresse e-mail utilisée pour inscrire le nouvel utilisateur et un lien **Se déconnecter**.

![Application web ouverte dans Microsoft Edge. Le lien S’inscrire est remplacé par le texte Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Déployer l’application sur Azure

Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez **Publier...**

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

Dans la boîte de dialogue **Publier** :

* Sélectionnez **Microsoft Azure App Service**.
* Sélectionnez l’icône d’engrenage, puis **Créer un profil**.
* Sélectionnez **Créer un profil**.

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Créer des ressources Azure

La boîte de dialogue **Créer App Service** s’affiche :

* Entrez votre abonnement.
* Les champs d’entrée **Nom de l’application**, **Groupe de ressources** et **Plan App Service** sont renseignés. Vous pouvez conserver ces noms ou les changer.

![Boîte de dialogue App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Sélectionnez l’onglet **Services** pour créer une base de données.

* Sélectionnez l’icône **+** verte pour créer une base de données SQL.

![Nouvelle base de données SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* Sélectionnez **Nouveau...** dans la boîte de dialogue **Configurer la base de données SQL** pour créer une base de données.

![Nouvelle base de données et nouveau serveur SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

La boîte de dialogue **Configurer le serveur SQL Server** s’affiche.

* Entrez un nom d’utilisateur et un mot de passe administrateur, puis sélectionnez **OK**. Vous pouvez conserver le **Nom du serveur** par défaut. 

> [!NOTE]
> « admin » n’est pas autorisé comme nom d’utilisateur administrateur.

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Sélectionnez **OK**.

Visual Studio retourne la boîte de dialogue **Créer App Service**.

* Sélectionnez **Créer** dans la boîte de dialogue **Créer App Service**.

![Boîte de dialogue Configurer la base de données SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio crée l’application web et SQL Server sur Azure. Cette étape peut prendre quelques minutes. Pour plus d’informations sur les ressources créées, consultez [Ressources supplémentaires](#additonal-resources).

Quand le déploiement est terminé, sélectionnez **Paramètres** :

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Dans la page **Paramètres** de la boîte de dialogue **Publier** :

  * Développez **Bases de données**, puis cochez **Utilisez cette chaîne de connexion au moment de l’exécution**.
  * Développez **Migrations Entity Framework**, puis cochez **Appliquer cette migration lors de la publication**.

* Sélectionnez **Enregistrer**. Visual Studio retourne à la boîte de dialogue **Publier**. 

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pubs.png)

Cliquez sur **Publier**. Visual Studio publie votre application sur Azure. Quand le déploiement est terminé, l’application est ouverte dans un navigateur.

### <a name="test-your-app-in-azure"></a>Tester votre application dans Azure

* Testez les liens **À propos de** et **Contact**

* Inscrivez un nouvel utilisateur

![Application web ouverte dans Microsoft Edge sur Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Mettre à jour l’application

* Modifiez la page Razor *Pages/About.cshtml*, puis changez son contenu. Par exemple, vous pouvez modifier le paragraphe pour indiquer « Hello ASP.NET Core! » : [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

* Une fois l’application publiée, vérifiez que les changements apportés sont disponibles sur Azure.

![Vérifiez que la tâche est terminée](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Nettoyer

Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.

* Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé.

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Dans la page **Groupes de ressources**, sélectionnez **Supprimer**.

![Portail Azure : page Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**. Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.

### <a name="next-steps"></a>Étapes suivantes

* [Déploiement continu sur Azure avec Visual Studio et Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)

## <a name="additonal-resources"></a>Ressources supplémentaires

* [Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
* [Groupes de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)
