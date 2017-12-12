---
uid: web-forms/overview/data-access/editing-inserting-and-deleting-data/adding-client-side-confirmation-when-deleting-cs
title: "Ajout de Confirmation côté Client lors de la suppression (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans les interfaces que nous avons créé jusqu'à présent, un utilisateur peut supprimer accidentellement des données en cliquant sur le bouton de suppression lorsqu’ils destinée à cliquer sur le bouton Modifier. Dans ce t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/17/2006
ms.topic: article
ms.assetid: f6e2a12a-2b5e-48fd-8db3-1e94a500c19a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/editing-inserting-and-deleting-data/adding-client-side-confirmation-when-deleting-cs
msc.type: authoredcontent
ms.openlocfilehash: c5e8ee76224a48d3132597016b81099bd70a1776
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-client-side-confirmation-when-deleting-c"></a>Ajout de Confirmation côté Client lors de la suppression (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger l’exemple d’application](http://download.microsoft.com/download/9/c/1/9c1d03ee-29ba-4d58-aa1a-f201dcc822ea/ASPNET_Data_Tutorial_22_CS.exe) ou [télécharger le PDF](adding-client-side-confirmation-when-deleting-cs/_static/datatutorial22cs1.pdf)

> Dans les interfaces que nous avons créé jusqu'à présent, un utilisateur peut supprimer accidentellement des données en cliquant sur le bouton de suppression lorsqu’ils destinée à cliquer sur le bouton Modifier. Dans ce didacticiel, nous allons ajouter une boîte de dialogue de confirmation côté client qui s’affiche lorsque l’utilisateur clique sur le bouton Supprimer.


## <a name="introduction"></a>Introduction

Sur les didacticiels de plusieurs cours nous ve vu comment utiliser notre architecture d’application et les données de contrôles Web ObjectDataSource conjointement pour fournir une insertion, de modification et de suppression de fonctionnalités. La suppression des interfaces nous avons examiné jusqu’ici ont été composé d’une suppression bouton qui, lorsque vous cliquez sur, provoque une publication (postback) et appelle les opérations de mappage ObjectDataSource `Delete()` (méthode). Le `Delete()` méthode appelle ensuite la méthode configurée à partir de la couche de logique métier, qui se propage l’appel à la couche d’accès aux données, émission réelle `DELETE` instruction à la base de données.

Alors que cette interface utilisateur permet de visiteurs supprimer des enregistrements dans les contrôles GridView, DetailsView ou FormView, il ne dispose pas de toute sorte de confirmation lorsque l’utilisateur clique sur le bouton Supprimer. Si un utilisateur clique sur accidentellement le bouton de suppression lorsqu’ils destinée à cliquer sur Modifier, l’enregistrement qu'ils destinée à mettre à jour est supprimé à la place. Pour résoudre ce problème, dans ce didacticiel, nous allons ajouter une boîte de dialogue de confirmation côté client qui s’affiche lorsque l’utilisateur clique sur le bouton Supprimer.

Le code JavaScript `confirm(string)` fonction affiche son paramètre d’entrée de chaîne en tant que le texte à l’intérieur d’une boîte de dialogue modale qui comprend deux boutons - OK et Annuler (voir Figure 1). Le `confirm(string)` fonction retourne une valeur booléenne en fonction de l’utilisateur clique sur le bouton (`true`, si l’utilisateur clique sur OK, et `false` s’il clique sur Annuler).


![La méthode de confirm(string) JavaScript affiche Modal, côté Client Messagebox](adding-client-side-confirmation-when-deleting-cs/_static/image1.png)

**Figure 1**: le code JavaScript `confirm(string)` méthode affiche un Messagebox Modal, côté Client


