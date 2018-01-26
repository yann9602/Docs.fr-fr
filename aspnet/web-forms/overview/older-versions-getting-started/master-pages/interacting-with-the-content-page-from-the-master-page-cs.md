---
uid: web-forms/overview/older-versions-getting-started/master-pages/interacting-with-the-content-page-from-the-master-page-cs
title: "Interagir avec la Page de contenu à partir de la Page maître (c#) | Documents Microsoft"
author: rick-anderson
description: "Explique comment appeler des méthodes et de définir des propriétés, etc., de la Page de contenu à partir du code dans la Page maître."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/11/2008
ms.topic: article
ms.assetid: 3282df5e-516c-4972-8666-313828b90fb5
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/master-pages/interacting-with-the-content-page-from-the-master-page-cs
msc.type: authoredcontent
ms.openlocfilehash: 4d7f6eeac084f3516ab470adf8973351cf08a7f1
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="interacting-with-the-content-page-from-the-master-page-c"></a>Interagir avec la Page de contenu à partir de la Page maître (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/1/8/4/184e24fa-fcc8-47fa-ac99-4b6a52d41e97/ASPNET_MasterPages_Tutorial_07_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/e/b/4/eb4abb10-c416-4ba4-9899-32577715b1bd/ASPNET_MasterPages_Tutorial_07_CS.pdf)

> Explique comment appeler des méthodes et de définir des propriétés, etc., de la Page de contenu à partir du code dans la Page maître.


## <a name="introduction"></a>Introduction

Le didacticiel précédent examiné comment avoir à la page de contenu d’interagir par programme avec sa page maître. Gardez à l’esprit que nous avons mis à jour la page maître pour inclure un contrôle GridView répertorié les cinq derniers ajouté des produits. Nous avons ensuite créé une page de contenu à partir de laquelle l’utilisateur peut ajouter un nouveau produit. Lors de l’ajout d’un nouveau produit, la page de contenu est nécessaire pour indiquer à la page maître pour actualiser ses GridView afin qu’il inclurait le produit juste ajoutés. Cette fonctionnalité a été obtenue par l’ajout d’une méthode publique à la page maître qu’actualisé les données liées au GridView, puis d’appeler cette méthode à partir de la page de contenu.

La forme la plus commune de contenu et l’interaction de la page maître provient de la page de contenu. Toutefois, il est possible pour la page maître à rouse la page de contenu en cours en action, et cette fonctionnalité peut être nécessaire si la page maître contient les éléments d’interface utilisateur qui permettent aux utilisateurs de modifier les données qui s’affiche également sur la page de contenu. Considérez qu’affiche les informations de produits dans un GridView contrôle et une page maître qui inclut un bouton de contrôle qui, lorsque vous cliquez sur une page de contenu, de double les prix de tous les produits. Comme l’exemple du didacticiel précédent, le contrôle GridView doit être actualisé après le prix double clic sur le bouton afin qu’il affiche le nouveau tarif, mais dans ce scénario, il est la page maître devant rouse la page de contenu dans l’action.

Ce didacticiel explique comment avoir à la page maître appeler la fonctionnalité définie dans la page de contenu.

### <a name="instigating-programmatic-interaction-via-an-event-and-event-handlers"></a>Menant interactions par programme via un événement et des gestionnaires d’événements

L’appel de fonctionnalités de la page de contenu à partir d’une page maître est plus complexe que l’autre sens. Car une page de contenu comporte une seule page maître, lors de l’interaction par programmation à partir de la page de contenu d’origine de celles-ci, nous savons quelles sont les propriétés et les méthodes publiques à notre disposition. Une page maître, toutefois, peut avoir plusieurs pages de contenu différents, chacun avec son propre ensemble de propriétés et méthodes. Comment procéder, puis, pouvons nous écrire du code dans la page maître pour effectuer une action dans sa page de contenu lorsque nous ne savez pas quelle page de contenu sera appelé avant l’exécution ?

