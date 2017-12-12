---
uid: web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-asp-net-ajax-debugging-capabilities
title: "Présentation des fonctionnalités de débogage de ASP.NET AJAX | Documents Microsoft"
author: scottcate
description: "La possibilité de déboguer du code est que tous les développeurs doivent être placés dans leur arsenal quelle que soit la technologie qu’ils utilisent. Alors que de nombreux développeurs sont en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/28/2008
ms.topic: article
ms.assetid: 7f9380c6-19f7-4c82-a019-916ec6dffc9c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-asp-net-ajax-debugging-capabilities
msc.type: authoredcontent
ms.openlocfilehash: 426d0182978faf7fc7516203fcc84ef0152790ba
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="understanding-aspnet-ajax-debugging-capabilities"></a>Présentation des fonctionnalités de débogage de ASP.NET AJAX
====================
par [ble pour indiquer Scott](https://github.com/scottcate)

[Télécharger le PDF](http://download.microsoft.com/download/C/1/9/C19A3451-1D14-477C-B703-54EF22E197EE/AJAX_tutorial06_Debugging_MS_Ajax_Applications_cs.pdf)

> La possibilité de déboguer du code est que tous les développeurs doivent être placés dans leur arsenal quelle que soit la technologie qu’ils utilisent. Alors que de nombreux développeurs sont habitués à déboguer des applications ASP.NET qui utilisent le code VB.NET ou c# à l’aide de Visual Studio .NET ou Web Developer Express, certaines pas conscience qu’il est également très utile pour déboguer le code côté client tels que JavaScript. Le même type de techniques utilisées pour déboguer des applications .NET peut également être appliqué à des applications compatibles avec AJAX et plus spécifiquement les applications ASP.NET AJAX.


## <a name="debugging-aspnet-ajax-applications"></a>Déboguer des Applications ASP.NET AJAX

Dan Wahlin

La possibilité de déboguer du code est que tous les développeurs doivent être placés dans leur arsenal quelle que soit la technologie qu’ils utilisent. Il va sans dire que le comprendre les différentes options de débogage qui sont disponibles permettre enregistrer énormément de temps dans un projet et voire quelques maux de tête. Alors que de nombreux développeurs sont habitués à déboguer des applications ASP.NET qui utilisent le code VB.NET ou c# à l’aide de Visual Studio .NET ou Web Developer Express, certaines pas conscience qu’il est également très utile pour déboguer le code côté client tels que JavaScript. Le même type de techniques utilisées pour déboguer des applications .NET peut également être appliqué à des applications compatibles avec AJAX et plus spécifiquement les applications ASP.NET AJAX.

Dans cet article, vous verrez comment Visual Studio 2008 et autres outils peuvent servir à déboguer des applications ASP.NET AJAX à localiser rapidement les bogues et autres problèmes. Cette discussion comporte des informations sur l’activation d’Internet Explorer 6 ou version ultérieure pour le débogage, à l’aide de Visual Studio 2008 et l’Explorateur de Script pour parcourir votre code ainsi qu’à l’aide d’autres outils gratuits tels que de l’Assistant de développement Web. Vous apprendrez également comment déboguer des applications ASP.NET AJAX Firefox à l’aide de qu'une extension nommée Firebug qui vous permet de parcourir le code JavaScript directement dans le navigateur sans d’autres outils. Enfin, vous allez découvrir à des classes dans la bibliothèque ASP.NET AJAX qui peuvent aider à différentes tâches de débogage telles que le suivi et les instructions d’assertion de code.

Avant d’essayer de déboguer les pages affichées dans Internet Explorer, il existe quelques étapes que vous devez effectuer pour l’activer pour le débogage. Jetons un œil à certaines exigences de configuration de base qui doivent être effectuées pour commencer.

## <a name="configuring-internet-explorer-for-debugging"></a>Configuration d’Internet Explorer pour le débogage

La plupart des personnes ne sont pas intéressé de voir les problèmes de JavaScript a rencontré sur un site Web affiché avec Internet Explorer. En fait, l’utilisateur moyen même utilité si elles vu un message d’erreur. Par conséquent, les options de débogage sont désactivées par défaut dans le navigateur. Toutefois, il est très simple activer le débogage et le placer à utiliser lors du développement de nouvelles applications AJAX.

Pour activer la fonctionnalité de débogage, accédez à outils/Options Internet dans le menu d’Internet Explorer et sélectionnez l’onglet Avancé. Dans la section navigation, assurez-vous que les éléments suivants sont désactivées :

- Désactiver le débogage des scripts (Internet Explorer)
- Désactiver le script de débogage (autre)

Mais pas obligatoire, si vous essayez de déboguer une application, vous souhaiterez probablement des erreurs JavaScript dans la page soit immédiatement visible et évident. Vous pouvez forcer toutes les erreurs pour afficher une boîte de message en activant la case à cocher « Afficher une notification de chaque erreur de script ». Lorsque cette option est idéale pour allumer quand vous développez une application, il peut rapidement devenir ennuyeux si vous êtes simplement vous parcourez attentivement autres sites Web depuis votre il est probable de rencontrer des erreurs JavaScript.

La figure 1 montre quelles Internet Explorer dialogue avancé doit ressembler après que qu’il a été configuré correctement pour le débogage.


[![Configuration d’Internet Explorer pour le débogage.](understanding-asp-net-ajax-debugging-capabilities/_static/image2.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image1.png)

**Figure 1**: configuration d’Internet Explorer pour le débogage.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image3.png))


Une fois le débogage a été activé, vous verrez un nouvel élément de menu s’affichent dans le menu affichage nommé débogueur de Script. Il a deux options disponibles, notamment les ouvrir et de saut à la prochaine instruction. Lors de l’ouverture est sélectionné, vous êtes invité à la page déboguer dans Visual Studio 2008 (Notez que Visual Web Developer Express peut également être utilisé pour le débogage). Si Visual Studio .NET est en cours d’exécution, vous pouvez choisir d’utiliser cette instance ou pour créer une nouvelle instance. Lors de l’arrêt au niveau de l’instruction suivante est sélectionnée, vous êtes invité à déboguer la page lors de l’exécution du code JavaScript. Si le code JavaScript qui s’exécute dans l’événement onLoad de la page, vous pouvez actualiser la page pour déclencher une session de débogage. Si le code JavaScript est exécuté après qu’un clic est effectué le débogueur exécute immédiatement après un clic sur le bouton.

> *>[!NOTE] si vous exécutez sur Windows Vista avec accès contrôle utilisateur (UAC) activé, et que Visual Studio 2008 configuré pour s’exécuter en tant qu’administrateur, Visual Studio ne pourront pas attacher au processus lorsque vous êtes invité à joindre. Pour contourner ce problème, démarrez Visual Studio tout d’abord et permet de déboguer cette instance.*


Bien que la section suivante va vous montrer comment déboguer une page ASP.NET AJAX directement à partir de Visual Studio 2008, à l’aide de l’option du débogueur de Script Internet Explorer est utile lorsqu’une page est déjà ouverte et que vous souhaitez inspecter plus en détail.

## <a name="debugging-with-visual-studio-2008"></a>Débogage avec Visual Studio 2008

Visual Studio 2008 fournit des fonctionnalités de débogage aux développeurs dans le monde s’appuient sur tous les jours à déboguer les applications .NET. Le débogueur intégré vous permet de vous permet de parcourir le code, la vue de données d’objet, espion pour des variables spécifiques, surveiller la pile des appels et bien plus encore. En plus du débogage de code VB.NET ou c#, le débogueur s’avère également utile pour le débogage des applications ASP.NET AJAX et vous permet de parcourir le code JavaScript, ligne par ligne. Les détails qui suivent le focus sur les techniques qui peuvent être utilisés pour déboguer les fichiers de script côté client, plutôt que de fournir un discours sur le processus de débogage des applications à l’aide de Visual Studio 2008.

Le processus de débogage d’une page dans Visual Studio 2008 peut être démarré de plusieurs façons différentes. Tout d’abord, vous pouvez utiliser l’option de débogueur de Script Internet Explorer mentionnée dans la section précédente. Cela fonctionne bien quand une page est déjà chargée dans le navigateur et vous souhaitez démarrer le débogage. Vous pouvez également, avec le bouton droit sur une page .aspx dans l’Explorateur de solutions et sélectionnez Définir comme Page de démarrage dans le menu. Si vous avez l’habitude de débogage des pages ASP.NET puis vous avez probablement fait auparavant. Une fois que vous appuyez sur F5, la page peut être déboguée. Toutefois, si vous pouvez généralement définir un point d’arrêt n’importe où vous voulez que dans le code VB.NET ou c#, qui n’est pas toujours le cas avec JavaScript comme vous le verrez ensuite.

*Embedded par rapport à des Scripts externes*

Le débogueur de Visual Studio 2008 traite JavaScript dans une page différente de celle des fichiers JavaScript externes. Avec les fichiers de script externe, vous pouvez ouvrir le fichier et définir un point d’arrêt sur n’importe quelle ligne que vous choisissez. Points d’arrêt peuvent être définies en cliquant dans la zone de la barre grise à gauche de la fenêtre d’éditeur de code. Lorsque JavaScript est directement incorporé dans une page à l’aide de la `<script>` balise, en définissant un point d’arrêt en cliquant dans la zone grise de barre d’état n’est pas une option. Tente de définir un point d’arrêt sur une ligne de script incorporé génère un avertissement indiquant que « Cela n’est pas un emplacement valide pour un point d’arrêt ».

Vous pouvez contourner ce problème en déplaçant le code dans un fichier .js externe et y faire référence à l’aide de l’attribut src de le &lt;script&gt; balise :


[!code-html[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample1.html)]

Que se passe-t-il si vous déplacez le code dans un fichier externe n’est pas une option ou nécessite plus de travail qu’elle ne vaut ? Pendant que vous ne pouvez définir un point d’arrêt à l’aide de l’éditeur, vous pouvez ajouter l’instruction debugger directement dans le code où vous souhaitez démarrer le débogage. Vous pouvez également utiliser la classe Sys.Debug disponible dans la bibliothèque ASP.NET AJAX pour forcer le démarrage du débogage. Vous en apprendrez davantage sur la classe Sys.Debug plus loin dans cet article.

Un exemple d’utilisation de la `debugger` (mot clé) est indiqué dans la liste 1. Cet exemple oblige le débogueur approprié avant d’effectuer un appel à une fonction de mise à jour.

**La liste 1. À l’aide du mot clé débogueur pour forcer l’arrêt du débogueur Visual Studio .NET.**


[!code-javascript[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample2.js)]

Une fois que l’instruction debugger est atteint, vous serez invité à déboguer la page à l’aide de Visual Studio .NET et pouvez commencer à exécuter le code. Alors que faire ce que vous pouvez rencontrer un problème avec l’accès aux fichiers de script ASP.NET AJAX library utilisés dans la page essai, commençons par examiner à l’aide de Visual Studio. Explorateur de scripts de NET.

## <a name="using-visual-studio-net-windows-to-debug"></a>Pour déboguer à l’aide de Visual Studio .NET Windows

Une fois qu’une session de débogage est démarrée et commencer à parcourir le code à l’aide de la touche F11 par défaut, vous pouvez rencontrer l’erreur boîte de dialogue voir la Figure 2, sauf si tous les fichiers de script utilisés dans la page sont ouverts et disponibles pour le débogage.


[![Boîte de dialogue erreur affiché lorsque aucun code source n’est disponible pour le débogage.](understanding-asp-net-ajax-debugging-capabilities/_static/image5.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image4.png)

**Figure 2**: boîte de dialogue erreur affiché lorsque aucun code source n’est disponible pour le débogage.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image6.png))


