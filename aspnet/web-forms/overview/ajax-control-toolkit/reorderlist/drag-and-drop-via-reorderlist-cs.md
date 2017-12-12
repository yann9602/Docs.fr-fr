---
uid: web-forms/overview/ajax-control-toolkit/reorderlist/drag-and-drop-via-reorderlist-cs
title: "Faites glisser et déposez via ReorderList fournissant (c#) | Documents Microsoft"
author: wenz
description: "Le contrôle ReorderList fournissant dans la boîte à outils de contrôle AJAX fournit une liste qui peut être réorganisée par l’utilisateur via la fonction glisser- déposer. L’ordre actuel de la liste est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 6350ee8e-11d6-4aff-b51c-942878014835
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/reorderlist/drag-and-drop-via-reorderlist-cs
msc.type: authoredcontent
ms.openlocfilehash: 6afecfc7330647e6f4944c507e308afec6d2401b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="drag-and-drop-via-reorderlist-c"></a>Faites glisser et déposez via ReorderList fournissant (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/ReorderList5.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/reorderlist5CS.pdf)

> Le contrôle ReorderList fournissant dans la boîte à outils de contrôle AJAX fournit une liste qui peut être réorganisée par l’utilisateur via la fonction glisser- déposer. L’ordre actuel de la liste doit être conservé sur le serveur.


## <a name="overview"></a>Vue d'ensemble

Le `ReorderList` contrôle dans la boîte à outils de contrôle AJAX fournit une liste qui peut être réorganisée par l’utilisateur via la fonction glisser- déposer. L’ordre actuel de la liste doit être conservé sur le serveur.

## <a name="steps"></a>Étapes

Le `ReorderList` contrôle prend en charge la liaison de données à partir d’une base de données à la liste. De plus, il prend également en charge l’écriture des modifications à l’ordre de l’élément de liste dans le magasin de données.

Cet exemple utilise Microsoft SQL Server 2005 Express Edition en tant que le magasin de données. La base de données est une partie facultative (et gratuite) d’une installation de Visual Studio, y compris l’édition expresse. Il est également disponible en téléchargement séparé sous [https://go.microsoft.com/fwlink/?LinkId=64064](https://go.microsoft.com/fwlink/?LinkId=64064). Pour cet exemple, nous partons du principe que l’instance de SQL Server 2005 Express Edition est appelée `SQLEXPRESS` et réside sur le même ordinateur que le serveur web ; il s’agit également de la configuration par défaut. Si votre programme d’installation est différente, vous devez adapter les informations de connexion pour la base de données.

Pour configurer la base de données le plus simple consiste à utiliser le Microsoft SQL Server Management Studio Express ([https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang = fr](https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang=en) ). Se connecter au serveur, double-cliquez sur `Databases` et créer une base de données (avec le bouton droit et choisissez `New Database`) appelée `Tutorials`.

Dans cette base de données, créez une nouvelle table appelée `AJAX` avec les quatre colonnes suivantes :

- `id`(entier de clés, primaire, identité, non NULL)
- `char`(char (1), valeur NULL)
- `description`(varchar (50), valeur NULL)
- `position`(int, NULL)


[![La disposition de la table AJAX](drag-and-drop-via-reorderlist-cs/_static/image2.png)](drag-and-drop-via-reorderlist-cs/_static/image1.png)

La disposition de la table AJAX ([cliquez pour afficher l’image en taille réelle](drag-and-drop-via-reorderlist-cs/_static/image3.png))


Ensuite, remplir la table avec deux valeurs. Notez que la `position` colonne contient l’ordre de tri des éléments.


[![Les données initiales dans la table AJAX](drag-and-drop-via-reorderlist-cs/_static/image5.png)](drag-and-drop-via-reorderlist-cs/_static/image4.png)

Les données initiales dans la table AJAX ([cliquez pour afficher l’image en taille réelle](drag-and-drop-via-reorderlist-cs/_static/image6.png))


L’étape suivante a besoin pour générer un `SqlDataSource` contrôle de communiquer avec la nouvelle base de données et sa table. La source de données doit prendre en charge la `SELECT` et `UPDATE` des commandes SQL. Lorsque l’ordre des éléments de liste est modifié, le `ReorderList` contrôle envoie automatiquement des deux valeurs à la source de données `Update` commande : la nouvelle position et l’ID de l’élément. Par conséquent, la source de données doit un `<UpdateParameters>` section pour ces deux valeurs :

[!code-aspx[Main](drag-and-drop-via-reorderlist-cs/samples/sample1.aspx)]

Le `ReorderList` contrôle doit définir les attributs suivants :

- `AllowReorder`: Indique si les éléments de liste peuvent être réorganisées
- `DataSourceID`: L’ID de la source de données
- `DataKeyField`: Le nom de la colonne de clé primaire dans la source de données
- `SortOrderField`: La colonne de source de données qui fournit l’ordre de tri pour les éléments de liste

Dans le `<DragHandleTemplate>` et `<ItemTemplate>` sections, la disposition de la liste peut être ajustée. En outre, la liaison de données est possible à l’aide de la `Eval()` méthode, comme illustré ci-après :

[!code-aspx[Main](drag-and-drop-via-reorderlist-cs/samples/sample2.aspx)]

Informations de style CSS suivantes (référencé dans le `<DragHandleTemplate>` section de la `ReorderList` contrôle) permet de s’assurer que le pointeur de la souris change en conséquence lorsqu’il pointe sur la poignée de déplacement :

[!code-css[Main](drag-and-drop-via-reorderlist-cs/samples/sample3.css)]

Enfin, un `ScriptManager` contrôle initialise ASP.NET AJAX pour la page :

[!code-aspx[Main](drag-and-drop-via-reorderlist-cs/samples/sample4.aspx)]

Exécuter cet exemple dans le navigateur et réorganiser les éléments de liste un peu. Ensuite, rechargez la page et/ou un coup de œil à la base de données. Les positions modifiées ont été conservées et sont également reflétées par les valeurs de la `position` colonne dans la base de données et que tout sans code, juste à l’aide de balisage.


[![Les données dans les modifications de base de données en fonction de la nouvelle commande d’élément de liste](drag-and-drop-via-reorderlist-cs/_static/image8.png)](drag-and-drop-via-reorderlist-cs/_static/image7.png)

Élément de données dans les modifications de base de données en fonction de la nouvelle liste d’ordre ([cliquez pour afficher l’image en taille réelle](drag-and-drop-via-reorderlist-cs/_static/image9.png))

>[!div class="step-by-step"]
[Précédent](using-postbacks-with-reorderlist-cs.md)
[Suivant](using-postbacks-with-reorderlist-vb.md)
