---
uid: mvc/overview/older-versions-1/overview/understanding-models-views-and-controllers-vb
title: "Fonctionnement des modèles, vues et contrôleurs (VB) | Documents Microsoft"
author: StephenWalther
description: "Vous ne comprenez pas sur les modèles, vues et contrôleurs ? Dans ce didacticiel, Stephen Walther présente les différentes parties d’une application ASP.NET MVC."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/19/2008
ms.topic: article
ms.assetid: a106374a-5e74-4fd0-9ac0-1a32280e5d0d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/overview/understanding-models-views-and-controllers-vb
msc.type: authoredcontent
ms.openlocfilehash: 36f70503f7e96210e5fb8a2d361da6759c18c85b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="understanding-models-views-and-controllers-vb"></a>Fonctionnement des modèles, vues et contrôleurs (VB)
====================
par [Stephen Walther](https://github.com/StephenWalther)

> Vous ne comprenez pas sur les modèles, vues et contrôleurs ? Dans ce didacticiel, Stephen Walther présente les différentes parties d’une application ASP.NET MVC.


Ce didacticiel vous fournit une vue d’ensemble d’ASP.NET MVC modèles, vues et contrôleurs. En d’autres termes, elle explique la lettre M », V » et C » dans ASP.NET MVC.

Après avoir lu ce didacticiel, vous devez comprendre comment les différentes parties d’une application ASP.NET MVC fonctionnent ensemble. Vous devez également comprendre la façon dont l’architecture d’une application ASP.NET MVC diffère d’une application ASP.NET Web Forms ou une application Active Server Pages.

## <a name="the-sample-aspnet-mvc-application"></a>L’exemple d’Application ASP.NET MVC

Le modèle de Visual Studio par défaut pour la création d’Applications Web ASP.NET MVC inclut un exemple très simple d’application qui peut être utilisée pour comprendre les différentes parties d’une application ASP.NET MVC. Nous tirer parti de cette application simple dans ce didacticiel.

Vous créez une application ASP.NET MVC avec le modèle MVC par le lancement de Visual Studio 2008 et en sélectionnant l’option de menu fichier, nouveau projet (voir Figure 1). Dans la boîte de dialogue Nouveau projet, sélectionnez votre langage de programmation favori sous Types de projets (Visual Basic ou c#) et **Application Web ASP.NET MVC** sous modèles. Cliquez sur le bouton OK.


[![Boîte de dialogue Nouveau projet](understanding-models-views-and-controllers-vb/_static/image1.jpg)](understanding-models-views-and-controllers-vb/_static/image1.png)

**Figure 01**: boîte de dialogue Nouveau projet ([cliquez pour afficher l’image en taille réelle](understanding-models-views-and-controllers-vb/_static/image2.png))


Lorsque vous créez une application ASP.NET MVC, la **créer un projet de Test unitaire** boîte de dialogue s’affiche (voir Figure 2). Cette boîte de dialogue vous permet de créer un projet distinct dans votre solution pour tester votre application ASP.NET MVC. Sélectionnez l’option **non, ne pas créer le projet de test unitaire** et cliquez sur le **OK** bouton.


[![Créer la boîte de dialogue de Test unitaire](understanding-models-views-and-controllers-vb/_static/image2.jpg)](understanding-models-views-and-controllers-vb/_static/image3.png)

**Figure 02**: boîte de dialogue du Test unitaire créer ([cliquez pour afficher l’image en taille réelle](understanding-models-views-and-controllers-vb/_static/image4.png))


La nouvelle ASP.NET MVC après l’application est créée. Vous verrez plusieurs dossiers et fichiers dans la fenêtre de l’Explorateur de solutions. En particulier, vous verrez trois dossiers nommés de modèles, vues et contrôleurs. Comme vous pouvez deviner à partir des noms de dossier, ces dossiers contiennent les fichiers pour l’implémentation de modèles, vues et contrôleurs.

Si vous développez le dossier contrôleurs, vous devez voir un fichier nommé AccountController.vb et un fichier nommé HomeController.vb. Si vous développez le dossier Views, vous devez voir trois sous-dossiers nommés Account, Home et Shared. Si vous développez le dossier de base, vous verrez deux fichiers supplémentaires About.aspx et Index.aspx (voir Figure 3). Ces fichiers constituent l’exemple d’application inclus avec le modèle ASP.NET MVC par défaut.


[![La fenêtre Explorateur de solutions](understanding-models-views-and-controllers-vb/_static/image3.jpg)](understanding-models-views-and-controllers-vb/_static/image5.png)

**Figure 03**: la fenêtre Explorateur de solutions ([cliquez pour afficher l’image en taille réelle](understanding-models-views-and-controllers-vb/_static/image6.png))


Vous pouvez exécuter l’exemple d’application en sélectionnant l’option de menu **déboguer, démarrer le débogage**. Vous pouvez également appuyer sur la touche F5.

Lorsque vous exécutez une application ASP.NET pour la première fois, dans la Figure 4, la boîte de dialogue s’affiche qui vous recommande d’activer le mode de débogage. Cliquez sur le bouton OK et l’application s’exécutera.


[![Boîte de dialogue débogage non activé](understanding-models-views-and-controllers-vb/_static/image4.jpg)](understanding-models-views-and-controllers-vb/_static/image7.png)

**Figure 04**: boîte de dialogue débogage non activé ([cliquez pour afficher l’image en taille réelle](understanding-models-views-and-controllers-vb/_static/image8.png))


Lorsque vous exécutez une application ASP.NET MVC, Visual Studio lance l’application dans votre navigateur web. L’exemple d’application se compose de deux pages : la page d’Index et de la page à propos. Au premier démarrage de l’application, la page d’Index s’affiche (voir Figure 5). Vous pouvez accéder à la page à propos en cliquant sur le lien du menu en haut à droite de l’application.


[![La Page d’Index](understanding-models-views-and-controllers-vb/_static/image5.jpg)](understanding-models-views-and-controllers-vb/_static/image9.png)

**Figure 05**: la Page d’Index ([cliquez pour afficher l’image en taille réelle](understanding-models-views-and-controllers-vb/_static/image10.png))


Notez que les URL dans la barre d’adresses de votre navigateur. Par exemple, lorsque vous cliquez sur le lien à propos du menu, l’URL dans la barre d’adresse de navigateur modifie en **/accueil/sur**.

Si vous fermez la fenêtre du navigateur et que vous revenez à Visual Studio, vous ne pourrez pas rechercher un fichier avec la page d’accueil chemin d’accès/sur. Les fichiers n’existent pas. Comment est-ce possible ?

## <a name="a-url-does-not-equal-a-page"></a>Une URL n’est pas égale une Page

Lorsque vous générez une application Web Forms ASP.NET traditionnelle ou une application Active Server Pages, il existe une correspondance univoque entre une URL et d’une page. Si vous demandez une page nommée SomePage.aspx à partir du serveur, puis sera une page sur le disque nommé SomePage.aspx. Si le fichier SomePage.aspx n’existe pas, vous obtenez un inconvénients **404 - Page introuvable** erreur.

Lorsque vous créez une application ASP.NET MVC, en revanche, n’existe aucune correspondance entre l’URL que vous tapez dans la barre d’adresses de votre navigateur et les fichiers que vous trouvez dans votre application. Dans une application ASP.NET MVC, une URL correspond à une action de contrôleur à la place d’une page sur le disque.

Dans une application ASP.NET ou ASP classique, les demandes du navigateur sont mappées aux pages. Dans une application ASP.NET MVC, en revanche, les demandes du navigateur sont mappées aux actions de contrôleur. Une application Web Forms ASP.NET est centrées sur le contenu. Une application ASP.NET MVC, en revanche, est centré sur la logique d’application.

## <a name="understanding-aspnet-routing"></a>Présentation du routage ASP.NET

Une demande de navigateur Obtient le mappage à une action de contrôleur via une fonctionnalité de l’infrastructure ASP.NET appelé *le routage ASP.NET*. Routage d’ASP.NET est utilisé par l’infrastructure ASP.NET MVC pour *itinéraire* les demandes entrantes vers les actions de contrôleur.

Service de routage ASP.NET utilise une table de routage pour gérer les demandes entrantes. Cette table d’itinéraires est créée au premier démarrage de votre application web. La table de routage est configuré dans le fichier Global.asax. Le fichier Global.asax de MVC par défaut est contenu dans la liste 1.

**La liste 1 - Global.asax**

[!code-vb[Main](understanding-models-views-and-controllers-vb/samples/sample1.vb)]

Lorsqu’une application ASP.NET commence, l’Application\_méthode Start() est appelée. Dans la liste 1, cette méthode appelle la méthode RegisterRoutes() et la méthode RegisterRoutes() crée la table d’itinéraires par défaut.

La table d’itinéraires par défaut se compose d’un itinéraire. Cet itinéraire par défaut s’arrête toutes les demandes entrantes en trois segments (un segment d’URL est entre des barres obliques). Le premier segment est mappé à un nom de contrôleur, le deuxième segment est mappé à un nom d’action et le dernier segment est mappé à un paramètre passé à l’action nommée ID.

Par exemple, considérez l’URL suivante :

/ Produits/détails/3

Cette URL est analysée dans les trois paramètres comme suit :

Contrôleur = produit

Action = détails

ID = 3

L’itinéraire par défaut défini dans le fichier Global.asax inclut les valeurs par défaut pour les trois paramètres. La valeur par défaut contrôleur Home est l’Action par défaut est l’Index et l’Id par défaut est une chaîne vide. Avec ces paramètres par défaut à l’esprit, envisagez de l’analyse de l’URL suivante :

/ Employé

Cette URL est analysée dans les trois paramètres comme suit :

Contrôleur = employé

action = Index

ID =

Enfin, si vous ouvrez une Application ASP.NET MVC sans fournir n’importe quelle URL (par exemple, `http://localhost`), l’URL est analysé comme suit :

contrôleur = Home

action = Index

ID =

La demande est routée vers l’action Index() sur la classe HomeController.

## <a name="understanding-controllers"></a>Présentation des contrôleurs

Un contrôleur est chargé de contrôler la façon dont un utilisateur interagit avec une application MVC. Un contrôleur contient la logique de flux de contrôle pour une application ASP.NET MVC. Un contrôleur détermine la réponse à renvoyer à un utilisateur lorsqu’un utilisateur effectue une demande de navigateur.

Un contrôleur est simplement une classe (par exemple, une classe Visual Basic ou c#). L’exemple application ASP.NET MVC inclut un contrôleur nommé HomeController.vb situé dans le dossier contrôleurs. Le contenu du fichier HomeController.vb est reproduit dans le Listing 2.

**Listing 2 - HomeController.cs**

[!code-vb[Main](understanding-models-views-and-controllers-vb/samples/sample2.vb)]

Notez que le fichier HomeController possède deux méthodes nommées Index() et About(). Ces deux méthodes correspondent aux deux actions exposées par le contrôleur. L’URL /Home/Index appelle la méthode HomeController.Index() et URL /Home/sur appelle la méthode HomeController.About().

Aucune méthode publique dans un contrôleur est exposée comme une action du contrôleur. Vous devez faire attention à ce sujet. Cela signifie que n’importe quelle méthode publique contenue dans un contrôleur peut être appelé par toute personne ayant accès à Internet en entrant l’URL de droite dans un navigateur.

## <a name="understanding-views"></a>Présentation des vues

Les actions de deux contrôleur exposées par la classe HomeController, Index() et About(), les deux renvoient une vue. Une vue contient le balisage HTML et le contenu qui est envoyé au navigateur. Une vue est l’équivalent d’une page lorsque vous travaillez avec une application ASP.NET MVC.

Vous devez créer vos affichages à l’emplacement correct. L’action HomeController.Index() retourne une vue située dans le chemin d’accès suivant :

\Views\Home\Index.aspx

L’action HomeController.About() retourne une vue située dans le chemin d’accès suivant :

\Views\Home\About.aspx

En règle générale, si vous souhaitez renvoyer un affichage pour une action du contrôleur, vous devez créer un sous-dossier dans le dossier vues portant le même nom que votre contrôleur. Dans le sous-dossier, vous devez créer un fichier .aspx avec le même nom que l’action du contrôleur.

Le fichier de liste 3 contient la vue About.aspx.

**La liste 3 - About.aspx**

[!code-aspx[Main](understanding-models-views-and-controllers-vb/samples/sample3.aspx)]

Si vous ignorez la première ligne dans la liste 3, la plupart du reste de la vue se compose du code HTML standard. Vous pouvez modifier le contenu de la vue en entrant le code HTML que vous souhaitez ici.

Une vue est très similaire à une page dans les Pages ASP ou ASP.NET Web Forms. Une vue peut contenir des scripts et le contenu HTML. Vous pouvez écrire des scripts dans vos favoris .NET (par exemple, c# ou Visual Basic .NET) de langage de programmation. Vous utilisez des scripts pour afficher le contenu dynamique tel que les données de la base de données.

## <a name="understanding-models"></a>Fonctionnement des modèles

Nous avons parlé des contrôleurs et nous avons parlé des vues. La dernière rubrique que nous devons aborder est de modèles. Qu’est un modèle MVC ?

Un modèle MVC contient toutes les votre logique d’application qui n’est pas contenue dans une vue ou d’un contrôleur. Le modèle doit contenir de votre logique métier d’application, la logique de validation et la logique d’accès de base de données. Par exemple, si vous utilisez Microsoft Entity Framework pour accéder à votre base de données, vous devez créer vos classes Entity Framework (votre fichier .edmx) dans le dossier de modèles.

Une vue doit contenir uniquement la logique relatives à la génération de l’interface utilisateur. Un contrôleur doit contenir uniquement le strict minimum de la logique nécessaire pour retourner la vue de droite ou de rediriger l’utilisateur vers une autre action (contrôle de flux). Tout le reste doit être contenu dans le modèle.

En règle générale, vous devez vous efforcer de modèles fat et contrôleurs réduits. Vos méthodes de contrôleur doivent contenir uniquement quelques lignes de code. Si une action de contrôleur obtient trop fat, vous devez envisager la logique de déplacement vers une nouvelle classe dans le dossier de modèles.

## <a name="summary"></a>Résumé

Ce didacticiel vous fourni une vue d’ensemble générale des différentes parties d’un ASP.NET MVC application web. Vous avez appris comment le routage ASP.NET mappe les demandes entrantes de navigateur pour les actions de contrôleur spécifique. Vous avez appris comment contrôleurs orchestrent la manière dont les vues sont retournées dans le navigateur. Enfin, vous avez appris comment les modèles contiennent des professionnels de l’application, la validation et la logique de base de données access.
