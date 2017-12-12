---
uid: web-pages/overview/ui-layouts-and-themes/3-creating-a-consistent-look
title: "Création d’une disposition cohérente dans ASP.NET Web Pages (Razor) Sites | Documents Microsoft"
author: tfitzmac
description: "Pour le rendre plus efficace de créer des pages web pour votre site, vous pouvez créer des blocs réutilisables de contenu (par exemple, les en-têtes et pieds de page) pour votre site Web, puis vous c..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/10/2014
ms.topic: article
ms.assetid: d7bd001b-6db2-4422-9b78-f3d08b743b00
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/3-creating-a-consistent-look
msc.type: authoredcontent
ms.openlocfilehash: 2c7631017f7c0fb31f43320c2ab78baddd87b516
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-a-consistent-layout-in-aspnet-web-pages-razor-sites"></a>Création d’une disposition cohérente dans les Sites ASP.NET Web Pages (Razor)
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article explique comment vous pouvez utiliser des pages de disposition dans un site Web ASP.NET Web Pages (Razor) pour créer des blocs réutilisables de contenu (par exemple, les en-têtes et pieds de page) et pour créer une apparence cohérente pour toutes les pages du site.
> 
> **Ce que vous allez apprendre :** 
> 
> - Comment créer des blocs réutilisables de contenu tels que les en-têtes et pieds de page.
> - Comment créer une apparence cohérente pour toutes les pages de votre site à l’aide d’une mise en page.
> - Explique comment passer des données au moment de l’exécution à une page de disposition.
> 
> Voici les fonctionnalités d’ASP.NET introduites dans l’article :
> 
> - Blocs de contenu, qui sont des fichiers qui contiennent le contenu au format HTML à insérer dans plusieurs pages.
> - Pages de disposition, qui sont des pages qui contiennent le contenu au format HTML qui peut être partagé par les pages sur le site Web.
> - Le `RenderPage`, `RenderBody`, et `RenderSection` , ces méthodes indiquent ASP.NET où insérer des éléments de la page.
> - Le `PageData` dictionnaire qui vous permet de partager des données entre les blocs de contenu et les pages de disposition.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


## <a name="about-layout-pages"></a>Sur les Pages de disposition

De nombreux sites Web dont le contenu est affiché sur chaque page, comme un en-tête et pied de page ou une zone qui indique aux utilisateurs que qu’ils soient connectés. ASP.NET vous permet de créer un fichier distinct avec un bloc de contenu qui peut contenir de texte, le balisage et le code, tout comme une page web normale. Vous pouvez ensuite insérer le bloc de contenu dans d’autres pages sur le site où vous souhaitez afficher les informations. De cette façon, que vous n’êtes pas obligé de copier et coller le même contenu dans chaque page. Création du contenu commun à ceci facilite également mettre à jour votre site. Si vous devez modifier le contenu, vous pouvez simplement mettre à jour d’un seul fichier et les modifications sont alors répercutées partout où le contenu a été inséré.

Le diagramme suivant montre comment des blocs de contenu. Lorsqu’un navigateur demande une page à partir du serveur web, ASP.NET insère des blocs de contenu au point où la `RenderPage` méthode est appelée dans la page principale. La page (fusionnée) terminé est ensuite envoyée au navigateur.

![Diagramme conceptuel montrant comment la méthode RenderPage insère une page référencée dans la page actuelle.](3-creating-a-consistent-look/_static/image1.jpg)

Dans cette procédure, vous allez créer une page qui fait référence à deux blocs de contenu (un en-tête et un pied de page) qui sont trouvent dans des fichiers distincts. Vous pouvez utiliser ces mêmes blocs de contenu dans n’importe quelle page de votre site. Lorsque vous avez terminé, vous obtiendrez une page comme suit :

![Capture d’écran montrant une page dans le navigateur qui résulte de l’exécution d’une page qui inclut les appels à la méthode RenderPage.](3-creating-a-consistent-look/_static/image2.jpg)

