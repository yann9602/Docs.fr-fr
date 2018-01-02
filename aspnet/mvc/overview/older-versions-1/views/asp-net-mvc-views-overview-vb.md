---
uid: mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-vb
title: "ASP.NET MVC affiche la vue d’ensemble (VB) | Documents Microsoft"
author: StephenWalther
description: "Quelle est la vue ASP.NET MVC et en quoi il diffère une page HTML ? Dans ce didacticiel, Stephen Walther présente les vues et montre comment vous pouvez t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/16/2008
ms.topic: article
ms.assetid: c28ba88d-3a93-47f5-a306-049bd766714d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-vb
msc.type: authoredcontent
ms.openlocfilehash: c85b969aa4457d0326b4a16da218db9e11d01e10
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-mvc-views-overview-vb"></a>ASP.NET MVC affiche la vue d’ensemble (VB)
====================
par [Stephen Walther](https://github.com/StephenWalther)

> Quelle est la vue ASP.NET MVC et en quoi il diffère une page HTML ? Dans ce didacticiel, Stephen Walther présente les vues et montre comment vous pouvez tirer parti de la vue de données et des programmes d’assistance HTML dans une vue.


L’objectif de ce didacticiel est de vous fournir une brève introduction aux vues d’ASP.NET MVC, afficher les données et les programmes d’assistance HTML. À la fin de ce didacticiel, vous devez comprendre comment créer des vues, passer des données à partir d’un contrôleur à une vue et utiliser des programmes d’assistance HTML pour générer le contenu dans une vue.

## <a name="understanding-views"></a>Présentation des vues

Contrairement à ASP.NET ou des pages Active Server Pages, ASP.NET MVC n’inclut pas tout ce qui correspond directement à une page. Dans une application ASP.NET MVC, il n’est pas une page sur le disque qui correspond au chemin d’accès dans l’URL que vous tapez dans la barre d’adresses de votre navigateur. La chose la plus proche à une page dans une application ASP.NET MVC est un élément appelé un *vue*.

Dans une application ASP.NET MVC, les demandes entrantes de navigateur sont mappées aux actions de contrôleur. Une action de contrôleur peut retourner une vue. Toutefois, une action de contrôleur peut effectuer un autre type d’action telle que la redirection vers une autre action du contrôleur.

Le listing 1 contient un contrôleur simple nommé le fichier HomeController. Le fichier HomeController expose deux actions de contrôleur nommées Index() et Details().

**La liste 1 - HomeController.vb**

[!code-vb[Main](asp-net-mvc-views-overview-vb/samples/sample1.vb)]

Vous pouvez appeler la première action, l’action Index(), en tapant l’URL suivante dans la barre d’adresse de votre navigateur :

/ Home/Index

Vous pouvez appeler la seconde action, l’action Details(), en tapant cette adresse dans votre navigateur :

/ Home/détails

L’action Index() retourne une vue. La plupart des actions que vous créez retournera des vues. Toutefois, une action peut retourner d’autres types de résultats d’action. Par exemple, l’action Details() retourne un RedirectToActionResult qui redirige la demande entrante à l’action Index().

L’action Index() contienne la ligne de code suivante :

View()

Cette ligne de code retourne une vue qui doit se trouve à l’emplacement suivant sur votre serveur web :

\Views\Home\Index.aspx

Le chemin d’accès à la vue est déduit à partir du nom du contrôleur et le nom de l’action du contrôleur.

Si vous préférez, vous pouvez être explicite sur la vue. La ligne de code suivante retourne une vue nommée Fred :

Vue (Fred)

Lorsque cette ligne de code est exécutée, une vue est retournée à partir de l’emplacement suivant :

\Views\Home\Fred.aspx

> [!NOTE] 
> 
> Si vous envisagez de créer des tests unitaires pour votre application ASP.NET MVC il est judicieux d’être explicite sur les noms d’affichage. De cette façon, vous pouvez créer un test unitaire pour vérifier que la vue attendue a été retournée par une action de contrôleur.


## <a name="adding-content-to-a-view"></a>Ajout de contenu à une vue

Une vue est une norme de (document HTML qui peut contenir des scripts X). Vous utilisez des scripts pour ajouter du contenu dynamique à une vue.

Par exemple, la vue dans la liste 2 affiche la date et heure actuelles.

**Listing 2 - \Views\Home\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample2.aspx)]

Notez que le corps de la page HTML dans la liste 2 contient le script suivant :

&lt;% Response.Write(DateTime.Now) %&gt;

Vous utilisez les délimiteurs de script &lt;et %&gt; pour marquer le début et la fin d’un script. Ce script est écrit en Visual basic. Il affiche la date et heure actuelles en appelant la méthode Response.Write () pour afficher le contenu dans le navigateur. Les délimiteurs de script &lt;et %&gt; peut être utilisé pour exécuter une ou plusieurs instructions.

Étant donné que vous appelez souvent Response.Write (), Microsoft vous fournit un raccourci pour appeler la méthode Response.Write (). La vue dans la liste 3 utilise les délimiteurs &lt;% = et %&gt; comme raccourci pour l’appel à Response.Write ().

**La liste 3 - Views\Home\Index2.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample3.aspx)]

Vous pouvez utiliser n’importe quel langage .NET pour générer le contenu dynamique dans une vue. Normalement, ll vous utilisez Visual Basic .NET ou c# pour écrire vos contrôleurs et les vues.

## <a name="using-html-helpers-to-generate-view-content"></a>À l’aide de programmes d’assistance HTML pour générer le contenu de la vue

