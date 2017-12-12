---
uid: web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax
title: "Présentation des mises à jour de Page partielle avec ASP.NET AJAX | Documents Microsoft"
author: scottcate
description: "La fonctionnalité la plus visible des Extensions ASP.NET AJAX est peut-être la possibilité d’effectuer des mises à jour une page partielle ou incrémentielle sans effectuer une publication (postback) complète à t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/28/2008
ms.topic: article
ms.assetid: 54d9df99-1161-4899-b4e8-2679c85915e7
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax
msc.type: authoredcontent
ms.openlocfilehash: 1d8d3009df0a264e466d3f7decfb65978d8ae7a4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="understanding-partial-page-updates-with-aspnet-ajax"></a>Page partielle de présentation des mises à jour avec ASP.NET AJAX
====================
par [ble pour indiquer Scott](https://github.com/scottcate)

[Télécharger le PDF](http://download.microsoft.com/download/C/1/9/C19A3451-1D14-477C-B703-54EF22E197EE/AJAX_tutorial01_Partial_Page_Updates_cs.pdf)

> La fonctionnalité la plus visible des Extensions ASP.NET AJAX est peut-être la possibilité d’effectuer des mises à jour une page partielle ou incrémentielle sans effectuer une publication complète sur le serveur, sans les modifications du code et les modifications de balisage minimale. Les avantages sont étendues : l’état de votre multimédia (par exemple, Adobe Flash ou un support de Windows) est inchangée, réduction des coûts de bande passante et le client ne connaît pas le scintillement généralement associé à une publication (postback).


## <a name="introduction"></a>Introduction

Technologie ASP.NET de Microsoft offre un modèle de programmation orientée objet et pilotées par événements et il regroupe les avantages du code compilé. Toutefois, son modèle de traitement côté serveur présente plusieurs inconvénients inhérents à la technologie :

- Mises à jour de la page nécessitent un aller-retour vers le serveur, ce qui nécessite une actualisation de la page.
- Boucles ne sont pas conservent à des effets générés par Javascript ou une autre technologie côté client (par exemple, Adobe Flash)
- Pendant la publication, les navigateurs autres que Microsoft Internet Explorer ne gèrent pas la restauration automatique de la position de défilement. Et même dans Internet Explorer, il est toujours un scintillement l’actualisation de la page.
- Publications (postback) peut impliquer une grande quantité de bande passante que la \_ \_peut augmenter de champ de formulaire VIEWSTATE, surtout lorsque vous traitez des contrôles tels qu’un contrôle GridView ou répéteurs.
- Il n’existe aucun modèle unifié pour l’accès aux Services Web via JavaScript ou une autre technologie côté client.

Entrez les extensions ASP.NET AJAX de Microsoft. AJAX, ce qui signifie **A** synchrone **J** avaScript **A** nd **X** ML, est une infrastructure intégrée permettant la page incrémentielle mises à jour via inter-plateformes, JavaScript, composé du code côté serveur comprenant l’infrastructure AJAX de Microsoft et un composant de script appelé Script Microsoft AJAX Library. Les extensions ASP.NET AJAX fournissent également une prise en charge multiplateforme pour accéder à des Services Web ASP.NET via JavaScript.

Ce livre blanc examine la fonctionnalité de mises à jour de page partielle des Extensions ASP.NET AJAX, qui inclut le composant ScriptManager, le contrôle UpdatePanel et le contrôle UpdateProgress et prend en compte les scénarios dans lesquels ils doivent ou ne doivent pas être utilisé.

Ce livre blanc est basé sur la version bêta 2 de Visual Studio 2008 et .NET Framework 3.5, qui intègre les Extensions ASP.NET AJAX dans la bibliothèque de classes de Base (où il était précédemment un composant additionnel disponible pour ASP.NET 2.0). Ce livre blanc suppose également que vous utilisez Visual Studio 2008 et pas Visual Web Developer Express Edition ; Certains modèles de projet qui sont référencés n’est peut-être pas disponibles pour les utilisateurs de Visual Web Developer Express.

## <a name="partial-page-updates"></a>Mises à jour de Page partielle

La fonctionnalité la plus visible des Extensions ASP.NET AJAX est peut-être la possibilité d’effectuer des mises à jour une page partielle ou incrémentielle sans effectuer une publication complète sur le serveur, sans les modifications du code et les modifications de balisage minimale. Les avantages sont étendues : l’état de votre multimédia (par exemple, Adobe Flash ou un support de Windows) est inchangée, réduction des coûts de bande passante et le client ne connaît pas le scintillement généralement associé à une publication (postback).

La possibilité d’intégrer le rendu de page partielle est intégrée à ASP.NET avec des modifications minimes dans votre projet.

## <a name="walkthrough-integrating-partial-rendering-into-an-existing-project"></a>Procédure pas à pas : Intégration de rendu partiel dans un projet existant


1. Dans Microsoft Visual Studio 2008, créez un projet de Site Web ASP.NET en accédant à *fichier*  *- &gt; nouveau*  *- &gt; le Site Web* et en sélectionnant le Site Web ASP.NET à partir de la boîte de dialogue. Vous pouvez la nommer comme vous le souhaitez, et vous pouvez l’installer dans le système de fichiers ou dans Internet Information Services (IIS).
2. Vous verrez la page vierge par défaut avec le balisage de base ASP.NET (un formulaire côté serveur et un `@Page` directive). Supprimer une étiquette nommée `Label1` et un bouton nommé `Button1` sur la page dans l’élément de formulaire. Vous pouvez définir leurs propriétés de texte à votre convenance.
3. En mode conception, double-cliquez sur `Button1` pour générer un gestionnaire d’événements code-behind. Dans ce gestionnaire d’événements, définir `Label1.Text` à, vous avez cliqué sur le bouton ! .

**La liste 1 : Le balisage de default.aspx avant le rendu partiel est activé**

[!code-aspx[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample1.aspx)]

**Listing 2 : Code-behind (supprimé) dans default.aspx.cs**

[!code-csharp[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample2.cs)]

1. Appuyez sur F5 pour lancer votre site web. Visual Studio vous invite à ajouter un fichier web.config pour activer le débogage ; le faire. Lorsque vous cliquez sur le bouton, notez que la page est actualisée pour modifier le texte dans l’étiquette, et est un bref scintillement que la page est redessinée.
2. Après la fermeture de la fenêtre du navigateur, revenir à Visual Studio et à la page de balisage. Faites défiler vers le bas dans la boîte à outils de Visual Studio et l’onglet Extensions AJAX. (Si vous n’avez pas cet onglet, car vous utilisez une version antérieure des extensions AJAX ou Atlas, reportez-vous à la procédure pas à pas pour enregistrer les éléments de boîte à outils des Extensions AJAX plus loin dans ce livre blanc, ou installez la version en cours avec le programme d’installation de Windows téléchargeable depuis le site Web).


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image2.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image1.png)

([Cliquez pour afficher l’image en taille réelle](understanding-partial-page-updates-with-asp-net-ajax/_static/image3.png))


1. *Problème connu :*si vous installez Visual Studio 2008 sur un ordinateur équipé de Visual Studio 2005 est installé avec les Extensions ASP.NET 2.0 AJAX, Visual Studio 2008 importe les éléments de boîte à outils des Extensions AJAX. Vous pouvez déterminer si c’est le cas en examinant l’info-bulle des composants ; Il doivent indiquer la Version 3.5.0.0. S’il déclare la Version 2.0.0.0, puis vous avez importé vos anciens éléments de boîte à outils et devez les importer manuellement à l’aide de la boîte de dialogue Choisir des éléments de boîte à outils dans Visual Studio. Il se peut que vous ne pouvez pas ajouter des contrôles de Version 2 via le concepteur.

1. Avant du `<asp:Label>` balise commence, créez une ligne d’espaces et double-cliquez sur le contrôle UpdatePanel dans la boîte à outils. Notez qu’un nouveau `@Register` la directive est incluse en haut de la page, indiquant que les contrôles dans l’espace de noms System.Web.UI doivent être importés à l’aide de la `asp:` préfixe.
2. Faites glisser la clôture `</asp:UpdatePanel>` marquer au-delà de la fin de l’élément Button, afin que l’élément est de forme correct avec les contrôles d’étiquette et un bouton encapsulés.
3. Après l’ouverture `<asp:UpdatePanel>` de balise, de commencer une nouvelle balise d’ouverture. Notez qu’IntelliSense vous propose deux options. Dans ce cas, créez un `<ContentTemplate>` balise. Veillez à entourer cette balise d’étiquette et un bouton afin que le balisage est bien formé.


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image5.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image4.png)