1. Dans le dossier racine de votre site Web, créez un fichier nommé *Index.cshtml*.
2. Remplacez la balise existante avec les éléments suivants :

    [!code-html[Main](3-creating-a-consistent-look/samples/sample1.html)]
3. Dans le dossier racine, créez un dossier nommé *partagé*.

    > [!NOTE]
    > Il est courant pour stocker les fichiers qui sont partagées entre les pages web dans un dossier nommé *partagé*.
4. Dans le *Shared* dossier, créez un fichier nommé  *\_Header.cshtml*.
5. Remplacer le contenu existant avec les éléments suivants :

    [!code-html[Main](3-creating-a-consistent-look/samples/sample2.html)]

    Notez que le nom de fichier est  *\_Header.cshtml*, avec un trait de soulignement (\_) en tant que préfixe. ASP.NET ne sont pas envoyer une page au navigateur si son nom commence par un trait de soulignement. Cela évite que les utilisateurs de demander (par inadvertance ou autre) ces pages directement. Il est judicieux d’utiliser les pages de nom comportant des blocs de contenu, un trait de soulignement, car vous ne souhaitez pas réellement les utilisateurs puissent demander ces pages &#8212; ils existent uniquement pour être inséré dans les autres pages.
6. Dans le *Shared* dossier, créez un fichier nommé  *\_Footer.cshtml* et remplacez le contenu par les éléments suivants :

    [!code-html[Main](3-creating-a-consistent-look/samples/sample3.html)]
7. Dans le *Index.cshtml* , ajoutez les deux appels à la `RenderPage` méthode, comme illustré ici :

    [!code-html[Main](3-creating-a-consistent-look/samples/sample4.html)]

    Cet exemple montre comment insérer un bloc de contenu dans une page web. Vous appelez le `RenderPage` méthode et lui transmettre le nom du fichier dont le contenu à insérer à ce stade. Ici, vous insérez le contenu de la  *\_Header.cshtml* et  *\_Footer.cshtml* de fichiers dans le *Index.cshtml* fichier.
8. Exécutez le *Index.cshtml* page dans un navigateur. (Dans WebMatrix, dans le **fichiers** espace de travail, cliquez sur le fichier, puis sélectionnez **lancer dans un navigateur**.)
9. Dans le navigateur, affichez la source de la page. (Par exemple, dans Internet Explorer, cliquez sur la page, puis **afficher la Source**.)

    Cela vous permet de voir le balisage de page web qui est envoyé au navigateur, qui combine le balisage de page d’index avec les blocs de contenu. L’exemple suivant montre la page source qui est restituée pour *Index.cshtml*. Les appels à `RenderPage` insérés dans *Index.cshtml* ont été remplacés par le contenu réel des fichiers d’en-tête et pied de page.

    [!code-html[Main](3-creating-a-consistent-look/samples/sample5.html)]

## <a name="creating-a-consistent-look-using-layout-pages"></a>Création d’une apparence cohérente à l’aide des Pages de disposition

Jusqu'à présent, vous avez vu qu’il est facile d’inclure le même contenu sur plusieurs pages. Une approche plus structurée à la création d’une apparence cohérente pour un site consiste à utiliser les pages de disposition. Une page de disposition définit la structure d’une page web, mais ne contient pas de contenu réel. Une fois que vous avez créé une page de disposition, vous pouvez créer des pages web qui contiennent le contenu et les lier à la page de disposition. Lorsque ces pages sont affichés, ils allez mis en forme en fonction de la page de disposition. (Dans ce sens, une page de disposition agit comme un type de modèle pour le contenu qui est défini dans d’autres pages.)

La page de disposition est tout comme n’importe quelle page HTML, sauf qu’il contient un appel à la `RenderBody` (méthode). La position de la `RenderBody` méthode dans la page de disposition détermine où les informations à partir de la page de contenu seront incluses.

