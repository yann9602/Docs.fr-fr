---
uid: mvc/overview/older-versions-1/nerddinner/create-a-new-aspnet-mvc-project
title: "Créer un nouveau projet ASP.NET MVC | Documents Microsoft"
author: microsoft
description: "Étape 1 illustre la manière de mettre la structure d’application de NerdDinner base en place."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 7e0e9928-8fdc-4b74-9882-55fac0976628
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/create-a-new-aspnet-mvc-project
msc.type: authoredcontent
ms.openlocfilehash: 4d30a6803b1478014a2afb814ac317df27394446
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="create-a-new-aspnet-mvc-project"></a>Créer un nouveau projet ASP.NET MVC
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 1 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 1 illustre la manière de mettre la structure d’application de NerdDinner base en place.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-1-file-gtnew-project"></a>NerdDinner étape 1 : Fichier -&gt;nouveau projet

Nous allons commencer notre application NerdDinner en sélectionnant le **fichier -&gt;nouveau projet** élément de menu dans Visual Studio 2008 ou de la libre Visual Web Developer 2008 Express.

La boîte de dialogue « Nouveau projet » s’affiche. Pour créer une application ASP.NET MVC, nous allons sélectionnez le nœud de « Web » sur le côté gauche de la boîte de dialogue, puis choisissez le modèle de projet « Application Web ASP.NET MVC » sur la droite :

![](create-a-new-aspnet-mvc-project/_static/image1.png)

*Important : Assurez-vous que vous avez téléchargé et installé ASP.NET MVC - sinon il n’apparaît pas dans la boîte de dialogue Nouveau projet. Vous pouvez utiliser V2 de la [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) si elle ne l’avez pas encore installé (ASP.NET MVC est disponible dans le « Web Platform -&gt;infrastructures et les exécutions de « section).*

Nous allons nommer le nouveau projet, que nous allons créer « NerdDinner », puis sur le bouton « OK » pour la créer.

Lorsque vous cliquez sur « OK » Visual Studio s’affiche une boîte de dialogue supplémentaire qui vous invite à entrer éventuellement créer un projet de test unitaire pour la nouvelle application ainsi. Ce projet de test unitaire permet de créer des tests automatisés qui vérifient les fonctionnalités et le comportement de notre application (quelque chose que nous allons décrire comment des tâches plus loin dans ce didacticiel).

![](create-a-new-aspnet-mvc-project/_static/image2.png)

La liste déroulante de « Test framework » dans la boîte de dialogue ci-dessus est remplie avec tous les ASP.NET MVC unité test de modèles de projet disponibles installés sur l’ordinateur. Versions peuvent être téléchargées pour NUnit et XUnit MBUnit. L’infrastructure de Test unitaire Visual Studio intégrée est également pris en charge.

*Remarque : L’infrastructure des tests unitaires Visual Studio est uniquement disponible avec Visual Studio 2008 Professional et versions ultérieures. Si vous utilisez Visual Studio 2008 Standard Edition ou Visual Web Developer 2008 Express, vous devrez télécharger et installer les extensions NUnit, MBUnit ou XUnit pour ASP.NET MVC pour afficher cette boîte de dialogue. La boîte de dialogue n’affiche pas s’il n’existe pas de toutes les infrastructures de test installés.*

Nous allons utiliser le nom de « NerdDinner.Tests » par défaut pour le projet de test que nous créons et utilisez l’option de framework « Tests unitaires Visual Studio ». Lorsque vous cliquez sur le bouton « OK » de Visual Studio va créer une solution pour nous avec deux projets - un pour notre application web et l’autre pour nos tests unitaires :

![](create-a-new-aspnet-mvc-project/_static/image3.png)

### <a name="examining-the-nerddinner-directory-structure"></a>Étude de la structure de répertoire de NerdDinner

Lorsque vous créez une application ASP.NET MVC avec Visual Studio, il ajoute automatiquement un nombre de fichiers et de répertoires au projet :

![](create-a-new-aspnet-mvc-project/_static/image4.png)

Par défaut, les projets ASP.NET MVC ont six répertoires de niveau supérieur :

| **Répertoire** | **Fonction** |
| --- | --- |
| **/ Contrôleurs** | Dans lequel vous enregistrez des classes de contrôleur qui gèrent les demandes d’URL |
| **/ Modèles** | Dans lequel vous enregistrez des classes qui représentent et manipulent des données |
| **/ Vues** | Dans lequel vous enregistrez les fichiers de modèle de l’interface utilisateur qui est responsables de la sortie de rendu |
| **Scripts** | Dans lequel vous enregistrez les fichiers de la bibliothèque JavaScript et les scripts (.js) |
| **/ Contenu** | Dans lequel vous enregistrez le code CSS et des fichiers image et autre contenu non-dynamique/non-JavaScript |
| **/ Application\_données** | Lorsque vous stockez les fichiers de données, vous souhaitez en lecture/écriture. |