([Cliquez pour afficher l’image en taille réelle](understanding-partial-page-updates-with-asp-net-ajax/_static/image6.png))


1. N’importe où dans le `<form>` élément, inclure un contrôle ScriptManager en double-cliquant sur le `ScriptManager` élément dans la boîte à outils.
2. Modifier la `<asp:ScriptManager>` balise afin qu’elle contient l’attribut `EnablePartialRendering= true`.

**La liste 3 : Le balisage de default.aspx avec le rendu partiel activé**

[!code-aspx[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample3.aspx)]

1. Ouvrez votre fichier web.config. Notez que Visual Studio ajoute automatiquement une référence de compilation à System.Web.Extensions.dll.

1. Nouveautés de Visual Studio 2008 : le fichier web.config qui est fournie automatiquement avec les modèles de projet de Site Web ASP.NET inclut toutes les références nécessaires pour les Extensions ASP.NET AJAX et inclut des sections commentées des informations de configuration qui peuvent être non commentée pour activer des fonctionnalités supplémentaires. Visual Studio 2005 avaient des modèles similaires lorsque ASP.NET 2.0 AJAX Extensions ont été installées. Toutefois, dans Visual Studio 2008, les Extensions AJAX sont Annuler par défaut (autrement dit, ils sont référencés par défaut, mais peut être supprimées en tant que références).


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image8.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image7.png)