Le diagramme suivant montre comment contenus pages et pages de disposition sont associées au moment de l’exécution pour produire la page web terminé. Le navigateur demande une page de contenu. La page contenue contient du code qui spécifie la page de disposition à utiliser pour la structure de la page. Dans la page de disposition, le contenu est inséré au point où la `RenderBody` méthode est appelée. Contenu de blocs peuvent également être insérées dans la page de disposition en appelant le `RenderPage` (méthode), la façon que vous l’avez fait dans la section précédente. Lorsque la page web est terminée, il est envoyé au navigateur.

![Capture d’écran montrant une page dans le navigateur qui résulte de l’exécution d’une page qui inclut les appels à la méthode RenderBody.](3-creating-a-consistent-look/_static/image3.jpg)

La procédure suivante montre comment créer une disposition des pages de contenu et un lien hypertexte à ce dernier.

1. Dans le *Shared* dossier de votre site Web, créez un fichier nommé  *\_Layout1.cshtml*.
2. Remplacer le contenu existant avec les éléments suivants :

    [!code-html[Main](3-creating-a-consistent-look/samples/sample6.html)]

    Vous utilisez la `RenderPage` méthode dans une page de disposition pour insérer des blocs de contenu. Une page de disposition peut contenir qu’un seul appel à la `RenderBody` (méthode).
3. Dans le *Shared* dossier, créez un fichier nommé  *\_Header2.cshtml* et remplacer le contenu existant par le code suivant :

    [!code-html[Main](3-creating-a-consistent-look/samples/sample7.html)]
4. Dans le dossier racine, créez un dossier et nommez-le *Styles*.
5. Dans le *Styles* dossier, créez un fichier nommé *Site.css* et ajoutez les définitions de style suivantes :

    [!code-css[Main](3-creating-a-consistent-look/samples/sample8.css)]

    Ces définitions de style sont ici uniquement pour montrer comment les feuilles de style peuvent être utilisées avec les pages de disposition. Si vous le souhaitez, vous pouvez définir vos propres styles pour ces éléments.
6. Dans le dossier racine, créez un fichier nommé *Content1.cshtml* et remplacer le contenu existant par le code suivant :

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample9.cshtml)]

    Il s’agit d’une page qui utilise une page de disposition. Le bloc de code en haut de la page indique quelle page de disposition à utiliser pour formater ce contenu.
7. Exécutez *Content1.cshtml* dans un navigateur. La page rendue utilise le format et la feuille de style est défini dans  *\_Layout1.cshtml* et le texte (contenu) défini dans *Content1.cshtml*.

    ![[image]](3-creating-a-consistent-look/_static/image4.jpg)

    Vous pouvez répéter l’étape 6 pour créer des pages de contenu supplémentaires que vous peuvent ensuite partager la même page de disposition.

    > [!NOTE]
    > Vous pouvez configurer votre site afin que vous pouvez utiliser automatiquement de la même page de disposition pour toutes les pages de contenu dans un dossier. Pour plus d’informations, consultez [personnalisation du comportement au niveau du Site pour ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202906).

## <a name="designing-layout-pages-that-have-multiple-content-sections"></a>Création de Pages de disposition qui ont plusieurs Sections de contenu

Une page de contenu peut avoir plusieurs sections, ce qui est utile si vous souhaitez utiliser des schémas qui ont plusieurs zones de contenu remplaçable. Dans la page de contenu, vous attribuez un nom unique chaque section. (Le reste de la section par défaut sans nom.) Dans la page de disposition, vous ajoutez un `RenderBody` méthode pour spécifier l’emplacement de la section sans nom (par défaut). Vous ajoutez ensuite distinct `RenderSection` méthodes pour restituer des sections nommées individuellement.

Le diagramme suivant illustre la manière dont ASP.NET gère un contenu qui est divisé en plusieurs sections. Chaque section nommée est contenue dans un bloc de section dans la page de contenu. (Ils sont nommés `Header` et `List` dans l’exemple.) Le framework insère section de contenu dans la page de disposition au point où la `RenderSection` méthode est appelée. La section sans nom (par défaut) est insérée au point où la `RenderBody` méthode est appelée, comme vous l’avez vu plus haut.

