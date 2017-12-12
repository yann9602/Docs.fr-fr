---
uid: web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-7
title: "Partie 7 : Ajout de fonctionnalités | Documents Microsoft"
author: JoeStagner
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 7 ajoute des fonctionnalités supplémentaires, telles que le volet du compte..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2010
ms.topic: article
ms.assetid: 50223ee9-11b9-4cf3-bca2-e2f10bf471f3
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-7
msc.type: authoredcontent
ms.openlocfilehash: 5280de44b3e75f9d1ae85e0248bc3ef6d5444f6d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-7-adding-features"></a>Partie 7 : Fonctionnalités d’ajout
====================
par [Joe Stagner](https://github.com/JoeStagner)

> Tailspin Spyworks montre comment extrêmement simple est de créer des applications puissantes et évolutives pour la plateforme .NET. Il illustre comment utiliser les nouvelles fonctionnalités dans ASP.NET 4 pour créer un magasin en ligne, y compris les achats, l’extraction et l’administration.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 7 ajoute des fonctionnalités supplémentaires, telles que votre compte, les évaluations de produits et les « éléments populaires » et les contrôles utilisateur « également acheté ».


## <a id="_Toc260221673"></a>Ajout de fonctionnalités

Bien que les utilisateurs peuvent parcourir notre catalogue, placer des éléments dans son panier d’achat et le processus de validation, il existe qu'un certain nombre de prise en charge des fonctionnalités que nous inclura pour améliorer notre site.

1. Examen du compte (liste de commandes placées et afficher les détails.)
2. Ajouter un contenu spécifique de contexte à la première page.
3. Ajouter une fonctionnalité pour permettre aux utilisateurs de vérifier les produits dans le catalogue.
4. Créer un contrôle utilisateur pour afficher les éléments populaires et Place qui contrôlent sur la première page.
5. Créer un contrôle utilisateur « Également acheté » et l’ajouter à la page Détails du produit.
6. Ajouter un Contact Page.
7. Ajouter un sur la Page.
8. Erreur globale

## <a id="_Toc260221674"></a>Votre compte

Dans le dossier « Compte », créez deux pages .aspx OrderList.aspx nommé un et l’autre OrderDetails.aspx nommé

OrderList.aspx reposera sur les contrôles GridView et EntityDataSoure autant que nous avons précédemment.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample1.aspx)]

Le EntityDataSoure sélectionne les enregistrements de la table Orders filtrée sur le nom d’utilisateur (voir le WhereParameter) qui nous défini dans une variable de session lorsque le journal de l’utilisateur 's.

Notez également ces paramètres dans le HyperlinkField du contrôle GridView :

[!code-xml[Main](tailspin-spyworks-part-7/samples/sample2.xml)]

Permet de spécifier le lien vers la vue Détails de commande pour chaque produit en spécifiant le champ OrderID comme un paramètre de chaîne de requête à la page OrderDetails.aspx.

## <a id="_Toc260221675"></a>OrderDetails.aspx

Nous allons utiliser un contrôle EntityDataSource pour accéder aux commandes et un FormView pour afficher les données de commande et un autre EntityDataSource avec un GridView pour afficher de toutes les commandes lignes.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample3.aspx)]

Dans le fichier Code-Behind (OrderDetails.aspx.cs), nous avons deux bits peu de ménage.

Nous devons d’abord vous assurer que OrderDetails obtient toujours un OrderId.

[!code-csharp[Main](tailspin-spyworks-part-7/samples/sample4.cs)]

Nous devons également calculer et afficher le total à partir des éléments de ligne de commande.

[!code-csharp[Main](tailspin-spyworks-part-7/samples/sample5.cs)]

## <a id="_Toc260221676"></a>La Page d’accueil

Vous allez ajouter du contenu statique à la page Default.aspx.

Tout d’abord, je vais créer un dossier « Content » et qu’il contient un dossier Images (et je vais inclure une image à utiliser sur la page d’accueil.)

Dans l’espace réservé en bas de la page Default.aspx, ajoutez le balisage suivant.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample6.aspx)]

## <a id="_Toc260221677"></a>Évaluations de produits

Tout d’abord, nous allons ajouter un bouton avec un lien à un formulaire que nous pouvons utiliser pour entrer une évaluation de produit.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample7.aspx)]

![](tailspin-spyworks-part-7/_static/image1.jpg)

