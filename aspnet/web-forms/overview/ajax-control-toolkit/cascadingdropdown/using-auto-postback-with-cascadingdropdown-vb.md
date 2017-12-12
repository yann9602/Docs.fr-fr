---
uid: web-forms/overview/ajax-control-toolkit/cascadingdropdown/using-auto-postback-with-cascadingdropdown-vb
title: "À l’aide de la publication automatique CascadingDropDown (VB) | Documents Microsoft"
author: wenz
description: "Le contrôle CascadingDropDown dans la boîte à outils de contrôle AJAX étend un contrôle DropDownList afin que les modifications apportées à une charge de DropDownList associés à des valeurs dans anoth..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 0b34f7f6-a0cc-4b9f-9761-643fb0bb3ece
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/cascadingdropdown/using-auto-postback-with-cascadingdropdown-vb
msc.type: authoredcontent
ms.openlocfilehash: f8059f44b4efbf59ebe7b3d2fd5400e886642a90
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-auto-postback-with-cascadingdropdown-vb"></a>À l’aide de la publication automatique CascadingDropDown (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/0/7/907760b1-2c60-4f81-aeb6-ca416a573b0d/cascadingdropdown3.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/cascadingdropdown3VB.pdf)

> Le contrôle CascadingDropDown dans la boîte à outils de contrôle AJAX étend un contrôle DropDownList afin que les modifications apportées à une charge de DropDownList associés à des valeurs dans une autre DropDownList. Toutefois lorsque le contrôle CascadingDropDown, ASP. Fonctionnalité de AutoPostBack du contrôle de NET DropDownList ne fonctionne pas, étant donné que le chargement asynchrone de données dans la liste génère une publication (inutile) lui-même. Avec du code JavaScript, vous pouvez éviter cet effet.


## <a name="overview"></a>Vue d'ensemble

Le contrôle CascadingDropDown dans la boîte à outils de contrôle AJAX étend un contrôle DropDownList afin que les modifications apportées à une charge de DropDownList associés à des valeurs dans une autre DropDownList. (Par exemple, une seule liste fournit une liste des États, et la liste suivante est ensuite remplie de grandes villes dans cet état.) Toutefois lorsque le contrôle CascadingDropDown, ASP. Fonctionnalité de AutoPostBack du contrôle de NET DropDownList ne fonctionne pas, étant donné que le chargement asynchrone de données dans la liste génère une publication (inutile) lui-même. Avec du code JavaScript, vous pouvez éviter cet effet.

## <a name="steps"></a>Étapes

Pour activer la fonctionnalité d’ASP.NET AJAX et les outils de contrôle, le `ScriptManager` contrôle doit être placé n’importe où sur la page (mais dans le &lt; `form` &gt; élément) :

[!code-aspx[Main](using-auto-postback-with-cascadingdropdown-vb/samples/sample1.aspx)]

Ensuite, un contrôle DropDownList est nécessaire :

[!code-aspx[Main](using-auto-postback-with-cascadingdropdown-vb/samples/sample2.aspx)]

Pour cette liste, un extendeur CascadingDropDown est ajouté, qui fournit des informations de URL et la méthode de service web :

[!code-aspx[Main](using-auto-postback-with-cascadingdropdown-vb/samples/sample3.aspx)]

L’extendeur CascadingDropDown puis asynchrone appelle un service web avec la signature de méthode suivante :

[!code-vb[Main](using-auto-postback-with-cascadingdropdown-vb/samples/sample4.vb)]

La méthode retourne un tableau de type CascadingDropDown valeur. Le constructeur du type attend la première légende de l’entrée liste, puis la valeur (HTML `value` attribut).

[!code-aspx[Main](using-auto-postback-with-cascadingdropdown-vb/samples/sample5.aspx)]

Chargement de la page dans le navigateur remplit la liste déroulante avec trois fournisseurs, l’autre est présélectionnée. En outre, ASP.NET définit la `__doPostBack()` méthode JavaScript. Une fois que la page a été chargée, cet appel JavaScript est ajouté à la liste déroulante, mais uniquement si des éléments qu’il contient. S’il n’y a aucun éléments dans la liste, la boîte à outils de contrôle est actuellement leur chargement, afin que le code JavaScript utilise un délai d’attente et tente à nouveau une demi-seconde.

[!code-html[Main](using-auto-postback-with-cascadingdropdown-vb/samples/sample6.html)]

De cette manière, une publication (postback) est exécutée uniquement lorsqu’il existe réellement des éléments dans la liste et l’utilisateur sélectionne une entrée.


[![Sélection d’un élément de liste entraîne une publication](using-auto-postback-with-cascadingdropdown-vb/_static/image2.png)](using-auto-postback-with-cascadingdropdown-vb/_static/image1.png)

Sélection d’un élément de liste entraîne une publication ([cliquez pour afficher l’image en taille réelle](using-auto-postback-with-cascadingdropdown-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](presetting-list-entries-with-cascadingdropdown-vb.md)