Pour le rendre plus facile d’ajouter du contenu à une vue, vous pouvez tirer parti de ce que l'on appelle un *programme d’assistance HTML*. Une application d’assistance HTML, est en général, une méthode qui génère une chaîne. Vous pouvez utiliser des programmes d’assistance HTML pour générer des éléments HTML standard tels que les zones de texte, les liens, les listes déroulantes et les zones de liste.

Par exemple, la vue dans la liste 4 tire profit des trois des programmes d’assistance HTML--les BeginForm(), TextBox() et Password() programmes d’assistance--pour générer une connexion forment (voir Figure 1).

**La liste 4--\Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample4.aspx)]


[![La boîte de dialogue Nouveau projet](asp-net-mvc-views-overview-vb/_static/image1.jpg)](asp-net-mvc-views-overview-vb/_static/image1.png)

**Figure 01**: un formulaire de connexion standard ([cliquez pour afficher l’image en taille réelle](asp-net-mvc-views-overview-vb/_static/image2.png))


Toutes les méthodes de programmes d’assistance HTML sont appelées sur la propriété Html de la vue. Par exemple, vous restituez une zone de texte en appelant la méthode Html.TextBox().

Notez que vous utilisez les délimiteurs de script &lt;% = et %&gt; lors de l’appel des programmes d’assistance à la fois le Html.TextBox() et Html.Password(). Ces programmes d’assistance simplement retournent une chaîne. Vous devez appeler Response.Write () pour restituer la chaîne dans le navigateur.

À l’aide des méthodes de programme d’assistance HTML est facultative. Ils vous simplifier la vie en réduisant la quantité de code HTML et le script que vous devez écrire. La vue dans la liste 5 restitue la même forme exacte que la vue dans la liste 4 sans utiliser de programmes d’assistance HTML.

**La liste 5--\Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample5.aspx)]

Vous avez également la possibilité de créer vos propres programmes d’assistance HTML. Par exemple, vous pouvez créer une méthode d’assistance GridView() qui affiche un ensemble d’enregistrements de base de données dans une table HTML automatiquement. Nous avons ce sujet, consultez le didacticiel **création de programmes d’assistance HTML personnalisé**.

## <a name="using-view-data-to-pass-data-to-a-view"></a>À l’aide des données d’affichage pour passer des données à une vue

Afficher les données vous permet de passer des données à partir d’un contrôleur à une vue. Considérez les données d’affichage comme un package que vous envoyez par courrier. Toutes les données transmises à partir d’un contrôleur à une vue doivent être envoyées à l’aide de ce package. Par exemple, le contrôleur dans la liste 6 ajoute un message pour afficher les données.

**La liste 6 - ProductController.vb**

[!code-vb[Main](asp-net-mvc-views-overview-vb/samples/sample6.vb)]

Le contrôleur de propriété ViewData représente une collection de paires nom / valeur. Dans la liste 6, la méthode Index() ajoute un élément à la collection de données d’affichage nommée message avec la valeur Hello World !. Lorsque la vue est retournée par la méthode Index(), les données d’affichage sont passées automatiquement à la vue.

La vue dans la liste 7 récupère le message à partir des données d’affichage et restitue le message dans le navigateur.

**La liste 7--\Views\Product\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-vb/samples/sample7.aspx)]

Notez que la vue tire parti de la méthode d’assistance HTML de Html.Encode() lors du rendu du message. Le programme d’assistance HTML de Html.Encode() encode les caractères spéciaux tels que &lt; et &gt; en caractères qui sont sécurisés à afficher dans une page web. Chaque fois que vous restituez le contenu qu’un utilisateur soumet à un site Web, vous devez les encoder le contenu à empêcher les attaques par injection de code JavaScript.

(Étant donné que nous avons créé le message nous-mêmes dans le ProductController, nous ne pas réellement besoin d’encoder le message. Toutefois, il est une bonne habitude à toujours appeler la méthode Html.Encode() lors de l’affichage du contenu récupéré des données d’affichage dans une vue.)

Dans la liste 7, nous avons pris parti des données d’affichage pour passer un message de chaîne simple à partir d’un contrôleur à une vue. Afficher les données permet également de transmettre d’autres types de données, comme une collection d’enregistrements de base de données, à partir d’un contrôleur à une vue. Par exemple, si vous souhaitez afficher le contenu de la table de base de données de produits dans une vue, vous pouvez transmettre la collection de base de données enregistre dans la vue données.

Vous avez également la possibilité de passage des données d’affichage fortement typé à partir d’un contrôleur à une vue. Nous avons ce sujet, consultez le didacticiel **présentation fortement typée afficher les données et les vues**.

## <a name="summary"></a>Résumé

Ce didacticiel fourni une brève introduction aux vues d’ASP.NET MVC, afficher les données et les programmes d’assistance HTML. Dans la première section, vous avez appris comment ajouter de nouvelles vues à votre projet. Vous avez appris que vous devez ajouter une vue vers le dossier approprié pour pouvoir pour l’appeler à partir d’un contrôleur spécifique. Ensuite, nous avons abordé le sujet de programmes d’assistance HTML. Vous avez appris comment programmes d’assistance HTML permettent de créer facilement un contenu HTML standard. Enfin, vous avez appris comment tirer parti des données d’affichage pour passer des données à partir d’un contrôleur à une vue.

>[!div class="step-by-step"]
[Précédent](passing-data-to-view-master-pages-cs.md)
[Suivant](creating-custom-html-helpers-vb.md)