Notez que nous passons ProductID dans la chaîne de requête

Suivant nous allons ajouter une page nommée ReviewAdd.aspx

Cette page utilise les outils de contrôle ASP.NET AJAX. Si vous n'avez pas déjà fait donc vous pouvez le télécharger à partir de [DevExpress](http://devexpress.com/act) et il existe des instructions sur la configuration de la boîte à outils à utiliser avec Visual Studio ici [https://www.asp.net/learn/ajax-videos/video-76.aspx](../../../videos/ajax-control-toolkit/how-do-i-get-started-with-the-aspnet-ajax-control-toolkit.md).

En mode Création, faites glisser des contrôles et des programmes de validation à partir de la boîte à outils et créer un formulaire semblable à celui ci-dessous.

![](tailspin-spyworks-part-7/_static/image2.jpg)

Le balisage ressemblera à ceci.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample8.aspx)]

Maintenant que nous pouvons entrer revues, permet d’afficher les révisions sur la page du produit.

Ajoutez ce balisage à la page ProductDetails.aspx.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample9.aspx)]

Notre application est maintenant en cours d’exécution et en accédant à un produit affiche les informations de produit, y compris les révisions du client.

![](tailspin-spyworks-part-7/_static/image3.jpg)

## <a id="_Toc260221678"></a>Contrôle des éléments populaires (création de contrôles utilisateur)

Afin d’augmenter les ventes sur votre site web, nous allons ajouter quelques fonctionnalités « donneur d’ordre suggérées » des produits populaires ou connexes.

La première de ces fonctions sera une liste du produit dans notre catalogue de produits les plus populaires.

Nous allons créer un « contrôle utilisateur » pour afficher les meilleurs éléments sur la page d’accueil de notre application. Dans la mesure où il s’agit d’un contrôle, nous pouvons l’utiliser sur n’importe quelle page par simplement glisser -déplacer le contrôle dans le Concepteur de Visual Studio sur n’importe quelle page qui nous convient.

Dans l’Explorateur de solutions de Visual Studio, avec le bouton droit sur le nom de la solution et créer un nouveau répertoire nommé « Contrôles ». Il n’est pas nécessaire pour ce faire, nous permettent de conserver notre projet organisé en créant tous les contrôles utilisateur dans le répertoire « Contrôles ».

Avec le bouton droit sur le dossier de contrôles et sélectionnez « Nouvel élément » :

![](tailspin-spyworks-part-7/_static/image4.jpg)

Spécifiez un nom pour notre contrôle de « PopularItems ». Notez que l’extension de fichier pour les contrôles utilisateur est .ascx pas .aspx.

Notre contrôle utilisateurs éléments courants sera défini comme suit.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample10.aspx)]

Ici, nous utilisons une méthode que nous n'avons pas encore utilisé dans cette application. Nous utilisons le contrôle du répéteur et au lieu d’utiliser un contrôle de source de données, nous nous lions le contrôle du répéteur aux résultats d’une requête LINQ to Entities.

Dans le code-behind de notre contrôle nous le faire comme suit.

[!code-csharp[Main](tailspin-spyworks-part-7/samples/sample11.cs)]

Notez également cette ligne importante en haut de la balise de notre contrôle.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample12.aspx)]

Étant donné que les éléments les plus populaires ne changer selon les minutes, nous pouvons ajouter une directive aching pour améliorer les performances de votre application. Cette directive amène le code de contrôles doit uniquement être exécutée lors de la sortie mise en cache du contrôle. Dans le cas contraire, la version mise en cache de la sortie du contrôle servira.

À présent que nous devons faire inclure notre nouveau contrôle dans notre page Default.aspc.

Utilisation et faites-la glisser pour placer une instance du contrôle dans la colonne ouvrir de notre formulaire par défaut.

![](tailspin-spyworks-part-7/_static/image5.jpg)

Quand nous exécutons notre application, la page d’accueil affiche maintenant les éléments les plus populaires.

![](tailspin-spyworks-part-7/_static/image6.jpg)

## <a id="_Toc260221679"></a>« Également acheté » contrôle (contrôles utilisateur avec des paramètres)

Le deuxième contrôle utilisateur que nous allons créer prendra suggéré de vente au niveau suivant en ajoutant la spécificité de contexte.

La logique pour calculer les premiers éléments « Également acheté » est non trivial.