([Cliquez pour afficher l’image en taille réelle](understanding-partial-page-updates-with-asp-net-ajax/_static/image9.png))


1. Appuyez sur F5 pour lancer votre site Web. Notez comment aucune modification de code source ont été requises pour prendre en charge le rendu partiel uniquement le balisage a été modifié.

Lorsque vous lancez votre site Web, vous devez voir que le rendu partiel est maintenant activée, car lorsque vous cliquez sur le bouton il sera sans scintillement, et il ne toute modification apportée à la position de défilement de page (cet exemple ne montre pas qui). Si vous deviez rechercher dans la source de rendu de la page après avoir cliqué sur le bouton, il confirme qu’en fait une copie de sauvegarde après n’a pas eu lieu - le texte d’étiquette d’origine est toujours partie du balisage source, et l’étiquette a changé via JavaScript.

Visual Studio 2008 ne semble pas être accompagnée d’un modèle prédéfini pour un site web compatible ASP.NET AJAX. Toutefois, ce type de modèle n’était disponible dans Visual Studio 2005 si le Visual Studio 2005 et ASP.NET 2.0 AJAX Extensions ont été installées. Par conséquent, configuration d’un site web et commençant par le modèle de Site Web d’Enabled sera probablement encore plus simple, comme le modèle doit inclure un fichier web.config d’entièrement configuré (prenant en charge toutes les Extensions ASP.NET AJAX, notamment l’accès aux Services Web et sérialisation JSON - JavaScript Object Notation) et inclut un UpdatePanel et le ContentTemplate dans la page Web Forms principale par défaut. L’activation de rendu partiel avec cette page par défaut est aussi simple que de réviser l’étape 10 de cette procédure pas à pas et la suppression de contrôles sur la page.

## <a name="the-scriptmanager-control"></a>Le contrôle ScriptManager

## <a name="scriptmanager-control-reference"></a>Référence de contrôle ScriptManager

Propriétés des révisions activée :

| **Nom de propriété** | **Type** | **Description** |
| --- | --- | --- |
| Redirection AllowCustomErrors | Bool | Spécifie s’il faut utiliser la section d’erreurs personnalisées du fichier web.config pour gérer les erreurs. |
| Message de AsyncPostBackError | Chaîne | Obtient ou définit le message d’erreur envoyé au client si une erreur est générée. |
| Délai d’attente de AsyncPostBack | Int32 | Obtient ou définit la quantité par défaut d’une durée de qu'un client doit attendre la demande asynchrone terminer. |
| Globalisation de EnableScript | Bool | Obtient ou définit si la globalisation de script est activée. |
| EnableScript-localisation | Bool | Obtient ou définit si la localisation de script est activée. |
| ScriptLoadTimeout | Int32 | Détermine le nombre de secondes autorisé pour le chargement des scripts dans le client |
| ScriptMode | Enum (automatique, Debug, Release, héritent) | Obtient ou définit s’il faut restituer les versions release des scripts |
| ScriptPath | Chaîne | Obtient ou définit le chemin d’accès racine à l’emplacement des fichiers de script à envoyer au client. |

Propriétés du code uniquement :

