---
uid: identity/overview/getting-started/developing-aspnet-apps-with-windows-azure-active-directory
title: "Développement d’applications ASP.NET avec Azure Active Directory | Documents Microsoft"
author: Rick-Anderson
description: "Outils Microsoft ASP.NET pour Azure Active Directory facilite activer l’authentification pour les applications web hébergées sur Azure. Vous pouvez utiliser Azure Authenti..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2014
ms.topic: article
ms.assetid: 457d7eaf-ee76-4ceb-9082-c7c1721435ad
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/getting-started/developing-aspnet-apps-with-windows-azure-active-directory
msc.type: authoredcontent
ms.openlocfilehash: 425f8edff41588db363055d166995d5f563c5a23
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="developing-aspnet-apps-with-azure-active-directory"></a>Développement d’applications ASP.NET avec Azure Active Directory
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Des outils Microsoft ASP.NET pour Azure Active Directory facilite l’activer l’authentification pour les applications web hébergées sur [Azure](https://www.windowsazure.com/en-us/home/features/web-sites/). Vous pouvez utiliser l’authentification Azure pour authentifier les utilisateurs d’Office 365 à partir de votre organisation, les comptes d’entreprise synchronisés à partir de votre annuaire Active Directory sur site ou les utilisateurs créés dans votre domaine Azure Active Directory personnalisé. Activer l’authentification de Windows Azure configure votre application pour authentifier les utilisateurs à l’aide d’un seul [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) client.
> 
>  Ce didacticiel a été rédigé par Rick Anderson[@RickAndMSFT](https://twitter.com/#!/RickAndMSFT)


Ce didacticiel va vous montrer comment créer une application ASP.NET qui est configurée pour l’authentification avec [Azure Active Directory](https://msdn.microsoft.com/en-us/library/azure/mt168838.aspx) (Azure AD). Vous apprendrez également comment appeler l’API Graph pour obtenir des informations sur l’utilisateur actuellement connecté et le déploiement de l’application sur Azure.

## <a name="prerequisites"></a>Conditions préalables

1. [Visual Studio Express 2013 pour le Web](https://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express) ou [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads).
2. [Visual Studio 2013 Update 4](https://www.microsoft.com/en-us/download/details.aspx?id=44921) -mise à jour 3 ou version ultérieure est requis.
3. Un compte Azure. [Cliquez ici](https://azure.microsoft.com/en-us/pricing/free-trial/) pour un essai gratuit si vous n’avez pas déjà un compte.

## <a name="add-a-global-administrator-to-your-active-directory"></a>Ajouter un administrateur Global pour votre annuaire Active Directory

1. Se connecter à la [portail de gestion Azure](https://manage.windowsazure.com/).
2. Tous les comptes Azure contiennent un **répertoire par défaut** --cliquez dessus, puis cliquez sur le **utilisateurs** onglet en haut de la page (voir l’image ci-dessous).
3. Cliquez sur Ajouter un utilisateur.  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image1.png)
4. Créer un nouvel utilisateur avec le **administrateur Global** rôle. Cliquez sur **utilisateurs** dans le menu supérieur, puis cliquez sur le **ajouter un utilisateur** bouton sur la barre de commandes.
5. Dans le **ajouter un utilisateur** boîte de dialogue, entrez un nom pour le nouvel utilisateur, puis cliquez sur la flèche droite.  
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image2.png)
6. Entrez le nom d’utilisateur et le rôle de la valeur **administrateur Global**. Les administrateurs globaux requièrent une adresse de messagerie pour à des fins de récupération de mot de passe. Une fois que vous avez terminé, cliquez sur la flèche droite.  
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image3.png)
7. Dans la page suivante de la boîte de dialogue, cliquez sur **créer**. Un mot de passe temporaire sera créé pour le nouvel utilisateur et affiché dans la boîte de dialogue.   
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image4.png)  
  
 Enregistrer le mot de passe, vous serez requis pour modifier le mot de passe après la première connexion. L’illustration suivante montre le nouveau compte d’administrateur. Vous devez utiliser Azure Active Directory pour vous connecter à votre application, et non le compte Microsoft également affiché sur cette page.  
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image5.png)

## <a name="create-an-aspnet-application"></a>Créer une Application ASP.NET

Les étapes suivantes utilisent [Visual Studio Express 2013 pour le Web](https://www.microsoft.com/en-us/download/details.aspx?id=40747)et nécessite [Visual Studio 2013 Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=43721).

1. Dans Visual Studio, cliquez sur **fichier** , puis **nouveau projet**. Sur le **nouveau projet** boîte de dialogue, sélectionnez Visual c# Web du projet dans le menu de gauche, cliquez sur **OK**. Vous pouvez également désactiver le **ajouter Application Insights au projet** si vous ne souhaitez pas que les fonctionnalités de votre application.
2. Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez **MVC**, puis cliquez sur **modifier l’authentification**.   
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image6.png)
3. Sur le **modifier l’authentification** boîte de dialogue, sélectionnez **comptes professionnels**. Ces options peuvent être utilisées pour automatiquement enregistrer votre application auprès d’Azure AD ainsi que pour configurer automatiquement votre application à intégrer à Azure AD. Vous n’êtes pas obligé d’utiliser le **modifier l’authentification** boîte de dialogue pour inscrire et configurer votre application, mais il rend plus facile. Si vous utilisez Visual Studio 2012 par exemple, vous pouvez manuellement inscrire l’application dans le portail de gestion Azure et mettre à jour sa configuration à intégrer à Azure AD.  
 Dans les menus de la liste déroulante, sélectionnez **Cloud - organisation** et **Single Sign On, les données d’annuaire en lecture**. Entrez le domaine de votre annuaire Azure AD, par exemple (dans les images ci-dessous) *aricka0yahoo.onmicrosoft.com*, puis cliquez sur **OK**. Vous pouvez obtenir le nom de domaine à partir de l’onglet domaines pour le répertoire par défaut sur le portail azure (voir l’image suivante vers le bas).   
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image7.png)  
  
 L’illustration suivante montre le nom de domaine à partir du portail Azure.  
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image8.png)  

    > [!NOTE]
    > Vous pouvez éventuellement configurer l’URI ID d’Application qui sera inscrit dans Azure AD en cliquant sur **plus d’Options**. L’URI ID d’application est l’identificateur unique pour une application, ce qui est inscrit dans Azure AD et utilisée par l’application pour s’identifier lors de la communication avec Azure AD. Pour plus d’informations sur l’URI ID d’application et d’autres propriétés des applications, consultez [cette rubrique](https://msdn.microsoft.com/en-us/library/azure/dn499820.aspx#BKMK_Registering). En cliquant sur la case à cocher sous le champ URI ID d’application, vous pouvez également choisir de remplacer une inscription existante dans Azure AD, qui utilise le même URI ID d’application.
4. Après avoir cliqué sur **OK**, une boîte de dialogue Connexion s’affiche et vous devez vous connecter à l’aide d’un compte d’administrateur général (pas le compte Microsoft associé à votre abonnement). Si vous avez créé un nouveau compte d’administrateur précédemment, vous allez être amené à modifier le mot de passe et puis vous reconnecter en utilisant le nouveau mot de passe.   
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image9.png)
5. Une fois que vous avez été authentifié avec succès, le **nouveau projet ASP.NET** boîte de dialogue affiche votre choix de l’authentification (**société** ) et le répertoire où la nouvelle application sera inscrite (*aricka0yahoo.onmicrosoft.com* dans l’image ci-dessous). Sous ces informations, sélectionnez la case à cocher **hôte dans le cloud**. Si cette case à cocher est sélectionnée, le projet sera déployé comme une application web Azure et sera activé pour la publication facile ultérieurement. Cliquez sur **OK**.   
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image10.png)
6. Le **configurer le site Web Azure** boîte de dialogue apparaît, à l’aide d’un nom généré automatiquement le site et la région. Notez également le compte que vous êtes actuellement connecté dans la boîte de dialogue. Vous souhaitez vous assurer que ce compte est celui que votre abonnement Azure est associée, en général, un compte Microsoft.

    > [!NOTE]
    > Ce projet nécessite une base de données. Vous devez sélectionner une de vos bases de données existants ou créez-en un. Une base de données est requise, car le projet utilise déjà un fichier de base de données locale pour stocker une petite quantité de données de configuration de l’authentification. Lorsque vous déployez l’application sur un site Web Azure, cette base de données n’est pas fourni avec le déploiement, vous devez choisir un qui est accessible dans le cloud. Cliquez sur **OK**.

    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image11.png)