Cette boîte de dialogue s’affiche, car Visual Studio .NET n’est pas vraiment comment obtenir le code source de certains scripts référencés par la page. Cela peut être très frustrant dans un premier temps, il existe une solution simple. Une fois que vous avez démarré une session de débogage et atteint un point d’arrêt, accédez à la fenêtre Explorateur de scripts Windows déboguer dans le menu Visual Studio 2008, ou utilisez le raccourci clavier Ctrl + Alt + N.

> *>[!NOTE] Si vous ne voyez pas le menu de l’Explorateur de Script répertorié, accédez à outils* *personnaliser* *commandes du menu de Visual Studio .NET. Recherchez l’entrée de débogage dans la section Catégories, puis cliquez dessus pour afficher toutes les entrées de menu disponibles. Dans la liste de commandes, faites défiler jusqu'à l’Explorateur de Script et puis faites glisser sur le débogage* *menu Windows mentionné précédemment. Cela rend l’entrée de menu Explorateur de scripts disponibles chaque fois que vous exécutez Visual Studio .NET.*


L’Explorateur de Script peut être utilisé pour afficher tous les scripts utilisés dans une page et de les ouvrir dans l’éditeur de code. Une fois que l’Explorateur de Script est ouvert, double-cliquez sur la page .aspx en cours de débogage pour l’ouvrir dans la fenêtre d’éditeur de code. Effectuer l’action de même pour tous les autres scripts présentés dans l’Explorateur de Script. Une fois que tous les scripts sont ouverts dans la fenêtre de code que vous pouvez appuyer sur F11 (et utilisez les autres touches de raccourci de débogage) pour parcourir votre code. La figure 3 montre un exemple de l’Explorateur de Script. Il répertorie le fichier actuel en cours de débogage (Demo.aspx), ainsi que deux scripts personnalisés et les deux scripts dynamiquement injectés dans la page par ASP.NET AJAX ScriptManager.