Envisagez d’un contrôle Web ASP.NET, tels que le contrôle bouton. Un contrôle bouton peut apparaître sur n’importe quel nombre de pages ASP.NET et a besoin d’un mécanisme par lequel peut informer la page qu’il a cliqué. Pour cela, à l’aide de *événements*. En particulier, le contrôle de bouton déclenche son `Click` événement lorsqu’il est sélectionné ; la page ASP.NET qui contient le bouton peut éventuellement répondre à cette notification via un *Gestionnaire d’événements*.

Ce modèle même peut être utilisé pour accéder à une page maître déclencheur fonctionnalités dans ses pages de contenu :

1. Ajouter un événement à la page maître.
2. Déclenchez l’événement chaque fois que la page maître doit communiquer avec sa page de contenu. Par exemple, si la page maître a besoin de son contenu page des alertes que l’utilisateur a doublé les prix, son événement est déclenché immédiatement après que le prix a été doublé.
3. Créez un gestionnaire d’événements dans les pages de contenu qui doivent intervenir.

Cette suite de ce didacticiel implémente l’exemple décrite dans l’Introduction ; à savoir, une page de contenu qui répertorie les produits dans la base de données et une page maître qui inclut un bouton de contrôle à double les prix.

## <a name="step-1-displaying-products-in-a-content-page"></a>Étape 1 : Affichage des produits dans une Page de contenu

Notre première chose est à créer une page de contenu qui répertorie les produits à partir de la base de données Northwind. (Nous avons ajouté la base de données Northwind au projet du didacticiel précédent, [ *interagir avec la Page maître à partir de la Page de contenu*](interacting-with-the-master-page-from-the-content-page-cs.md).) Commencez par ajouter une nouvelle page ASP.NET pour le `~/Admin` dossier nommé `Products.aspx`, rendant veillez à lier à la `Site.master` page maître. Figure 1 montre l’Explorateur de solutions, une fois que cette page a été ajoutée au site Web.


[![Ajouter une nouvelle Page ASP.NET dans le dossier Admin](interacting-with-the-content-page-from-the-master-page-cs/_static/image2.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image1.png)

**Figure 01**: ajouter une nouvelle Page ASP.NET pour le `Admin` dossier ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image3.png))


N’oubliez pas que dans les [ *spécifiant le titre, les balises Meta et les autres en-têtes HTML dans la Page maître* ](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-cs.md) didacticiel, nous avons créé une classe de page de base personnalisée nommée `BasePage` qui génère le titre de la page si elle n’est pas définir explicitement. Accédez à la `Products.aspx` code-behind de la page de classe et de la dériver de `BasePage` (au lieu d’à partir de `System.Web.UI.Page`).

Enfin, mettre à jour le `Web.sitemap` fichier à inclure une entrée pour cette leçon. Ajoutez le balisage suivant sous la `<siteMapNode>` pour le contenu à la leçon d’Interaction de la Page maître :


[!code-xml[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample1.xml)]

L’ajout de ce `<siteMapNode>` élément est reflété dans les leçons liste (voir Figure 5).

Retour à `Products.aspx`. Dans le contrôle de contenu pour `MainContent`, ajouter un contrôle GridView et nommez-le `ProductsGrid`. Lier le contrôle GridView à un nouveau contrôle SqlDataSource nommé `ProductsDataSource`.


[![Lier le contrôle GridView à un nouveau contrôle SqlDataSource](interacting-with-the-content-page-from-the-master-page-cs/_static/image5.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image4.png)

**Figure 02**: lier le contrôle GridView à un nouveau contrôle SqlDataSource ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image6.png))


Configurer l’Assistant afin qu’il utilise la base de données Northwind. Si vous avez parcouru le didacticiel précédent, vous disposez déjà d’une chaîne de connexion nommée `NorthwindConnectionString` dans `Web.config`. Choisissez cette chaîne de connexion dans la liste déroulante, comme indiqué dans la Figure 3.


[![Configurer le SqlDataSource pour utiliser la base de données Northwind](interacting-with-the-content-page-from-the-master-page-cs/_static/image8.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image7.png)

**Figure 03**: configurer le SqlDataSource pour utiliser la base de données Northwind ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image9.png))


