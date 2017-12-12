---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-1
title: "Partie 1 : Vue d’ensemble et fichier -> Nouveau projet | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 1 traite de vue d’ensemble et de fichier -> Nouveau projet."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: bd356ca3-5bdb-4067-9dac-c9e9923a86e8
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-1
msc.type: authoredcontent
ms.openlocfilehash: 1e3373a21c7d1766cfad390a7ba68b1363d8d895
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-1-overview-and-file-new-project"></a>Partie 1 : Vue d’ensemble et fichier -> Nouveau projet
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.  
>   
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 1 couvre la vue d’ensemble et fichier -&gt;nouveau projet.


## <a name="overview"></a>Vue d'ensemble

Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Web Developer pour le développement web. Nous allons commencer lentement, afin de l’expérience de développement de niveau web débutant est OK.

L’application que nous allons créer est un magasin de musique simple. Il existe trois éléments principaux à l’application : achats, extraction et administration.

![](mvc-music-store-part-1/_static/image1.jpg)

Les visiteurs peuvent parcourir des Albums par Genre :

![](mvc-music-store-part-1/_static/image2.jpg)

Ils peuvent afficher un seul album et l’ajouter à leur panier d’achat :

![](mvc-music-store-part-1/_static/image3.jpg)

Ils peuvent consulter leur panier d’achat, suppression de tous les éléments qu’ils ne voulez plus que :

![](mvc-music-store-part-1/_static/image4.jpg)

Procéder à la validation les invite à se connecter ou s’inscrire pour un compte d’utilisateur.

![](mvc-music-store-part-1/_static/image1.png)

![](mvc-music-store-part-1/_static/image2.png)

Après avoir créé un compte, il peut effectuer l’ordre en remplissant les informations d’expédition et de paiement. Pour simplifier les choses, nous exécutons une promotion incroyable : tout est gratuit s’il a entré un code de promotion « Gratuit » !

![](mvc-music-store-part-1/_static/image5.jpg)

Après le classement, ils voient un écran de confirmation simple :

![](mvc-music-store-part-1/_static/image6.jpg)

En plus des pages du client-faceing, nous également générer une section d’administrateur qui affiche une liste d’albums à partir de laquelle les administrateurs peuvent créer, modifier et supprimer des albums :

![](mvc-music-store-part-1/_static/image7.jpg)

## <a name="1-file--gt-new-project"></a>1. Fichier -&gt; nouveau projet

### <a name="installing-the-software"></a>L’installation du logiciel

Ce didacticiel commence par créer un nouveau projet ASP.NET MVC 3 à l’aide de la libre Visual Web Developer 2010 Express (qui est disponible), puis nous allons ajouter progressivement des fonctionnalités pour créer une application fonctionnelle complète. Avant cela, nous aborderons accès de base de données, les scénarios de validation de formulaire, validation des données, à l’aide de pages maîtres pour la mise en page cohérente, à l’aide d’AJAX pour les mises à jour de la page et la validation, connexion de l’utilisateur et bien plus encore.

Peut suivre la procédure étape par étape, ou vous pouvez télécharger l’application terminée à partir de [magasin de musique MVC](https://github.com/evilDave/MVC-Music-Store).

Vous pouvez utiliser Visual Studio 2010 SP1 ou Visual Web Developer 2010 Express SP1 (une version gratuite de Visual Studio 2010) pour générer l’application. Nous utiliserons SQL Server Compact (également gratuit) pour héberger la base de données. Avant de commencer, assurez-vous que vous avez installé les composants requis répertoriés ci-dessous.


- [Configuration requise de visual Studio Web Developer Express SP1]
- [ASP.NET MVC 3 Tools Update]
- [SQL Server Compact 4.0 -], y compris la prise en charge runtime et outils


### <a name="creating-a-new-aspnet-mvc-3-project"></a>Création d’un projet ASP.NET MVC 3

Nous allons commencer en sélectionnant « Nouveau projet » dans le menu fichier dans Visual Web Developer. De la boîte de dialogue Nouveau projet s’affiche.

![](mvc-music-store-part-1/_static/image5.png)

Nous allons sélectionner le Visual c# -&gt; modèles Web groupe situé à gauche, puis choisissez le modèle de « Application Web ASP.NET MVC 3 » dans la colonne centrale. Nommez votre projet MvcMusicStore et appuyez sur le bouton OK.

![](mvc-music-store-part-1/_static/image8.jpg)

Cette action affiche une boîte de dialogue secondaire qui vous permet de définir des paramètres spécifiques de MVC pour notre projet. Sélectionnez les éléments suivants :

Modèle de projet, sélectionnez vide

Moteur d’affichage - Sélectionnez Razor

Utiliser des balises sémantiques de HTML5 - activée

Vérifiez que vos paramètres sont indiquées ci-dessous, puis appuyez sur le bouton OK.

![](mvc-music-store-part-1/_static/image9.jpg)

Cela va créer notre projet. Examinons les dossiers qui ont été ajoutés à notre application dans l’Explorateur de solutions sur le côté droit.

![](mvc-music-store-part-1/_static/image10.jpg)

Le modèle vide MVC 3 n’est pas vide, il ajoute une structure de dossiers de base :

![](mvc-music-store-part-1/_static/image6.png)

ASP.NET MVC utilise des conventions d’affectation de noms de base pour les noms de dossier :

| **Dossier** | **Fonction** |
| --- | --- |
| **/ Contrôleurs** | Contrôleurs de répondent à l’entrée à partir du navigateur, décider quoi faire avec ce dernier et retourner une réponse à l’utilisateur. |
| **/ Vues** | Vues contiennent nos modèles de l’interface utilisateur |
| **/ Modèles** | Les modèles contiennent et manipulent des données |
| **/ Contenu** | Ce dossier conserve nos images, CSS et tout autre contenu statique |
| **Scripts** | Ce dossier conserve nos fichiers JavaScript |

Ces dossiers sont inclus même dans une application vide ASP.NET MVC parce que l’infrastructure ASP.NET MVC par défaut utilise une approche « convention avant la configuration » et fait des hypothèses par défaut basés sur les conventions d’affectation de noms de dossier. Par exemple, les contrôleurs de recherchent des vues dans le dossier vues par défaut sans avoir à spécifier explicitement ceci dans votre code. Les conventions par défaut ne conserverez réduit la quantité de code, vous devez écrire, et peut également faciliter par d’autres développeurs à comprendre votre projet. Nous allons expliquer ces conventions plus que nous créons notre application.

>[!div class="step-by-step"]
[Next](mvc-music-store-part-2.md)
