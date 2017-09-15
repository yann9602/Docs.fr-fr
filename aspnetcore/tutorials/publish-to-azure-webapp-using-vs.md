---
title: "Publier une application ASP.NET Core sur Azure à l’aide de Visual Studio"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: c4df5f4551760fbc4ed4b11362d249b24f186e00
ms.sourcegitcommit: 8f5277871eff86134ebf68d3737196cfd4a62c2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a>Publier une application web ASP.NET Core sur Azure App Service à l’aide de Visual Studio

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Cesar Blum Silveira](https://github.com/cesarbs)

## <a name="set-up-the-development-environment"></a>Configurer l’environnement de développement

* Installez la dernière version du [SDK Azure pour Visual Studio](https://www.visualstudio.com/features/azure-tools-vs). Le SDK installe Visual Studio s’il ne l’est pas déjà.

> [!NOTE]
> L’installation du SDK peut prendre plus de 30 minutes si votre ordinateur ne possède pas la plupart des dépendances.

* Installer [.NET Core et les outils Visual Studio](http://go.microsoft.com/fwlink/?LinkID=798306)

* Vérifiez votre [compte Azure](https://portal.azure.com/). Vous pouvez [ouvrir un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/) ou [activer les avantages pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="create-a-web-app"></a>Créer une application web

Dans la page de démarrage de Visual Studio, appuyez sur **Nouveau projet...**

![Page de démarrage](publish-to-azure-webapp-using-vs/_static/new_project.png)

Vous pouvez également créer un projet à l’aide des menus. Appuyez sur **Fichier > Nouveau > Projet...**

![Menu Fichier](publish-to-azure-webapp-using-vs/_static/alt_new_project.png)

Renseignez la boîte de dialogue **Nouveau projet** :

* Dans le volet gauche, appuyez sur **Web**

* Dans le volet central, appuyez sur **Application web ASP.NET Core (.NET Core)**

* Appuyez sur **OK**

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Dans la boîte de dialogue **Nouvelle application web ASP.NET Core (.NET Core)** :

* Appuyez sur **Application web**

* Vérifiez que **Authentification** est défini sur **Comptes d’utilisateur individuels**

* Vérifiez que la case **Hôte dans le cloud** n’est **pas** cochée

* Appuyez sur **OK**

![Boîte de dialogue Nouvelle application web ASP.NET Core (.NET Core)](publish-to-azure-webapp-using-vs/_static/noath.png)

## <a name="test-the-app-locally"></a>Tester l’application localement

* Appuyez sur **Ctrl+F5** pour exécuter l’application localement.

* Appuyez sur les liens **À propos de** et **Contact**. En fonction de la taille de votre appareil, vous devrez peut-être appuyer sur l’icône de navigation pour afficher ces liens.

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

* Appuyez sur **S’inscrire**, puis inscrivez un nouvel utilisateur. Vous pouvez utiliser une adresse e-mail fictive. Quand vous effectuez l’envoi, l’erreur suivante s’affiche :

![Erreur de serveur interne : Une opération de base de données a échoué lors du traitement de la requête. Exception SQL : Impossible d’ouvrir la base de données. Appliquer des migrations existantes pour le contexte de base de données d’application peut résoudre ce problème.](publish-to-azure-webapp-using-vs/_static/mig.png)

Vous pouvez résoudre le problème de deux manières différentes :

* Appuyez sur **Appliquer les migrations**, puis, une fois la page mise à jour, actualisez-la ; ou

* Exécutez la commande suivante à partir d’une invite de commandes dans le répertoire du projet :

  <!-- literal_block {"ids": [], "xml:space": "preserve"} -->

  ```
  dotnet ef database update
     ```

L’application affiche l’adresse e-mail utilisée pour inscrire le nouvel utilisateur et un lien **Fermer la session**.

![Application web ouverte dans Microsoft Edge. Le lien S’inscrire est remplacé par le texte Hello abc@example.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Déployer l’application sur Azure

Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution. Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution. Le déploiement ne peut pas se produire car les fichiers verrouillés ne peuvent pas être copiés.

Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez **Publier...**

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

Dans la boîte de dialogue **Publier**, appuyez sur **Microsoft Azure App Service**.

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

Appuyez sur **Nouveau...** pour créer un groupe de ressources. La création d’un groupe de ressources permet de supprimer plus facilement toutes les ressources Azure que vous créez dans ce didacticiel.

![Boîte de dialogue App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Créez un groupe de ressources et un plan App Service :

* Appuyez sur **Nouveau...** pour le groupe de ressources, puis entrez un nom pour le nouveau groupe de ressources

* Appuyez sur **Nouveau...** pour le plan App Service, puis sélectionnez un lieu près de chez vous. Vous pouvez conserver le nom généré par défaut

* Appuyez sur **Explorer des services Azure supplémentaires** pour créer une base de données

![Boîte de dialogue Nouveau groupe de ressources : panneau Hébergement](publish-to-azure-webapp-using-vs/_static/cas.png)

* Appuyez sur l’icône **+** verte pour créer une base de données SQL

![Boîte de dialogue Nouveau groupe de ressources : panneau Services](publish-to-azure-webapp-using-vs/_static/sql.png)

* Appuyez sur **Nouveau...** dans la boîte de dialogue **Configurer la base de données SQL** pour créer un serveur de base de données.

![Boîte de dialogue Configurer la base de données SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

* Entrez un nom d’utilisateur et un mot de passe administrateur, puis appuyez sur **OK**. Mémorisez le nom d’utilisateur et le mot de passe que vous créez à cette étape. Vous pouvez conserver le **Nom du serveur** par défaut

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

> [!NOTE]
> « admin » n’est pas autorisé comme nom d’utilisateur administrateur.

* Appuyez sur **OK** dans la boîte de dialogue **Configurer la base de données SQL**

![Boîte de dialogue Configurer la base de données SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

* Appuyez sur **Créer** dans la boîte de dialogue **Créer App Service**

![Boîte de dialogue Créer App Service](publish-to-azure-webapp-using-vs/_static/create_as.png)

* Appuyez sur **Suivant** dans la boîte de dialogue **Publier**

![Boîte de dialogue Publier : panneau Connexion](publish-to-azure-webapp-using-vs/_static/pubc.png)

* Dans la phase **Paramètres** de la boîte de dialogue **Publier** :

  * Développez **Bases de données**, puis cochez **Utilisez cette chaîne de connexion au moment de l’exécution**

  * Développez **Migrations Entity Framework**, puis cochez **Appliquer cette migration lors de la publication**

* Appuyez sur **Publier**, puis attendez que Visual Studio ait terminé de publier votre application

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pubs.png)

Visual Studio publie votre application sur Azure et lance l’application cloud dans votre navigateur.

### <a name="test-your-app-in-azure"></a>Tester votre application dans Azure

* Testez les liens **À propos de** et **Contact**

* Inscrivez un nouvel utilisateur

![Application web ouverte dans Microsoft Edge sur Azure App Service](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="update-the-app"></a>Mettre à jour l’application

* Modifiez le fichier vue `Views/Home/About.cshtml` Razor et changez son contenu. Exemple :

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [7]}} -->

```html
@{
       ViewData["Title"] = "About";
   }
   <h2>@ViewData["Title"].</h2>
   <h3>@ViewData["Message"]</h3>

   <p>My updated about page.</p>
   ```

* Cliquez avec le bouton droit sur le projet, puis appuyez à nouveau sur **Publier...**

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

* Une fois l’application publiée, vérifiez que les modifications apportées sont disponibles sur Azure

### <a name="clean-up"></a>Nettoyer

Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.

* Sélectionnez **Groupes de ressources**, puis appuyez sur le groupe de ressources que vous avez créé

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Dans le panneau **Groupe de ressources**, appuyez sur **Supprimer**

![Portail Azure : panneau Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Entrez le nom du groupe de ressources, puis appuyez sur **Supprimer**. Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimés d’Azure

### <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec ASP.NET Core MVC et Visual Studio](first-mvc-app/start-mvc.md)

* [Présentation d’ASP.NET Core](../index.md)

* [Notions de base](../fundamentals/index.md)
