---
uid: aspnet/web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio
title: "Programmation ASP.NET Web Pages (Razor) à l’aide de Visual Studio | Documents Microsoft"
author: tfitzmac
description: "Cette annexe décrit comment vous pouvez utiliser Visual Studio 2010 ou Visual Web Developer 2010 Express pour le programme ASP.NET Web Pages avec la syntaxe Razor."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/13/2014
ms.topic: article
ms.assetid: 0acfec5a-48f2-4766-a801-a0f426966f0a
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio
msc.type: authoredcontent
ms.openlocfilehash: 5cfeda206eda8fb3fd769d34fb40bae2c3b65093
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="programming-aspnet-web-pages-razor-using-visual-studio"></a>Programmation de Pages Web ASP.NET (Razor) à l’aide de Visual Studio
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment vous pouvez utiliser Visual Studio ou Visual Web Developer Express au programme des sites Web ASP.NET Web Pages (Razor).
> 
> Vous allez apprendre
> 
> - Vous devez installer (le cas échéant) pour travailler avec des Pages Web ASP.NET dans votre version de Visual Studio.
> - Comment ajouter la prise en charge pour les Pages Web ASP.NET pour Visual Web Developer 2010 Express.
> - L’utilisation des fonctionnalités dans Visual Studio pour travailler avec des pages ASP.NET Razor, notamment IntelliSense et le débogueur.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
> - Visual Studio 2013
> - WebMatrix 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2, Visual Studio 2012, Visual Studio 2010 et WebMatrix 2.


Vous pouvez programmer des pages Web ASP.NET avec syntaxe Razor à l’aide de WebMatrix ou nombreux autres éditeurs de code. Vous pouvez également utiliser Microsoft Visual Studio est un environnement complet de développement intégré (IDE) qui fournit un ensemble puissant d’outils pour la création de nombreux types d’applications (pas seulement des sites Web). Pour utiliser des pages ASP.NET Razor, vous pouvez utiliser l’une des éditions complètes de Visual Studio ou gratuit [Visual Studio Express pour Web](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-2013-express) edition.

Deux fonctionnalités particulièrement utiles que Visual Studio fournit pour la programmation avec des pages web ASP.NET Razor sont :

- *IntelliSense*. La fonctionnalité IntelliSense intégrée à Visual Studio est plus complet que IntelliSense dans WebMatrix.
- *Débogueur*. Le débogueur vous permet de résoudre les problèmes de votre code en arrêtant un programme pendant qu’il est en cours d’exécution, examen des variables et parcourir le code ligne par ligne.

## <a name="using-visual-studio-with-different-versions-of-aspnet-web-pages"></a>À l’aide de Visual Studio avec différentes Versions des Pages Web ASP.NET

Visual Studio 2012 et Visual Studio 2013 incluent la prise en charge pour les Pages Web ASP.NET. (Les packages qui sont nécessaires pour prendre en charge des Pages Web ASP.NET sont installés lorsque vous installez Visual Studio)

Visual Studio 2010 n’inclut pas prise en charge par défaut pour ASP.NET Web Pages. Pour utiliser les Pages Web ASP.NET avec Visual Studio 2010, vous devez installer le package d’ASP.NET MVC. Pour obtenir ASP.NET Web Pages 2, vous installez ASP.NET MVC 4.

Le tableau suivant récapitule la prise en charge pour les Pages Web ASP.NET dans les différentes versions de Visual Studio.

|  | Visual Studio 2010 | Visual Studio 2012 | Visual Studio 2013 |
| --- | --- | --- | --- |
| **ASP.NET Web Pages 2** | Installez ASP.NET MVC 4 | (Inclus) | (Inclus) |
| **ASP.NET Web Pages 3** |  | Mise à jour d’ASP.NET Web Pages 3 NuGet via | (Inclus) |