Au cours de l’envoi d’un formulaire, si la valeur `false` est retourné à partir d’un gestionnaire d’événements côté client, puis l’envoi du formulaire est annulée. À l’aide de cette fonctionnalité, nous pouvons avoir Delete bouton s côté client `onclick` Gestionnaire d’événements retourne la valeur d’un appel à `confirm("Are you sure you want to delete this product?")`. Si l’utilisateur clique sur Annuler, `confirm(string)` renvoie false, ce qui provoque l’envoi du formulaire Annuler. Supprimer avec aucune publication (postback), le produit dont bouton de suppression a gagné t. Si, toutefois, l’utilisateur clique sur OK dans la boîte de dialogue de confirmation, la publication (postback) va continuer sans interruption, et le produit sera supprimé. Consultez [à l’aide de JavaScript s `confirm()` méthode à l’envoi du formulaire contrôle](http://www.webreference.com/programming/javascript/confirm/) pour plus d’informations sur cette technique.

Ajouter le script côté client nécessaire est légèrement différente si vous utilisez des modèles que lorsque vous utilisez un CommandField. Par conséquent, dans ce didacticiel, nous allons examiner un GridView et FormView exemple.

> [!NOTE]
> À l’aide de techniques de confirmation du côté client, telles que celles présentées dans ce didacticiel, part du principe que vos utilisateurs visitent avec les navigateurs qui prennent en charge JavaScript et qu’ils ont JavaScript est activé. Si une de ces hypothèses ne sont pas true pour un utilisateur particulier, en cliquant sur le bouton Supprimer provoque immédiatement une publication (ne pas et affiche un messagebox confirmer).


## <a name="step-1-creating-a-formview-that-supports-deletion"></a>Étape 1 : Création d’un FormView qui prend en charge la suppression

Commencez par ajouter un FormView à le `ConfirmationOnDelete.aspx` page dans le `EditInsertDelete` dossier, en le liant à un nouveau ObjectDataSource qui extrait dans les informations de produit via le `ProductsBLL` classe s `GetProducts()` (méthode). Également configurer ObjectDataSource afin que le `ProductsBLL` classe s `DeleteProduct(productID)` méthode mappée à la s ObjectDataSource `Delete()` méthode ; Vérifiez que les onglets d’insertion et de mise à jour de listes déroulantes sont définies à (None). Enfin, vérifiez la case à cocher Activer la pagination dans la balise active de s FormView.

Après ces étapes, le balisage déclaratif de nouveau ObjectDataSource s doit ressembler à ce qui suit :


[!code-aspx[Main](adding-client-side-confirmation-when-deleting-cs/samples/sample1.aspx)]

Comme dans nos exemples précédentes qui n’utilisent pas d’accès concurrentiel optimiste, prenez un moment pour effacer le s ObjectDataSource `OldValuesParameterFormatString` propriété.

Dans la mesure où il a été lié à un contrôle ObjectDataSource prend uniquement en charge la suppression, le FormView s `ItemTemplate` offre uniquement le bouton Supprimer manque les boutons Nouveau et mise à jour. Toutefois, le balisage déclaratif s FormView, inclut un superflu `EditItemTemplate` et `InsertItemTemplate`, qui peut être supprimé. Prenez un moment pour personnaliser le `ItemTemplate` sorte qu’il affiche uniquement un sous-ensemble du produit de champs de données. Je ve configuré mine pour afficher le nom de produit s dans un `<h3>` titre au-dessus de ses noms de fournisseur et la catégorie (ainsi que le bouton Supprimer).


[!code-aspx[Main](adding-client-side-confirmation-when-deleting-cs/samples/sample2.aspx)]

Avec ces modifications, nous avons une page web entièrement fonctionnelle qui permet à un utilisateur basculer entre les produits d’un à la fois, avec la possibilité de supprimer un produit en cliquant simplement sur le bouton Supprimer. La figure 2 illustre une capture d’écran de notre progression jusque-là lorsqu’ils sont affichés via un navigateur.


[![Le FormView affiche des informations sur un produit unique](adding-client-side-confirmation-when-deleting-cs/_static/image3.png)](adding-client-side-confirmation-when-deleting-cs/_static/image2.png)

**Figure 2**: le FormView affiche des informations sur un produit unique ([cliquez pour afficher l’image en taille réelle](adding-client-side-confirmation-when-deleting-cs/_static/image4.png))


## <a name="step-2-calling-the-confirmstring-function-from-the-delete-buttons-client-side-onclick-event"></a>Étape 2 : Appel de la fonction confirm(string) à partir de la supprimer des boutons onclick côté Client événement

Avec le contrôle FormView créé, l’étape finale consiste à configurer le bouton supprimer ces qui, lorsqu’il s cliqué par le visiteur, le code JavaScript `confirm(string)` fonction est appelée. Ajout d’un script côté client à un bouton, LinkButton ou ImageButton s côté client `onclick` événement peut être effectué à l’aide de la `OnClientClick property`, qui est une nouveauté d’ASP.NET 2.0. Étant donné que nous voulons que la valeur de la `confirm(string)` renvoyée de la fonction, définissez simplement cette propriété sur :`return confirm('Are you certain that you want to delete this product?');`

Après cette modification de la syntaxe déclarative de supprimer le LinkButton s doit ressembler à :


[!code-aspx[Main](adding-client-side-confirmation-when-deleting-cs/samples/sample3.aspx)]

Il est tout s ! Figure 3 illustre une capture d’écran de confirmation de cette action. En cliquant sur le bouton Supprimer affiche la boîte de dialogue de confirmation. Si l’utilisateur clique sur Annuler, la publication est annulée et le produit n’est pas supprimé. Si, toutefois, l’utilisateur clique sur OK, continue de la publication (postback) et s ObjectDataSource `Delete()` méthode est appelée, sanctionné dans l’enregistrement de base de données en cours de suppression.

> [!NOTE]
> La chaîne passée dans le `confirm(string)` fonction JavaScript est délimitée par des apostrophes (plutôt que des guillemets). Dans JavaScript, les chaînes peuvent être délimitées à l’aide de type caractère. Nous utilisons des apostrophes ici afin que les délimiteurs de la chaîne passée dans `confirm(string)` n’introduisent une ambiguïté avec séparateurs utilisés pour la `OnClientClick` valeur de propriété.


[![Une Confirmation est maintenant affiché lorsque en cliquant sur le bouton Supprimer](adding-client-side-confirmation-when-deleting-cs/_static/image6.png)](adding-client-side-confirmation-when-deleting-cs/_static/image5.png)

**Figure 3**: une Confirmation est maintenant affiché lorsque en cliquant sur le bouton Supprimer ([cliquez pour afficher l’image en taille réelle](adding-client-side-confirmation-when-deleting-cs/_static/image7.png))


## <a name="step-3-configuring-the-onclientclick-property-for-the-delete-button-in-a-commandfield"></a>Étape 3 : Configuration de la propriété OnClientClick pour le bouton Supprimer dans un CommandField

Lorsque vous travaillez avec un bouton, LinkButton ou ImageButton directement dans un modèle, une boîte de dialogue de confirmation peut être associée à celui-ci, simplement en configurant ses `OnClientClick` propriété pour retourner les résultats du script JavaScript `confirm(string)` (fonction). Toutefois, le CommandField, qui ajoute un champ de boutons de suppression à un contrôle GridView ou le contrôle DetailsView - n’a pas un `OnClientClick` propriété qui peut être définie de manière déclarative. Au lieu de cela, nous devons faire référence par programme le bouton Supprimer dans le GridView ou DetailsView s approprié `DataBound` Gestionnaire d’événements et définissez son `OnClientClick` propriété il.

> [!NOTE]
> Lorsque vous définissez le bouton Supprimer s `OnClientClick` propriété dans les zones appropriées `DataBound` Gestionnaire d’événements, nous avons accès à lié aux données à l’enregistrement actif. Cela signifie que nous pouvons étendre le message de confirmation pour inclure des détails sur l’enregistrement particulier, tel que « Êtes-vous sûr de que vouloir supprimer le produit Tran sont ? » Cette personnalisation est également possible dans les modèles à l’aide de la syntaxe de liaison de données.


Pour le paramètre recommandé le `OnClientClick` propriété pour le bouton de suppression dans un CommandField, s let ajouter un contrôle GridView à la page. Configurez cette GridView pour utiliser le même contrôle ObjectDataSource par le FormView. Également limiter les s GridView BoundFields à inclure uniquement le nom de produit, la catégorie et le fournisseur. Enfin, vérifiez la case à cocher Activer la suppression de la balise active de GridView s. Cela ajoutera un CommandField au GridView s `Columns` collection avec son `ShowDeleteButton` propriété `true`.

Après avoir apporté ces modifications, votre balisage déclaratif de GridView s doit se présenter comme suit :


[!code-aspx[Main](adding-client-side-confirmation-when-deleting-cs/samples/sample4.aspx)]

Le CommandField contient une seule instance de supprimer un LinkButton qui sont accessibles par programme à partir de la s GridView `RowDataBound` Gestionnaire d’événements. Une fois référencé, nous pouvons définir son `OnClientClick` propriété en conséquence. Créer un gestionnaire d’événements pour le `RowDataBound` événement utilisant le code suivant :


[!code-csharp[Main](adding-client-side-confirmation-when-deleting-cs/samples/sample5.cs)]

Ce gestionnaire d’événements fonctionne avec les lignes de données (ceux qui ont le bouton Supprimer) et commence en par programmation en référençant le bouton Supprimer. En général, utilisez le modèle suivant :


[!code-csharp[Main](adding-client-side-confirmation-when-deleting-cs/samples/sample6.cs)]

*ButtonType* est le type de bouton utilisé par le CommandField - Button, LinkButton ou ImageButton. Par défaut, le CommandField utilise LinkButton, mais elle peut être personnalisée via le s CommandField `ButtonType property`. Le *commandFieldIndex* est l’index ordinal de la CommandField dans le GridView s `Columns` collection, tandis que la *controlIndex* est l’index du bouton Supprimer dans le s CommandField `Controls` collection. Le *controlIndex* valeur dépend de la position du bouton s par rapport à d’autres boutons dans la CommandField. Par exemple, si le bouton uniquement affiché dans le CommandField est le bouton Supprimer, utilisez un index de 0. Si, toutefois, s’il n’y a un bouton Modifier qui précède le bouton Supprimer, utilisez un index de 2. La raison pour laquelle un index de 2 est utilisé est car les deux contrôles sont ajoutés à la CommandField avant le bouton de suppression : le bouton Modifier et LiteralControl s utilisé pour ajouter un espace entre les boutons Modifier et supprimer.

Dans notre exemple particulier, la CommandField utilise LinkButton et, l’est le champ le plus à gauche, a un *commandFieldIndex* 0. Étant donné qu’aucun autre bouton, mais le bouton Supprimer dans le CommandField, nous utilisons un *controlIndex* 0.

Après vous référencez le bouton Supprimer dans le CommandField, nous extrayons ensuite des informations sur le produit lié à la ligne actuelle de GridView. Enfin, nous avons défini le bouton Supprimer s `OnClientClick` propriété JavaScript approprié, qui inclut le nom de produit s. Étant donné que la chaîne JavaScript passé dans le `confirm(string)` fonction est délimitée par des apostrophes, nous devons d’échappement d’apostrophe qui apparaissent dans le nom du produit s. En particulier, les apostrophes dans le nom du produit s sont précédées par «`\'`».

Avec ces modifications terminées, cliquez sur un bouton Supprimer dans le GridView s’affiche une boîte de dialogue de confirmation personnalisé zone (voir Figure 4). Comme avec le messagebox confirmation dans le FormView, si l’utilisateur clique sur Annuler la publication est annulée, ce qui évite la suppression ne se produise.

> [!NOTE]
> Cette technique peut également être utilisée pour accéder par programme le bouton Supprimer dans le CommandField dans un contrôle DetailsView. Pour le contrôle DetailsView, toutefois, vous d créer un gestionnaire d’événements pour le `DataBound` événement, le contrôle DetailsView n’ayant pas un `RowDataBound` événement.


[![En cliquant sur le bouton de suppression s GridView affiche une boîte de dialogue de Confirmation personnalisé](adding-client-side-confirmation-when-deleting-cs/_static/image9.png)](adding-client-side-confirmation-when-deleting-cs/_static/image8.png)

**Figure 4**: en cliquant sur le bouton Supprimer de s GridView affiche une boîte de dialogue de Confirmation personnalisé ([cliquez pour afficher l’image en taille réelle](adding-client-side-confirmation-when-deleting-cs/_static/image10.png))


## <a name="using-templatefields"></a>Utilisation de TemplateField

Un des inconvénients de la CommandField est que les boutons doivent être accessibles via l’indexation et que l’objet résultant doit être converti au type de bouton approprié (Button, LinkButton ou ImageButton). À l’aide de « nombres magiques » et codé en dur les types peuvent causer des problèmes qui ne peut pas être détectées avant l’exécution. Par exemple, si vous ou un autre développeur, ajoute de nouveaux boutons à la CommandField à un moment donné dans le futur (par exemple, un bouton Modifier) ou des modifications du `ButtonType` propriété, le code existant continuera à compiler sans erreur, mais sur la page peut-être générer une exception ou un comportement inattendu, selon la façon dont votre code a été écrit et quelles modifications ont été apportées.

Une autre approche consiste à convertir des s GridView et DetailsView CommandFields dans TemplateField. Cette opération génère un TemplateField avec un `ItemTemplate` qui a un LinkButton (ou bouton ou ImageButton) pour chaque bouton de la CommandField. Ces boutons `OnClientClick` propriétés peuvent être attribuées de façon déclarative, comme nous avons vu avec le contrôle FormView ou accessible par programmation dans les zones appropriées `DataBound` Gestionnaire d’événements à l’aide du modèle suivant :


[!code-csharp[Main](adding-client-side-confirmation-when-deleting-cs/samples/sample7.cs)]

Où *controlID* est la valeur du bouton s `ID` propriété. Alors que ce modèle nécessite toujours un type codé en dur pour la conversion, il supprime la nécessité pour l’indexation, ce qui permet la mise en page Modifier sans entraîne une erreur d’exécution.

## <a name="summary"></a>Résumé

Le code JavaScript `confirm(string)` fonction est une technique couramment utilisée pour le contrôle du flux de travail envoi du formulaire. Lors de l’exécution, la fonction affiche une boîte de dialogue modale, côté client qui inclut les deux boutons OK et Annuler. Si l’utilisateur clique sur OK, la `confirm(string)` fonction renvoie `true`; cliquez sur Annuler pour revenir `false`. Cette fonctionnalité, couplée avec un comportement de navigateur s pour annuler l’envoi d’un formulaire si un gestionnaire d’événements pendant le processus de soumission retourne `false`, peut être utilisé pour afficher un messagebox de confirmation lorsque vous supprimez un enregistrement.

Le `confirm(string)` fonction peut être associée à un bouton Web contrôle s côté client `onclick` Gestionnaire d’événements via le contrôle s `OnClientClick` propriété. Lorsque vous travaillez avec un bouton Supprimer dans un modèle - dans un des modèles FormView s ou dans TemplateField dans le contrôle DetailsView ou GridView - cette propriété peut être définie soit de façon déclarative ou par programme, comme nous l’avons vu dans ce didacticiel.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

>[!div class="step-by-step"]
[Précédent](implementing-optimistic-concurrency-cs.md)
[Suivant](limiting-data-modification-functionality-based-on-the-user-cs.md)
