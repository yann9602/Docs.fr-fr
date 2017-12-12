---
uid: web-forms/overview/ajax-control-toolkit/cascadingdropdown/using-cascadingdropdown-with-a-database-vb
title: "À l’aide de CascadingDropDown avec une base de données (VB) | Documents Microsoft"
author: wenz
description: "Le contrôle CascadingDropDown dans la boîte à outils de contrôle AJAX étend un contrôle DropDownList afin que les modifications apportées à une charge de DropDownList associés à des valeurs dans anoth..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 97a3d33c-c856-43f3-8acb-f1ccbc48221a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/cascadingdropdown/using-cascadingdropdown-with-a-database-vb
msc.type: authoredcontent
ms.openlocfilehash: 65b9a499dd9b500338ccdb90e56b742ff50a1024
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-cascadingdropdown-with-a-database-vb"></a>À l’aide de CascadingDropDown avec une base de données (Visual Basic)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/0/7/907760b1-2c60-4f81-aeb6-ca416a573b0d/cascadingdropdown1.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/cascadingdropdown1VB.pdf)

> Le contrôle CascadingDropDown dans la boîte à outils de contrôle AJAX étend un contrôle DropDownList afin que les modifications apportées à une charge de DropDownList associés à des valeurs dans une autre DropDownList. Pour que cela fonctionne, un service web spéciale doit être créé.


## <a name="overview"></a>Vue d'ensemble

Le contrôle CascadingDropDown dans la boîte à outils de contrôle AJAX étend un contrôle DropDownList afin que les modifications apportées à une charge de DropDownList associés à des valeurs dans une autre DropDownList. (Par exemple, une seule liste fournit une liste des États, et la liste suivante est ensuite remplie de grandes villes dans cet état.) Pour que cela fonctionne, un service web spéciale doit être créé.

## <a name="steps"></a>Étapes