| **Nom de propriété** | **Type** | **Description** |
| --- | --- | --- |
| AuthenticationService | Gestionnaire de AuthenticationService | Obtient des informations sur le proxy de Service d’authentification ASP.NET qui sera envoyé au client. |
| IsDebuggingEnabled | Bool | Obtient si le script et le débogage du code est activé. |
| IsInAsyncPostback | Bool | Détermine si la page est en cours d’une demande de publication asynchrone. |
| Utilisation de ProfileService | Gestionnaire de ProfileService | Obtient des informations sur le proxy de Service de profilage ASP.NET qui sera envoyé au client. |
| scripts ; | Collection&lt;référence de Script&gt; | Obtient une collection de références de script qui sera envoyé au client. |
| Services | Collection&lt;référence de Service&gt; | Obtient une collection de références de proxy de Service Web qui sera envoyé au client. |
| SupportsPartialRendering | Bool | Détermine si le client actuel prend en charge le rendu partiel. Si cette propriété retourne **false**, toutes les demandes de page seront alors des publications (postback) standard. |

Méthodes de Code public :

| **Nom de la méthode** | **Type** | **Description** |
| --- | --- | --- |
| SetFocus(string) | Void | Définit le focus du client à un contrôle spécifique lors de la demande est terminée. |

Balisage Descendants :

| **Balise** | **Description** |
| --- | --- |
| &lt;AuthenticationService&gt; | Fournit des détails sur le serveur proxy pour le service d’authentification ASP.NET. |
| &lt;Utilisation de ProfileService&gt; | Fournit des détails sur le serveur proxy pour le service de profilage ASP.NET. |
| &lt;Scripts&gt; | Fournit des références de script supplémentaires. |
| &lt;ASP : ScriptReference&gt; | Indique une référence de script spécifiques. |
| &lt;Service&gt; | Fournit des références de Service Web supplémentaires qui disposera des classes proxy générées. |
| &lt;ASP : ServiceReference&gt; | Indique une référence de Service Web spécifique. |

Le contrôle ScriptManager est l’essence essentiel pour les Extensions ASP.NET AJAX. Il fournit l’accès à la bibliothèque de scripts (y compris le système de type complète un script côté client), prend en charge le rendu partiel et fournit la prise en charge étendue pour les services ASP.NET supplémentaires (par exemple, l’authentification et de profilage, mais également d’autres Services Web). Le contrôle ScriptManager fournit également la prise en charge de globalisation et localisation pour les scripts de client.

## <a name="providing-alterative-and-supplemental-scripts"></a>En fournissant des Scripts supplémentaires et d’autre

Bien que les Extensions AJAX Microsoft ASP.NET 2.0 inclure le code de script en entier dans les versions debug et release éditions en tant que ressources incorporées dans les assemblys référencés, les développeurs sont libres de rediriger ScriptManager aux fichiers de script personnalisés, ainsi que d’inscrire scripts nécessaires supplémentaires.

Pour substituer la liaison par défaut pour les scripts sont en général inclus (tels que ceux qui prennent en charge de l’espace de noms Sys.WebForms et le système de types personnalisé), vous pouvez vous inscrire pour la `ResolveScriptReference` événement de la classe ScriptManager. Lorsque cette méthode est appelée, le Gestionnaire d’événements a la possibilité de modifier le chemin d’accès au fichier de script en question ; le Gestionnaire de script envoie ensuite une copie différente ou personnalisée des scripts au client.

En outre, les références de script (représenté par la `ScriptReference` classe) peut être inclus par programmation ou via le balisage. Pour ce faire, vous devez soit modifier par programmation la `ScriptManager.Scripts` collection, ou incluez `<asp:ScriptReference>` balises sous le `<Scripts>` balise, qui est un enfant de premier niveau du contrôle ScriptManager.

## <a name="custom-error-handling-for-updatepanels"></a>Gestion d’UpdatePanel d’erreur personnalisée

Bien que les mises à jour sont gérées par les déclencheurs spécifiés par les contrôles UpdatePanel, la prise en charge pour la gestion des erreurs et messages d’erreur personnalisés est gérée par une instance du contrôle ScriptManager d’une page. Pour cela, exposez un événement, `AsyncPostBackError`, à la page qui peut ensuite fournir une logique de gestion des exceptions personnalisée.

En consommant de l’événement AsyncPostBackError, vous pouvez spécifier le `AsyncPostBackErrorMessage` propriété, ce qui provoque un message d’alerte à signaler à l’achèvement du rappel.