Spécifiez ensuite le contrôle de source de données `SELECT` instruction en choisissant la table Products dans la liste déroulante et en retournant le `ProductName` et `UnitPrice` colonnes (voir Figure 4). Cliquez sur Suivant, puis sur Terminer pour terminer l’Assistant Configurer la Source de données.


[![Retourner le ProductName et UnitPrice champs à partir de la Table Products](interacting-with-the-content-page-from-the-master-page-cs/_static/image11.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image10.png)

**Figure 04**: retourner le `ProductName` et `UnitPrice` des champs la `Products` Table ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image12.png))


C’est tout cela ! Après la fin de l’Assistant Visual Studio ajoute deux BoundFields pour le contrôle GridView à mettre en miroir de deux champs retournés par le contrôle SqlDataSource. Balisage des contrôles GridView et SqlDataSource suit. La figure 5 illustre les résultats lors de l’affichage via un navigateur.


[!code-aspx[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample2.aspx)]


[![Chaque produit et son prix est répertorié dans le contrôle GridView](interacting-with-the-content-page-from-the-master-page-cs/_static/image14.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image13.png)

**Figure 05**: chaque produit et son prix est répertorié dans le GridView ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image15.png))


> [!NOTE]
> N’hésitez pas à nettoyer l’apparence du contrôle GridView. Voici quelques suggestions incluent la mise en forme la valeur de UnitPrice affichée sous forme de devise et de l’utilisation de polices et couleurs d’arrière-plan pour améliorer l’apparence de la grille. Pour plus d’informations sur l’affichage et la mise en forme des données dans ASP.NET, reportez-vous à mon [utilisation de la série de didacticiels données](../../data-access/index.md).


## <a name="step-2-adding-a-double-prices-button-to-the-master-page"></a>Étape 2 : Ajout d’un bouton de prix Double à la Page maître

La tâche suivante consiste à ajouter un contrôle bouton Web au maître de page qui, lorsque vous cliquez dessus, sera double le prix de tous les produits dans la base de données. Ouvrez le `Site.master` page maître et faites glisser un bouton à partir de la boîte à outils vers le concepteur, placer sous le `RecentProductsDataSource` contrôle SqlDataSource que nous avons ajouté dans le didacticiel précédent. Affectez à la `ID` propriété `DoublePrice` et son `Text` propriété « Double les prix des produits ».

Ensuite, ajoutez un contrôle SqlDataSource à la page maître, nommez-la `DoublePricesDataSource`. Cette SqlDataSource servira à exécuter la `UPDATE` instruction en double tous les prix. Plus précisément, nous devons définir son `ConnectionString` et `UpdateCommand` propriétés de la chaîne de connexion appropriée et `UPDATE` instruction. Il faut appeler de ce contrôle SqlDataSource `Update` méthode lors de la `DoublePrice` du bouton. Pour définir le `ConnectionString` et `UpdateCommand` propriétés, sélectionnez le contrôle SqlDataSource et passez à la fenêtre Propriétés. Le `ConnectionString` listes de propriétés de ces chaînes de connexion stockées dans `Web.config` dans une liste déroulante ; choisissez la `NorthwindConnectionString` option comme indiqué dans la Figure 6.


[![Configurer le SqlDataSource pour utiliser le NorthwindConnectionString](interacting-with-the-content-page-from-the-master-page-cs/_static/image17.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image16.png)

**Figure 06**: configurer le SqlDataSource à utiliser le `NorthwindConnectionString` ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image18.png))


Pour définir le `UpdateCommand` propriété, recherchez l’option UpdateQuery dans la fenêtre Propriétés. Lorsqu’elle est sélectionnée, cette propriété affiche un bouton avec des points de suspension ; Cliquez sur ce bouton pour afficher la boîte de dialogue Éditeur de commandes et paramètre indiquée dans la Figure 7. Tapez la commande suivante `UPDATE` instruction dans la zone de texte de la boîte de dialogue :


[!code-sql[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample3.sql)]

Cette instruction, lors de l’exécution, doubler le `UnitPrice` valeur pour chaque enregistrement dans le `Products` table.


