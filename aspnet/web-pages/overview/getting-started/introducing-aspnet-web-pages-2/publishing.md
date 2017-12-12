---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/publishing
title: "Présentation des Pages Web ASP.NET - publication d’un Site à l’aide de WebMatrix | Documents Microsoft"
author: tfitzmac
description: "Ce didacticiel est la dernière partie du jeu de didacticiel présente les Pages Web ASP.NET et Microsoft WebMatrix. Il explique comment publier votre site t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: 7e85c70e-1a88-4408-8b3d-29611c7713ed
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/publishing
msc.type: authoredcontent
ms.openlocfilehash: 1e718c92a2f94df50fcf7af6859917746a4982ac
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introducing-aspnet-web-pages---publishing-a-site-by-using-webmatrix"></a>Présentation des Pages Web ASP.NET - publication d’un Site à l’aide de WebMatrix
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel est la dernière partie du jeu de didacticiel présente les Pages Web ASP.NET et Microsoft WebMatrix. Elle explique comment publier votre site à Internet afin que d’autres peuvent utiliser. Il suppose que vous avez terminé la série via [création d’une zone de recherche cohérent pour les Sites ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251585).
> 
> Vous allez apprendre à publier votre site à l’aide de :
> 
> - Microsoft Azure
> - Société d’hébergement Web


## <a name="about-publishing-your-site"></a>Sur la publication de votre Site.

Jusqu'à présent, vous avez effectué l’ensemble de votre travail sur un ordinateur local, notamment le test de vos pages. Pour exécuter votre*.cshtml* pages, vous avez utilisé le serveur web intégré à WebMatrix, à savoir IIS Express. Mais bien sûr, personne ne peut afficher le site que vous avez créé à l’exception de vous. Pour d’autres permettent de travailler avec votre site, vous devez le publier sur Internet.

Sauf si vous avez déjà accès à un serveur web public, publication signifie que vous devez disposer d’un compte avec un *plateforme cloud* ou un *fournisseur d’hébergement*. Une plateforme cloud, tels que Microsoft Azure, fournit une infrastructure de la demande pour vos applications. Un fournisseur d’hébergement est une société qui possède des serveurs web accessible publiquement et qui sera louer vous espace pour votre site. Hébergement des plans d’exécution à partir de quelques dollars par mois (ou même gratuits) pour les sites de petite taille à plusieurs centaines de tous les mois pour les sites Web commerciaux de haut volume.

> [!NOTE]
> Vous aurez accès à un serveur web publics via le fournisseur de services internet (ISP) qui vous permet d’obtenir le service internet à votre domicile. Toutefois, votre fournisseur d’hébergement doit prendre en charge des Pages Web ASP.NET. Ne de nombreux fournisseurs de services Internet, mais il est toujours intéressant de vérification.


Dans ce didacticiel, nous vous donnons une vue d’ensemble de la publication. Il n’est pas pratique de fournir les détails exacts pour tous les éléments, car le processus diffère légèrement pour chaque fournisseur d’hébergement. Mais vous obtiendrez une bonne idée de fonctionnement du processus.

Ce didacticiel contient quatre sections :