[![L’Explorateur de Script fournit un accès facile aux scripts utilisés dans une page.](understanding-asp-net-ajax-debugging-capabilities/_static/image8.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image7.png)

**Figure 3**. L’Explorateur de Script fournit un accès facile aux scripts utilisés dans une page.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image9.png))


Plusieurs autres windows peuvent également être utilisés pour fournir des informations utiles à mesure que vous parcourez code dans une page. Par exemple, vous pouvez utiliser la fenêtre variables locales pour voir les valeurs des différentes variables utilisées dans la page, la fenêtre exécution pour évaluer des variables spécifiques ou des conditions et d’afficher la sortie. Vous pouvez également utiliser la fenêtre Sortie pour afficher les instructions de trace écrites à l’aide de la fonction Sys.Debug.trace (qui est abordée plus loin dans cet article) ou Debug.writeln, fonction de d’Internet Explorer.

Mesure que vous parcourez le code à l’aide du débogueur vous pouvez placez la souris sur les variables dans le code pour afficher la valeur qui leur sont affectés. Toutefois, le débogueur de script occasionnellement n’indique pas tout ce que vous placez la souris sur une variable JavaScript donnée. Pour afficher la valeur, mettez en surbrillance de l’instruction ou la variable que vous tentez d’afficher dans la fenêtre d’éditeur de code et puis déplacez la souris dessus. Bien que cette technique ne fonctionne pas dans tous les cas, nombre de fois où vous serez en mesure d’afficher la valeur sans avoir à rechercher dans une fenêtre de débogage différents tels que la fenêtre variables locales.

