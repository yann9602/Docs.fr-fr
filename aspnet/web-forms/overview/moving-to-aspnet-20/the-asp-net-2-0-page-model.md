---
uid: web-forms/overview/moving-to-aspnet-20/the-asp-net-2-0-page-model
title: "Le modèle de Page 2.0 ASP.NET | Documents Microsoft"
author: microsoft
description: "Dans ASP.NET 1.x, les développeurs devaient choisir entre un modèle de code inline et un modèle de code-behind du code. Code-behind peut être implémenté à l’aide soit du attr Src..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: af4575a3-0ae3-4638-ba4d-218fad7a1642
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/the-asp-net-2-0-page-model
msc.type: authoredcontent
ms.openlocfilehash: e008f197cf08bec81c560018f2d42306598f9e6d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="the-aspnet-20-page-model"></a>Le modèle de Page 2.0 d’ASP.NET
====================
par [Microsoft](https://github.com/microsoft)

> Dans ASP.NET 1.x, les développeurs devaient choisir entre un modèle de code inline et un modèle de code-behind du code. Code-behind peut être implémenté à l’aide de l’attribut Src ou l’attribut code-behind de la @Page la directive. Dans ASP.NET 2.0, les développeurs ont encore un choix entre le code inline et code-behind, mais il y a eu des améliorations importantes pour le modèle code-behind.


Dans ASP.NET 1.x, les développeurs devaient choisir entre un modèle de code inline et un modèle de code-behind du code. Code-behind peut être implémenté à l’aide de l’attribut Src ou l’attribut code-behind de la @Page la directive. Dans ASP.NET 2.0, les développeurs ont encore un choix entre le code inline et code-behind, mais il y a eu des améliorations importantes pour le modèle code-behind.

## <a name="improvements-in-the-code-behind-model"></a>Améliorations dans le modèle Code-Behind

Afin de comprendre parfaitement les modifications dans le modèle code-behind dans ASP.NET 2.0, il est préférable de vérifier rapidement le modèle tel qu’il existait dans ASP.NET 1.x.

## <a name="the-code-behind-model-in-aspnet-1x"></a>Le modèle de Code-Behind dans ASP.NET 1.x

Dans ASP.NET 1.x, le modèle code-behind consistait en un fichier ASPX (le formulaire Web) et un fichier code-behind contenant le code de programmation. Les deux fichiers ont été connectés à l’aide de la @Page directive dans le fichier ASPX. Chaque contrôle sur la page ASPX avait une déclaration correspondante dans le fichier code-behind comme une variable d’instance. Le fichier code-behind contenait du code pour la liaison de l’événement également et généré le code nécessaire pour le concepteur Visual Studio. Ce modèle relativement a bien fonctionné, mais étant donné que chaque élément ASP.NET dans la page ASPX requis code correspondant dans le fichier code-behind, il a été aucune véritable séparation du code et du contenu. Par exemple, si un concepteur a ajouté un nouveau contrôle serveur dans un fichier ASPX en dehors de l’IDE de Visual Studio, l’application compromettrait en raison de l’absence d’une déclaration de ce contrôle dans le fichier code-behind.

## <a name="the-code-behind-model-in-aspnet-20"></a>Le modèle de Code-Behind dans ASP.NET 2.0

ASP.NET 2.0 améliore considérablement ce modèle. Dans ASP.NET 2.0, le code-behind est implémentée à l’aide de la nouvelle *classes partielles* fournis dans ASP.NET 2.0. La classe code-behind dans ASP.NET 2.0 est défini comme une classe partielle, ce qui signifie qu’il contient uniquement une partie de la définition de classe. La partie restante de la définition de classe est générée dynamiquement par ASP.NET 2.0 à l’aide de la page ASPX lors de l’exécution, ou lorsque le site Web précompilé. Le lien entre le fichier code-behind et la page ASPX est toujours établi à l’aide de la directive @ Page. Toutefois, au lieu d’un attribut Src ou de code-behind, ASP.NET 2.0 utilise désormais l’attribut CodeFile. L’attribut Inherits est également utilisé pour spécifier le nom de classe pour la page.

Une directive @ Page classique peut ressembler à ceci :

[!code-aspx[Main](the-asp-net-2-0-page-model/samples/sample1.aspx)]

Une définition de classe dans un fichier de code-behind d’ASP.NET 2.0 peut ressembler à ceci :

[!code-csharp[Main](the-asp-net-2-0-page-model/samples/sample2.cs)]

> [!NOTE]
> C# et Visual Basic sont des langages managés uniquement qui prennent en charge des classes partielles. Par conséquent, les développeurs qui utilisent J# ne sera pas en mesure d’utiliser le modèle code-behind dans ASP.NET 2.0.


Le nouveau modèle améliore le modèle code-behind, car les développeurs peuvent désormais les fichiers de code qui contiennent uniquement le code qu’ils ont créées. Il prévoit également une véritable séparation du code et du contenu, car il n’y aucune déclaration de variable d’instance dans le fichier code-behind.

> [!NOTE]
> La classe partielle pour la page ASPX étant où la liaison de l’événement a lieu, les développeurs Visual Basic peuvent réaliser une légère augmentation des performances à l’aide du mot clé Handles dans le code-behind pour lier les événements. C# ne possède aucun mot clé équivalent.


## <a name="new--page-directive-attributes"></a>Nouveaux attributs de la Directive @ Page

ASP.NET 2.0 ajoute de nombreux nouveaux attributs à la directive @ Page. Les attributs suivants sont Nouveautés dans ASP.NET 2.0.

## <a name="async"></a>Async

L’attribut Async vous permet de configurer la page doit être exécuté de façon asynchrone. Mesure couvrent des pages asynchrones plus loin dans ce module.

## <a name="asynctimeout"></a>AsyncTimeout

Spécifié le délai d’expiration pour les pages asynchrones. La valeur par défaut est de 45 secondes.

## <a name="codefile"></a>CodeFile

L’attribut CodeFile est le remplacement de l’attribut code-behind dans Visual Studio 2002/2003.

### <a name="codefilebaseclass"></a>CodeFileBaseClass

L’attribut CodeFileBaseClass est utilisé dans les cas où vous souhaitez plusieurs pages pour dériver à partir d’une seule classe de base. En raison de l’implémentation des classes partielles dans ASP.NET, sans cet attribut, une classe de base qui utilise des champs communs partagés pour référencer les contrôles déclarés dans une page ASPX ne fonctionnera pas correctement, car ASP. Moteur de compilation filets crée automatiquement les nouveaux membres basées sur les contrôles dans la page. Par conséquent, si vous souhaitez une classe de base commune pour les deux ou plusieurs pages dans ASP.NET, vous devez définir spécifier votre classe de base dans l’attribut CodeFileBaseClass et puis dérivez chaque classe de pages à partir de cette classe de base. L’attribut CodeFile est également requis lorsque cet attribut est utilisé.

## <a name="compilationmode"></a>compilationMode

Cet attribut vous permet de définir la propriété CompilationMode de la page ASPX. La propriété CompilationMode est une énumération qui contient les valeurs **toujours**, **automatique**, et **jamais**. La valeur par défaut est **toujours**. Le **automatique** paramètre empêche ASP.NET dynamiquement la compilation de la page si possible. À l’exception des pages à partir de la compilation dynamique améliore les performances. Toutefois, si une page qui est exclue contient ce code doit être compilé, une erreur est levée lors de la page.

## <a name="enableeventvalidation"></a>EnableEventValidation

Cet attribut spécifie si les événements de publication et de rappel sont validés. Lorsque cette option est activée, les arguments à la publication (postback) ou les événements de rappel sont vérifiés pour s’assurer qu’ils proviennent du contrôle serveur rendus à l’origine.

## <a name="enabletheming"></a>EnableTheming

Cet attribut spécifie si les thèmes ASP.NET sont utilisées sur une page. La valeur par défaut est **false**. Thèmes ASP.NET sont traités dans [Module 10](profiles-themes-and-web-parts.md).

## <a name="linepragmas"></a>LinePragmas

Cet attribut spécifie si les pragmas de ligne doivent être ajoutées lors de la compilation. Pragmas de ligne sont des options utilisées par les débogueurs pour marquer des sections spécifiques du code.

## <a name="maintainscrollpositiononpostback"></a>MaintainScrollPositionOnPostback

Cet attribut spécifie si JavaScript est injecté dans la page afin de maintenir la position de défilement entre publications (postback). Cet attribut est **false** par défaut.

Lorsque cet attribut est **true**, ASP.NET ajoutera un &lt;script&gt; bloquer en cas de publication (postback) qui ressemble à ceci :

[!code-html[Main](the-asp-net-2-0-page-model/samples/sample3.html)]

Notez que le src pour ce bloc de script est WebResource.axd. Cette ressource n’est pas un chemin d’accès physique. Lorsque ce script est demandé, ASP.NET crée dynamiquement le script.

### <a name="masterpagefile"></a>MasterPageFile

Cet attribut spécifie le fichier de page maître pour la page actuelle. Le chemin d’accès peut être relatif ou absolu. Les pages maîtres sont traités dans [Module 4](master-pages.md).

## <a name="stylesheettheme"></a>StyleSheetTheme

Cet attribut vous permet de substituer les propriétés d’apparence de l’interface utilisateur définies par un thème ASP.NET 2.0. Les thèmes sont traités dans [Module 10](profiles-themes-and-web-parts.md).

## <a name="theme"></a>Thème

Spécifie le thème de la page. Si une valeur n’est pas spécifiée pour l’attribut StyleSheetTheme, l’attribut Theme remplace tous les styles appliqués aux contrôles sur la page.

## <a name="title"></a>Titre

Définit le titre de la page. La valeur spécifiée ici s’affiche dans le &lt;titre&gt; élément de la page rendue.

### <a name="viewstateencryptionmode"></a>ViewStateEncryptionMode

Définit la valeur de l’énumération ViewStateEncryptionMode. Les valeurs disponibles sont **toujours**, **automatique**, et **jamais**. La valeur par défaut est **automatique**. Lorsque cet attribut est défini sur la valeur **automatique**, état d’affichage est chiffré est un contrôle le demande en appelant le **RegisterRequiresViewStateEncryption** (méthode).

## <a name="setting-public-property-values-via-the--page-directive"></a>Définition des valeurs de propriété publique via la Directive @ Page

Une autre nouvelle fonctionnalité de la directive @ Page dans ASP.NET 2.0 est la possibilité de définir la valeur initiale des propriétés publiques d’une classe de base. Supposons, par exemple, que vous disposez d’une propriété publique d’appelé **SomeText** dans votre classe de base et d comme il vous être initialisées sur **Hello** lorsqu’une page est chargée. Ce faire, vous pouvez simplement définir la valeur de la directive @ Page comme suit :

[!code-aspx[Main](the-asp-net-2-0-page-model/samples/sample4.aspx)]

Le **SomeText** attribut de la directive @ Page définit la valeur initiale de la propriété SomeText dans la classe de base *Hello !*. La vidéo ci-dessous est une procédure pas à pas de la définition de la valeur initiale d’une propriété publique dans une classe de base à l’aide de la directive @ Page.


![](the-asp-net-2-0-page-model/_static/image1.png)


[Ouvrez plein écran](the-asp-net-2-0-page-model/_static/setprop1.wmv)


## <a name="new-public-properties-of-the-page-class"></a>Nouvelles propriétés publiques de la classe de Page

Les propriétés publiques suivantes sont nouvelles dans ASP.NET 2.0.

## <a name="apprelativetemplatesourcedirectory"></a>AppRelativeTemplateSourceDirectory

Retourne le chemin d’accès relatif à l’application à la page ou le contrôle. Par exemple, pour une page à http://app/folder/page.aspx, la propriété retourne ~ / dossier /.

## <a name="apprelativevirtualpath"></a>AppRelativeVirtualPath

Retourne le chemin d’accès du répertoire virtuel relatif à la page ou le contrôle. Par exemple pour une page à http://app/folder/page.aspx, la propriété retourne ~ / folder/page.aspx.

## <a name="asynctimeout"></a>AsyncTimeout

Obtient ou définit le délai d’expiration utilisé pour la gestion de la page asynchrone. (Les pages asynchrones seront abordées plus loin dans ce module.)

## <a name="clientquerystring"></a>ClientQueryString

Propriété en lecture seule qui renvoie la partie de chaîne de requête de l’URL demandée. Cette valeur est encodé en URL. Vous pouvez utiliser la méthode UrlDecode de la classe HttpServerUtility à décoder.

## <a name="clientscript"></a>ClientScript

Cette propriété retourne un objet ClientScriptManager qui peut être utilisé pour gérer les émissions de ASP.NETs de script côté client. (La classe ClientScriptManager est présentée plus loin dans ce module.)

## <a name="enableeventvalidation"></a>EnableEventValidation

Cette propriété contrôle si la validation d’événement est activée pour les événements de publication et de rappel. Lorsque activé, les arguments de publication (postback) ou les événements de rappel sont vérifiées pour vous assurer qu’ils proviennent du contrôle serveur rendus à l’origine.

## <a name="enabletheming"></a>EnableTheming

Cette propriété obtient ou définit une valeur booléenne qui spécifie si un thème ASP.NET 2.0 s’applique à la page.

## <a name="form"></a>Formulaire

Cette propriété retourne le formulaire HTML dans la page ASPX en tant qu’objet HtmlForm.

## <a name="header"></a>Header

Cette propriété retourne une référence à un objet de HtmlHead qui contient l’en-tête de page. Vous pouvez utiliser l’objet de HtmlHead retourné pour obtenir/définir des feuilles de style, les balises Meta, etc.

## <a name="idseparator"></a>IdSeparator

Cette propriété en lecture seule Obtient le caractère utilisé pour séparer les identificateurs de contrôle lorsque ASP.NET génère un ID unique pour les contrôles sur une page. Elle n'est pas destinée à être utilisée directement à partir du code.

## <a name="isasync"></a>IsAsync

Cette propriété permet de pages asynchrones. Pages asynchrones sont décrites plus loin dans ce module.

## <a name="iscallback"></a>IsCallback

Cette propriété en lecture seule retourne **true** si la page est le résultat d’un rappel. Rappels sont décrites plus loin dans ce module.

## <a name="iscrosspagepostback"></a>IsCrossPagePostBack

Cette propriété en lecture seule retourne **true** si la page fait partie d’une publication (postback) entre pages. Publications (postback) entre pages est décrites plus loin dans ce module.

## <a name="items"></a>Éléments

Retourne une référence à une instance IDictionary qui contient tous les objets stockés dans le contexte de pages. Vous pouvez ajouter des éléments à cet objet IDictionary et ils seront disponibles tout au long de la durée de vie du contexte.

## <a name="maintainscrollpositiononpostback"></a>MaintainScrollPositionOnPostBack

Cette propriété contrôle si ASP.NET émet JavaScript qui tient à jour que les pages de position de défilement dans le navigateur après qu’une publication (postback) se produit. (Les détails de cette propriété ont été décrites plus haut dans ce module.)

## <a name="master"></a>Master

Cette propriété en lecture seule retourne une référence à l’instance de page maître pour une page à laquelle une page maître a été appliquée.

## <a name="masterpagefile"></a>MasterPageFile

Obtient ou définit le nom du fichier de page maître pour la page. Cette propriété peut uniquement être définie dans la méthode PreInit.

## <a name="maxpagestatefieldlength"></a>MaxPageStateFieldLength

Cette propriété obtient ou définit la longueur maximale de l’état des pages en octets. Si la propriété est définie par un nombre positif, l’état d’affichage pages sera être divisé en plusieurs champs masqués afin qu’il ne dépasse pas le nombre d’octets spécifié. Si la propriété est un nombre négatif, l’état d’affichage n’est pas interrompue en segments.

## <a name="pageadapter"></a>PageAdapter

Retourne une référence à l’objet PageAdapter qui modifie la page pour le navigateur demandeur.

## <a name="previouspage"></a>PreviousPage

Retourne une référence à la page précédente en cas d’un Server.Transfer ou une publication (postback) entre pages.

## <a name="skinid"></a>SkinID

Spécifie l’apparence d’ASP.NET 2.0 à appliquer à la page.

## <a name="stylesheettheme"></a>StyleSheetTheme

Cette propriété obtient ou définit la feuille de style qui est appliquée à une page.

## <a name="templatecontrol"></a>TemplateControl

Retourne une référence au contrôle conteneur pour la page.

## <a name="theme"></a>Thème

Obtient ou définit le nom du thème ASP.NET 2.0 appliqué à la page. Cette valeur doit être définie avant la méthode PreInit.

## <a name="title"></a>Titre

Cette propriété obtient ou définit le titre de la page obtenues à partir de l’en-tête des pages.

## <a name="viewstateencryptionmode"></a>ViewStateEncryptionMode

Obtient ou définit le ViewStateEncryptionMode de la page. Consultez une description détaillée de cette propriété précédemment dans ce module.

## <a name="new-protected-properties-of-the-page-class"></a>Nouvelles propriétés protégées de la classe de Page

Voici les nouvelles propriétés protégées de la classe de Page dans ASP.NET 2.0.

## <a name="adapter"></a>Adaptateur

Retourne une référence à la ControlAdapter qui restitue la page sur le périphérique qui a demandé.

## <a name="asyncmode"></a>AsyncMode

Cette propriété indique si la page traitée de manière asynchrone. Il est prévu pour une utilisation par le runtime et non directement dans le code.

## <a name="clientidseparator"></a>ClientIDSeparator

Cette propriété retourne le caractère utilisé comme séparateur lors de la création des ID de client unique pour les contrôles. Il est prévu pour une utilisation par le runtime et non directement dans le code.

## <a name="pagestatepersister"></a>PageStatePersister

Cette propriété retourne l’objet PageStatePersister pour la page. Cette propriété est principalement utilisée par les développeurs de contrôles ASP.NET.

## <a name="uniquefilepathsuffix"></a>UniqueFilePathSuffix

Cette propriété retourne un suffic unique qui est ajouté au chemin de fichier de mise en cache des navigateurs. La valeur par défaut est \_ \_ufps = et un nombre à 6 chiffres.

## <a name="new-public-methods-for-the-page-class"></a>Nouvelles méthodes publiques de la classe de Page

Les méthodes publiques suivantes sont nouvelles pour la classe de Page dans ASP.NET 2.0.

## <a name="addonprerendercompleteasync"></a>AddOnPreRenderCompleteAsync

Cette méthode enregistre les délégués de gestionnaires d’événements pour l’exécution de page asynchrone. Pages asynchrones sont décrites plus loin dans ce module.

## <a name="applystylesheetskin"></a>ApplyStyleSheetSkin

Applique les propriétés dans une feuille de style des pages à la page.

## <a name="executeregisteredasynctasks"></a>ExecuteRegisteredAsyncTasks

Spécialiste de cette méthode une tâche asynchrone.

### <a name="getvalidators"></a>GetValidators

Retourne une collection de validateurs pour le groupe de validation spécifié ou le groupe de validation par défaut si aucun n’est spécifié.

## <a name="registerasynctask"></a>RegisterAsyncTask

Cette méthode inscrit une nouvelle tâche asynchrone. Pages asynchrones sont décrites plus loin dans ce module.

## <a name="registerrequirescontrolstate"></a>RegisterRequiresControlState

Cette méthode indique à ASP.NET que l’état du contrôle pages doit être persistante.

## <a name="registerrequiresviewstateencryption"></a>RegisterRequiresViewStateEncryption

Cette méthode indique à ASP.NET que l’état d’affichage pages requiert le chiffrement.

## <a name="resolveclienturl"></a>ResolveClientUrl

Retourne une URL relative qui peut être utilisée pour les demandes de client pour les images, etc.

## <a name="setfocus"></a>SetFocus

Cette méthode définit le focus au contrôle qui est spécifié lorsque la page est chargée initialement.

## <a name="unregisterrequirescontrolstate"></a>UnregisterRequiresControlState

Cette méthode annule le contrôle qui lui est passé comme n’étant plus nécessitant persistance d’état de contrôle.

## <a name="changes-to-the-page-lifecycle"></a>Modifications apportées au cycle de vie de la Page

Le cycle de vie de page dans ASP.NET 2.0 n’a pas changé considérablement, mais il existe certaines nouvelles méthodes que vous devez connaître. Le cycle de vie de page ASP.NET 2.0 est décrit ci-dessous.

## <a name="preinit-new-in-aspnet-20"></a>PreInit (nouveau dans ASP.NET 2.0)

L’événement PreInit est la première étape du cycle de vie qui peut accéder à un développeur. L’ajout de cet événement permet de modifier les thèmes d’ASP.NET 2.0, les pages maîtres par programme, d’accéder aux propriétés d’un profil d’ASP.NET 2.0, un etc. Si vous êtes dans un état de publication (postback), il est important de savoir que Viewstate n’a pas encore été appliquée à des contrôles à ce stade dans le cycle de vie. Par conséquent, si un développeur modifie une propriété d’un contrôle à ce stade, il ne sera probablement remplacé plus loin dans le cycle de vie des pages.

## <a name="init"></a>Init

L’événement Init n’a pas changé depuis ASP.NET 1.x. Il s’agit où vous pouvez lire ou initialiser des propriétés des contrôles sur votre page. À ce stade, les pages maîtres, les thèmes, etc. sont déjà appliquées à la page.

## <a name="initcomplete-new-in-20"></a>InitComplete (nouveauté de la version 2.0)

L’événement InitComplete est appelée à la fin de l’étape d’initialisation de pages. À ce stade du cycle de vie, vous pouvez accéder à des contrôles sur la page, mais leur état n’a pas encore été remplie.

## <a name="preload-new-in-20"></a>Préchargement (nouveauté de la version 2.0)

Cet événement est appelé une fois que toutes les données de publication (postback) a été appliquée et juste avant la Page\_charge.

## <a name="load"></a>Load

L’événement de chargement n’a pas changé depuis ASP.NET 1.x.

## <a name="loadcomplete-new-in-20"></a>LoadComplete (nouveauté de la version 2.0)

L’événement LoadComplete est le dernier événement dans l’étape de chargement des pages. À ce stade, toutes les données de publication (postback) et l’état d’affichage a été appliqué à la page.

## <a name="prerender"></a>PreRender

Si vous souhaitez que viewstate peut être géré correctement pour les contrôles qui sont ajoutés dynamiquement à la page, l’événement PreRender est la possibilité de les ajouter.

## <a name="prerendercomplete-new-in-20"></a>PreRenderComplete (nouveauté de la version 2.0)

Au stade PreRenderComplete, tous les contrôles ont été ajoutés à la page et la page est prête à être affiché. L’événement PreRenderComplete est le dernier événement déclenché avant l’enregistrement de l’état d’affichage pages.

## <a name="savestatecomplete-new-in-20"></a>SaveStateComplete (nouveauté de la version 2.0)

L’événement SaveStateComplete est appelée immédiatement après l’enregistrement de tous les États d’état d’affichage et le contrôle de page. Il s’agit du dernier événement avant que la page est réellement rendue dans le navigateur.

## <a name="render"></a>Afficher

La méthode de rendu n’a pas changé depuis ASP.NET 1.x. Il s’agit où la HtmlTextWriter est initialisée et que la page est rendue dans le navigateur.

## <a name="cross-page-postback-in-aspnet-20"></a>Publication entre pages dans ASP.NET 2.0

Dans ASP.NET 1.x, les publications (postback) étaient requises à valider dans la même page. Publications (postback) entre pages n’étaient pas autorisés. ASP.NET 2.0 ajoute la possibilité de publier sur une autre page via l’interface IButtonControl. Tout contrôle qui implémente la nouvelle interface IButtonControl (bouton LinkButton et ImageButton en plus des contrôles personnalisés de tiers) permettre tirer parti de cette nouvelle fonctionnalité via l’utilisation de l’attribut PostBackUrl. Le code suivant montre un contrôle bouton qui publie sur une deuxième page.

[!code-aspx[Main](the-asp-net-2-0-page-model/samples/sample5.aspx)]

Lorsque la page est publiée, la Page qui lance la publication est accessible via la propriété PreviousPage sur la deuxième page. Cette fonctionnalité est implémentée via le formulaire Web\_fonction DoPostBackWithOptions côté client qui restitue le ASP.NET 2.0 sur la page lorsqu’un contrôle publie vers une autre page. Cette fonction JavaScript est fournie par le nouveau gestionnaire WebResource.axd qui émet un script pour le client.

La vidéo ci-dessous est une procédure pas à pas d’une publication entre pages.


![](the-asp-net-2-0-page-model/_static/image2.png)


[Ouvrez plein écran](the-asp-net-2-0-page-model/_static/xpage1.wmv)


## <a name="more-details-on-cross-page-postbacks"></a>Plus d’informations sur les publications (postback) entre pages

### <a name="viewstate"></a>État d’affichage

Vous pouvez avoir invité vous-même déjà sur ce qui se passe à l’état d’affichage de la première page dans un scénario de publication (postback) entre pages. Après tout, n’importe quel contrôle qui n’implémente pas IPostBackDataHandler persistera son état via viewstate, par conséquent, pour accéder aux propriétés de ce contrôle dans la deuxième page d’une publication entre pages, vous devez avoir accès à l’état d’affichage de la page. ASP.NET 2.0 prend en charge ce scénario à l’aide d’un nouveau champ masqué dans la deuxième page appelé \_ \_PREVIOUSPAGE. Le \_ \_champ de formulaire PREVIOUSPAGE contient l’état d’affichage pour la première page afin que vous pouvez avoir accès aux propriétés de tous les contrôles dans la deuxième page.

### <a name="circumventing-findcontrol"></a>Contournement de FindControl

Dans la procédure pas à pas vidéo d’une publication entre pages, j’ai utilisé la méthode FindControl pour obtenir une référence au contrôle de zone de texte sur la première page. Cette méthode fonctionne correctement à cette fin, mais FindControl est coûteuse, et elle requiert l’écriture de code supplémentaire. Heureusement, ASP.NET 2.0 fournit une alternative à FindControl à cette fin qui fonctionne dans de nombreux scénarios. La directive PreviousPageType vous permet d’avoir une référence fortement typée à la page précédente à l’aide de l’attribut VirtualPath ou nom de type. L’attribut de nom de type vous permet de spécifier le type de la page précédente pendant que l’attribut VirtualPath vous permet de faire référence à la page précédente à l’aide d’un chemin d’accès virtuel. Une fois que vous avez défini la directive PreviousPageType, vous devez ensuite exposer les contrôles, etc. auquel vous souhaitez autoriser l’accès à l’aide des propriétés publiques.

## <a name="lab-1-cross-page-postback"></a>Publication d’entre pages atelier 1

Dans cet atelier, vous allez créer une application qui utilise la nouvelle fonctionnalité de publication (postback) entre pages d’ASP.NET 2.0.

1. Ouvrez Visual Studio 2005 et créer un nouveau site Web ASP.NET.
2. Ajouter un nouveau formulaire Web appelé page2.aspx.
3. Ouvrez la page Default.aspx en mode Création et ajoutez un contrôle bouton et un contrôle de zone de texte. 

    1. Donner le contrôle bouton ID **SubmitButton** et un ID de contrôle de la zone de texte **nom d’utilisateur**.
    2. Définir la propriété PostBackUrl du bouton Page2.aspx.
4. Ouvrez page2.aspx en mode Source.
5. Ajoutez une directive @ PreviousPageType comme indiqué ci-dessous :
6. Ajoutez le code suivant à la Page\_charge de code-behind de page2.aspx : 

    [!code-csharp[Main](the-asp-net-2-0-page-model/samples/sample6.cs)]
7. Générez le projet en cliquant sur la Build dans le menu Générer.
8. Ajoutez le code suivant pour le code-behind pour Default.aspx : 

    [!code-csharp[Main](the-asp-net-2-0-page-model/samples/sample7.cs)]
9. Modifier la Page\_charge page2.aspx à ce qui suit : 

    [!code-csharp[Main](the-asp-net-2-0-page-model/samples/sample8.cs)]
10. Générez le projet.
11. Exécutez le projet.
12. Entrez votre nom dans la zone de texte et cliquez sur le bouton.
13. Que se passe-t-il ?

## <a name="asynchronous-pages-in-aspnet-20"></a>Pages asynchrones dans ASP.NET 2.0

De nombreux problèmes de contention de ASP.NET sont dus à la latence des appels externes (tels que les appels de base de données ou service Web), latence des e/s de fichier, etc. Lorsqu’une demande est faite par rapport à une application ASP.NET, ASP.NET utilise un de ses threads de travail pour traiter cette demande. Cette demande est propriétaire de ce thread jusqu'à ce que la demande est terminée et la réponse a été envoyée. ASP.NET 2.0 s’efforce de résoudre les problèmes de latence avec ces types de problèmes en ajoutant la possibilité d’exécuter des pages de façon asynchrone. Cela signifie qu’un thread de travail peut démarrer la demande et ensuite remettre d’exécution supplémentaires à un autre thread, revenir au pool de threads disponibles rapidement. Lorsque les e/s de fichier, les appels de base de données, etc. est terminée, un nouveau thread est obtenu du pool de threads pour terminer la demande.

La première étape de création d’une page exécuter de façon asynchrone consiste à définir le **Async** attribut de la directive de page comme suit :

[!code-aspx[Main](the-asp-net-2-0-page-model/samples/sample9.aspx)]

Cet attribut indique à ASP.NET pour implémenter le IHttpAsyncHandler pour la page.

L’étape suivante consiste à appeler la méthode AddOnPreRenderCompleteAsync à un point dans le cycle de vie de la page avant PreRender. (Cette méthode est généralement appelée dans la Page\_charge.) La méthode AddOnPreRenderCompleteAsync prend deux paramètres ; un BeginEventHandler et un EndEventHandler. La BeginEventHandler retourne un élément IAsyncResult qui est ensuite transmis en tant que paramètre à la EndEventHandler.

La vidéo ci-dessous est une procédure pas à pas d’une demande de page asynchrone.


![](the-asp-net-2-0-page-model/_static/image3.png)


[Ouvrez plein écran](the-asp-net-2-0-page-model/_static/async1.wmv)


> [!NOTE]
> Une page async ne rend pas dans le navigateur, jusqu'à ce que le EndEventHandler est terminée. Aucun doute mais que certains développeurs pense que des demandes asynchrones sont semblables à des rappels asynchrones. Il est important de savoir qu’ils ne sont pas. L’avantage pour les demandes asynchrones est que le premier thread de travail peut être retourné au pool de threads pour les nouvelles demandes de service, ce qui réduit la contention en raison d’un e/s liée, etc.


## <a name="script-callbacks-in-aspnet-20"></a>Rappels de script dans ASP.NET 2.0

Les développeurs Web ont toujours recherché des moyens pour éviter le scintillement associé à un rappel. Dans ASP.NET 1.x, SmartNavigation était la méthode la plus courante pour éviter le scintillement, mais SmartNavigation a provoqué des problèmes pour certains développeurs en raison de la complexité de son exécution sur le client. ASP.NET 2.0 résout ce problème avec des rappels de script. Les rappels de script utilisent XMLHttp pour effectuer des demandes sur le serveur Web via JavaScript. La demande XMLHttp retourne des données XML qui peuvent ensuite être manipulées par le biais DOM de l’Explorateur Code de XMLHttp est masquée à partir de l’utilisateur par le nouveau gestionnaire WebResource.axd.

Il existe plusieurs étapes sont nécessaires pour configurer un rappel de script dans ASP.NET 2.0.

## <a name="step-1--implement-the-icallbackeventhandler-interface"></a>Étape 1 : Implémenter l’Interface ICallbackEventHandler

Dans l’ordre pour ASP.NET de reconnaître votre page comme participant dans un rappel de script, vous devez implémenter l’interface ICallbackEventHandler. Vous pouvez les faire cela dans votre fichier code-behind comme suit :

[!code-csharp[Main](the-asp-net-2-0-page-model/samples/sample10.cs)]

Vous pouvez également cela à l’aide d’autres directive @ Implements ainsi :

[!code-aspx[Main](the-asp-net-2-0-page-model/samples/sample11.aspx)]

Vous utiliserez généralement la directive @ Implements lorsque vous utilisez le code inline ASP.NET.

## <a name="step-2--call-getcallbackeventreference"></a>Étape 2 : Appelez GetCallbackEventReference

Comme mentionné précédemment, l’appel XMLHttp est encapsulé dans le gestionnaire WebResource.axd. Lorsque votre page est rendue, ASP.NET ajoute un appel à WebForm\_DoCallback, un script client qui est fourni par WebResource.axd. Le Web Form\_DoCallback fonction remplace la \_ \_fonction doPostBack pour un rappel. N’oubliez pas que \_ \_doPostBack envoie par programme le formulaire sur la page. Dans un scénario de rappel, vous souhaitez empêcher une publication (postback), par conséquent, \_ \_doPostBack n’est pas suffisante.

> [!NOTE]
> \_\_doPostBack est toujours rendu dans la page dans un scénario de rappel de script client. Toutefois, il n’est pas utilisé pour le rappel.


Les arguments pour le Web Form\_DoCallback (fonction) côté client sont fournies via la fonction côté serveur GetCallbackEventReference qui devrait être appelé dans la Page\_charge. Un appel typique à GetCallbackEventReference peut ressembler à ceci :

[!code-csharp[Main](the-asp-net-2-0-page-model/samples/sample12.cs)]

> [!NOTE]
> Dans ce cas, cm est une instance de ClientScriptManager. La classe ClientScriptManager est abordée plus loin dans ce module.


Il existe plusieurs versions surchargées de GetCallbackEventReference. Dans ce cas, les arguments sont les suivantes :

`this`

Une référence au contrôle où GetCallbackEventReference est appelée. Dans ce cas, il est la page elle-même.

[!code-javascript[Main](the-asp-net-2-0-page-model/samples/sample13.js)]

Un argument de chaîne qui est passé à partir du code côté client à l’événement côté serveur. Dans ce cas, en passant la valeur d’une liste déroulante de messagerie instantanée appelé ddlCompany.

`ShowCompanyName`

Le nom de la fonction côté client qui accepte la valeur de retour (en tant que chaîne) à partir de l’événement de rappel côté serveur. Cette fonction sera uniquement être appelée lorsque le rappel côté serveur a réussi. Par conséquent, pour des raisons de robustesse, il est généralement recommandé d’utiliser la version surchargée de GetCallbackEventReference qui accepte un argument de chaîne supplémentaire en spécifiant le nom d’une fonction côté client à exécuter en cas d’erreur.

`null`

Chaîne représentant une fonction côté client qu’il a initié avant le rappel sur le serveur. Dans ce cas, il n’est aucun script de ce type, par conséquent, l’argument est null.

`true`

Valeur booléenne spécifiant ou non effectuer le rappel de façon asynchrone.

L’appel à WebForm\_DoCallback sur le client passe ces arguments. Par conséquent, lorsque cette page est rendue sur le client, il se présente comme suit :

[!code-javascript[Main](the-asp-net-2-0-page-model/samples/sample14.js)]

Notez que la signature de la fonction sur le client est un peu différente. La fonction côté client passe 5 chaînes et une valeur booléenne. La chaîne supplémentaire (qui a la valeur null dans l’exemple ci-dessus) contient la fonction côté client qui gère les erreurs du rappel côté serveur.

## <a name="step-3--hook-the-client-side-control-event"></a>Étape 3 : Raccorder l’événement de contrôle côté Client

Notez que la valeur de retour de GetCallbackEventReference ci-dessus a été assignée à une variable de chaîne. Cette chaîne est utilisée pour raccorder un événement côté client pour le contrôle qui initialise le rappel. Dans cet exemple, le rappel est initié par une liste déroulante dans la page, je souhaite raccorder le *OnChange* événement.

Pour raccorder l’événement côté client, ajoutez simplement un gestionnaire pour le balisage côté client comme suit :

[!code-csharp[Main](the-asp-net-2-0-page-model/samples/sample15.cs)]

N’oubliez pas que *cbRef* est la valeur de retour de l’appel à GetCallbackEventReference. Il contient l’appel à WebForm\_DoCallback qui a été indiqué ci-dessus.

## <a name="step-4--register-the-client-side-script"></a>Étape 4 : Inscrire le Script côté Client

Souvenez-vous que l’appel à GetCallbackEventReference spécifié qu’un script côté client appelée **ShowCompanyName** sont exécutées lorsque le rappel côté serveur réussit. Ce script doit être ajouté à la page à l’aide d’une instance ClientScriptManager. (La classe ClientScriptManager seront décrits plus loin dans ce module). Vous effectuez donc ce type :

[!code-javascript[Main](the-asp-net-2-0-page-model/samples/sample16.js)]

## <a name="step-5--call-the-methods-of-the-icallbackeventhandler-interface"></a>Étape 5 : Appelez les méthodes de l’Interface ICallbackEventHandler

Le ICallbackEventHandler contient deux méthodes dont vous avez besoin d’implémenter dans votre code. Ils sont **RaiseCallbackEvent** et **GetCallbackEvent**.

**RaiseCallbackEvent** prend une chaîne en tant qu’argument et ne retourne rien. L’argument de chaîne est passé à partir de l’appel côté client WebForm\_DoCallback. Dans ce cas, cette valeur est la *valeur* attribut de la liste déroulante appelée ddlCompany. Votre code côté serveur doit être placé dans la méthode RaiseCallbackEvent. Par exemple, si votre rappel effectue un WebRequest par rapport à une ressource externe, ce code doit être placé dans RaiseCallbackEvent.

**GetCallbackEvent** est chargé de traiter le retour du rappel au client. Il n’accepte aucun argument et retourne une chaîne. La chaîne qu’elle retourne est être passée en tant qu’argument à la fonction côté client, dans ce cas *ShowCompanyName*.

Une fois que vous avez effectué les étapes ci-dessus, vous êtes prêt à effectuer un rappel de script dans ASP.NET 2.0.


![](the-asp-net-2-0-page-model/_static/image4.png)


[Ouvrez plein écran](the-asp-net-2-0-page-model/_static/callback1.wmv)


Les rappels de script dans ASP.NET sont prises en charge dans n’importe quel navigateur qui prend en charge les appels XMLHttp. Incluant tous les navigateurs modernes utilisé aujourd'hui. Internet Explorer utilise l’objet XMLHttp ActiveX tandis que d’autres navigateurs modernes (y compris l’Internet Explorer 7 à venir) utilisent un objet XMLHttp intrinsèque. Pour déterminer par programme si un navigateur prend en charge les rappels, vous pouvez utiliser la **Request.Browser.SupportCallback** propriété. Cette propriété retourne **true** si le client demande prend en charge les rappels de script.

## <a name="working-with-client-script-in-aspnet-20"></a>Utilisation de Script Client dans ASP.NET 2.0

Les scripts de client dans ASP.NET 2.0 sont gérées via l’utilisation de la classe ClientScriptManager. La classe ClientScriptManager effectue le suivi des scripts de client à l’aide d’un type et un nom. Cela empêche le même script d’être insérées par programme plusieurs fois sur une page.

> [!NOTE]
> Une fois un script a été correctement inscrit sur une page, toute tentative ultérieure pour inscrire le même script entraîne simplement le script ne pas inscrit une deuxième fois. Aucun script en double n’est ajoutées et aucune exception ne se produit. Pour éviter des calculs inutiles, il existe les méthodes que vous pouvez utiliser pour déterminer si un script est déjà inscrit afin que vous n’essayez pas de l’inscrire plusieurs fois.


Les méthodes de ClientScriptManager doivent connaître pour tous les développeurs ASP.NET en cours :

## <a name="registerclientscriptblock"></a>RegisterClientScriptBlock

Cette méthode ajoute un script en haut de la page rendue. Cela est utile pour l’ajout de fonctions qui seront appelées explicitement sur le client.

Il existe deux versions surchargées de cette méthode. Trois des quatre arguments sont communes entre eux. Il s'agit des éléments suivants :

`type (string)`

Le ***type*** argument identifie un type pour le script. Il est généralement judicieux d’utiliser le type de la page (cela. GetType()) pour le type.

`key (string)`

Le ***clé*** argument est une clé définie par l’utilisateur pour le script. Ce doit être unique pour chaque script. Si vous essayez d’ajouter un script avec la même clé et le type d’un script existant, il ne sera pas ajouté.

`script (string)`

Le ***script*** argument est une chaîne contenant le script à ajouter. Il est recommandé d’utiliser un StringBuilder pour créer le script, puis la méthode ToString() sur StringBuilder pour affecter le ***script*** argument.

Si vous utilisez le RegisterClientScriptBlock surchargée qui accepte uniquement les trois arguments, vous devez inclure les éléments de script (&lt;script&gt; et &lt;/script&gt;) dans votre script.

Vous pouvez choisir d’utiliser la surcharge de RegisterClientScriptBlock qui accepte un quatrième argument. Le quatrième argument est une valeur booléenne qui spécifie si ASP.NET doit ajouter des éléments de script pour vous. Si cet argument est **true**, votre script ne doit pas inclure explicitement les éléments de script.

Utilisez la méthode IsClientScriptBlockRegistered pour déterminer si un script a déjà été inscrit. Cela vous permet d’éviter une tentative de ré-enregistrer un script qui a déjà été inscrit.

### <a name="registerclientscriptinclude-new-in-20"></a>RegisterClientScriptInclude (nouveauté de la version 2.0)

La balise RegisterClientScriptInclude crée un bloc de script qui établit un lien vers un fichier de script externe. Il possède deux surcharges. L’une prend une clé et une URL. Le second ajoute un troisième argument spécifiant le type.

Par exemple, le code suivant génère un bloc de script qui se lie au jsfunctions.js à la racine du dossier scripts de l’application :

[!code-csharp[Main](the-asp-net-2-0-page-model/samples/sample17.cs)]

Ce code génère le code suivant dans la page rendue :

[!code-html[Main](the-asp-net-2-0-page-model/samples/sample18.html)]

> [!NOTE]
> Le bloc de script est rendu au bas de la page.


Utilisez la méthode IsClientScriptIncludeRegistered pour déterminer si un script a déjà été inscrit. Cela vous permet d’éviter une tentative de ré-enregistrer un script.

## <a name="registerstartupscript"></a>RegisterStartupScript

La méthode RegisterStartupScript accepte les mêmes arguments que la méthode RegisterClientScriptBlock. Un script inscrit avec RegisterStartupScript s’exécute après que la page, mais avant l’événement du côté client OnLoad. Dans 1.X, les scripts enregistrés avec RegisterStartupScript ont été placés juste avant la fermeture &lt;/écran&gt; tandis que les scripts enregistrés avec RegisterClientScriptBlock ont été placés immédiatement après l’ouverture de la balise &lt;formulaire&gt; balise. Dans ASP.NET 2.0, les deux sont placés immédiatement avant la fermeture &lt;/écran&gt; balise.

> [!NOTE]
> Si vous enregistrez une fonction avec RegisterStartupScript, cette fonction s’exécute pas jusqu'à ce que vous l’appelez explicitement dans le code côté client.


Utilisez la méthode IsStartupScriptRegistered pour déterminer si un script a déjà été inscrit et d’éviter de ré-enregistrer un script.

## <a name="other-clientscriptmanager-methods"></a>Autres méthodes ClientScriptManager

Voici quelques-unes des autres méthodes de la classe ClientScriptManager utiles.

| **GetCallbackEventReference** | Consultez les rappels de script plus haut dans ce module. |
| --- | --- |
| **GetPostBackClientHyperlink** | Obtient une référence JavaScript (javascript :&lt;appeler&gt;) qui peut être utilisé pour publier à partir d’un événement côté client. |
| **GetPostBackEventReference** | Obtient une chaîne qui peut être utilisée pour lancer une publication à partir du client. |
| **GetWebResourceUrl** | Retourne une URL à une ressource qui est incorporée dans un assembly. Doit être utilisé conjointement avec **RegisterClientScriptResource**. |
| **RegisterClientScriptResource** | Enregistre une ressource Web avec la page. Il s’agit de ressources incorporé dans un assembly et gérée par le gestionnaire WebResource.axd nouvelle. |
| **RegisterHiddenField** | Inscrit un champ masqué avec la page. |
| **RegisterOnSubmitStatement** | Inscrit le code côté client qui s’exécute lorsque l’envoi du formulaire HTML. |