[![Définir la propriété de UpdateCommand du SqlDataSource](interacting-with-the-content-page-from-the-master-page-cs/_static/image20.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image19.png)

**Figure 07**: définissez SqlDataSource `UpdateCommand` propriété ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image21.png))


Après avoir défini ces propriétés, balisage déclaratif de vos contrôles bouton et SqlDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample4.aspx)]

Ne reste plus qu’à appeler son `Update` méthode lorsque le `DoublePrice` clic sur le bouton. Créer un `Click` Gestionnaire d’événements pour le `DoublePrice` bouton et ajoutez le code suivant :


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample5.cs)]

Pour tester cette fonctionnalité, visitez le `~/Admin/Products.aspx` page, nous avons créé à l’étape 1 et cliquez sur le bouton « Double prix des produits ». Cliquez sur le bouton provoque une publication (postback) et exécute la `DoublePrice` du bouton `Click` Gestionnaire d’événements, doubler les prix de tous les produits. La page est ensuite nouveau rendue le balisage est renvoyé et ré-affiché dans le navigateur. Le contrôle GridView dans la page de contenu, répertorie Toutefois, les mêmes prix comme avant les « prix de produit « Double clic. Il s’agit, car les données sont initialement chargées dans le GridView avaient son état stocké dans l’état d’affichage, ce qui ne sont pas rechargée lors des publications sauf instruction contraire. Si vous visitez une page différente, puis revenez à la `~/Admin/Products.aspx` page, vous verrez les prix mis à jour.

## <a name="step-3-raising-an-event-when-the-prices-are-doubled"></a>Étape 3 : Le déclenchement d’un événement lorsque le prix sont doublées.

Étant donné que le contrôle GridView dans le `~/Admin/Products.aspx` page ne reflète pas immédiatement le double de prix, un utilisateur peut considérer, qu’ils s’est pas cliquer sur le bouton « Prix des produits Double », ou qu’il n’a pas fonctionné. Ils peuvent essayer d’en cliquant sur le bouton plus quelques heures, doubler les prix fois. Pour y remédier, nous devons disposer de la grille dans le contenu de page affiche le nouveau tarif dès qu’ils sont doublées.

Comme indiqué précédemment dans ce didacticiel, nous avons besoin déclencher un événement dans la page maître chaque fois que l’utilisateur clique sur le `DoublePrice` bouton. Un événement est un moyen d’une seule classe (un éditeur d’événements) à notifier à un autre un ensemble d’autres classes que quelque chose d’intéressant s’est produite (les abonnés aux événements). Dans cet exemple, la page maître est l’éditeur de l’événement. les pages de contenu qui intéressent quand le `DoublePrice` bouton sont les abonnés.

Une classe s’abonne à un événement en créant une *Gestionnaire d’événements*, qui est une méthode qui est exécutée en réponse à l’événement déclenché. Le serveur de publication définit les événements qu’il déclenche en définissant un *délégué d’événement*. Le délégué d’événement spécifie les paramètres d’entrée, le Gestionnaire d’événements doit accepter. Dans le .NET Framework, les délégués d’événements ne pas retourner aucune valeur et accepter deux paramètres d’entrée :

- Un `Object`, qui identifie la source d’événements, et
- Une classe dérivée à partir de`System.EventArgs`

Le second paramètre transmis au gestionnaire d’événements peut inclure des informations supplémentaires sur l’événement. Lors de la base de `EventArgs` classe ne passe pas d’informations, le .NET Framework inclut un nombre de classes qui étendent `EventArgs` et englobe les propriétés supplémentaires. Par exemple, un `CommandEventArgs` instance est passée aux gestionnaires d’événements qui répondent à la `Command` événement et inclut deux propriétés d’information : `CommandArgument` et `CommandName`.

> [!NOTE]
> Pour plus d’informations sur la création de déclenchement et la gestion des événements, consultez [événements et des délégués](https://msdn.microsoft.com/library/17sde2xt.aspx) et [délégués d’événements en anglais Simple](http://www.codeproject.com/KB/cs/eventdelegates.aspx).


Pour définir un événement utilisent la syntaxe suivante :


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample6.cs)]