Un didacticiel vidéo décrivant certaines des fonctionnalités présentées ici peut être consultée dans [http://www.xmlforasp.net](http://www.xmlforasp.net).

## <a name="debugging-with-web-development-helper"></a>Débogage avec l’Assistant de développement Web

Bien que Visual Studio 2008 et Visual Web Developer Express 2008 sont très efficaces, outils de débogage, il existe des options supplémentaires qui peuvent également être utilisées sont plus léger. Un des outils plus récentes libérée est l’application d’assistance du développement Web. Microsoft Nikhil Kothari (parmi les architectes de ASP.NET AJAX clés chez Microsoft) a écrit cet excellent outil qui peut effectuer différentes tâches de débogage simple à l’affichage de messages de demande et de réponse HTTP. Assistant de développement Web peuvent être téléchargé sur [http://projects.nikhilk.net/Projects/WebDevHelper.aspx](http://projects.nikhilk.net/Projects/WebDevHelper.aspx).

Assistant de développement Web peut être utilisé directement à l’intérieur d’Internet Explorer qui la rend facile à utiliser. Il est démarré en sélectionnant Outils Assistant de développement Web dans le menu d’Internet Explorer. L’outil s’ouvre dans la partie inférieure du navigateur qui est pratique, car vous n’êtes pas obligé de laisser le navigateur pour effectuer plusieurs tâches telles que la journalisation de message de demande et de réponse HTTP. Figure 4 montre l’Assistant de développement Web aspect en action.


[![Assistant de développement Web](understanding-asp-net-ajax-debugging-capabilities/_static/image11.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image10.png)

**Figure 4**: Assistant de développement Web ([cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image12.png))


Assistant de développement Web n’est pas un outil que vous allez utiliser pour parcourir le code ligne par ligne en tant qu’avec Visual Studio 2008. Toutefois, il peut être utilisé pour afficher la sortie de trace, évaluer les variables dans un script ou facilement Explorer les données sont à l’intérieur d’un objet JSON. Il est également très utile pour l’affichage des données qui sont passées vers et depuis une page ASP.NET AJAX et un serveur.

Une fois l’Assistant de développement Web est ouverte dans Internet Explorer, le débogage de script doit être activé en sélectionnant Script activer le débogage de Script à partir du menu d’aide de développement Web, comme indiqué plus haut dans la Figure 4. Cela lui permet d’intercepter les erreurs qui se produisent lors de l’exécution d’une page. Il permet également un accès facile aux messages de trace sont affichés dans la page. Pour afficher les informations de trace ou exécuter des commandes de script pour tester différentes fonctions au sein d’une page, sélectionnez le Script afficher la Console Script à partir du menu de l’Assistant de développement Web. Cela fournit l’accès à une fenêtre de commande et d’une simple fenêtre exécution.

*Affichage des Messages de Trace et les données de l’objet JSON*

La fenêtre exécution peut être utilisée pour exécuter des commandes de script de charger ou enregistrer les scripts qui sont utilisés pour tester différentes fonctions dans une page. La fenêtre de commande affiche les messages de trace ou debug écrits par la page affichée. Listing 2 montre comment écrire un message de trace à l’aide de Debug.writeln, fonction de d’Internet Explorer.

**Liste 2. Écrit un message de trace côté client à l’aide de la classe Debug.**


[!code-javascript[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample3.js)]

Si la propriété LastName contient une valeur de Doe, Assistant de développement Web affiche le message « nom de personne : Doe » dans la fenêtre de commande de la console script (en supposant que le débogage a été activé). Assistant de développement Web ajoute également un objet de niveau supérieur debugService dans les pages qui peuvent être utilisés pour écrire des informations de trace ou d’afficher le contenu d’objets JSON. La liste 3 montre un exemple d’à l’aide de la fonction de trace de la classe debugService.

**La liste 3. À l’aide de classe de l’Assistant de développement Web debugService pour écrire un message de trace.**


[!code-javascript[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample4.js)]

Une fonctionnalité très utile de la classe debugService est qu’il fonctionnera même si le débogage n’est pas activé dans Internet Explorer, ce qui facilite le toujours accéder aux données de trace lors de l’Assistant de développement Web est en cours d’exécution. Lorsque l’outil n’est pas utilisé pour déboguer une page, les instructions de trace seront ignorées, car l’appel à window.debugService renvoie la valeur false.

La classe debugService permet également des données d’objet JSON être affichés à l’aide de la fenêtre d’inspecteur de l’assistance de développement Web. La liste 4 crée un objet JSON simple contenant des données de la personne. Une fois que l’objet est créé, un appel est fait à la debugService la classe inspecter la fonction pour permettre à l’objet JSON à inspecter visuellement.

**La liste 4. À l’aide de la fonction debugService.inspect pour afficher les données de l’objet JSON.**


[!code-javascript[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample5.js)]

Dans la page ou via la fenêtre exécution, l’appel de la fonction GetPerson() entraîne la boîte de dialogue Inspecteur de l’objet s’affiche comme indiqué dans la Figure 5. Propriétés de l’objet peuvent être modifiées dynamiquement en mettant en surbrillance, puis en cliquant sur le lien de mise à jour et modifier la valeur indiquée dans la zone de texte valeur. À l’aide de l’inspecteur de l’objet simplifie afficher des données de l’objet JSON et faire des essais avec différentes valeurs s’applique aux propriétés.

*Erreurs de débogage*

Outre l’octroi des données de trace et les objets JSON à afficher, d’aide au développement de Web peut également contribuer à déboguer les erreurs dans une page. Si une erreur s’est produite, vous devrez continuer à la ligne suivante de code ou de déboguer le script (voir Figure 6). La fenêtre de boîte de dialogue Erreur de Script indique la complète pile des appels, ainsi que les numéros de ligne afin de pouvoir identifier facilement où sont des problèmes au sein d’un script.


[![À l’aide de la fenêtre d’inspecteur de l’objet pour afficher un objet JSON.](understanding-asp-net-ajax-debugging-capabilities/_static/image14.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image13.png)

**Figure 5**: à l’aide de la fenêtre d’inspecteur de l’objet pour afficher un objet JSON.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image15.png))


En sélectionnant l’option de débogage vous permet d’exécuter des instructions de script directement dans la fenêtre exécution de l’Assistant de développement Web pour afficher la valeur des variables, écrire des objets JSON, et plus encore. Si l’action qui a déclenché l’erreur est effectuée à nouveau et Visual Studio 2008 est disponible sur l’ordinateur, vous devrez démarrer une session de débogage pour que vous pouvez parcourir le code ligne par ligne, comme indiqué dans la section précédente.


[![Boîte de dialogue Erreur d’aide au développement Script de Web](understanding-asp-net-ajax-debugging-capabilities/_static/image17.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image16.png)

**Figure 6**: boîte de dialogue Erreur de Script de l’assistance de développement Web ([cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image18.png))


*Inspection des Messages de demande et réponse*

Lors du débogage des pages ASP.NET AJAX, il est souvent utile de consulter les messages de demande et de réponse envoyés entre une page et le serveur. Affichage du contenu dans les messages vous permet de vous permet de voir si les données appropriées sont passées, ainsi que la taille des messages. Assistant de développement Web fournit une fonctionnalité de journalisation de message HTTP excellente qui facilite la tâche Afficher les données sous forme de texte brut ou dans un format plus lisible.

Pour afficher les messages de demande et de réponse d’ASP.NET AJAX, l’enregistreur d’événements HTTP doit être activé en sélectionnant Activer HTTP journalisation HTTP à partir du menu de l’Assistant de développement Web. Une fois activée, tous les messages envoyés à partir de la page actuelle sont consultables dans la visionneuse du journal HTTP qui est accessible en sélectionnant HTTP afficher les journaux HTTP.

Bien que l’affichage du texte brut envoyé dans chaque message de demande/réponse est certainement utile (et une option dans l’Assistant de développement Web), il est souvent plus facile à consulter les données du message dans un format graphique plus. Une fois la journalisation HTTP a été activée et les messages ont été enregistrés, les données de message peuvent être affichées en double-cliquant sur le message dans la visionneuse du journal HTTP. Cela vous permet d’afficher tous les en-têtes associés à un message, ainsi que le message réel contenu. Figure 7 montre un exemple d’un message de demande et le message de réponse affichées dans la fenêtre de la visionneuse du journal HTTP.


[![À l’aide de la visionneuse du journal HTTP pour afficher les données de message de demande et de réponse.](understanding-asp-net-ajax-debugging-capabilities/_static/image20.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image19.png)

**Figure 7**: à l’aide de la visionneuse du journal HTTP pour afficher les données de message de demande et de réponse.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image21.png))


La visionneuse du journal HTTP traite les objets JSON automatiquement et les affiche à l’aide d’une vue d’arborescence rend rapide et facile afficher les données de propriété de l’objet. Quand un UpdatePanel est utilisé dans une page ASP.NET AJAX, la visionneuse s’arrête chaque partie du message en éléments individuels comme indiqué dans la Figure 8. Il s’agit d’une fonctionnalité intéressante qui rend plus facile à voir et de comprendre ce qui est dans le message par rapport à l’affichage des données brutes de message.


[![Un message de réponse de UpdatePanel affiché à l’aide de la visionneuse du journal HTTP.](understanding-asp-net-ajax-debugging-capabilities/_static/image23.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image22.png)

**Figure 8**: message de réponse d’un UpdatePanel affichés à l’aide de la visionneuse du journal HTTP.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image24.png))


Il existe plusieurs autres outils qui peuvent être utilisés pour afficher les messages de demande et de réponse en plus de l’Assistant de développement Web. Une autre solution intéressante est Fiddler qui est disponible gratuitement sur [http://www.fiddlertool.com](http://www.fiddlertool.com). Bien que Fiddler n’est pas abordé ici, il est également un bon choix lorsque vous devez examiner soigneusement les données et les en-têtes de message.

## <a name="debugging-with-firefox-and-firebug"></a>Débogage avec Firefox et Firebug

Microsoft Internet Explorer est toujours le navigateur plus largement utilisé, d’autres navigateurs, telles que Firefox sont devenues très répandus et sont plus utilisés. Par conséquent, que vous souhaitez afficher et de déboguer vos pages ASP.NET AJAX dans Firefox, ainsi que Internet Explorer pour vous assurer que vos applications fonctionnent correctement. Bien que Firefox ne peut pas lier directement dans Visual Studio 2008 pour le débogage, il a une extension appelée Firebug qui peut être utilisé pour le débogage des pages. Firebug peut être téléchargé gratuitement en accédant à [http://www.getfirebug.com](http://www.getfirebug.com).

Firebug fournit un environnement de débogage complète qui peut être utilisé pour parcourir le code ligne par ligne, accéder à tous les scripts utilisés dans une page, afficher des structures de DOM, afficher des styles CSS et même suivre les événements qui se produisent dans une page. Une fois installé, Firebug est accessible en sélectionnant Firebug d’outils Firebug ouvert à partir du menu de Firefox. Comme l’Assistant de développement Web, Firebug est utilisé directement dans le navigateur, bien qu’il peut également être utilisé comme une application autonome.

Une fois Firebug est en cours d’exécution, les points d’arrêt peuvent être définis sur n’importe quelle ligne d’un fichier JavaScript si le script est incorporé dans une page ou non. Pour définir un point d’arrêt, d’abord charger la page appropriée que vous souhaitez déboguer dans Firefox. Une fois que la page est chargée, sélectionnez le script pour déboguer à partir de la liste déroulante de Scripts de Firebug. Tous les scripts utilisés par la page seront affichera. Un point d’arrêt est défini en cliquant sur dans de Firebug grise barre d’état de la ligne où le point d’arrêt doit être doit comme vous le feriez dans Visual Studio 2008.

Une fois qu’un point d’arrêt a été défini dans Firebug, vous pouvez effectuer l’action requise pour exécuter le script qui doit être déboguée comme un bouton ou l’actualisation du navigateur pour déclencher l’événement onLoad. L’exécution s’arrête automatiquement sur la ligne contenant le point d’arrêt. La figure 9 illustre un exemple d’un point d’arrêt a été déclenché dans Firebug.


[![La gestion des points d’arrêt dans Firebug.](understanding-asp-net-ajax-debugging-capabilities/_static/image26.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image25.png)

**Figure 9**: la gestion des points d’arrêt dans Firebug.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image27.png))


Une fois qu’un point d’arrêt est atteint pas à pas détaillé, pas à pas principal ou pas à pas sortant du code en utilisant les flèches. À mesure que vous parcourez le code, les variables de script sont affichés dans la partie droite du débogueur, ce qui vous permet de voir les valeurs et l’exploration des objets. Firebug inclut également une liste déroulante de pile des appels pour afficher les étapes de l’exécution du script qui ont conduit à la ligne actuelle en cours de débogage.

Firebug inclut également une fenêtre de console qui peut être utilisée pour tester les instructions de script différent, évaluer les variables et afficher la sortie de trace. Il est accessible en cliquant sur l’onglet de la Console en haut de la fenêtre Firebug. La page en cours de débogage permettre également être « analysée » pour afficher sa structure DOM et son contenu en cliquant sur l’onglet inspecter. Lorsque vous pointez la souris sur les différents éléments DOM indiqué dans la fenêtre Inspecteur la partie appropriée de la page sont mises en surbrillance ce qui permet de voir où l’élément est utilisé dans la page. Valeurs d’attribut associés à un élément donné peuvent être modifiées en « live » pour faire des essais avec différentes largeurs, styles, etc. s’applique à un élément. Il s’agit d’une fonctionnalité intéressante qui vous évite de devoir constamment basculer entre l’éditeur de code source et le navigateur Firefox pour afficher la simplicité modifications affectent une page.

La figure 10 illustre un exemple d’utilisation de l’inspecteur de DOM pour localiser une zone de texte nommée txtCountry dans la page. L’inspecteur Firebug peut également servir à afficher des styles CSS utilisés dans une page, ainsi que les événements qui se produisent telles que le suivi des mouvements de souris, les clics de bouton, ainsi que d’informations.


[![À l’aide de l’inspecteur de Firebug DOM.](understanding-asp-net-ajax-debugging-capabilities/_static/image29.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image28.png)

**La figure 10**: l’inspecteur de Firebug à l’aide de DOM.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image30.png))


Firebug offre un moyen de légers déboguer rapidement une page directement dans Firefox, ainsi que d’un excellent outil pour l’inspection des différents éléments dans la page.

## <a name="debugging-support-in-aspnet-ajax"></a>Prise en charge du débogage dans ASP.NET AJAX

La bibliothèque ASP.NET AJAX inclut de nombreuses classes différentes qui peuvent être utilisés pour simplifier le processus d’ajout de fonctionnalités AJAX dans une page Web. Vous pouvez utiliser ces classes pour localiser les éléments dans une page les manipuler, ajouter de nouveaux contrôles, appeler des Services Web et gérer des événements. La bibliothèque ASP.NET AJAX contient également des classes qui peuvent être utilisés pour améliorer le processus de débogage des pages. Dans cette section, vous allez découvrir la classe Sys.Debug et voir comment il peut être utilisé dans les applications.

*À l’aide de la classe Sys.Debug*

La classe Sys.Debug (classe JavaScript dans l’espace de noms Sys) peut être utilisée pour effectuer plusieurs fonctions, y compris l’écriture de sortie de trace, l’exécution des assertions de code et forcer le code d’échec afin qu’il peut être débogué. Largement utilisée dans les fichiers de débogage de la bibliothèque ASP.NET AJAX (installés par défaut dans C:\Program Files\Microsoft ASP.NET\ASP.NET 2.0 AJAX Extensions\v1.0.61025\MicrosoftAjaxLibrary\System.Web.Extensions\1.0.61025.0) pour effectuer des tests conditionnels) appelé assertions) qui vous assurer de paramètres sont passés correctement aux fonctions, que les objets contiennent les données attendues et d’écrire des instructions de trace.

