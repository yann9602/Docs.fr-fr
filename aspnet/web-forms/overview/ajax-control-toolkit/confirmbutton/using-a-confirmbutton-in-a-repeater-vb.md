---
uid: web-forms/overview/ajax-control-toolkit/confirmbutton/using-a-confirmbutton-in-a-repeater-vb
title: "À l’aide d’un ConfirmButton dans un répéteur (VB) | Documents Microsoft"
author: wenz
description: "L’extendeur ConfirmButton dans la boîte à outils de contrôle AJAX crée Oui/pas de fenêtre contextuelle lorsque l’utilisateur clique sur un bouton (y compris contrôle LinkButton). Uniquement si Oui est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 18c31709-3f9d-4d93-8b01-f1356bf610b4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/confirmbutton/using-a-confirmbutton-in-a-repeater-vb
msc.type: authoredcontent
ms.openlocfilehash: 5da8491c157ad6f35c2c25803680f262a35ce6e1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-a-confirmbutton-in-a-repeater-vb"></a>À l’aide d’un ConfirmButton dans un répéteur (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/8/6/d/86dea6c6-bb92-4fa6-aa14-f8c0f82100f5/ConfirmButton1.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/confirmbutton1VB.pdf)

> L’extendeur ConfirmButton dans la boîte à outils de contrôle AJAX crée Oui/pas de fenêtre contextuelle lorsque l’utilisateur clique sur un bouton (y compris contrôle LinkButton). Uniquement si l’utilisateur est cliqué sur Oui, l’action du bouton est exécutée, sinon annulée. Cela est également possible dans un répéteur.


## <a name="overview"></a>Vue d'ensemble

L’extendeur ConfirmButton dans la boîte à outils de contrôle AJAX crée Oui/pas de fenêtre contextuelle lorsque l’utilisateur clique sur un bouton (y compris contrôle LinkButton). Uniquement si l’utilisateur est cliqué sur Oui, l’action du bouton est exécutée, sinon annulée. Cela est également possible dans un répéteur.

## <a name="steps"></a>Étapes

Tout d’abord, une source de données est requise. Cet exemple utilise la base de données AdventureWorks et Microsoft SQL Server 2005 Express Edition. La base de données est une partie facultative d’une installation de Visual Studio (y compris l’édition expresse) et est également disponible en tant que téléchargement séparé sous [https://go.microsoft.com/fwlink/?LinkId=64064](https://go.microsoft.com/fwlink/?LinkId=64064). La base de données AdventureWorks fait partie des exemples de SQL Server 2005 et exemples de bases de données (téléchargement à l’adresse [https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang = fr](https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang=en)). Pour configurer la base de données le plus simple consiste à utiliser le Microsoft SQL Server Management Studio Express ([https://www.microsoft.com/downloads/details.aspx? FamilyID = c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang = fr](https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang=en)) et d’attacher le `AdventureWorks.mdf` le fichier de base de données.

Pour cet exemple, nous partons du principe que l’instance de SQL Server 2005 Express Edition est appelée `SQLEXPRESS` et réside sur le même ordinateur que le serveur web ; il s’agit également de la configuration par défaut. Si votre programme d’installation est différente, vous devez adapter les informations de connexion pour la base de données.

Pour activer la fonctionnalité d’ASP.NET AJAX et les outils de contrôle, le `ScriptManager` contrôle doit être placé n’importe où sur la page (mais dans le `<form>` élément) :

[!code-aspx[Main](using-a-confirmbutton-in-a-repeater-vb/samples/sample1.aspx)]

Ensuite, une source de données est nécessaire. Par souci de simplicité, uniquement les cinq premières entrées dans la table de fournisseurs AdventureWorks sont récupérées. Notez que lors de l’utilisation de l’Assistant Visual Studio pour créer la source de données, le nom de table (`Vendors`) est actuellement pas correctement préfixés avec `Purchasing`. Le balisage suivant est correct :

[!code-aspx[Main](using-a-confirmbutton-in-a-repeater-vb/samples/sample2.aspx)]

Cette source de données peut ensuite servir dans un répéteur. Comme d’habitude, le `DataBinder.Eval()` méthode récupère des données à partir de la source de données. Le `ConfirmButtonExtender` contrôle puis doit être placé dans la `<ItemTemplate>` section du répéteur afin qu’elle s’affiche pour chaque entrée de la source de données.

[!code-aspx[Main](using-a-confirmbutton-in-a-repeater-vb/samples/sample3.aspx)]


[![Le bouton de confirmation s’affiche en regard de chaque entrée de la source de données](using-a-confirmbutton-in-a-repeater-vb/_static/image2.png)](using-a-confirmbutton-in-a-repeater-vb/_static/image1.png)

Le bouton de confirmation s’affiche en regard de chaque entrée de la source de données ([cliquez pour afficher l’image en taille réelle](using-a-confirmbutton-in-a-repeater-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](using-a-confirmbutton-in-a-repeater-cs.md)