Personnalisation côté client est également possible, au lieu d’utiliser la zone alerte par défaut ; par exemple, vous pouvez souhaiter afficher un texte personnalisé `<div>` élément plutôt que dans la boîte de dialogue par défaut navigateur modal. Dans ce cas, vous pouvez gérer l’erreur dans le script client :

**La liste 5 : Un script côté Client pour afficher les erreurs personnalisées**

[!code-html[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample4.html)]

Tout simplement le script ci-dessus inscrit un rappel avec le runtime d’AJAX côté client pour quand la demande asynchrone a été effectuée. Vérifie si une erreur a été signalée, ensuite il et dans ce cas, traite les détails de celui-ci, enfin qui indique au runtime que l’erreur a été gérée dans le script personnalisé.

## <a name="globalization-and-localization-support"></a>Prise en charge de la localisation et de globalisation

Le contrôle ScriptManager fournit la prise en charge complète pour la localisation des chaînes de script et des composants d’interface utilisateur ; Toutefois, cette rubrique est en dehors de la portée de ce livre blanc. Pour plus d’informations, consultez le livre blanc, prise en charge de la globalisation dans ASP.NET AJAX Extensions.

## <a name="the-updatepanel-control"></a>Le contrôle UpdatePanel

## <a name="updatepanel-control-reference"></a>Référence de contrôle UpdatePanel

Propriétés des révisions activée :

| **Nom de propriété** | **Type** | **Description** |
| --- | --- | --- |
| ChildrenAsTriggers | bool | Spécifie si les contrôles enfants appellent automatiquement l’actualisation de publication (postback). |
| RenderMode | enum (bloc, Inline) | Spécifie que la manière le contenu s’afficheront visuellement. |
| UpdateMode | enum (toujours, conditionnel) | Indique si UpdatePanel est toujours actualisé pendant un rendu partiel ou si elles sont actualisées uniquement lorsqu’un déclencheur est atteint. |

Propriétés du code uniquement :

| **Nom de propriété** | **Type** | **Description** |
| --- | --- | --- |
| IsInPartialRendering | bool | Indique si UpdatePanel prend en charge le rendu partiel pour la requête actuelle. |
| ContentTemplate | ITemplate | Obtient le modèle de balisage pour la demande de mise à jour. |
| ContentTemplateContainer | Contrôle | Obtient le modèle de programmation pour la demande de mise à jour. |
| Déclencheurs | UpdatePanel - TriggerCollection | Obtient la liste des déclencheurs associés UpdatePanel actuel. |

Méthodes de Code public :

| **Nom de la méthode** | **Type** | **Description** |
| --- | --- | --- |
| Update() | Void | Met à jour de UpdatePanel spécifié par programme. Permet à une demande de serveur déclencher un rendu partiel d’un UpdatePanel sinon-non déclenchée. |

Balisage Descendants :

| **Balise** | **Description** |
| --- | --- |
| &lt;ContentTemplate&gt; | Spécifie la balise à utiliser pour restituer le résultat de rendu partiel. Enfant de &lt;asp : UpdatePanel&gt;. |
| &lt;Déclencheurs&gt; | Spécifie une collection de  *n*  contrôles associés à la mise à jour cette UpdatePanel. Enfant de &lt;asp : UpdatePanel&gt;. |
| &lt;ASP : AsyncPostBackTrigger&gt; | Spécifie un déclencheur qui appelle un rendu de page partielle pour le contrôle UpdatePanel donné. Cela peut ou peut ne pas être un contrôle en tant que descendant de UpdatePanel en question. Niveau de granularité de l’événement. Enfant de &lt;déclencheurs&gt;. |
| &lt;ASP : PostBackTrigger&gt; | Spécifie un contrôle qui génère l’ensemble de la page à actualiser. Cela peut ou peut ne pas être un contrôle en tant que descendant de UpdatePanel en question. Granulaire à l’objet. Enfant de &lt;déclencheurs&gt;. |

Le `UpdatePanel` est le contrôle qui délimite le contenu côté serveur qui participera à la fonctionnalité de rendu partiel des Extensions AJAX. Il n’existe aucune limite au nombre de contrôles UpdatePanel qui peuvent se trouver sur une page, et ils peuvent être imbriqués. Chaque UpdatePanel est isolé, afin que chacun peut travailler indépendamment (vous pouvez avoir deux UpdatePanel en cours d’exécution en même temps, de rendu différentes parties de la page, indépendamment de la publication (postback) de la page).