La classe Sys.Debug expose plusieurs fonctions différentes qui peuvent être utilisées pour gérer le suivi, des assertions de code ou des défaillances comme indiqué dans le tableau 1.

**Tableau 1. Fonctions de classe Sys.Debug.**

| **Nom de la fonction** | **Description** |
| --- | --- |
| Assert (condition, message, displayCaller) | Déclare que le paramètre de condition est true. Si la condition testée a la valeur false, une boîte de message est utilisée pour afficher la valeur de paramètre de message. Si le paramètre displayCaller est true, la méthode affiche également des informations sur l’appelant. |
| clearTrace() | Efface la sortie des instructions à partir d’opérations de suivi. |
| Fail(message) | Le programme s’arrête l’exécution et entre dans le débogueur. Le paramètre de message peut être utilisé pour indiquer la raison de l’échec. |
| trace(message) | Écrit le paramètre de message dans la sortie de trace. |
| traceDump (objet, nom) | Génère des données d’un objet dans un format lisible. Le paramètre de nom peut être utilisé pour fournir une étiquette pour le vidage de la trace. Les sous-objets au sein de l’objet de vidage seront écrit par défaut. |

Le suivi côté client peut être utilisé dans la même façon que la fonctionnalité de suivi disponible dans ASP.NET. Il permet des différents messages facilement être visible sans interrompre le flux de l’application. La liste 5 montre un exemple d’utilisation de la fonction Sys.Debug.trace à écrire dans le journal des traces. Cette fonction accepte simplement le message doit être écrit en tant que paramètre.

