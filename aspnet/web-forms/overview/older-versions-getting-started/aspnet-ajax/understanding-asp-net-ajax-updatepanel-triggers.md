---
uid: web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-asp-net-ajax-updatepanel-triggers
title: "ASP.NET AJAX UpdatePanel déclencheurs | Documents Microsoft"
author: scottcate
description: "Lorsque vous travaillez dans l’éditeur de balisage dans Visual Studio, vous pouvez remarquer (à partir d’IntelliSense) qu’il existe deux éléments enfants d’un contrôle UpdatePanel. Une des Much..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/12/2008
ms.topic: article
ms.assetid: faab8503-2984-48a9-8a40-7728461abc50
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-asp-net-ajax-updatepanel-triggers
msc.type: authoredcontent
ms.openlocfilehash: 1338ef0763d9bfab451bc30cafa39f715200153d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="understanding-aspnet-ajax-updatepanel-triggers"></a>Déclencheurs de UpdatePanel ASP.NET AJAX
====================
par [ble pour indiquer Scott](https://github.com/scottcate)

[Télécharger le PDF](http://download.microsoft.com/download/C/1/9/C19A3451-1D14-477C-B703-54EF22E197EE/AJAX_tutorial02_Triggers_cs.pdf)

> Lorsque vous travaillez dans l’éditeur de balisage dans Visual Studio, vous pouvez remarquer (à partir d’IntelliSense) qu’il existe deux éléments enfants d’un contrôle UpdatePanel. Un est l’élément de déclencheurs, qui spécifie les contrôles sur la page (ou le contrôle utilisateur, si vous en utilisez un) qui déclenchera un rendu partiel du contrôle UpdatePanel dans lequel se trouve l’élément.


## <a name="introduction"></a>Introduction

Technologie ASP.NET de Microsoft offre un modèle de programmation orientée objet et pilotées par événements et il regroupe les avantages du code compilé. Toutefois, son modèle de traitement côté serveur a plusieurs inconvénients inhérents à la technologie, qui peuvent être traités par les nouvelles fonctionnalités incluses dans les Extensions AJAX Microsoft ASP.NET 3.5. Ces extensions permettent de nombreuses nouvelles fonctionnalités clientes, y compris le rendu partiel de pages sans nécessiter une actualisation de la page entière, la possibilité d’accéder aux Services Web via le script client (y compris les API de profilage ASP.NET) et une API côté client étendue conçu pour mettre en miroir la plupart des modèles de contrôle dans le jeu de contrôle côté serveur ASP.NET.

Ce livre blanc examine les fonctionnalités XML déclencheurs d’ASP.NET AJAX `UpdatePanel` composant. Les déclencheurs XML donner un contrôle granulaire sur les composants qui peuvent provoquer le rendu partiel pour les contrôles de UpdatePanel spécifiques.

Ce livre blanc est basé sur la version bêta 2 de .NET Framework 3.5 et Visual Studio 2008. Les Extensions ASP.NET AJAX, précédemment d’un assembly de module complémentaire ciblé sur ASP.NET 2.0, sont désormais intégrées dans la bibliothèque de classes de Base de .NET Framework. Ce livre blanc suppose également que vous allez travailler avec Visual Studio 2008, pas Visual Web Developer Express et fournissez des procédures pas à pas, en fonction de l’interface utilisateur de Visual Studio (bien que les listes de codes sera entièrement compatibles indépendamment du environnement de développement).

## <a name="triggers"></a>*Déclencheurs*

Pour un UpdatePanel donné, par défaut, les déclencheurs incluent automatiquement tous les contrôles enfants qui font appel à une publication (postback), y compris (par exemple) des contrôles de zone de texte qui ont leur `AutoPostBack` propriété **true**. Toutefois, il est possible que les déclencheurs peuvent être également inclus de façon déclarative à l’aide de balisage ; Cette opération est effectuée dans la `<triggers>` section de la déclaration de contrôle UpdatePanel. Bien que les déclencheurs sont accessibles la `Triggers` propriété de collection, il est recommandé que vous inscrivez des déclencheurs de rendu partiel au moment de l’exécution (par exemple, si un contrôle n’est pas disponible au moment du design) à l’aide de la `RegisterAsyncPostBackControl(Control)` méthode de la ScriptManager de l’objet de votre page, dans le `Page_Load` événement. N’oubliez pas que les Pages sont sans état, par conséquent, vous devez réinscrire ces contrôles chaque fois qu’ils sont créés.

Inclusion de déclencheur enfant automatique peut également être désactivée (de sorte que les contrôles enfants qui créent des publications (postback) ne déclenchent pas automatiquement est rendu partiel) en définissant le `ChildrenAsTriggers` propriété **false**. Cela permet la plus grande flexibilité lors de l’affectation des contrôles spécifiques peut-être appeler un rendu de page et est recommandée, afin qu’un développeur sera opter pour répondre à un événement, plutôt que de gestion des événements qui peuvent survenir.

Notez que lorsque les contrôles UpdatePanel sont imbriqués, lorsque le UpdateMode a la valeur **conditionnelle**, si les enfants de UpdatePanel est déclenchée, mais le parent n’est pas, puis seulement l’enfant UpdatePanel doit être actualisé. Toutefois, si le parent UpdatePanel est actualisé, puis les enfants de UpdatePanel également seront actualisés.

## <a name="the-lttriggersgt-element"></a>*Le &lt;déclencheurs&gt; élément*

Lorsque vous travaillez dans l’éditeur de balisage dans Visual Studio, vous remarquerez peut-être (à partir d’IntelliSense) qu’il existe deux éléments enfants d’un `UpdatePanel` contrôle. L’élément plus fréquents est le `<ContentTemplate>` élément qui encapsule les essentiellement le contenu qui sera retenu par le panneau de mise à jour (le contenu pour lequel nous allons l’activation de rendu partiel). L’autre élément est la `<Triggers>` élément, qui spécifie les contrôles sur la page (ou le contrôle utilisateur, si vous en utilisez un) qui déclenchera un rendu partiel du contrôle UpdatePanel dans lequel le &lt;déclencheurs&gt; élément réside.

Le `<Triggers>` élément peut contenir n’importe quel nombre de deux nœuds enfants : `<asp:AsyncPostBackTrigger>` et `<asp:PostBackTrigger>`. Les deux acceptent deux attributs, `ControlID` et `EventName`et vous pouvez spécifier n’importe quel contrôle au sein de l’unité actuelle d’encapsulation (par exemple, si votre contrôle UpdatePanel se trouve dans un contrôle utilisateur Web, vous ne devez pas essayer fait référence à un contrôle sur la Page sur laquelle réside le contrôle utilisateur).

Le `<asp:AsyncPostBackTrigger>` élément est particulièrement utile dans la mesure où il peut cibler n’importe quel événement à partir d’un contrôle qui existe en tant qu’enfant de *tout* contrôle UpdatePanel dans l’unité d’encapsulation, pas seulement le UpdatePanel sous lequel ce déclencheur est un enfant . Par conséquent, n’importe quel contrôle peut être effectué pour déclencher une mise à jour de page partielle.

De même, le `<asp:PostBackTrigger>` élément peut être utilisé à déclencheur rendu d’une page partielle, mais qui requiert un accès complet au serveur. Cet élément déclencheur peut également être utilisé pour forcer un rendu de page entière quand un contrôle déclencherait sinon normalement un rendu de page partielle (par exemple, lorsqu’un `Button` contrôle existe dans le `<ContentTemplate>` élément d’un contrôle UpdatePanel). Là encore, l’élément PostBackTrigger permettre spécifier n’importe quel contrôle est un enfant de n’importe quel contrôle UpdatePanel dans l’unité actuelle d’encapsulation.

## <a name="lttriggersgt-element-reference"></a>*&lt;Déclencheurs&gt; référence des éléments*

*Balisage Descendants :*

| **Balise** | **Description** |
| --- | --- |
| &lt;ASP : AsyncPostBackTrigger&gt; | Spécifie un contrôle et les événements susceptibles d’entraîner une mise à jour de page partielle pour le contrôle UpdatePanel qui contient cette référence de déclencheur. |
| &lt;ASP : PostBackTrigger&gt; | Spécifie un contrôle et l’événement qui provoque une mise à jour pleine page (une actualisation de la page entière). Cette balise peut être utilisée pour forcer une actualisation complète quand un contrôle déclenche sinon rendu partiel. |

## <a name="walkthrough-cross-updatepanel-triggers"></a>*Procédure pas à pas : Cross-UpdatePanel déclencheurs*

1. Créer une nouvelle page ASP.NET avec un objet de ScriptManager défini pour activer le rendu partiel. Ajouter deux UpdatePanel sur cette page - dans la première, inclure une étiquette (Label1) et deux contrôles Button (Button1 et Button2). Button1 doit indiquer Cliquez pour mettre à jour à la fois et Button2 doit indiquer Cliquez sur Mettre à jour cette, ou quelque chose dans ces lignes. Dans la deuxième UpdatePanel, incluent uniquement un contrôle Label (Label2), mais sa propriété de couleur de premier plan sur une valeur différente de la valeur par défaut pour le différencier.
2. Définissez la propriété UpdateMode les deux balises UpdatePanel pour **conditionnelle**.

**La liste 1 : La balise de default.aspx :** 

[!code-aspx[Main](understanding-asp-net-ajax-updatepanel-triggers/samples/sample1.aspx)]

1. Dans le Gestionnaire d’événements Click pour Button1, la valeur Label1.Text et Label2.Text quelque chose dépendant du temps (par exemple, DateTime.Now.ToLongTimeString()). Le Gestionnaire d’événements Click pour Button2, définissez Label1.Text uniquement à la valeur de temps.

**Listing 2 : Code-behind (supprimé) dans default.aspx.cs :** 

[!code-csharp[Main](understanding-asp-net-ajax-updatepanel-triggers/samples/sample2.cs)]

1. Appuyez sur F5 pour générer et exécuter le projet. Notez que, lorsque vous cliquez sur les panneaux de mise à jour à la fois, les deux étiquettes modifier du texte ; Toutefois, lorsque vous cliquez sur Panneau de configuration. cette mise à jour, Label1 uniquement des mises à jour.


[![](understanding-asp-net-ajax-updatepanel-triggers/_static/image2.png)](understanding-asp-net-ajax-updatepanel-triggers/_static/image1.png)

([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-updatepanel-triggers/_static/image3.png))


## <a name="under-the-hood"></a>*Sous le capot*

En utilisant l’exemple que nous venez de créer, nous pouvons effectuer de voir ce que fait ASP.NET AJAX et le fonctionnement de nos déclencheurs cross-Panneau de UpdatePanel. Pour ce faire, nous utiliserons la source HTML de la page générée, ainsi que l’extension de Mozilla Firefox appelé FireBug -, nous pouvons facilement examiner les publications (postback) AJAX. Nous allons également utiliser l’outil .NET Reflector par Lutz Roeder. Ces deux outils sont disponibles gratuitement en ligne et vous pouvez trouver avec une recherche sur internet.

Un examen du code source page montre presque rien ne configuration particulière ; les contrôles UpdatePanel sont rendus sous forme `<div>` conteneurs et nous pouvons voir la ressource de script inclut fournies par le `<asp:ScriptManager>`. Il existe également certains nouveaux appels spécifique à AJAX à PageRequestManager qui sont internes à la bibliothèque de script client AJAX. Enfin, nous voyons les deux conteneurs de UpdatePanel - un avec rendu `<input>` boutons avec les deux `<asp:Label>` contrôles rendus sous forme de `<span>` conteneurs. (Si vous examinez l’arborescence DOM dans FireBug, vous remarquerez que les étiquettes sont estompés pour indiquer qu’ils ne sont pas produit le contenu visible).

Cliquez sur le bouton de mise à jour ce panneau de configuration et notez Qu'updatepanel supérieur sera mise à jour avec l’heure actuelle. Dans FireBug, choisissez l’onglet de la Console afin que vous pouvez examiner la demande. Examinez les paramètres de la demande POST tout d’abord :


[![](understanding-asp-net-ajax-updatepanel-triggers/_static/image5.png)](understanding-asp-net-ajax-updatepanel-triggers/_static/image4.png)

([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-updatepanel-triggers/_static/image6.png))


Notez que le contrôle UpdatePanel a indiqué au code AJAX côté serveur précisément l’arborescence du contrôle a été déclenché via le paramètre ScriptManager1 : `Button1` de la `UpdatePanel1` contrôle. Maintenant, cliquez sur le bouton de mise à jour des panneaux à la fois. Puis, en examinant la réponse, consultez une série délimitée par des canaux de variables définies dans une chaîne ; plus précisément, nous constatons UpdatePanel supérieur, `UpdatePanel1`, a l’intégralité de son code HTML envoyé au navigateur. La bibliothèque de script client AJAX substitue d’origine du contenu HTML de UpdatePanel avec le nouveau contenu via le `.innerHTML` propriété, et par conséquent, le serveur envoie le contenu est modifié à partir du serveur au format HTML.

Maintenant, cliquez sur le bouton de mise à jour des panneaux à la fois et examiner les résultats à partir du serveur. Les résultats sont très similaires : les deux UpdatePanel reçoit la nouvelle page HTML à partir du serveur. Comme avec le rappel précédent, état de la page supplémentaire est envoyé.

Comme nous pouvons le voir, car aucun code spécial n’est utilisé pour effectuer une publication (postback) AJAX, la bibliothèque de script client AJAX est en mesure d’intercepter les publications de formulaire sans code supplémentaire. Les contrôles serveur utilisent automatiquement JavaScript afin qu’ils n’envoient pas automatiquement le formulaire - ASP.NET injecte automatiquement le code de validation de formulaire et l’état déjà, obtenue principalement par l’inclusion de ressources de script automatique, la classe PostBackOptions et la classe ClientScriptManager.

Prenons par exemple un contrôle de case à cocher ; Examinez le code machine de classe dans le .NET Reflector. Pour ce faire, assurez-vous que votre assembly System.Web est ouvert et accédez à la `System.Web.UI.WebControls.CheckBox` (classe), ouvrir le `RenderInputTag` (méthode). Recherchez une condition qui vérifie le `AutoPostBack` propriété :


[![](understanding-asp-net-ajax-updatepanel-triggers/_static/image8.png)](understanding-asp-net-ajax-updatepanel-triggers/_static/image7.png)

([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-updatepanel-triggers/_static/image9.png))


Lorsque la publication (postback) automatique est activée sur un `CheckBox` contrôler (via la propriété AutoPostBack est true), résultant `<input>` balise est par conséquent rendue avec un événement ASP.NET gère le script dans sa `onclick` attribut. L’interception de l’envoi du formulaire, puis, permet à ASP.NET AJAX à injectés dans la page nonintrusively, contribue à éviter les éventuelles modifications avec rupture qui peuvent se produire en utilisant un remplacement de chaîne éventuellement imprécise. En outre, cela permet de *tout* contrôle ASP.NET personnalisé à la puissance d’ASP.NET AJAX sans code supplémentaire pour prendre en charge son utilisation dans un conteneur de UpdatePanel.

Le `<triggers>` fonctionnalité correspond aux valeurs initialisés dans l’appel de PageRequestManager \_updateControls (Notez que la bibliothèque de script client ASP.NET AJAX utilise la convention que méthodes, les événements et les noms de champs qui commencent avec un trait de soulignement sont marqués comme internes et ne sont pas conçu pour une utilisation en dehors de la bibliothèque elle-même). Avec elle, nous pouvons observer les contrôles qui sont destinés à provoquer des publications (postback) AJAX.

Par exemple, vous allez ajouter deux contrôles supplémentaires à la page, en conservant un contrôle en dehors d’UpdatePanel entièrement et en conservant un dans un UpdatePanel. Nous ajouter un contrôle de case à cocher dans UpdatePanel supérieur et supprimer une liste déroulante avec un nombre de couleurs sont définies dans la liste. Voici le balisage :

**À la liste de 3 : Balisage de nouveau**

[!code-aspx[Main](understanding-asp-net-ajax-updatepanel-triggers/samples/sample3.aspx)]

Et Voici le nouveau code-behind :

**La liste 4 : code-behind**

[!code-csharp[Main](understanding-asp-net-ajax-updatepanel-triggers/samples/sample4.cs)]

L’idée derrière cette page est que la liste déroulante sélectionne une des trois couleurs pour afficher le deuxième contrôle label, que la case à cocher détermine s’il est en gras, et indique si les étiquettes d’affichent la date, ainsi que le temps. La case à cocher ne devrait pas provoquer une mise à jour AJAX, mais la liste déroulante doit, même si elle n’est pas hébergée dans un UpdatePanel.


[![](understanding-asp-net-ajax-updatepanel-triggers/_static/image11.png)](understanding-asp-net-ajax-updatepanel-triggers/_static/image10.png)

([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-updatepanel-triggers/_static/image12.png))


Comme les informations s’affichent dans la capture d’écran ci-dessus, un clic sur le bouton la plus récente a été la mise à jour de ce panneau de configuration, qui le temps supérieur, indépendamment de la durée de la partie inférieure de mise à jour avec le bouton droit. La date a été désactivée également entre les clics, comme la date est visible dans l’étiquette du bas. Enfin, d’intérêt est la couleur de l’étiquette bas : il a été mis à jour plus récemment que le texte de l’étiquette, qui montre que l’état du contrôle est important, et les utilisateurs s’attendent à être conservées lors des publications (postback) AJAX. *Toutefois*, l’heure n’a pas été mis à jour. Le temps a été complétée automatiquement via la persistance de la \_ \_champ d’état d’affichage de la page est interprétée par le runtime ASP.NET lorsque le contrôle a été restitué sur le serveur. Le code de serveur ASP.NET AJAX ne reconnaît pas dans lequel les contrôles de méthodes modifiez état ; Il remplit simplement à partir de l’état d’affichage et exécute ensuite les événements qui sont appropriés.

Il convient de préciser, toutefois, avais je l’heure dans la Page initialisé\_événement de chargement, l’heure est ont été correctement incrémentée. Par conséquent, les développeurs doivent Méfiez-vous que le code approprié est en cours d’exécution pendant les gestionnaires d’événements appropriée et éviter l’utilisation de la Page\_lorsqu’un gestionnaire d’événements de contrôle peut s’avérer judicieux de charge.

## <a name="summary"></a>Résumé

Le contrôle ASP.NET AJAX Extensions UpdatePanel est flexible et peut utiliser plusieurs méthodes pour identifier les événements de contrôle doivent entraîner sa mise à jour. Il prend en charge la mise à jour automatiquement par ses contrôles enfants, mais peut également répondre aux événements de contrôle sur la page.

Pour réduire le risque potentiel de la charge de traitement serveur, il est recommandé que le `ChildrenAsTriggers` propriété de UpdatePanel être définie sur `false`, et que les événements être choisi en plutôt qu’inclus par défaut. Cela empêche également tous les événements inutiles de provoquer des effets potentiellement indésirables, notamment les validations et les modifications apportées aux champs d’entrée. Ces types de bogues peuvent être difficiles à isoler, car la page mises à jour de façon transparente à l’utilisateur, et la cause peut donc pas immédiatement évidente.

En examinant le fonctionnement interne de la forme d’ASP.NET AJAX valider le modèle de l’interception, nous avons pu déterminer qu’il utilise l’infrastructure déjà fournie par ASP.NET. Ce faisant, il préserve la compatibilité maximale avec les contrôles conçus à l’aide de la même infrastructure et impose au minimum sur n’importe quel code JavaScript écrit pour la page.

## <a name="bio"></a>BIO

Rob Paveza est un développeur d’applications .NET senior à Terralever ([www.terralever.com](http://www.terralever.com)), une société de marketing début interactive dans Tempe, en Arizona. Il peut être atteint à [ robpaveza@gmail.com ](mailto:robpaveza@gmail.com), et son blog se trouve dans [http://geekswithblogs.net/robp/](http://geekswithblogs.net/robp/).

Scott caté travaille avec les technologies Microsoft Web depuis 1997 et est le directeur de myKB.com ([www.myKB.com](http://www.myKB.com)) où il est spécialisé dans l’écriture d’ASP.NET en fonction des applications axées sur les solutions logicielles de la Base de connaissances. Scott peut être contacté par courrier électronique en [ scott.cate@myKB.com ](mailto:scott.cate@myKB.com) ou son blog à [ScottCate.com](http://ScottCate.com)

>[!div class="step-by-step"]
[Précédent](understanding-partial-page-updates-with-asp-net-ajax.md)
[Suivant](understanding-asp-net-ajax-authentication-and-profile-application-services.md)