![Diagramme conceptuel montrant comment la méthode RenderSection insère des sections de références dans la page actuelle.](3-creating-a-consistent-look/_static/image5.jpg)

Cette procédure montre comment créer une page de contenu qui a plusieurs sections de contenu et le rendu à l’aide d’une page de disposition qui prend en charge plusieurs sections de contenu.

1. Dans le *Shared* dossier, créez un fichier nommé  *\_Layout2.cshtml*.
2. Remplacer le contenu existant avec les éléments suivants :

    [!code-html[Main](3-creating-a-consistent-look/samples/sample10.html)]

    Vous utilisez la `RenderSection` méthode pour restituer des sections de l’en-tête et la liste.
3. Dans le dossier racine, créez un fichier nommé *Content2.cshtml* et remplacer le contenu existant par le code suivant :

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample11.cshtml)]

    Cette page de contenu contient un bloc de code en haut de la page. Chaque section nommée est contenue dans un bloc de section. Le reste de la page contient la section de contenu par défaut (sans nom).
4. Exécutez *Content2.cshtml* dans un navigateur.

    ![Capture d’écran montrant une page dans le navigateur qui résulte de l’exécution d’une page qui inclut les appels à la méthode RenderSection.](3-creating-a-consistent-look/_static/image6.jpg)

## <a name="making-content-sections-optional"></a>Sections de contenu de fabrication facultatif

En règle générale, les sections que vous créez dans une page de contenu doivent correspondre les sections définies dans la page de disposition. Vous pouvez obtenir des erreurs si les éléments suivants se produisent :

- La page de contenu contient une section qui ne dispose d’aucune section correspondante dans la page de disposition.
- La page de disposition contient une section pour lequel il n’existe aucun contenu.
- La page de disposition inclut des appels de méthode que vous essayez d’afficher plusieurs fois de la même section.

Toutefois, vous pouvez substituer ce comportement pour une section nommée en déclarant la section facultative dans la page de disposition. Cela vous permet de définir plusieurs pages de contenu qui peuvent partager une page de disposition, mais qui peut ou peut ne pas avoir de contenu pour une section spécifique.

1. Ouvrez *Content2.cshtml* et supprimez la section suivante :

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample12.cshtml)]
2. Enregistrez la page et ensuite l’exécuter dans un navigateur. Un message d’erreur s’affiche, car la page de contenu ne fournit pas le contenu d’une section définie dans la page de disposition, à savoir la section d’en-tête.

    ![Capture d’écran qui affiche l’erreur se produit si vous exécutez une page qui appelle la méthode de RenderSection, mais la section correspondante n’est pas fournie.](3-creating-a-consistent-look/_static/image7.jpg)
3. Dans le *Shared* dossier, ouvrez le  *\_Layout2.cshtml* page et remplacez cette ligne :

    [!code-javascript[Main](3-creating-a-consistent-look/samples/sample13.js)]

    avec le code suivant :

    [!code-javascript[Main](3-creating-a-consistent-look/samples/sample14.js)]

    En guise d’alternative, vous pouvez remplacer la ligne de code précédente par le bloc de code suivant, qui produit les mêmes résultats :

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample15.cshtml)]
4. Exécutez le *Content2.cshtml* page dans un navigateur à nouveau. (Si cette page est toujours ouvert dans le navigateur, vous pouvez simplement l’actualiser.) Cette fois la page s’affiche sans erreur, même si elle n’a aucun en-tête.

## <a name="passing-data-to-layout-pages"></a>Passage de données pour les Pages de disposition

Vous pouvez avoir des données définies dans la page de contenu dont vous avez besoin de faire référence dans une page de disposition. Dans ce cas, vous devez passer les données à partir de la page de contenu à la page de disposition. Par exemple, vous souhaiterez peut-être afficher l’état de connexion d’un utilisateur, ou vous pouvez souhaiter afficher ou masquer les zones de contenu en fonction de l’entrée d’utilisateur.