**La liste 5. À l’aide de la fonction Sys.Debug.trace.**


[!code-javascript[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample6.js)]

Si vous exécutez le code figurant dans la liste 5, vous ne voyez aucune sortie de trace dans la page. La seule façon de les visualiser est d’utiliser une fenêtre de console disponible dans Visual Studio .NET, Assistant de développement Web ou Firebug. Si vous ne souhaitez pas afficher la sortie de trace dans la page puis vous devez ajouter une balise TextArea et lui donner un id de TraceConsole comme le montre l’illustration suivante :


[!code-html[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample7.html)]

Toutes les instructions Sys.Debug.trace dans la page seront écrite à le TraceConsole TextArea.

Dans le cas où vous souhaitez voir les données contenues dans un objet JSON, vous pouvez utiliser la Sys.Debug traceDump fonction des classes. Cette fonction accepte deux paramètres, y compris l’objet doit être enregistré dans la console de trace et un nom qui peut être utilisé pour identifier l’objet dans la sortie de trace. La liste 6 montre un exemple d’utilisation de la fonction traceDump.

**La liste 6. À l’aide de la fonction Sys.Debug.traceDump.**


[!code-javascript[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample8.js)]

Figure 11 illustre la sortie de l’appel de la fonction Sys.Debug.traceDump. Notez qu’en plus de l’écriture de données de l’objet de la personne, elle écrit également out adresse données de l’objet-sub.

