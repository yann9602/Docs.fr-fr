---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part3
title: "Ajout d’une vue | Documents Microsoft"
author: shanselman
description: "Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: e8f1515c-c277-47ff-a23e-224118f13f02
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part3
msc.type: authoredcontent
ms.openlocfilehash: 509dd301eef7c00431eae194a0df69d70e6d80f8
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-view"></a>Ajout d’une vue
====================
par [Scott Hanselman](https://github.com/shanselman)

> Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données. Visitez le [centre d’apprentissage ASP.NET MVC](../../../index.md) pour rechercher d’autres ASP.NET MVC didacticiels et exemples.


Dans cette section, nous allons examiner comment nous pouvons ont notre classe HelloWorldController utiliser un fichier de modèle de vue pour encapsulent parfaitement la génération de réponses HTML à un client.

Nous pouvons commencer à l’aide d’un modèle d’affichage avec notre méthode de l’Index. La méthode est appelée Index et se trouve dans le HelloWorldController. Actuellement, notre méthode Index() retourne une chaîne avec un message qui est codé en dur au sein de la classe de contrôleur.

[!code-csharp[Main](getting-started-with-mvc-part3/samples/sample1.cs)]

Nous allons maintenant modifier de la méthode d’Index au lieu de cela ressemble à ceci :

[!code-csharp[Main](getting-started-with-mvc-part3/samples/sample2.cs)]

Vous allez maintenant ajouter un modèle d’affichage à notre projet que nous pouvons utiliser pour notre méthode Index(). Pour ce faire, le bouton droit de la souris quelque part au milieu de la méthode d’Index et cliquez sur Ajouter une vue...

![image](getting-started-with-mvc-part3/_static/image1.png)

La boîte de dialogue « Ajouter une vue » nous offre des options pour la façon dont nous voulons créer un modèle d’affichage qui peut être utilisé par la méthode d’Index s’affiche. Pour l’instant, ne change rien et cliquez simplement sur le bouton Ajouter.

[![Afficher la boîte de dialogue Ajouter](getting-started-with-mvc-part3/_static/image3.png)](getting-started-with-mvc-part3/_static/image2.png)

Une fois que vous cliquez sur Ajouter, un nouveau dossier et un nouveau fichier seront affiche dans le dossier de Solution, comme illustré ci-après. J’ai maintenant un dossier HelloWorld sous vues et un fichier Index.aspx dans ce dossier.

[![solutionexplorerwithindex](getting-started-with-mvc-part3/_static/image5.png)](getting-started-with-mvc-part3/_static/image4.png)

Le fichier d’Index est également déjà ouvert et prêt pour la modification. Ajouter du texte sous la première &lt;h2&gt;Index&lt;/h2&gt; comme « Hello World ».

[!code-html[Main](getting-started-with-mvc-part3/samples/sample3.html)]

Exécutez votre application, vous accédez à [ `http://localhost:xx/HelloWorld` ](http://localhostxx) dans votre navigateur. Dans notre contrôleur dans cet exemple, la méthode d’Index n’a pas d’effectuer le travail, mais n’a appelé « retour View() », qui indique que nous voulons utiliser un fichier de modèle de vue pour restituer une réponse au client. Car nous ne spécifie pas explicitement le nom du fichier de modèle de vue à utiliser, ASP.NET MVC par défaut à l’aide du fichier de vue Index.aspx au sein du dossier \Views\HelloWorld. Nous maintenant voir la chaîne que nous avons codées en dur dans notre vue.

[![Index - Windows Internet Explorer](getting-started-with-mvc-part3/_static/image7.png)](getting-started-with-mvc-part3/_static/image6.png)

Il semble bon. Toutefois, notez que les titre du navigateur indiquant « Index » et le gros titre sur la page indique « Mon Application MVC ». Nous allons modifier ceux.

### <a name="changing-views-and-master-pages"></a>Modification des vues et des Pages maîtres

Tout d’abord, nous allons modifier le texte « Mon Application MVC ». Ce texte est partagé et s’affiche sur chaque page. Il apparaît réellement dans un seul endroit dans notre code, même s’il est sur chaque page dans notre application. Accédez au dossier /Views/Shared dans l’Explorateur de solutions et ouvrez le fichier Site.Master. Ce fichier s’appelle une Page maître et il s’agit de l’interface « partagé » que toutes les autres pages de nos utilisent.

Notez du texte indiquant que ContentPlaceholder « MainContent » dans ce fichier.

[!code-aspx[Main](getting-started-with-mvc-part3/samples/sample4.aspx)]

Cet espace réservé est où toutes les pages que vous créez seront afficheront, « encapsulées » dans la page maître. Essayez de modifier le titre, puis exécutez votre application, vous accédez à plusieurs pages. Vous remarquerez que votre une modification apparaît sur plusieurs pages.

[!code-html[Main](getting-started-with-mvc-part3/samples/sample5.html)]

Chaque page aura l’en-tête principal - qui consiste H1 - de « Mon Application de film MVC. » Qui gère le texte blanc en haut, il est partagé entre toutes les pages.

Voici le Site.Master dans son intégralité avec notre titre modifié :

[!code-aspx[Main](getting-started-with-mvc-part3/samples/sample6.aspx)]

Maintenant, nous allons modifier le titre de la page d’Index.

Ouvrez /HelloWorld/Index.aspx. Il existe deux emplacements à modifier. Tout d’abord, le titre qui s’affiche dans le titre du navigateur, puis l’en-tête secondaire - qui est H2 - également. Vous allez rendre chaque légèrement différent afin de voir les bits de code modifie la partie de l’application.

[!code-aspx[Main](getting-started-with-mvc-part3/samples/sample7.aspx)]

Exécuter votre application, vous accédez à /Movies. Notez que le titre du navigateur, l’en-tête principal et les en-têtes secondaires ont été modifiés. Il est facile d’effectuer des modifications importantes dans votre application avec petites modifications à votre affichage.

[![Liste de films - Windows Internet Explorer](getting-started-with-mvc-part3/_static/image9.png)](getting-started-with-mvc-part3/_static/image8.png)

Notre peu de « données » (dans ce cas, « Hello World ! » message) a été dur codé cependant. Nous avons V (vues) et nous avons C (contrôleurs), mais encore aucune M (modèle). Nous allons bientôt procédure créer une base de données et extraire les données de modèle.

## <a name="passing-a-viewmodel"></a>En passant un ViewModel

Avant de nous accédez à une base de données et parler de modèles, cependant, tout d’abord parlons de « ViewModel. » Ce sont des objets qui représentent les un modèle d’affichage a besoin pour restituer une réponse HTML à un client. Ils sont généralement créés passé par une classe de contrôleur à un modèle d’affichage et ne doivent contenir que les données nécessitant le modèle d’affichage - et pas plus.

Précédemment avec notre exemple HelloWorld, notre méthode d’action Welcome() a un nom et un paramètre numTimes et les exporter dans le navigateur. Plutôt que d’avoir le contrôleur de continuer à rendre cette réponse directement, nous allons procéder à une petite classe pour contenir ces données et ignorer à un modèle de vue à restituer dans la réponse HTML à l’utiliser. De cette manière, le contrôleur est concerné par une chose et le modèle d’affichage autre – ce qui nous permet de mettre à jour de « séparation claire des préoccupations » au sein de notre application.

Revenir au fichier HelloWorldController.cs et ajoutez une nouvelle classe « WelcomeViewModel » et modifier la méthode de Bienvenue à l’intérieur de votre contrôleur. Voici le HelloWorldController.cs complète avec la nouvelle classe dans le même fichier.

[!code-csharp[Main](getting-started-with-mvc-part3/samples/sample8.cs)]

Bien qu’il soit sur plusieurs lignes, notre méthode de bienvenue est réellement uniquement deux instructions. La première instruction empaquette nos deux paramètres dans un objet ViewModel et le deuxième passe de l’objet résultant dans la vue.

Nous devons maintenant un modèle d’affichage de bienvenue ! Cliquez avec le bouton droit dans la méthode accueil et sélectionnez Ajouter une vue. Cette fois-ci, nous allons vérifier « Créer une vue fortement typée » et sélectionnez notre classe WelcomeViewModel à partir de la zone de liste déroulante. Cette nouvelle vue uniquement les connaissent WelcomeViewModels et aucune des autres types d’objets.

> *Remarque : Vous devez avoir compilé une fois après l’ajout de votre WelcomeViewModel pour s’affichent dans la liste déroulante.*


Voici ce que votre boîte de dialogue Ajouter une vue doit ressembler à. Cliquez sur le bouton Ajouter. ![Ajouter la que vue CERCLÉE](getting-started-with-mvc-part3/_static/image10.png)

Ajoutez ce code sous le &lt;h2&gt; dans votre nouvelle Welcome.aspx. Nous allons effectuer une boucle et découvrez autant de fois que l’utilisateur indique que nous devez !

[!code-aspx[Main](getting-started-with-mvc-part3/samples/sample9.aspx)]

Notez également que lorsque vous tapez qui car nous dit à cette vue sur le WelcomeViewModel (ils sont mariés, n’oubliez pas ?) que nous obtenons Intellisense utile chaque fois que nous référençons notre objet modèle vu dans la capture d’écran ci-dessous :

[![Code Source de NumTime](getting-started-with-mvc-part3/_static/image12.png)](getting-started-with-mvc-part3/_static/image11.png)

Exécutez votre application, vous accédez à `http://localhost:xx/HelloWorld/Welcome?name=Scott&numtimes=4` à nouveau. Maintenant nous allons prenant des données à partir de l’URL, il a été passée dans notre contrôleur automatiquement, notre contrôleur regroupe les données en un ViewModel et passe cet objet sur notre affichage. La vue qu’affiche les données au format HTML à l’utilisateur.

[![Bienvenue - Windows Internet Explorer](getting-started-with-mvc-part3/_static/image14.png)](getting-started-with-mvc-part3/_static/image13.png)

C’est un type d’un « M » pour le modèle, mais pas le type de base de données. Prenons nous avez appris et créer une base de données de films.

>[!div class="step-by-step"]
[Précédent](getting-started-with-mvc-part2.md)
[Suivant](getting-started-with-mvc-part4.md)