7. Le projet sera créé, et vos options d’authentification et les options de l’application web seront automatiquement configurées avec le projet. Une fois ce processus terminé, exécutez le projet localement en appuyant sur **^ F5**. Vous devez vous connecter en utilisant votre compte professionnel. Fournissez le nom d’utilisateur et un mot de passe pour le compte que vous avez créé précédemment et cliquez sur **connectez-vous**.   
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image12.png)
8. Après l’établissement de la connexion, le site ASP.NET affichera que vous avez été authentifié en affichant le nom d’utilisateur dans l’angle supérieur droit de la page.  
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image13.png)  
  
 Si vous obtenez l’erreur :  
 Valeur ne peut pas être null ou vide. Nom du paramètre : linkText   
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image14.png)  
  
 consultez le [déboguer](#dbg) section à la fin du didacticiel.

## <a name="basics-of-the-graph-api"></a>Notions de base de l’API Graph

[L’API Graph](https://msdn.microsoft.com/en-us/library/azure/hh974476.aspx) est l’interface de programmation permettant d’effectuer CRUD et autres opérations sur les objets dans votre annuaire Azure AD. Si vous sélectionnez une option de compte professionnel pour l’authentification lors de la création d’un nouveau projet dans Visual Studio 2013, votre application est déjà configurée pour appeler l’API Graph. Cette section vous montre brièvement le fonctionnement de l’API Graph.

1. Dans votre application en cours d’exécution, cliquez sur le nom de l’utilisateur connecté en haut à droite de la page. Cela ouvre la page de profil utilisateur, qui est une action sur le contrôleur Home. Vous remarquerez que la table contient des informations utilisateur sur le compte administrateur créé précédemment. Ces informations sont stockées dans votre annuaire, et l’API Graph est appelée pour récupérer ces informations lors de la charge de la page.   
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image15.png)
2. Revenez à Visual Studio et développer les **contrôleurs** , puis ouvrez le **HomeController.cs** fichier. Vous verrez un **UserProfile()** action qui contient le code pour récupérer un jeton, puis appeler l’API Graph. Ce code est dupliqué ci-dessous : 

    [!code-csharp[Main](developing-aspnet-apps-with-windows-azure-active-directory/samples/sample1.cs?highlight=22)]

    Pour appeler l’API Graph, vous devez d’abord récupérer un jeton. Lorsque le jeton est récupéré, sa valeur de chaîne doit être ajouté dans l’en-tête d’autorisation pour toutes les demandes ultérieures à l’API Graph. La plupart du code ci-dessus gère les détails de l’authentification à Azure AD pour obtenir un jeton, en utilisant le jeton pour effectuer un appel à l’API Graph, puis pour transformer la réponse afin qu’il peut être présenté dans la vue.

    La partie les plus pertinentes pour la discussion est la ligne en surbrillance suivante : `UserProfile profile = JsonConvert.DeserializeObject<UserProfile>(responseString);`. Cette ligne représente le nom de l’utilisateur, ce qui a été désérialisé à partir de la réponse JSON et est présentée dans la vue.

    Vous pouvez appeler l’API Graph à l’aide de HttpClient et gérer vous-même les données brutes, mais un moyen plus simple consiste à utiliser le [bibliothèque cliente de graphique qui est disponible via NuGet](http://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient/). La bibliothèque cliente gère les requêtes HTTP bruts et la transformation des données retournées pour vous et le rend beaucoup plus facile de travailler avec l’API Graph dans un environnement .NET. Consultez les exemples de code de l’API Graph connexes sur [GitHub.](https://github.com/AzureADSamples)

## <a name="deploy-the-application-to-azure"></a>Déployer l’Application sur Azure

Les étapes suivantes vous seront montrent comment déployer l’application sur Azure. Dans les étapes précédentes, vous connecté votre nouveau projet avec une application web sur Azure, il est prêt à être publié dans quelques étapes.

1. Dans Visual Studio, cliquez sur le projet et sélectionnez **publier**. Le **publier le site Web** boîte de dialogue apparaît avec chaque paramètre déjà configuré. Cliquez sur le **suivant** bouton pour accéder à la **paramètres** page. Vous pouvez être invité à effectuer l’authentification ; Assurez-vous que vous authentifiez à l’aide de votre compte d’abonnement Azure (en général, un compte Microsoft) et non le compte d’organisation que vous avez créé précédemment.  
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image16.png)
2. Vérifiez le **activer l’authentification d’organisation** option. Dans le **domaine** , saisissez le domaine de votre annuaire. À partir de la **niveau d’accès** liste déroulante, sélectionnez **Single Sign On, les données d’annuaire en lecture**. Vous remarquerez que la base de données précédente que vous avez utilisé est déjà remplie dans le **bases de données** section. Cliquez sur **Publier**.  
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image17.png)
3. Visual Studio commence le déploiement de votre site Web, puis une nouvelle fenêtre de navigateur s’affiche. Vous pouvez être invité à s’authentifier à nouveau à votre annuaire. Une fois que vous avez été authentifié, vous serez redirigé vers votre nouveau site Web publié sur Azure.  
  
    ![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image18.png)