Pour passer des données à partir d’une page de contenu à une page de disposition, vous pouvez placer des valeurs dans le `PageData` propriété de la page de contenu. Le `PageData` propriété est une collection de paires nom/valeur qui contiennent les données que vous souhaitez passer entre les pages. Dans la page de disposition, vous pouvez lire ensuite les valeurs de la `PageData` propriété.

Voici un autre diagramme. Cet extrait montre comment ASP.NET peut utiliser le `PageData` propriété pour passer des valeurs à partir d’une page de contenu à la page de disposition. Lorsque ASP.NET commence la construction de la page web, il crée le `PageData` collection. Dans la page de contenu, vous écrivez un programme pour placer des données le `PageData` collection. Les valeurs dans le `PageData` collection est également accessibles par d’autres sections de la page de contenu ou des blocs de contenu supplémentaires.

![Diagramme conceptuel qui montre comment une page de contenu peut remplir un dictionnaire PageData et transmettre ces informations à la page de disposition.](3-creating-a-consistent-look/_static/image8.jpg)

La procédure suivante montre comment passer des données à partir d’une page de contenu à une page de disposition. Lorsque la page s’exécute, il affiche un bouton qui permet à l’utilisateur de masquer ou afficher une liste qui est définie dans la page de disposition. Lorsque les utilisateurs cliquent sur le bouton, il définit une valeur vrai/faux (booléen) le `PageData` propriété. La page de disposition lit cette valeur et si elle est false, masque la liste. La valeur est également utilisée dans la page de contenu pour déterminer s’il faut afficher la **masquer la liste** bouton ou le **afficher la liste de** bouton.

![[image]](3-creating-a-consistent-look/_static/image9.jpg)

1. Dans le dossier racine, créez un fichier nommé *Content3.cshtml* et remplacer le contenu existant par le code suivant :

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample16.cshtml)]

    Le code stocke deux éléments de données dans le `PageData` propriété &#8212; le titre de la page web et la valeur true ou false pour spécifier s’il faut afficher la liste.

    Notez que ASP.NET vous permet de placer le balisage HTML dans la page de manière conditionnelle à l’aide d’un bloc de code. Par exemple, le `if/else` bloc dans le corps de la page détermine le formulaire à afficher, selon que `PageData["ShowList"]` est définie sur true.
2. Dans le *Shared* dossier, créez un fichier nommé  *\_Layout3.cshtml* et remplacer le contenu existant par le code suivant :

    [!code-cshtml[Main](3-creating-a-consistent-look/samples/sample17.cshtml)]

    La page de disposition inclut une expression dans le `<title>` élément qui obtient la valeur du titre de la `PageData` propriété. Elle utilise également le `ShowList` valeur de la `PageData` propriété pour déterminer s’il faut afficher le bloc de contenu de liste.
3. Dans le *Shared* dossier, créez un fichier nommé  *\_List.cshtml* et remplacer le contenu existant par le code suivant :

    [!code-html[Main](3-creating-a-consistent-look/samples/sample18.html)]
4. Exécutez le *Content3.cshtml* page dans un navigateur. La page s’affiche avec la liste visible sur le côté gauche de la page et un **masquer la liste** situé en bas.

    ![Capture d’écran montrant la page qui contient la liste et un bouton intitulé « Masquer la liste ».](3-creating-a-consistent-look/_static/image10.jpg)
5. Cliquez sur **masquer la liste**. Disparaît de la liste et le bouton devient **afficher la liste de**.

    ![Capture d’écran montrant la page qui n’inclut pas la liste et un bouton intitulé « Afficher la liste ».](3-creating-a-consistent-look/_static/image11.jpg)
6. Cliquez sur le **afficher la liste de** bouton et la liste s’affiche de nouveau.

## <a name="additional-resources"></a>Ressources supplémentaires


[Personnalisation du comportement de l’échelle du Site pour les Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202906)