Tout d’abord, une source de données est requise. Cet exemple utilise la base de données AdventureWorks et Microsoft SQL Server 2005 Express Edition. La base de données est une partie facultative d’une installation de Visual Studio (y compris l’édition expresse) et est également disponible en tant que téléchargement séparé sous [https://go.microsoft.com/fwlink/?LinkId=64064](https://go.microsoft.com/fwlink/?LinkId=64064). La base de données AdventureWorks fait partie des exemples de SQL Server 2005 et exemples de bases de données (téléchargement à l’adresse [https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang = fr](https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang=en)). Pour configurer la base de données le plus simple consiste à utiliser le Microsoft SQL Server Management Studio Express ([https://www.microsoft.com/downloads/details.aspx? FamilyID = c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang = fr](https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang=en)) et d’attacher le `AdventureWorks.mdf` le fichier de base de données.

Pour cet exemple, nous partons du principe que l’instance de SQL Server 2005 Express Edition est appelée `SQLEXPRESS` et réside sur le même ordinateur que le serveur web ; il s’agit également de la configuration par défaut. Si votre programme d’installation est différente, vous devez adapter les informations de connexion pour la base de données.

Pour activer la fonctionnalité d’ASP.NET AJAX et les outils de contrôle, le `ScriptManager` contrôle doit être placé n’importe où sur la page (mais dans le &lt; `form` &gt; élément) :

[!code-aspx[Main](using-cascadingdropdown-with-a-database-vb/samples/sample1.aspx)]

Dans l’étape suivante, deux contrôles DropDownList sont requis. Dans cet exemple, nous utilisons ces informations de fournisseur et contact d’AdventureWorks, par conséquent, nous créons une liste pour les fournisseurs disponibles et l’autre pour les contacts disponibles :

[!code-aspx[Main](using-cascadingdropdown-with-a-database-vb/samples/sample2.aspx)]

Ensuite, deux CascadingDropDown extendeurs doivent être ajoutés à la page. Un remplit la première liste (fournisseurs), et le remplit la deuxième liste (contacts). Les attributs suivants doivent être définis :

- `ServicePath`: URL du service web fournir les entrées de liste
- `ServiceMethod`: Méthode web fournir les entrées de liste
- `TargetControlID`: ID de la liste déroulante
- `Category`: Les informations de catégorie qui sont soumises à la méthode web lorsqu’elle est appelée
- `PromptText`: Texte à afficher lorsque le chargement asynchrone des données de liste à partir du serveur
- `ParentControlID`: (facultatif) parent liste déroulante que le chargement de déclencheurs de la liste actuelle

Selon le langage de programmation utilisé, le nom du service web en question change, mais toutes les autres valeurs d’attribut sont les mêmes. Voici l’élément CascadingDropDown pour la première liste déroulante :

[!code-aspx[Main](using-cascadingdropdown-with-a-database-vb/samples/sample3.aspx)]

Les extensions de contrôle pour la deuxième liste doivent définir le `ParentControlID` attribut afin que la sélection d’une entrée dans les déclencheurs de liste de fournisseurs du chargement des éléments dans la liste de contacts associés.

[!code-aspx[Main](using-cascadingdropdown-with-a-database-vb/samples/sample4.aspx)]

Le travail réel est ensuite effectué dans le service web, qui est configuré comme suit. Notez que le `[ScriptService]` attribut est utilisé, sinon ASP.NET AJAX ne peut pas créer le proxy JavaScript pour accéder aux méthodes web à partir du code de script côté client.

[!code-aspx[Main](using-cascadingdropdown-with-a-database-vb/samples/sample5.aspx)]

La signature des méthodes web appelée par CascadingDropDown est comme suit :

[!code-vb[Main](using-cascadingdropdown-with-a-database-vb/samples/sample6.vb)]

La valeur de retour doit être un tableau de type `CascadingDropDownNameValue` qui est défini par la boîte à outils de contrôle. Le `GetVendors()` méthode est très facile à implémenter : le code se connecte à la base de données AdventureWorks et interroge les 25 premières fournisseurs. Le premier paramètre de la `CascadingDropDownNameValue` constructeur est la valeur est la légende de l’entrée de la liste, l’autre (attribut de valeur dans du HTML &lt; `option` &gt; élément). Voici le code :

[!code-vb[Main](using-cascadingdropdown-with-a-database-vb/samples/sample7.vb)]

Mise en route les contacts associés pour un fournisseur (nom de la méthode : `GetContactsForVendor()`) est un peu plus complexe. Tout d’abord, le fournisseur qui a été sélectionné dans la première liste déroulante doit être déterminé. La boîte à outils de contrôle définit une méthode d’assistance pour cette tâche : le `ParseKnownCategoryValuesString()` méthode retourne un `StringDictionary` élément avec les données de liste déroulante :

[!code-vb[Main](using-cascadingdropdown-with-a-database-vb/samples/sample8.vb)]

Pour des raisons de sécurité, ces données doivent être validées tout d’abord. Par conséquent, s’il existe une entrée de fournisseur (étant donné que la `Category` du premier élément CascadingDropDown est définie sur `"Vendor"`), l’ID du fournisseur sélectionné peut-être être récupérée :

[!code-vb[Main](using-cascadingdropdown-with-a-database-vb/samples/sample9.vb)]

Le reste de la méthode est assez simple, puis. CODE du fournisseur est utilisé en tant que paramètre pour une requête SQL qui Récupère tous les contacts associés pour ce fournisseur. Une fois encore, la méthode retourne un tableau de type `CascadingDropDownNameValue`.

[!code-vb[Main](using-cascadingdropdown-with-a-database-vb/samples/sample10.vb)]

Chargement de la page ASP.NET, et après une courte période, la liste des fournisseurs est remplie avec 25 entrées. Choisir une seule entrée et notez la façon dont la seconde liste déroulante est remplie avec des données.


[![La première liste est automatiquement renseignée.](using-cascadingdropdown-with-a-database-vb/_static/image2.png)](using-cascadingdropdown-with-a-database-vb/_static/image1.png)

La première liste est automatiquement ([cliquez pour afficher l’image en taille réelle](using-cascadingdropdown-with-a-database-vb/_static/image3.png))


[![La deuxième liste est remplie selon votre sélection dans la première liste](using-cascadingdropdown-with-a-database-vb/_static/image5.png)](using-cascadingdropdown-with-a-database-vb/_static/image4.png)

La deuxième liste est remplie selon votre sélection dans la première liste ([cliquez pour afficher l’image en taille réelle](using-cascadingdropdown-with-a-database-vb/_static/image6.png))

>[!div class="step-by-step"]
[Précédent](filling-a-list-using-cascadingdropdown-vb.md)
[Suivant](presetting-list-entries-with-cascadingdropdown-vb.md)