Pour travailler avec Visual Studio 2010, consultez [prise en charge pour les Pages Web ASP.NET dans Visual Studio 2010 installation](#vs2010support).

## <a name="launching-visual-studio-from-webmatrix"></a>Lancement de Visual Studio à partir de WebMatrix

Si vous avez démarré un projet dans WebMatrix et que vous souhaitez basculer vers Visual Studio, WebMatrix fournit un bouton pour facilement ouvrir le projet dans Visual Studio. Vous devez disposer de Visual Studio installée sur votre ordinateur pour que ce bouton soit activé. L’illustration suivante montre le bouton dans WebMatrix.

![Lancez Visual Studio](program-asp-net-web-pages-in-visual-studio/_static/image1.png)

Lorsque vous cliquez sur le bouton, le projet est ouvert dans Visual Studio. Vous pouvez basculer dans les deux sens entre WebMatrix et Visual Studio sans problème. Vous êtes averti si tous les fichiers ont été modifiés dans l’autre environnement et doivent être rechargés pour obtenir les dernières modifications.

## <a name="creating-aspnet-razor-site-in-visual-studio"></a>Création du site Web ASP.NET Razor dans Visual Studio

Pour créer un site Web ASP.NET Razor dans Visual Studio :

1. Démarrez Visual Studio ou Visual Web Developer.
2. Dans le **fichier** menu, cliquez sur **nouveau Site Web**.

    ![Créez un site web](program-asp-net-web-pages-in-visual-studio/_static/image2.png)
3. Dans le **nouveau Site Web** boîte de dialogue, sélectionnez la langue à utiliser (Visual c# ou Visual Basic).
4. Sélectionnez le **Site Web ASP.NET (Razor)** modèle.

    ![site de Razor](program-asp-net-web-pages-in-visual-studio/_static/image3.png)
5. Cliquez sur **OK**.

Votre nouveau projet existe et est rempli avec certaines des pages web par défaut pour vous aider à commencer.

### <a name="using-intellisense"></a>Using IntelliSense

Maintenant que vous avez créé un site, vous pouvez voir le fonctionne d’IntelliSense dans Visual Studio.

1. Dans le site Web que vous venez de créer, ouvrir le *Default.cshtml* page.
2. Après le `<h3>` balises dans la page, tapez `@ServerInfo.` (y compris le point). Notez comment IntelliSense affiche les méthodes disponibles pour le `ServerInfo` helper dans une liste déroulante. 

    ![IntelliSense](program-asp-net-web-pages-in-visual-studio/_static/image4.png)
3. Sélectionnez le `GetHtml` méthode dans la liste, puis appuyez sur ENTRÉE. IntelliSense complète automatiquement la méthode. (Comme avec n’importe quelle méthode en c#, vous devez ajouter `()` caractères après la méthode.)  
 Le code complet pour le `GetHtml` méthode ressemble à l’exemple suivant :  

    [!code-cshtml[Main](program-asp-net-web-pages-in-visual-studio/samples/sample1.cshtml)]
4. Appuyez sur Ctrl + F5 pour exécuter la page. Voici à quoi ressemble la page lorsque affiché dans un navigateur : 

    ![page par défaut dans le navigateur](program-asp-net-web-pages-in-visual-studio/_static/image5.png)
5. Fermez le navigateur.

### <a name="using-the-debugger"></a>L’utilisation du débogueur

1. En haut de la *Default.cshtml* page, après la ligne qui commence par `Page.Title`, ajoutez la ligne de code suivante : 

    [!code-csharp[Main](program-asp-net-web-pages-in-visual-studio/samples/sample2.cs)]
2. Dans la marge grise de l’éditeur à gauche du code, puis cliquez sur en regard de cette nouvelle ligne pour ajouter un *point d’arrêt*. Un point d’arrêt est un marqueur qui indique au débogueur d’arrêter l’exécution du programme à ce stade afin de voir ce qui se passe.

    ![point d’arrêt défini](program-asp-net-web-pages-in-visual-studio/_static/image6.png)
3. Supprimez l’appel à la `ServerInfo.GetHtml` (méthode) et ajoutez un appel à la `@myTime` variable à la place. Cet appel affiche la valeur de temps actuelle qui est retournée par la nouvelle ligne de code.
4. Appuyez sur F5 pour exécuter la page dans le débogueur. La page s’arrête sur le point d’arrêt que vous définissez. L’illustration suivante montre la page d’aperçu dans l’éditeur avec le point d’arrêt (en jaune). 

    ![point d’arrêt de débogage](program-asp-net-web-pages-in-visual-studio/_static/image7.png)
5. Dans la barre d’outils de débogage, cliquez sur le **pas à pas détaillé** bouton (ou appuyez sur F11) pour exécuter la ligne de code suivante. Chaque fois que vous cliquez sur ce bouton, vous permet de passer l’exécution à la ligne de code suivante.

    ![Pas à pas détaillé de bouton](program-asp-net-web-pages-in-visual-studio/_static/image8.png)
6. Examinez la valeur de la `myTime` variable en maintenant le pointeur de la souris dessus ou en examinant les valeurs affichées dans le **variables locales** et **pile des appels** windows. Visual Studio affiche la valeur de la variable.

    ![valeur d’afficher le temps](program-asp-net-web-pages-in-visual-studio/_static/image9.png)
7. Lorsque vous avez terminé d’examiner la variable et le code, appuyez sur F5 pour continuer l’exécution de la page sans vous arrêter à chaque ligne. Lorsque vous avez terminé d’exécuter pas à pas tout le code, le navigateur affiche la page.

Pour en savoir plus sur le débogueur et le débogage de code dans Visual Studio, consultez [procédure pas à pas : débogage des Pages Web dans Visual Web Developer](https://msdn.microsoft.com/library/z9e7w6cs.aspx).

## <a name="using-razor-in-aspnet-mvc-projects-with-visual-studio"></a>À l’aide de Razor dans les projets ASP.NET MVC avec Visual Studio

La syntaxe Razor est aussi largement utilisée dans les projets ASP.NET MVC. MVC est un moyen puissant, basé sur des modèles pour créer des sites Web dynamiques. Si votre site ASP.NET Web Pages devient difficile à gérer, vous souhaiterez sa conversion en une application ASP.NET MVC. Pour obtenir un exemple de création d’une application MVC, consultez [mise en route avec ASP.NET MVC 5](../../../mvc/overview/getting-started/introduction/getting-started.md).

<a id="vs2010support"></a>
## <a name="installing-support-for-aspnet-web-pages-in-visual-studio-2010"></a>L’installation de la prise en charge pour les Pages Web ASP.NET dans Visual Studio 2010

Cette section montre comment installer Visual Web Developer Express 2010 et les outils de Pages Web ASP.NET pour Visual Studio.

1. Si vous n’avez pas encore de Web Platform Installer, téléchargez-le à partir de l’URL suivante :

    [https://www.Microsoft.com/Web/downloads/Platform.aspx](https://www.microsoft.com/web/downloads/platform.aspx)
2. Exécutez le programme d’installation de la plateforme Web.
3. Cliquez sur le **produits** onglet.

    ![Onglet produits de WebPI](program-asp-net-web-pages-in-visual-studio/_static/image10.png)
4. Recherchez **ASP.NET MVC 4** (pour ASP.NET Web Pages 2) puis cliquez sur **ajouter**. Ces produits incluent des outils de Visual Studio pour créer des sites Web ASP.NET Razor.

    ![Options d’installation WebPi](program-asp-net-web-pages-in-visual-studio/_static/image11.png)
5. Cliquez sur **installer** pour terminer l’installation.