Outre le suivi, la classe Sys.Debug peut également être utilisée pour effectuer des assertions de code. Les assertions sont utilisées pour tester que les conditions spécifiques sont respectées pendant l’exécution d’une application. La version de débogage des scripts ASP.NET AJAX library contient plusieurs instructions pour tester différentes conditions d’assert.

La liste 7 montre un exemple d’utilisation de la fonction Sys.Debug.assert pour tester une condition. Le code vérifie si l’objet d’adresse est null avant la mise à jour d’un objet de la personne ou non.


[![Sortie de la fonction Sys.Debug.traceDump.](understanding-asp-net-ajax-debugging-capabilities/_static/image32.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image31.png)

**Figure 11**: sortie de la fonction Sys.Debug.traceDump.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image33.png))


**La liste 7. À l’aide de la fonction debug.assert.**


[!code-javascript[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample9.js)]

Trois paramètres sont passés, y compris la condition à évaluer, le message à afficher si l’assertion retourne false et si Oui ou non les informations sur l’appelant doivent être affichées. Dans les cas où une assertion échoue, le message s’affichera, ainsi que des informations sur l’appelant si le troisième paramètre a la valeur true. Figure 12 montre un exemple de la boîte de dialogue d’échec qui s’affiche si l’assertion indiquée dans la liste 7 échoue.

La fonction finale pour couvrir est Sys.Debug.fail. Lorsque vous souhaitez forcer le code d’échec d’une ligne donnée dans un script, vous pouvez ajouter un appel Sys.Debug.fail plutôt que l’instruction debugger généralement utilisées dans les applications JavaScript. La fonction Sys.Debug.fail accepte un paramètre de chaîne unique qui représente la raison de l’échec, comme le montre l’illustration suivante :


[!code-css[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample10.css)]


[![Un message d’erreur Sys.Debug.assert.](understanding-asp-net-ajax-debugging-capabilities/_static/image35.png)](understanding-asp-net-ajax-debugging-capabilities/_static/image34.png)

**Figure 12**: Sys.Debug.assert d’un message d’échec.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-debugging-capabilities/_static/image36.png))


Lorsqu’une instruction Sys.Debug.fail est rencontrée pendant l’exécution d’un script, la valeur du paramètre du message s’affichera dans la console d’une application de débogage telles que Visual Studio 2008 et vous serez invité à déboguer l’application. Un cas où cela peut être très utile est lorsque vous ne pouvez pas définir un point d’arrêt avec Visual Studio 2008 sur un script inline mais souhaitez que le code d’arrêt sur une ligne particulière pour que vous puissiez vérifier la valeur des variables.

*Présentation ScriptMode propriété du contrôle ScriptManager*

La bibliothèque ASP.NET AJAX inclut debug et release de versions de script qui sont installées dans C:\Program Files\Microsoft ASP.NET\ASP.NET 2.0 AJAX Extensions\v1.0.61025\MicrosoftAjaxLibrary\System.Web.Extensions\1.0.61025.0 par défaut. Les scripts de débogage sont formatée, facile à lire et aient plusieurs appels Sys.Debug.assert éparpillés dans leur tandis que les scripts de mise en production ont des espaces blancs sont supprimés et utilisent la classe Sys.Debug avec parcimonie afin de réduire leur taille globale.

Le contrôle ScriptManager ajouté aux pages ASP.NET AJAX lit l’attribut de débogage de l’élément de compilation dans le fichier web.config pour déterminer les versions des scripts de bibliothèque à charger. Toutefois, vous pouvez contrôler si debug ou release des scripts sont chargés (scripts de bibliothèque ou vos propres scripts personnalisés) en modifiant la propriété ScriptMode. ScriptMode accepte une énumération ScriptMode dont les membres incluent automatiquement, Debug, Release et hériter.

