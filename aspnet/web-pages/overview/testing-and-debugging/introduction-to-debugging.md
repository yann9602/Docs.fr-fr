---
uid: web-pages/overview/testing-and-debugging/introduction-to-debugging
title: "Introduction au débogage ASP.NET Web Pages (Razor) Sites | Documents Microsoft"
author: tfitzmac
description: "Le débogage est le processus de recherche et de correction des erreurs dans vos pages de codes. Ce chapitre explique certains outils et techniques que vous pouvez utiliser pour déboguer et analyz..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2014
ms.topic: article
ms.assetid: 68de4326-7611-4b9b-b5f6-79b7adc3069f
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/testing-and-debugging/introduction-to-debugging
msc.type: authoredcontent
ms.openlocfilehash: 2bc1f096540d17095ef760eed67b458fcd4e1372
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introduction-to-debugging-aspnet-web-pages-razor-sites"></a>Introduction au débogage ASP.NET Web Pages (Razor) Sites
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique les différentes façons de déboguer des pages dans un site Web ASP.NET Web Pages (Razor). Le débogage est le processus de recherche et de correction des erreurs dans vos pages de codes.
> 
> **Ce que vous allez apprendre :** 
> 
> - Comment afficher des informations qui vous aide à analyser et de déboguer des pages.
> - Les outils de l’utilisation de débogage dans Visual Studio.
>   
> 
> Voici les fonctionnalités d’ASP.NET introduites dans l’article :
> 
> - Le `ServerInfo` helper.
> - `ObjectInfo`application d’assistance.
>   
> 
> ## <a name="software-versions"></a>Versions du logiciel
> 
> 
> - Pages Web ASP.NET (Razor) 3
> - Visual Studio 2013
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2. Vous pouvez utiliser WebMatrix 3, mais le débogueur intégré n’est pas pris en charge.


Un aspect important de dépannage des erreurs et des problèmes dans votre code doit en premier lieu de les éviter. C’est également en plaçant des sections de votre code qui sont susceptibles de provoquer des erreurs dans `try/catch` blocs. Pour plus d’informations, consultez la section sur la gestion des erreurs dans [Introduction à ASP.NET Web Programming à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890).

Le `ServerInfo` d’assistance est un outil de diagnostic qui vous donne une vue d’ensemble des informations sur l’environnement de serveur web qui héberge votre page. Il montre également les informations de demande HTTP qui sont envoyées lorsqu’un navigateur demande la page. Le `ServerInfo` affiche l’identité de l’utilisateur actuel, le type de navigateur qui a effectué la demande, et ainsi de suite. Ce type d’informations peut vous aider à résoudre les problèmes courants.

1. Créer une nouvelle page web nommée *ServerInfo.cshtml*.
2. À la fin de la page, juste avant la fermeture `</body>` , ajoutez `@ServerInfo.GetHtml()`:

    [!code-cshtml[Main](introduction-to-debugging/samples/sample1.cshtml)]

    Vous pouvez ajouter la `ServerInfo` code n’importe où dans la page. Mais, ajoutez-le à la fin conserve sa sortie distinct à partir de votre contenu des autres pages, ce qui le rend plus facile à lire.

    > [!NOTE] 
    > 
    > **Important** vous devez supprimer tout code de diagnostic à partir de vos pages web avant de déplacer des pages web sur un serveur de production. Cela s’applique à la `ServerInfo` application d’assistance, ainsi que les autres techniques de diagnostic dans cet article qui impliquent l’ajout de code à une page. Vous ne souhaitez les visiteurs de votre site Web pour obtenir des informations sur votre nom de serveur, les noms d’utilisateur, les chemins d’accès sur votre serveur et des informations similaires, car ce type d’information peut être utile à des personnes malintentionnées.