1. [Configuration de la page par défaut](#defaultpage)
2. Publication (choisissez une des opérations suivantes)  
 a. [Publication de votre Site vers Microsoft Azure](#azure)  
 b. [Publication de votre Site à une société d’hébergement Web](#host)
3. [Mise à jour le Site actif : republication](#update)

<a id="defaultpage"></a>
## <a name="setting-up-the-default-page"></a>Configuration de la page par défaut

Lorsqu’un utilisateur accède à l’adresse de base pour votre site web, la page par défaut pour votre site s’affiche à l’utilisateur. Par exemple, lorsque Default.htm est définie en tant que la page par défaut pour le site à www.contoso.com, puis accédez à **www.contoso.com** est identique à la navigation vers **www.contoso.com/Default.htm**.

Actuellement, votre site utilise **Default.cshtml** en tant que la page par défaut. Cette page est tout indiquée pour votre page par défaut, mais dans ce didacticiel vous n’avez ajouté aucun contenu à cette page afin qu’il affiche une page vierge. Ouvrez Default.cshtml et remplacez le contenu par le code suivant.

[!code-cshtml[Main](publishing/samples/sample1.cshtml)]

Votre site est maintenant prête pour la publication. Tout d’abord, vous verrez comment déployer le site vers Azure, puis comment le déployer pour une société d’hébergement web. L’option fonctionne pour votre site web et que vous ne devez suivre l’une des options de déploiement.

<a id="azure"></a>
## <a name="publishing-your-site-to-microsoft-azure"></a>Publication de votre Site vers Microsoft Azure

Ce didacticiel vous indiquera tout d’abord comment déployer votre site vers Microsoft Azure. En vous connectant avec un compte Microsoft, vous pouvez créer jusqu'à 10 sites gratuits sur Azure. Ces sites gratuits fournissent un moyen pratique de tester vos sites. Vous pouvez toujours supprimer ce site exemple ultérieurement pour éviter d’utiliser tous vos sites gratuits. Vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [d’évaluation gratuite Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).

Dans le ruban de WebMatrix, cliquez sur le **publier** bouton.

![« Publier » un bouton dans le ruban de WebMatrix](publishing/_static/image1.png)

Le **publier votre Site** boîte de dialogue s’affiche. Si vous n’avez pas connecté à votre compte Microsoft, la boîte de dialogue contiendra une **prise en main Azure** lien. Cliquez sur ce lien.

![Publier votre site.](publishing/_static/image2.png)

Si vous n’avez pas connecté à un compte Microsoft, vous disposez à nouveau la possibilité de se connecter. Vous devez vous connecter à un compte Microsoft pour publier votre site sur Windows Azure.

![Se connecter](publishing/_static/image3.png)

Après s’être connecté à votre compte Microsoft, la boîte de dialogue contient des liens pour créer un nouveau site dans Azure ou de se connecter à un de vos sites existants sur Azure.

![Créer un nouveau Site](publishing/_static/image4.png)

Sélectionnez **créer un nouveau site**.

Si vous avez nommé votre projet **WebPagesMovies**, le nom par défaut pour votre site sera **webpagesmovies.azurewebsites.net**. Ce nom par défaut est probablement pas disponible, comme indiqué par le point d’exclamation rouge.

![nom du site Web par défaut](publishing/_static/image5.png)

Modifier le nom du site à un élément qui n’est disponible et sélectionnez un emplacement qui est proche de votre emplacement.

![nom du site modifié](publishing/_static/image6.png)

Cliquez sur **OK**.

WebMatrix performss un test pour déterminer si le serveur est compatible avec votre site.

![test de compatibilité](publishing/_static/image7.png)

Sélectionnez **continuer**.

Les résultats du test de compatibilité sont affichés.

![résultat de la compatibilité](publishing/_static/image8.png)

Sélectionnez **continuer**.

WebMatrix affiche les fichiers et les bases de données qui seront publiés sur le site. Dans la mesure où il s’agit de la première fois que vous publiez le site, tous les fichiers sont répertoriés. Vous pouvez désactiver un fichier qui n’est pas prêt à être publié. Dans les publications ultérieures, seuls les fichiers qui ont été modifiés seront affichera. Consultez [mise à jour le Site actif : republication](#update).

![Aperçu de la publication](publishing/_static/image9.png)

Sélectionnez **continuer**.

Une fois que le site a été déployé sur Azure, un message s’affiche indiquant que le déploiement est terminé.

![publication terminée](publishing/_static/image10.png)

Votre site et la base de données ont été publiés dans Azure et sont désormais disponibles au public. Cliquez sur le lien dans le message indiquant la publication est terminée, et vous voyez maintenant votre site déployé. Vous ou toute personne ayant accès à Internet pour ajouter ou modifier des enregistrements dans la base de données.

![](publishing/_static/image11.png)

<a id="host"></a>
## <a name="publishing-your-site-to-a-web-hosting-company"></a>Publication de votre Site à une société d’hébergement Web

Si vous décidez de ne pas publier sur Azure, vous pouvez à la place publier votre site à une société d’hébergement web.

Cliquez sur le **rechercher un hébergement web** lien.

![Bouton de « Rechercher un hébergement web » dans la boîte de dialogue Paramètres de publication](publishing/_static/image12.png)

Vous accédez à une page sur le site Microsoft qui répertorie les fournisseurs d’hébergement qui prennent en charge ASP.NET.

![Page sur le site Microsoft qui répertorie les fournisseurs d’hébergement](publishing/_static/image13.png)

Évidemment, il peut être difficile de savoir exactement quelles fonctionnalités d’hébergements vous pouvez être amené à long terme. Voici quelques éléments à prendre en compte :

- Pour des raisons du site WebPagesMovies, vous n’avez pas d’avoir un module complémentaire distinct pour SQL Server, ce qui est souvent très cher. Dans votre site, vous êtes à l’aide de SQL Server Compact Edition, qui est autonome. Toutefois, vous devrez peut-être accès à SQL Server pour certains travaux futurs de site Web. Si vous pensez que vous pouvez, assurez-vous que vous pouvez ajouter ultérieurement des capacités de SQL Server.
- Vérifiez si le fournisseur d’hébergement prend en charge le protocole de publication Web Deploy. Vous pouvez publier à l’aide du protocole FTP, mais il est plus commode d’utiliser Web Deploy.

Certains sites proposent une période d’évaluation gratuite. Une version d’évaluation gratuite est une bonne solution pour tenter de publier et d’hébergement pendant que vous êtes encore expérimenter WebMatrix et ASP.NET Web Pages.

Choisissez une qui vous convient. Pour ce didacticiel, nous avons sélectionné DiscountASP.NET, étant donné que pendant que nous avons le didacticiel Création, cette société avait une promotion qui permettent aux utilisateurs d’héberger un site gratuit pour quelques mois.

> [!NOTE]
> Notre choix d’un fournisseur d’hébergement pour ce didacticiel ne doit pas être interprété considéré comme une approbation de cette société sur n’importe quel autre. Mais nous avons dû choisir l’une à titre d’illustration et DiscountASP.NET est une des nombreuses sociétés qui prend en charge des Pages Web ASP.NET et le protocole Web Deploy pour la publication.


En règle générale, une fois que vous avez inscrit avec le fournisseur d’hébergement, la société vous envoie un message électronique qui contient un nom d’utilisateur et un mot de passe, l’URL du serveur de web et ainsi de suite. Si la société d’hébergement prend en charge le protocole Web Deploy, ils peuvent envoyer vous un fichier qui contient les paramètres de publication, ou vous permettent de télécharger un. Un fichier de paramètres de publication simplifie le processus pour vous.

Lorsque vous êtes abonné et que vous êtes prêt à publier, cliquez sur le **publier** bouton dans le ruban de WebMatrix. Le **paramètres de publication** boîte de dialogue s’affiche.

Si le fournisseur d’hébergement vous a envoyé un fichier de paramètres de publication, cliquez sur le **importer les paramètres de publication** lier, puis importez le fichier. Si vous n’avez pas un fichier de paramètres de publication, renseignez les champs en utilisant les valeurs de la société d’hébergement vous a envoyé par courrier électronique. Voici le **paramètres de publication** boîte de dialogue peut se présenter comme lorsque vous avez terminé :

![Paramètres de publication renseigné la boîte de dialogue Publier les paramètres](publishing/_static/image14.png)

Cliquez sur **valider la connexion**. Si tout est OK, la boîte de dialogue indique **connexion réussie**, ce qui signifie qu’il peut communiquer avec le serveur du fournisseur d’hébergement.

![Message de réussite si publier les paramètres sont corrects](publishing/_static/image15.png)

S’il existe un problème, WebMatrix fait de son mieux pour vous indiquer l’origine du problème :

![Message d’erreur s’il existe un problème avec les paramètres de publication](publishing/_static/image16.png)

Cliquez sur **enregistrer** pour enregistrer vos paramètres. WebMatrix vous permet d’effectuer un test pour vous assurer qu’il peut communiquer correctement avec le site d’hébergement :

![Message de l’offre pour effectuer un test de la procédure de publication](publishing/_static/image17.png)

Cliquez sur **Oui**. WebMatrix télécharge des fichiers d’exemple pour le fournisseur d’hébergement. Lorsque le test de compatibilité est terminé, WebMatrix signale les résultats :

![Résultats du test de publication](publishing/_static/image18.png)

Si vous êtes prêt, continuez et cliquez sur **continuer** pour démarrer le processus de publication par réel. WebMatrix détermine quels fichiers sont dans votre site et sont déjà sur le serveur hôte (actuellement, aucun), puis vous donne un aperçu du processus de publication :

![Aperçu des fichiers à télécharger le processus de publication](publishing/_static/image19.png)

La liste des fichiers à publier inclut les pages web que vous avez créé comme *Movies.cshtml*. La liste inclut également les fichiers de programmes d’assistance que vous avez installé, les fichiers pour exécuter SQL Server Compact Edition pour votre base de données et ainsi de suite. Par conséquent, les processus de publication d’initial peut être importante.

Cliquez sur **Continuer**. WebMatrix copie vos fichiers de serveur du fournisseur d’hébergement. Lorsqu’il est terminé, les résultats sont signalés dans la barre d’état :

![Message de barre d’état lorsque le processus de publication terminée avec succès](publishing/_static/image20.png)

Pour voir votre site en ligne, cliquez sur le lien dans la barre d’état. Ajouter *films* à l’URL, et vous verrez la *Movies.cshtml* fichier que vous avez créé :

![Le site actif affichant la page de films](publishing/_static/image21.png)

<a id="update"></a>
## <a name="updating-the-live-site-republishing"></a>Mise à jour le Site actif : republication

Une fois que vous avez publié votre site (pour Azure ou dans une société d’hébergement web), il existe deux copies &mdash; la version sur votre ordinateur et la version sur le fournisseur de services. Vous souhaiterez probablement continuer à développer le site (si rien d’autre, en tant que partie du jeu de didacticiel suivant). Lorsque vous procédez ainsi, vous devez republier votre site pour pouvoir copier des modifications apportées au fournisseur de services à partir de votre ordinateur. Le processus de publication dans WebMatrix peut déterminer ce que les fichiers ont été modifiés sur votre site et de publier uniquement les fichiers.

Pour voir comment fonctionnement la republication, ouvrez le *Movies.cshtml* de site, apporter une petite modification, puis enregistrez le fichier. Par exemple, remplacez le titre par `Movies - Updated`.

Cliquez sur le **publier** bouton du ruban. WebMatrix détermine ce qui est modifié et affiche un aperçu des fichiers qu’il publie.

![La boîte de dialogue « Publier » affichant modifiées fichiers prêts pour la réédition](publishing/_static/image22.png)

> [!IMPORTANT] 
> 
> Par défaut, WebMatrix publie votre base de données (*.sdf* fichier) uniquement la première fois que vous publierez le site. Une fois votre site est publié et les utilisateurs interagissent avec le site Web, la base de données sur le site actif a généralement des données réelles du site. Vous devez faire très attention à ne pas remplacer la base de données en direct avec le *.sdf* fichier présent sur votre ordinateur, qui contient généralement des données de test uniquement. C’est pourquoi vous recevez un avertissement **publication remplace toutes les bases de données distantes**, et pourquoi la case à cocher *WebPagesMovies.sdf* est désactivée par défaut.


Cliquez sur **Continuer**. WebMatrix publie les fichiers modifiés et vous montre un message de réussite, comme la première fois que vous avez publié.

Accédez au site dynamique (vous pouvez cliquer sur le lien dans le message de réussite s’il est toujours affiché) et vérifiez que votre modification a été publiée.

> [!TIP] 
> 
> **Modification des fichiers à distance**
> 
> Comme alternative à la modification de votre site, puis republication, vous pouvez modifier des fichiers distants directement dans WebMatrix. Dans ce scénario, vous ouvrez un fichier qui se trouve sur le fournisseur de services et WebMatrix télécharge une copie de celui-ci pour pouvoir les modifier. Chaque fois que vous enregistrez le fichier, WebMatrix envoie les modifications apportées au site.
> 
> Modification à distance est un moyen simple pour apporter des modifications à votre site dynamique. Toutefois, les modifications apportées ainsi ne sont pas synchronisées avec les fichiers dans votre site local. Pour synchroniser les fichiers locaux avec le site distant, vous pouvez télécharger les fichiers à distance. Ce processus fonctionne comme la publication, à l’exception dans l’ordre inverse.
> 
> Nous ne décrivons plus sur les téléchargement à distance et de modification à distance des installations de WebMatrix ici. Elles sont particulièrement utiles si plusieurs utilisateurs de travailler sur le même site sur des ordinateurs différents. Pour plus d’informations, consultez [publier et modifier un Site distant avec la version bêta 2 de WebMatrix](https://go.microsoft.com/fwlink/?LinkId=251591).


## <a name="additional-resources"></a>Ressources supplémentaires

- [Forum de WebMatrix ASP.NET Web Pages ASP.NET](https://forums.asp.net/1224.aspx/1?WebMatrix+and+ASP+NET+Web+Pages), un excellent point de publier des questions et obtenir des réponses.

>[!div class="step-by-step"]
[Précédent](layouts.md)