ScriptMode par défaut est la valeur Auto, ce qui signifie que ScriptManager vérifie l’attribut de débogage dans le fichier web.config. Lorsque le débogage a la valeur false ScriptManager chargera la version finale de scripts de bibliothèque ASP.NET AJAX. Quand le débogage a la valeur true la version de débogage des scripts est chargée. Modification de la propriété ScriptMode pour libérer ou déboguer force le ScriptManager à charger les scripts appropriés, quelle que soit la valeur de l’attribut de débogage dans le fichier web.config. Liste 8 montre un exemple d’utilisation du contrôle ScriptManager à charger les scripts de débogage à partir de la bibliothèque ASP.NET AJAX.

**Liste 8. Le chargement de déboguer des scripts à l’aide de ScriptManager**.


[!code-aspx[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample11.aspx)]

Vous pouvez également charger des versions différentes (debug ou release) de vos propres scripts personnalisés à l’aide de la propriété de ScriptManager Scripts, ainsi que le composant ScriptReference comme indiqué dans la liste 9.

**Liste 9. Lors du chargement des scripts personnalisés à l’aide de ScriptManager.**


[!code-aspx[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample12.aspx)]

> [!NOTE]
> Si vous chargez des scripts personnalisés à l’aide du composant ScriptReference, vous devez informer ScriptManager lorsque le script a terminé le chargement en ajoutant le code suivant en bas du script :


[!code-csharp[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample13.cs)]

Le code figurant dans la liste 9 indique le ScriptManager pour rechercher une version debug du script personne afin qu’il recherche automatiquement Person.debug.js au lieu de Person.js. Si le fichier Person.debug.js ne trouve pas qu'une erreur est générée.

Dans le cas où vous souhaitez un débogage ou de version d’un script personnalisé à charger en fonction de la valeur de la propriété ScriptMode sur le contrôle ScriptManager, vous pouvez définir la ScriptReference propriété du contrôle ScriptMode hériter. Cela entraîne la version correcte du script personnalisé à charger en fonction de la propriété de ScriptManager ScriptMode comme indiqué dans la liste de 10. Étant donné que la propriété ScriptMode du contrôle ScriptManager est définie pour le débogage, le script Person.debug.js est chargé et utilisé dans la page.

**Liste des 10. Hériter le ScriptMode ScriptManager aux scripts personnalisés.**


[!code-aspx[Main](understanding-asp-net-ajax-debugging-capabilities/samples/sample14.aspx)]

À l’aide de la propriété ScriptMode correctement, vous pouvez plus facilement déboguer des applications et simplifier le processus global. Les scripts de version de la bibliothèque ASP.NET AJAX sont difficiles à parcourir et lire depuis la mise en forme du code a été supprimé alors que les scripts de débogage sont mis en forme spécifiquement pour le débogage.

## <a name="conclusion"></a>Conclusion

Technologie de Microsoft ASP.NET AJAX fournit une base solide pour la création d’applications compatibles AJAX peuvent améliorer l’expérience globale de l’utilisateur final. Toutefois, comme avec n’importe quelle technologie de programmation, bogues et autres problèmes d’application certainement surviendront. En savoir sur les différentes options de débogage disponibles permettre enregistrer beaucoup de temps et les résultats dans un produit plus stable.

Dans cet article vous connaissez différentes techniques pour le débogage des pages ASP.NET AJAX, y compris Internet Explorer avec Visual Studio 2008, Assistant de développement Web et Firebug. Ces outils peuvent simplifier le processus de débogage global, car vous pouvez accéder aux données de variable, parcourir le code ligne par ligne et afficher les instructions de trace. En plus des différents outils de débogage décrites, vous avez également vu utilisation de la classe de Sys.Debug de la bibliothèque ASP.NET AJAX dans une application et comment la classe de ScriptManager peut être utilisée pour charger debug ou release des versions de scripts.

## <a name="bio"></a>BIO

Dan Wahlin (Microsoft Most Valuable Professional pour ASP.NET et les Services Web XML) est développement du formateur et architecture consultant .NET à la formation de l’Interface ([www.interfacett.com)](http://www.interfacett.com). Dan a créé le code XML pour le site Web des développeurs ASP.NET ([www.XMLforASP.NET](http://www.XMLforASP.NET)), se trouve sur le Bureau de l’INETA et participe à plusieurs conférences. Dan coauteur ADN Windows Professionnel (Wrox), ASP.NET : conseils, didacticiels et Code (SAM), ASP.NET 1.1 Insider Solutions, ASP.NET 2.0 AJAX Professionnel (Wrox), ASP.NET 2.0 MVP Hacks et XML créé pour les développeurs ASP.NET (SAM). Lorsqu’il n’écrit pas de code, des articles ou des livres, Dan bénéficie de l’écriture et l’enregistrement de musique et lecture de golf et basket avec sa femme et les enfants.

Scott caté travaille avec les technologies Microsoft Web depuis 1997 et est le directeur de myKB.com ([www.myKB.com](http://www.myKB.com)) où il est spécialisé dans l’écriture d’ASP.NET en fonction des applications axées sur les solutions logicielles de la Base de connaissances. Scott peut être contacté par courrier électronique en [ scott.cate@myKB.com ](mailto:scott.cate@myKB.com) ou son blog à [ScottCate.com](http://ScottCate.com)

>[!div class="step-by-step"]
[Précédent](understanding-asp-net-ajax-web-services.md)