Notre contrôle « Également acheté » sélectionne les enregistrements OrderDetails (précédemment achetés) pour ProductID actuellement sélectionné et saisissez les OrderIDs pour chaque commande unique est trouvé.

Ensuite, sélectionnez tous les produits à partir de toutes ces commandes et somme de quantités achetées. Nous trier les produits par cette somme de la quantité et afficher les éléments de cinq.

Étant donné la complexité de cette logique, nous implémenterons cet algorithme en tant qu’une procédure stockée.

Le code T-SQL pour la procédure stockée est la suivante.

[!code-sql[Main](tailspin-spyworks-part-7/samples/sample13.sql)]

Notez que cette procédure stockée (SelectPurchasedWithProducts) existe dans la base de données au moment où nous avons inclus dans notre application, lorsque nous avons généré l’Entity Data Model que nous avons spécifié, en plus des Tables et vues il est nécessaire, l’Entity Data Model doit inclure cette procédure stockée.

Pour accéder à la procédure stockée à partir de l’Entity Data Model que nous avons besoin d’importer la fonction.

Double-cliquez sur l’Entity Data Model dans l’Explorateur de Solutions pour l’ouvrir dans le concepteur et ouvrez l’Explorateur de modèles, puis avec le bouton droit dans le concepteur et sélectionnez « Ajouter une fonction importation ».

![](tailspin-spyworks-part-7/_static/image1.png)

Ainsi, cette boîte de dialogue s’ouvre.

![](tailspin-spyworks-part-7/_static/image2.png)

Renseignez les champs comme vous le constater ci-dessus, en sélectionnant la « SelectPurchasedWithProducts » et utilisez le nom de la procédure pour le nom de la fonction importée.

Cliquez sur « Ok ».

Avoir fait cela que nous pouvons programmer simplement la procédure stockée comme il peut être n’importe quel autre élément dans le modèle.

Par conséquent, dans notre dossier « Contrôles » vous devez créer un contrôle utilisateur nommé AlsoPurchased.ascx.

Le balisage pour ce contrôle reconnaîtront au contrôle PopularItems.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample14.aspx)]

La différence notable est qui ne sont pas en cache la sortie dans la mesure où l’élément doit être restitué varient par produit.

Le ProductId sera « property » pour le contrôle.

[!code-csharp[Main](tailspin-spyworks-part-7/samples/sample15.cs)]

Dans le Gestionnaire du contrôle événement PreRender nous seau pour effectuer trois actions.

1. Assurez-vous que le ProductID est défini.
2. Voir s’il existe des produits qui ont été achetés avec l’objet actuel.
3. Certains éléments, comme déterminé dans #2 de sortie.

Notez combien il est facile d’appeler la procédure stockée via le modèle.

[!code-csharp[Main](tailspin-spyworks-part-7/samples/sample16.cs)]

Après avoir déterminé qu’il sont « également acheté », nous pouvons simplement lier le répéteur aux résultats retournés par la requête.

[!code-csharp[Main](tailspin-spyworks-part-7/samples/sample17.cs)]

S’il n’existait pas tous les éléments « également acheté » nous affichons simplement autres éléments courants de notre catalogue.

[!code-csharp[Main](tailspin-spyworks-part-7/samples/sample18.cs)]

Pour afficher les éléments « Également acheté », ouvrez la page ProductDetails.aspx et faites glisser le contrôle AlsoPurchased à partir de l’Explorateur de Solutions afin qu’il apparaisse à cette position dans le balisage.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample19.aspx)]

Cela créera une référence au contrôle en haut de la page ProductDetails.

[!code-aspx[Main](tailspin-spyworks-part-7/samples/sample20.aspx)]

Étant donné que le contrôle utilisateur AlsoPurchased nécessite un nombre ProductId, nous allons définir la propriété ProductID de notre contrôle à l’aide d’une instruction Eval par rapport à l’élément de modèle de données en cours de la page.

![](tailspin-spyworks-part-7/_static/image3.png)

Lorsque nous créons et exécuter maintenant et accédez à un produit, nous voyons les éléments « Également acheté ».

![](tailspin-spyworks-part-7/_static/image7.jpg)

>[!div class="step-by-step"]
[Précédent](tailspin-spyworks-part-6.md)
[Suivant](tailspin-spyworks-part-8.md)
