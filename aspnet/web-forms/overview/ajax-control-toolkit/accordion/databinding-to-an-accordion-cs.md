---
uid: web-forms/overview/ajax-control-toolkit/accordion/databinding-to-an-accordion-cs
title: "Liaison de données à Accordion (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle Accordéon dans la boîte à outils de contrôle AJAX fournit plusieurs volets et permet à l’utilisateur afficher l’un d'entre eux à la fois. Panneaux est généralement déclaré w..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 9c8f0054-e319-46f8-80c0-35b606d2fbd4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/accordion/databinding-to-an-accordion-cs
msc.type: authoredcontent
ms.openlocfilehash: a8250f58655b8fe8638d8e7a7b084ee9c33fe986
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="databinding-to-an-accordion-c"></a>Liaison de données à Accordion (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/5/6/d/56d50cef-2011-4c8f-9891-7edc6dc57df9/Accordion1.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/accordion1CS.pdf)

> Le contrôle Accordéon dans la boîte à outils de contrôle AJAX fournit plusieurs volets et permet à l’utilisateur afficher l’un d'entre eux à la fois. Panneaux est généralement déclaré dans la page elle-même, mais la liaison à une source de données offre davantage de souplesse.


## <a name="overview"></a>Vue d'ensemble

Le contrôle Accordéon dans la boîte à outils de contrôle AJAX fournit plusieurs volets et permet à l’utilisateur afficher l’un d'entre eux à la fois. Panneaux est généralement déclaré dans la page elle-même, mais la liaison à une source de données offre davantage de souplesse.

## <a name="steps"></a>Étapes

Tout d’abord, une source de données est requise. Cet exemple utilise la base de données AdventureWorks et Microsoft SQL Server 2005 Express Edition. La base de données est une partie facultative d’une installation de Visual Studio (y compris l’édition expresse) et est également disponible en tant que téléchargement séparé sous [https://go.microsoft.com/fwlink/?LinkId=64064](https://go.microsoft.com/fwlink/?LinkId=64064). La base de données AdventureWorks fait partie des exemples de SQL Server 2005 et exemples de bases de données (téléchargement à l’adresse [https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang = fr](https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang=en)). Pour configurer la base de données le plus simple consiste à utiliser le Microsoft SQL Server Management Studio Express ([https://www.microsoft.com/downloads/details.aspx? FamilyID = c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang = fr](https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang=en)) et d’attacher le `AdventureWorks.mdf` le fichier de base de données.

Pour cet exemple, nous partons du principe que l’instance de SQL Server 2005 Express Edition est appelée `SQLEXPRESS` et réside sur le même ordinateur que le serveur web ; il s’agit également de la configuration par défaut. Si votre programme d’installation est différente, vous devez adapter les informations de connexion pour la base de données.

Pour activer la fonctionnalité d’ASP.NET AJAX et les outils de contrôle, le `ScriptManager` contrôle doit être placé n’importe où sur la page (mais dans le `<form>` élément) :

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample1.aspx)]

Ensuite, ajoutez une source de données à la page. Pour utiliser une quantité limitée de données, nous allons sélectionner uniquement les cinq premières entrées dans la table du fournisseur de la base de données AdventureWorks. Si vous utilisez l’assistant de Visual Studio pour créer la source de données, à l’esprit qu’un bogue dans la version actuelle ne précède pas le nom de table (`Vendor`) avec `Purchasing`. Le balisage suivant illustre la syntaxe correcte :

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample2.aspx)]

N’oubliez pas de nom (ID) de la source de données. Cette identification très doit ensuite être utilisée dans le `DataSourceID` propriété du contrôle Accordéon :

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample3.aspx)]

Dans le contrôle Accordéon, vous pouvez fournir des modèles pour les différentes parties du contrôle, y compris l’en-tête (`<HeaderTemplate>`) et le contenu (`<ContentTemplate>`). Au sein de ces éléments, de sortie uniquement les données à partir des données source, à l’aide de la `DataBinder.Eval()` méthode :

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample4.aspx)]

Lorsque la page est chargée, la source de données doit être liée à l’accordéon par le code côté serveur :

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample5.aspx)]

Pour conclure cet exemple, vous devez définir les deux classes CSS qui sont référencés dans le contrôle Accordéon (dans ses propriétés `HeaderCssClass` et `ContentCssClass`). Placez le balisage suivant dans la `<head>` section de la page :

[!code-css[Main](databinding-to-an-accordion-cs/samples/sample6.css)]


[![Les données dans l’accordéon proviennent directement à partir de la source de données](databinding-to-an-accordion-cs/_static/image2.png)](databinding-to-an-accordion-cs/_static/image1.png)

Les données dans l’accordéon proviennent directement à partir de la source de données ([cliquez pour afficher l’image en taille réelle](databinding-to-an-accordion-cs/_static/image3.png))

>[!div class="step-by-step"]
[Next](dynamically-adding-an-accordion-pane-cs.md)