ASP.NET MVC ne nécessite pas de cette structure. En fait, les développeurs qui travaillent sur des applications de grande taille généralement partitionne l’application sur plusieurs projets pour le rendre plus facile à gérer (par exemple : les classes de modèle de données passent généralement dans un projet de bibliothèque de classes distinct à partir de l’application web). Toutefois, la structure de projet par défaut, offre une convention de répertoire par défaut agréable que nous pouvons utiliser pour nettoyer les problèmes de notre application.

Lorsque nous développez le répertoire /Controllers, vous trouverez que Visual Studio a ajouté deux classes de contrôleur – HomeController et AccountController – par défaut pour le projet :

![](create-a-new-aspnet-mvc-project/_static/image5.png)

Lorsque nous développez le répertoire /Views, nous trouverez trois sous-répertoires – /Home, Account /Shared – ainsi que modèle plusieurs fichiers au sein de celles-ci ont été également ajoutés au projet par défaut :

![](create-a-new-aspnet-mvc-project/_static/image6.png)

Lorsque nous développez les répertoires / scripts/Content, vous trouverez un fichier Site.css qui est utilisé pour définir le style HTML sur le site, ainsi que les bibliothèques JavaScript qui permettent à ASP.NET AJAX et jQuery prend en charge dans l’application :

![](create-a-new-aspnet-mvc-project/_static/image7.png)

Lorsque nous développez le projet NerdDinner.Tests, vous trouverez deux classes qui contiennent des tests unitaires pour les classes de notre contrôleur :

![](create-a-new-aspnet-mvc-project/_static/image8.png)

Ces fichiers par défaut ajoutés par Visual Studio nous fournissent une structure de base pour une application - terminée avec la page d’accueil, sur la page, les pages de connexion/déconnexion / d’inscription de compte et une page d’erreur non gérée (tous les accès câblé et travail prêtes à l’emploi).

### <a name="running-the-nerddinner-application"></a>Exécution de l’Application de NerdDinner

Nous pouvons exécuter le projet en choisissant une le **Debug -&gt;démarrer le débogage** ou **Debug -&gt;démarrer sans débogage** des éléments de menu :

![](create-a-new-aspnet-mvc-project/_static/image9.png)

Cela lancer le serveur Web ASP.NET intégré-qui est fourni avec Visual Studio et exécutez votre application :

![](create-a-new-aspnet-mvc-project/_static/image10.png)

Voici la page d’accueil de notre nouveau projet (URL : « / ») lors de l’exécution :

![](create-a-new-aspnet-mvc-project/_static/image11.png)

En cliquant sur l’onglet « About » affiche un sur la page (URL : « / accueil/sur ») :

![](create-a-new-aspnet-mvc-project/_static/image12.png)

En cliquant sur le lien « Session » dans l’angle supérieur droit nous mène à une page de connexion (URL : « / Account / d’ouverture de session »)

![](create-a-new-aspnet-mvc-project/_static/image13.png)

Si nous n’avons pas un compte de connexion que nous pouvons cliquer sur le lien du Registre (URL : « / Account/Register ») pour en créer un :

![](create-a-new-aspnet-mvc-project/_static/image14.png)

Le code pour implémenter la page d’accueil ci-dessus, à propos et déconnexion / inscrire fonctionnalité a été ajoutée par défaut, lorsque nous avons créé notre nouveau projet. Nous allons l’utiliser comme point de départ de notre application.

### <a name="testing-the-nerddinner-application"></a>Test de l’Application de NerdDinner

Si nous utilisons l’édition Professional ou version plus récente de Visual Studio 2008, nous pouvons utiliser la prise en charge de l’IDE Visual Studio du test des unités pour tester le projet :

![](create-a-new-aspnet-mvc-project/_static/image15.png)

En choisissant une des options ci-dessus pour ouvrir le volet « Résultats de Test » dans l’IDE et nous fournir un état de réussite/échec sur les tests 27 unitaires inclus dans notre projet qui couvrent la fonctionnalité intégrée :

![](create-a-new-aspnet-mvc-project/_static/image16.png)

Plus loin dans ce didacticiel nous reviendrons plus de tests automatisés et ajouter des tests unitaires supplémentaires qui couvrent la fonctionnalité de l’application que nous implémentons.

### <a name="next-step"></a>Étape suivante

Nous avons désormais une structure d’application de base en place. Nous allons maintenant [créer une base de données pour stocker les données d’application](create-a-database.md).

>[!div class="step-by-step"]
[Précédent](introducing-the-nerddinner-tutorial.md)
[Suivant](create-a-database.md)