Étant donné que nous avons besoin uniquement à la page de contenu d’alerte lorsque l’utilisateur a cliqué sur le `DoublePrice` bouton et n’avez pas besoin de les transmettre à d’autres informations supplémentaires, nous pouvons utiliser le délégué d’événement `EventHandler`, qui définit un gestionnaire d’événements qui accepte en tant que le second un objet de type de paramètre `System.EventArgs`. Pour créer l’événement dans la page maître, ajoutez la ligne de code suivante à la classe code-behind de la page maître :


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample7.cs)]

Le code ci-dessus ajoute un événement public à la page maître nommée `PricesDoubled`. Nous devons maintenant déclencher cet événement, une fois que le prix a été doublé. Pour déclencher un événement utilisent la syntaxe suivante :


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample8.cs)]

Où *expéditeur* et *eventArgs* sont les valeurs que vous souhaitez passer au gestionnaire d’événements de l’abonné.

Mise à jour la `DoublePrice` `Click` Gestionnaire d’événements avec le code suivant :


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample9.cs)]

Comme précédemment, le `Click` Gestionnaire d’événements démarre en appelant le `DoublePricesDataSource` du contrôle SqlDataSource `Update` méthode doubler les prix de tous les produits. Après qu’il n’y a deux ajouts au gestionnaire d’événements. Tout d’abord, le `RecentProducts` les données de GridView sont actualisées. Ce contrôle GridView a été ajoutée à la page maître dans le didacticiel précédent et affiche les cinq produits les plus récemment ajoutée. Nous devons actualiser cette grille pour qu’il affiche les prix juste à double pour ces produits de cinq. Après cela, le `PricesDoubled` événement est déclenché. Une référence à la page maître elle-même (`this`) est envoyé au gestionnaire d’événements en tant que la source d’événements et vide `EventArgs` objet est envoyé en tant que les arguments d’événement.

## <a name="step-4-handling-the-event-in-the-content-page"></a>Étape 4 : Gérer l’événement dans la Page de contenu

À ce stade la page maître déclenche son `PricesDoubled` événement chaque fois que le `DoublePrice` contrôle bouton est activé. Toutefois, il s’agit de la moitié seulement du travail - nous devons toujours gérer l’événement dans l’abonné. Cela implique deux étapes : création du Gestionnaire d’événements et en ajoutant le code d’événement câblage afin que lorsque l’événement est déclenché le Gestionnaire d’événements est exécuté.

Commencez par créer un gestionnaire d’événements nommé `Master_PricesDoubled`. En raison de la façon dont nous avons défini le `PricesDoubled` événement dans la page maître deux paramètres de d’entrée du Gestionnaire d’événements doivent être de types `Object` et `EventArgs`, respectivement. Dans l’appel du Gestionnaire d’événements le `ProductsGrid` du GridView `DataBind` méthode lier de nouveau les données à la grille.


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample10.cs)]

Le code du Gestionnaire d’événements est terminé, mais nous avons encore au fil de la page maître `PricesDoubled` événement à ce gestionnaire d’événements. L’abonné relie un événement au gestionnaire d’événements via la syntaxe suivante :


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample11.cs)]

*serveur de publication* est une référence à l’objet qui offre l’événement *eventName*, et *methodName* est le nom du Gestionnaire d’événements défini dans l’abonné qui a une signature correspondante pour le *eventDelegate*. En d’autres termes, si le délégué de l’événement est `EventHandler`, puis *methodName* doit être le nom d’une méthode dans l’abonné qui ne retourne pas de valeur et accepte deux paramètres de types d’entrée `Object` et `EventArgs`, respectivement.

Ce code de câblage d’événement doit être exécuté sur la première visite de page et les publications suivantes et doit se produire à un point dans le cycle de vie de page qui précède lorsque l’événement peut être déclenché. Temps à ajouter du code de câblage d’événement est en phase PreInit, qui se produit très tôt dans le cycle de vie de page.

Ouvrez `~/Admin/Products.aspx` et créer un `Page_PreInit` Gestionnaire d’événements :


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample12.cs)]