3. Enregistrez la page et l’exécuter dans un navigateur.

    ![Débogage-1](introduction-to-debugging/_static/image1.jpg)

    Le `ServerInfo` affiche quatre tables de données dans la page :

    - Configuration du serveur. Cette section fournit des informations sur le serveur d’hébergement web, y compris le nom de l’ordinateur, la version de ASP.NET que vous exécutez, le nom de domaine et l’heure du serveur.
    - Variables de serveur ASP.NET. Cette section fournit des détails sur les nombreux détails de protocole HTTP (appelée HTTP variables) et les valeurs qui font partie de chaque demande de page web.
    - Informations d’exécution de HTTP. Cette section fournit des informations que la version de Microsoft .NET Framework que votre page web est en cours d’exécution sous le chemin d’accès, de plus d’informations sur le cache et ainsi de suite. (Comme vous l’avez appris dans [Introduction à ASP.NET Web Programming à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890), à l’aide de la syntaxe reposent sur la technologie de Microsoft ASP.NET web server, qui est elle-même basée sur un logiciel complet de Razor ASP.NET Web Pages bibliothèque de développement appelé le .NET Framework.)
    - Variables d’environnement. Cette section fournit une liste de toutes les variables d’environnement locales et leurs valeurs sur le serveur web.

    Une description complète de toutes les informations de serveur et de la demande est dépasse le cadre de cet article, mais vous pouvez voir que la `ServerInfo` programme d’assistance retourne un grand nombre d’informations de diagnostic. Pour plus d’informations sur les valeurs qui `ServerInfo` retourne, consultez [Variables d’environnement reconnu](https://technet.microsoft.com/en-us/library/dd560744(WS.10).aspx) sur le site Web Microsoft TechNet et [Variables serveur IIS](https://msdn.microsoft.com/en-us/library/ms524602(VS.90).aspx) sur le site Web MSDN.

## <a name="embedding-output-expressions-to-display-page-values"></a>Incorporation des Expressions de sortie pour afficher les valeurs de la Page

Une autre pour voir ce qui se passe dans votre code consiste à incorporer des expressions de sortie dans la page. Comme vous le savez, vous pouvez effectuer directement la sortie la valeur d’une variable en ajoutant un nom tel que `@myVariable` ou `@(subTotal * 12)` à la page. Pour le débogage, vous pouvez placer ces expressions de sortie aux points stratégiques dans votre code. Cela vous permet de vous permet de voir la valeur de clé variables ou le résultat des calculs lors de l’exécution de votre page. Lorsque vous avez terminé le débogage, vous pouvez supprimer les expressions ou commentaire. Cette procédure montre une manière classique pour utiliser des expressions incorporées pour vous aider à déboguer une page.

1. Créer une nouvelle page WebMatrix nommé *OutputExpression.cshtml*.
2. Remplacer la page de contenu avec les éléments suivants :

    [!code-html[Main](introduction-to-debugging/samples/sample2.html)]

    L’exemple utilise un `switch` instruction pour vérifier la valeur de la `weekday` variable et puis d’afficher un message de sortie différent selon le jour de la semaine, il s’agit. Dans l’exemple, le `if` bloc dans le premier bloc de code modifie arbitrairement le jour de la semaine en ajoutant un jour à la valeur du jour actuel. Il s’agit d’une erreur introduite à titre d’illustration.
3. Enregistrez la page et l’exécuter dans un navigateur.

    La page affiche le message pour un jour incorrect de la semaine. Le jour de la semaine qu’il est en fait, vous verrez le message pour un jour plus tard. Bien que dans ce cas, vous savez Pourquoi le message est désactivé (parce que le code qui définit la valeur du jour incorrect délibérément), en réalité, il est souvent difficile de savoir où des opérations incorrect dans le code. Pour déboguer, vous devez savoir ce qui se passe à la valeur des variables et des objets clés telles que `weekday`.
4. Ajouter des expressions de la sortie en insérant `@weekday` comme indiqué dans les deux emplacements indiqués par les commentaires du code. Ces expressions de sortie affiche les valeurs de la variable à ce stade dans l’exécution du code.

    [!code-csharp[Main](introduction-to-debugging/samples/sample3.cs?highlight=2-3,15-16)]
5. Enregistrez et exécutez la page dans un navigateur.

    La page affiche d’abord le jour réel de la semaine, puis le jour de mise à jour de la semaine qui résulte de l’ajout d’un jour, puis le message généré à partir de la `switch` instruction. La sortie de deux expressions de variable (`@weekday`), car vous n’avez pas ajouté le code HTML ne contient aucun espace entre le nombre de jours `<p>` balises à la sortie ; les expressions sont pour le test.

    ![Débogage-2](introduction-to-debugging/_static/image2.jpg)

    Vous pouvez maintenant voir où l’erreur est. Lorsque vous affichez le `weekday` variable dans le code, elle affiche le jour correct. Lorsque vous affichez il la deuxième fois, après le `if` dans le code, le jour est désactivée par un. Afin de savoir que quelque chose s’est produit entre l’apparence des première et deuxième de la variable de la semaine. S’il s’agissait d’un bogue réel, ce type d’approche vous permet d’affiner l’emplacement du code qui provoque le problème.
6. Corrigez le code dans la page en supprimant les expressions de deux de sortie que vous avez ajouté et en supprimant le code qui modifie le jour de la semaine. Le bloc restant et complet de code ressemble à l’exemple suivant :

    [!code-cshtml[Main](introduction-to-debugging/samples/sample4.cshtml)]
7. Exécutez la page dans un navigateur. Cette fois, vous voyez le message correct est affiché pour le jour réel de la semaine.

## <a name="using-the-objectinfo-helper-to-display-object-values"></a>Pour afficher les valeurs de l’objet à l’aide de l’application d’assistance ObjectInfo

Le `ObjectInfo` affiche le type et la valeur de chaque objet que vous passez à celui-ci. Vous pouvez l’utiliser pour afficher la valeur des variables et des objets dans votre code (comme vous le faisiez avec les expressions de sortie dans l’exemple précédent), ainsi que vous pouvez voir des données des informations sur l’objet de type.

1. Ouvrez le fichier nommé *OutputExpression.cshtml* que vous avez créé précédemment.
2. Remplacez tout le code dans la page avec le bloc de code suivant :

    [!code-html[Main](introduction-to-debugging/samples/sample5.html)]
3. Enregistrez et exécutez la page dans un navigateur.

    ![Débogage-4](introduction-to-debugging/_static/image3.jpg)

    Dans cet exemple, la `ObjectInfo` affiche deux éléments :

    - Type. Pour la première variable, le type est `DayOfWeek`. Pour la deuxième variable, le type est `String`.
    - La valeur. Dans ce cas, étant donné que vous affichez déjà la valeur de la variable de salutations dans la page, la valeur s’affiche de nouveau lorsque vous passez la variable à `ObjectInfo`.

    Pour les objets plus complexes, le `ObjectInfo` assistance peut afficher plus d’informations &#8212; en fait, il peut afficher les types et les valeurs de toutes les propriétés d’un objet.

## <a name="using-debugging-tools-in-visual-studio"></a>À l’aide des outils de débogage dans Visual Studio

Pour une expérience de débogage plus complète, utilisez Visual Studio 2013 ou gratuit [Visual Studio Express 2013 pour le Web](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-2013-express). Avec Visual Studio, vous pouvez définir un point d’arrêt dans votre code à la ligne que vous voulez inspecter.

![point d’arrêt défini](introduction-to-debugging/_static/image1.png)

Lorsque vous testez le site web, le code en cours d’exécution s’arrête au point d’arrêt.

![Atteindre le point d’arrêt](introduction-to-debugging/_static/image2.png)

Vous pouvez examiner les valeurs actuelles des variables et parcourir le code ligne par ligne.

![consultez les valeurs](introduction-to-debugging/_static/image3.png)

Pour plus d’informations sur l’utilisation du débogueur intégré dans Visual Studio pour déboguer des pages ASP.NET Razor, consultez [de programmation les Pages Web ASP.NET (Razor) à l’aide de Visual Studio](https://go.microsoft.com/fwlink/?LinkId=205854).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Programmation de Pages Web ASP.NET (Razor) à l’aide de Visual Studio](https://go.microsoft.com/fwlink/?LinkId=205854)
- [Variables de serveur IIS](https://msdn.microsoft.com/en-us/library/ms524602(VS.90).aspx) (MSDN)
- [Reconnu des Variables d’environnement](https://technet.microsoft.com/en-us/library/dd560744(WS.10).aspx) (TechNet)