UpdatePanel contrôle traite principalement avec des déclencheurs de contrôle : par défaut, n’importe quel contrôle contenu dans un UpdatePanel `ContentTemplate` qui crée une publication (postback) est enregistré en tant que déclencheur pour le contrôle UpdatePanel. Cela signifie que UpdatePanel peut fonctionner avec les contrôles liés aux données de par défaut (par exemple, le contrôle GridView), des contrôles utilisateur et ils peuvent être programmés dans le script.

Par défaut, lorsqu’un rendu de page partielle est déclenché, tous les contrôles UpdatePanel sur la page seront actualisées, ou non de contrôles de UpdatePanel défini par les déclencheurs de cette action. Par exemple, si un UpdatePanel définit un contrôle bouton et que ce bouton est activé, tous les contrôles UpdatePanel sur cette page seront actualisées par défaut. C’est pourquoi, par défaut, le `UpdateMode` de UpdatePanel est définie sur `Always`. Ou bien, vous pouvez définir la propriété UpdateMode `Conditional`, ce qui signifie que le UpdatePanel est actualisé uniquement si un déclencheur spécifique est atteint.

## <a name="custom-control-notes"></a>Notes de contrôle personnalisé

Un UpdatePanel peut être ajouté à n’importe quel contrôle utilisateur ou un contrôle personnalisé ; Toutefois, sur lequel ces contrôles sont inclus doit également inclure un contrôle ScriptManager avec la propriété EnablePartialRendering **true**.

Unidirectionnel dans lequel vous pouvez en tenir compte lors de l’utilisation de contrôles Web personnalisés consiste à substituer la méthode protégée `CreateChildControls()` méthode de la `CompositeControl` classe. En procédant ainsi, vous pouvez injecter un UpdatePanel entre les enfants du contrôle et le monde extérieur si vous déterminez que la page prend en charge le rendu partiel ; Sinon, vous pouvez simplement superposer les contrôles enfants dans un conteneur `Control` instance.

## <a name="updatepanel-considerations"></a>Considérations de UpdatePanel

UpdatePanel fonctionne comme un élément d’une boîte noire, des publications ASP.NET dans le contexte d’un JavaScript XMLHttpRequest d’habillage. Toutefois, il existe des considérations significative des performances à l’esprit, à la fois en termes de vitesse et de comportement. Pour comprendre le fonctionne de UpdatePanel, afin que vous pouvez décider mieux lorsque son utilisation est appropriée, vous devez examiner l’échange AJAX. L’exemple suivant utilise un site existant et, Mozilla Firefox avec l’extension Firebug (Firebug capture des données de XMLHttpRequest).

Envisagez un formulaire comprenant, entre autres choses, une zone de texte code postal qui est censé pour remplir un champ Ville et l’état sur un formulaire ou un contrôle. Ce formulaire finalement collecte les informations d’appartenance, y compris le nom, adresse et informations de contact d’un utilisateur. Il existe plusieurs considérations de conception à prendre en compte, selon les exigences d’un projet spécifique.


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image11.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image10.png)

([Cliquez pour afficher l’image en taille réelle](understanding-partial-page-updates-with-asp-net-ajax/_static/image12.png))


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image14.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image13.png)

([Cliquez pour afficher l’image en taille réelle](understanding-partial-page-updates-with-asp-net-ajax/_static/image15.png))


Dans l’itération d’origine de cette application, un contrôle a été généré incorporé de l’intégralité des données d’inscription utilisateur, y compris le code postal, ville et état. L’ensemble du contrôle a été encapsulé dans un UpdatePanel et déposé sur un formulaire Web. Lorsque le code postal est entré par l’utilisateur, UpdatePanel détecte l’événement (événement TextChanged correspondant dans le serveur principal, en spécifiant des déclencheurs ou à l’aide de la propriété ChildrenAsTriggers a la valeur true). AJAX valide tous les champs dans UpdatePanel, telles que capturées par FireBug (voir le diagramme à droite).

Comme indiqué dans la capture d’écran, les valeurs de chaque contrôle dans UpdatePanel sont remis (dans ce cas, elles sont toutes vides), ainsi que le champ d’état d’affichage. Plus de 9 Ko de données sont envoyée, quand en fait uniquement cinq octets de données ont été nécessaires pour effectuer cette demande particulière. La réponse est encore plus volumineux : au total, 57 Ko est envoyé au client, simplement pour mettre à jour un champ de texte et un champ de liste déroulante.