Pour exécuter ce code câblage nous avons besoin d’une référence de programmation à la page maître à partir de la page de contenu. Comme indiqué dans le didacticiel précédent, il existe deux manières de procéder :

- En effectuant un cast de le faiblement typée `Page.Master` propriété au type de page maître approprié, ou
- En ajoutant un `@MasterType` directive dans le `.aspx` page et l’utilisation de la fortement typée `Master` propriété.

Nous allons utiliser l’approche de ce dernier. Ajoutez le code suivant `@MasterType` directive au début du balisage déclaratif de la page :


[!code-aspx[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample13.aspx)]

Puis ajoutez le code de câblage d’événement suivant dans le `Page_PreInit` Gestionnaire d’événements :


[!code-csharp[Main](interacting-with-the-content-page-from-the-master-page-cs/samples/sample14.cs)]

Avec ce code en place, le contrôle GridView dans la page de contenu est actualisé chaque fois que le `DoublePrice` du bouton.

Les chiffres 8 et 9 illustrent ce comportement. La figure 8 illustre la page visitée en premier. Notez que les valeurs de prix à la fois dans le `RecentProducts` GridView (dans la colonne de gauche de la page maître) et le `ProductsGrid` GridView (dans la page de contenu). Montre la figure 9 le même écran immédiatement après le `DoublePrice` a cliqué. Comme vous pouvez le voir, les nouveaux prix sont instantanément répercutées dans les deux contrôles GridView.


[![Les valeurs de prix Initial](interacting-with-the-content-page-from-the-master-page-cs/_static/image23.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image22.png)

**Figure 08**: les valeurs initiales de prix ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image24.png))


[![Les prix Just-Doubled sont affichés dans les contrôles GridView](interacting-with-the-content-page-from-the-master-page-cs/_static/image26.png)](interacting-with-the-content-page-from-the-master-page-cs/_static/image25.png)

**Figure 09**: The Just-Doubled prix sont affichés dans les contrôles GridView ([cliquez pour afficher l’image en taille réelle](interacting-with-the-content-page-from-the-master-page-cs/_static/image27.png))


## <a name="summary"></a>Récapitulatif

Dans l’idéal, une page maître et ses pages de contenu sont complètement séparées les unes des autres et ne nécessitent aucun niveau d’interaction. Toutefois, si vous avez une page maître ou la page de contenu qui affiche les données qui peuvent être modifiées à partir de la page maître ou la page de contenu, puis vous devrez peut-être pour que la page maître à la page de contenu (ou inversement a) d’alerte lorsque les données sont modifiées afin que l’affichage peut être mis à jour. Dans le didacticiel précédent, nous avons vu comment avoir une page de contenu d’interagir par programme avec sa page maître ; Dans ce didacticiel, nous avons étudié comment : lancer une page maître l’interaction.

Lors de la programmation interaction entre un contenu et de la page maître peut provenir de contenu ou la page maître, le modèle d’interaction utilisé dépend de l’origine. Les différences sont dû au fait qu’une page de contenu a une seule page maître, mais une page maître peut avoir plusieurs pages de contenu. Au lieu d’avoir une page maître interagir directement avec une page de contenu, une meilleure approche consiste à avoir à la page maître de déclencher un événement pour signaler qu’une action a eu lieu. Ces pages de contenu qui intéressent l’action peuvent créer des gestionnaires d’événements.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [L’accès et la mise à jour des données dans ASP.NET](http://aspnet.4guysfromrolla.com/articles/011106-1.aspx)
- [Événements et délégués](https://msdn.microsoft.com/library/17sde2xt.aspx)
- [Passage des informations entre le contenu et les Pages maîtres](http://aspnet.4guysfromrolla.com/articles/013107-1.aspx)
- [Utilisation des données dans les didacticiels ASP.NET](../../data-access/index.md)

### <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de plusieurs manuels ASP/ASP.NET et de créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 3.5 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672329972/4guysfromrollaco). Scott peut être atteint à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Suchi Banerjee. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](interacting-with-the-master-page-from-the-content-page-cs.md)
[Suivant](master-pages-and-asp-net-ajax-cs.md)