<a id="dbg"></a>
## <a name="debugging-the-app"></a>Débogage de l’application

Si vous obtenez l’erreur suivante :   
 Valeur ne peut pas être null ou vide. Nom du paramètre : linkText   
   
![](developing-aspnet-apps-with-windows-azure-active-directory/_static/image19.png)  
  

Remplacez le code dans le *Views\Shared\\_LoginPartial.cshtml* fichier avec les éléments suivants :

[!code-cshtml[Main](developing-aspnet-apps-with-windows-azure-active-directory/samples/sample2.cshtml?highlight=1-8,15-16)]

Après l’exécution de l’application, si l’utilisateur connecté affiche « Utilisateur », déconnectez-vous et reconnectez-vous avec le compte Active Directory que vous avez créé précédemment.

Un excellent didacticiel à suivre est de Rick Rainey [approfondie : sites Web Azure et organisation à l’aide d’Azure AD](http://rickrainey.com/2014/08/19/deep-dive-azure-websites-and-organizational-authentication-using-azure-ad/).

## <a name="more-information"></a>Informations complémentaires

- [Présentation approfondie : Sites Web Azure et organisation de l’authentification à l’aide d’Azure AD](http://rickrainey.com/2014/08/19/deep-dive-azure-websites-and-organizational-authentication-using-azure-ad/)
- [Présentation de l’API Graph Azure AD](https://msdn.microsoft.com/en-us/library/azure/hh974476.aspx)
- [Scénarios d’authentification dans Azure AD](https://msdn.microsoft.com/en-us/library/azure/dn499820.aspx)
- [Exemples de Code de Azure AD sur GitHub](https://github.com/AzureADSamples)