Il peut également être intéressant de voir comment ASP.NET AJAX met à jour la présentation. La partie de la réponse de demande de mise à jour de UpdatePanel est indiquée dans l’affichage de la console Firebug à gauche ; Il s’agit un spécialement conçue délimitée par des canaux chaîne qui est divisé par le script client et puis sont assemblés à nouveau sur la page. Plus précisément, ASP.NET AJAX définit le *innerHTML* propriété de l’élément HTML sur le client qui représente votre UpdatePanel. Comme le navigateur génère à nouveau le modèle DOM, il existe un léger retard, selon la quantité d’informations qui doivent être traitée.

La régénération du modèle DOM déclenche un nombre de problèmes supplémentaires :


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image17.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image16.png)

([Cliquez pour afficher l’image en taille réelle](understanding-partial-page-updates-with-asp-net-ajax/_static/image18.png))


- Si l’élément HTML ayant le focus se trouve dans UpdatePanel, il perd le focus. Par conséquent, pour les utilisateurs enfoncée la touche Tab pour quitter la zone de texte code postal, leur destination suivante aurait été la zone de texte Ville. Toutefois, une fois l’actualisation de UpdatePanel l’affichage, le formulaire aurait dû n’est plus le focus, et en appuyant sur Tab a démarré la mise en surbrillance les éléments de la vue (par exemple, les liens).
- Si n’importe quel type de script côté client personnalisé est en cours d’utilisation qu’accède à des éléments DOM, références conservées par les fonctions peut-être devenir obsolète après une publication partielle.

UpdatePanel n’est pas destinées à être fourre-tout solutions. Au lieu de cela, ils fournissent une solution rapide pour certaines situations, notamment la création de prototypes, les mises à jour du contrôle de petite et fournissent une interface familière aux développeurs ASP.NET susceptibles d’être familiarisé avec le modèle objet .NET, mais moins donc avec le DOM. Il existe un nombre d’alternatives qui peut entraîner de meilleures performances, en fonction du scénario d’application :

- Envisagez d’utiliser PageMethods et JSON (JavaScript Object Notation) permet au développeur appeler des méthodes statiques sur une page, comme si un appel de service web a été appelé. Étant donné que les méthodes sont statiques, aucun état n’est nécessaire ; l’appelant de script fournit les paramètres et les résultats sont renvoyés de façon asynchrone.
- Envisagez d’utiliser un Service Web et du JSON si un seul contrôle doit être utilisé dans plusieurs emplacements dans une application. Cela requiert très peu de travail spéciaux et fonctionne de façon asynchrone.

Incorporation de fonctionnalités via les Services Web ou les méthodes de Page présente également des inconvénients. Tout d’abord, les développeurs ASP.NET ont tendance généralement à générer de petits composants de fonctionnalités dans des contrôles utilisateur (fichiers .ascx). Les méthodes de page ne peut pas être hébergés dans ces fichiers. ils doivent être hébergés au sein de la classe de page .aspx réel. De même, les services Web, doivent être hébergés dans la classe .asmx. Selon l’application, cette architecture peut enfreindre le principe de responsabilité unique, dans la mesure où les fonctionnalités d’un composant unique sont désormais réparties sur deux ou plusieurs composants physiques qui peuvent avoir peu ou pas ties cohérents.

Enfin, si une application nécessite qu’UpdatePanel est utilisés, les instructions suivantes doivent utile pour la résolution des problèmes et maintenance.

- **Imbrication UpdatePanel aussi peu que possible, non seulement au sein d’unités, mais également dans des unités de code.** Par exemple, un UpdatePanel sur une Page qui encapsule un contrôle, tandis que ce contrôle contient également un UpdatePanel, qui contient un autre contrôle qui contient un UpdatePanel, est unité entre l’imbrication. Ainsi, reste transparent quels éléments doivent être en cours d’actualisation et empêche les actualisations inattendues aux enfants d’UpdatePanel.
- **Conserver le *ChildrenAsTriggers* propriété la valeur false et définir explicitement le déclenchement d’événements.** Utilisation de la `<Triggers>` collection est un moyen beaucoup plus clair pour gérer des événements et peut empêcher un comportement inattendu, avec les tâches de maintenance et de forcer un développeur d’opter pour un événement.
- **Pour obtenir une fonctionnalité, utilisez la plus petite unité possible.** Comme indiqué dans la discussion du service de code postal, encapsulant qu'uniquement le strict minimum réduit le temps pour le serveur de traitement totale et l’encombrement de l’échange client-serveur, améliorer les performances.

## <a name="the-updateprogress-control"></a>Le contrôle UpdateProgress

## <a name="updateprogress-control-reference"></a>Référence de contrôle UpdateProgress

Propriétés des révisions activée :

| **Nom de propriété** | **Type** | **Description** |
| --- | --- | --- |
| AssociatedUpdate-PanelID | Chaîne | Spécifie l’ID de l’UpdatePanel auquel UpdateProgress doit signaler sur. |
| DisplayAfter | Int | Spécifie le délai d’attente en millisecondes avant que ce contrôle est affiché après le début de la demande asynchrone. |
| DispositionDynamique | bool | Spécifie si la progression est rendue dynamiquement. |

Balisage Descendants :

| **Balise** | **Description** |
| --- | --- |
| &lt;ProgressTemplate&gt; | Contient le modèle de contrôle pour le contenu qui sera affiché à l’aide de ce contrôle. |

Le contrôle UpdateProgress vous donne une mesure de vos commentaires à maintenir l’intérêt de vos utilisateurs tout en effectuant le travail nécessaire au transport pour le serveur. Cela peut permettre à vos utilisateurs de savoir que quelque chose même si elle ne soient pas apparent, en particulier dans la mesure où la plupart des utilisateurs sont utilisés à la page d’actualisation et d’afficher la barre d’état à mettre en surbrillance.

En tant qu’une note, les contrôles UpdateProgress peuvent apparaître n’importe où sur une hiérarchie de la page. Toutefois, dans le cas dans lequel une publication (postback) partielle est lancée à partir d’un enfant UpdatePanel (où un UpdatePanel est imbriqué dans un autre UpdatePanel), publications enfant UpdatePanel entraîne UpdateProgress modèles soient affichés pour l’enfant de déclenchement UpdatePanel ainsi que le parent de UpdatePanel. Toutefois, si le déclencheur est un enfant direct du parent UpdatePanel, seuls les modèles UpdateProgress est associés au parent seront affiche.

## <a name="summary"></a>Résumé

Les extensions Microsoft ASP.NET AJAX sont sophistiquées produits conçus pour vous aider à rendre le contenu web plus accessible et fournir une expérience utilisateur plus riche pour vos applications web. Parmi les Extensions ASP.NET AJAX, les contrôles de rendu de page partielle, y compris les contrôles ScriptManager, UpdatePanel et UpdateProgress sont certains composants plus visibles de la boîte à outils.

Le composant ScriptManager intègre la fourniture du code JavaScript client pour les Extensions, tout en permettant les différents composants côté serveur et côté client afin de fonctionner avec un investissement de développement minimal.

Le contrôle UpdatePanel est la zone magie apparente - balisage dans UpdatePanel peut avoir de code-behind du côté serveur et ne déclenche pas une actualisation de la page. Contrôles UpdatePanel peuvent être imbriqués et peuvent dépendre de contrôles dans un autre UpdatePanel. Par défaut, UpdatePanel gérer toutes les publications (postback), appelée par les contrôles descendants, bien que cette fonctionnalité peut être ajustée, soit de façon déclarative ou par programme.

Lorsque vous utilisez le contrôle UpdatePanel, les développeurs doivent être informés de l’impact des performances qui pourrait éventuellement survenir. Les alternatives possibles incluent les services web et les méthodes de page, bien que la conception de l’application doit être considérée comme.

Le UpdateProgress contrôle permet à l’utilisateur de savoir qu’il n’est pas ignoré, et que la demande favorise se passe lors de la page ne pas faire quelque chose pour répondre à l’entrée d’utilisateur. Il inclut également la possibilité d’annuler les résultats de rendu partiel.

Ensemble, ces outils pour la création d’une expérience utilisateur riche et transparente à effectuer le travail de serveur moins apparent à l’utilisateur et interrompre le flux de travail moins.

## <a name="bio"></a>BIO

Scott caté travaille avec les technologies Microsoft Web depuis 1997 et est le directeur de myKB.com ([www.myKB.com](http://www.myKB.com)) où il est spécialisé dans l’écriture d’ASP.NET en fonction des applications axées sur les solutions logicielles de la Base de connaissances. Scott peut être contacté par courrier électronique en [ scott.cate@myKB.com ](mailto:scott.cate@myKB.com) ou son blog à [ScottCate.com](http://ScottCate.com)

>[!div class="step-by-step"]
[Next](understanding-asp-net-ajax-updatepanel-triggers.md)
