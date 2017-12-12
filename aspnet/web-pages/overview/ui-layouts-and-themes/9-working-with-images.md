---
uid: web-pages/overview/ui-layouts-and-themes/9-working-with-images
title: Utilisation des Images dans un Site de Web Pages (Razor) ASP.NET | Documents Microsoft
author: tfitzmac
description: Ce chapitre explique comment ajouter, afficher et manipuler des images (redimensionner, retourner et ajouter des filigranes) dans votre site Web.
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2014
ms.topic: article
ms.assetid: 778c4e58-4372-4d25-bab9-aec4a8d8e38d
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/9-working-with-images
msc.type: authoredcontent
ms.openlocfilehash: 74838dd364a43f3f4c966c1417d0f0b2cc242f28
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="working-with-images-in-an-aspnet-web-pages-razor-site"></a>Utilisation des Images dans un Site de Pages (Razor) Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article vous explique comment ajouter, afficher et manipuler des images (redimensionner, retourner et ajouter des filigranes) dans un site Web ASP.NET Web Pages (Razor).
> 
> Ce que vous allez apprendre :
> 
> - Comment ajouter une image à une page dynamique.
> - Comment permettre aux utilisateurs de télécharger une image.
> - Comment redimensionner une image.
> - Comment retourner ou faire pivoter une image.
> - Comment ajouter un filigrane à une image.
> - Comment utiliser une image en filigrane.
> 
> Il s’agit de programmation des fonctionnalités introduites dans l’article ASP.NET :
> 
> - Le `WebImage` helper.
> - Le `Path` objet, qui fournit des méthodes qui vous permettent de manipuler les noms de fichier et chemin d’accès.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 2
> - WebMatrix 2
>   
> 
> Ce didacticiel fonctionne également avec WebMatrix 3.


<a id="Adding_an_Image"></a>
## <a name="adding-an-image-to-a-web-page-dynamically"></a>Ajout d’une Image à une Page Web dynamique

Vous pouvez ajouter des images à votre site Web et aux pages individuelles quand vous développez le site Web. Vous pouvez également laisser les utilisateurs télécharger des images, qui peuvent être utiles pour des tâches telles que leur permettant d’ajouter une photo de profil.

Si une image est déjà disponible sur votre site et que vous souhaitez simplement afficher sur une page, vous utilisez HTML `<img>` élément comme suit :

[!code-html[Main](9-working-with-images/samples/sample1.html)]

Parfois, cependant, vous devez être en mesure d’afficher dynamiquement les images &#8212; Autrement dit, vous ne connaissez pas quelle image pour afficher jusqu'à ce que la page est en cours d’exécution.

La procédure décrite dans cette section montre comment afficher une image à la volée, où les utilisateurs spécifient le nom du fichier image à partir d’une liste de noms d’images. Ils sélectionnent le nom de l’image à partir d’une liste déroulante et lorsqu’ils envoient la page, l’image qu’ils ont sélectionné s’affiche.

![[image] ] (9-working-with-images/_static/image1.jpg "ch9images-1.jpg")

1. Dans WebMatrix, créez un nouveau site Web.
2. Ajouter une nouvelle page nommée *DynamicImage.cshtml*.
3. Dans le dossier racine du site Web, ajoutez un nouveau dossier et nommez-le *images*.
4. Ajouter des images de quatre à la *images* dossier que vous venez de créer. (Toutes les images vous avez est pratique, mais ils doivent tenir sur une page). Renommer les images *Photo1.jpg*, *Photo2.jpg*, *Photo3.jpg*, et *Photo4.jpg*. (Vous n’utilisez pas *Photo4.jpg* dans cette procédure, mais vous l’utiliserez ultérieurement dans l’article.)
5. Vérifiez que les quatre images ne sont pas marqués comme étant en lecture seule.
6. Remplacez le contenu existant dans la page avec les éléments suivants :

    [!code-cshtml[Main](9-working-with-images/samples/sample2.cshtml)]

    Le corps de la page a une liste déroulante (un `<select>` élément) qui est nommé `photoChoice`. La liste comporte trois options et le `value` attribut de chaque option de liste porte le nom de l’une des images que vous incluez dans le *images* dossier. Essentiellement, la liste lui permet de sélectionner un nom convivial tel que &quot;Photo 1&quot;, et elle passe ensuite le *.jpg* nom de fichier lorsque la page est envoyée.

    Dans le code, vous pouvez obtenir la sélection d’utilisateur (en d’autres termes, le nom du fichier image) dans la liste en lisant `Request["photoChoice"]`. Tout d’abord, vous voyez s’il existe une sélection du tout. S’il existe, vous construisez un chemin d’accès de l’image qui se compose du nom du dossier pour les images et nom du fichier image de l’utilisateur. (Si vous avez essayé de construire un chemin d’accès, mais rien dans `Request["photoChoice"]`, vous obtenez une erreur.) Ainsi, un chemin d’accès relatif comme suit :

    *images/Photo1.jpg*

    Le chemin d’accès est stocké dans une variable nommée `imagePath` que vous aurez besoin plus tard dans la page.

    Dans le corps, il existe également un `<img>` élément qui est utilisé pour afficher l’image choisi par l’utilisateur. Le `src` attribut n’est pas défini sur un nom de fichier ou une URL, comme vous le feriez pour afficher un élément statique. Au lieu de cela, elle est définie sur `@imagePath`, c'est-à-dire qu’elle obtient sa valeur de chemin d’accès que vous définissez dans le code.

    La première fois que la page s’exécute, cependant, n’est pas d’image à afficher, car rien n’a pas sélectionné par l’utilisateur. Cela normalement signifie que la `src` attribut serait vide et l’image apparaît comme un rouge &quot;x&quot; (ou quel que soit le navigateur restitue lorsqu’il ne peut pas trouver une image). Pour éviter cela, vous placez le `<img>` élément dans une `if` bloc qui vérifie si le `imagePath` variable a tout ce qu’il contient. Si l’utilisateur fait une sélection, `imagePath` contient le chemin d’accès. Si l’utilisateur n’a pas sélectionné une image ou si c’est la première fois, la page s’affiche, le `<img>` élément n’est pas encore rendu.
7. Enregistrez le fichier et exécuter la page dans un navigateur. (Assurez-vous que la page est sélectionnée dans le **fichiers** espace de travail avant de l’exécuter.)
8. Sélectionnez une image dans la liste déroulante, puis cliquez **exemple d’Image**. Assurez-vous que vous voyez des images pour les différents choix.

<a id="Uploading_an_Image"></a>
## <a name="uploading-an-image"></a>Téléchargement d’une Image

L’exemple précédent a montré comment afficher une image de manière dynamique, mais cela a fonctionné uniquement avec les images qui étaient déjà présents sur votre site Web. Cette procédure montre comment permettre aux utilisateurs de télécharger une image, qui est ensuite affichée sur la page. Dans ASP.NET, vous pouvez manipuler des images à la volée à l’aide de la `WebImage` assistance, qui possède des méthodes qui permettent de créer, de manipuler et d’enregistrer des images. Le `WebImage` assistance prend en charge tous les web image types de fichiers courants, y compris *.jpg*, *.png*, et *.bmp*. Dans cet article, vous allez utiliser *.jpg* images, mais vous pouvez utiliser les types d’image.

![[image] ] (9-working-with-images/_static/image2.jpg "ch9images-2.jpg")

1. Ajoutez une nouvelle page et nommez-la *UploadImage.cshtml*.
2. Remplacez le contenu existant dans la page avec les éléments suivants : 

    [!code-cshtml[Main](9-working-with-images/samples/sample3.cshtml)]

    Le corps du texte a un `<input type="file">` élément, ce qui permet aux utilisateurs de sélectionner un fichier à télécharger. Lorsqu’ils cliquent sur **envoyer**, le fichier qu’ils prélevés est envoyé en même temps que le formulaire.

    Pour obtenir l’image chargée, vous utilisez la `WebImage` assistance, ce qui a toutes sortes de méthodes utiles pour travailler avec des images. Plus précisément, vous utilisez `WebImage.GetImageFromRequest` pour obtenir l’image chargée (le cas échéant) et les stocker dans une variable nommée `photo`.

    Une grande partie du travail dans cet exemple implique l’obtention et définition des noms de fichier et chemin d’accès. Le problème est que vous souhaitez obtenir le nom (et le nom) de l’image de l’utilisateur est téléchargé, puis créer un nouveau chemin d’accès où vous allez stocker l’image. Étant donné que les utilisateurs peuvent télécharger potentiellement plusieurs images qui ont le même nom, vous utilisez un peu de code supplémentaire pour créer des noms uniques et assurez-vous que les utilisateurs ne pas remplacer les images existantes.

    Si une image a réellement été téléchargée (le test `if (photo != null)`), vous obtenez le nom de l’image à partir de l’image `FileName` propriété. Lorsque l’utilisateur télécharge l’image, `FileName` contient le nom de l’utilisateur d’origine, qui inclut le chemin d’accès à partir de l’ordinateur de l’utilisateur. Il peut ressembler à ceci :

    *C:\Users\Joe\Pictures\SamplePhoto1.jpg*

    Vous ne souhaitez pas que toutes les informations de ce chemin d’accès, bien que &#8212; vous souhaitez simplement le nom de fichier réel (*SamplePhoto1.jpg*). Vous pouvez retirer simplement le fichier à partir d’un chemin d’accès à l’aide de la `Path.GetFileName` méthode, comme suit :

    [!code-csharp[Main](9-working-with-images/samples/sample4.cs)]

    Vous créez ensuite un nouveau nom de fichier unique en ajoutant un GUID pour le nom d’origine. (Pour plus d’informations sur les GUID, consultez [sur GUID](#SB_AboutGUIDs) plus loin dans cet article.) Puis vous créez un chemin d’accès complet que vous pouvez utiliser pour enregistrer l’image. L’enregistrement chemin d’accès est constitué par le nouveau nom de fichier, le dossier (images) et l’emplacement du site Web actuel.

    > [!NOTE]
    > Dans l’ordre de votre code enregistrer les fichiers dans le *images* dossier, l’application doit en lecture-écriture des autorisations pour ce dossier. Sur votre ordinateur de développement il ne s’agit pas généralement un problème. Toutefois, lorsque vous publiez votre site à serveur de web du fournisseur d’hébergement, vous devez explicitement définir ces autorisations. Si vous exécutez ce code sur le serveur du fournisseur d’hébergement et que vous obtenez des erreurs, contactez le fournisseur d’hébergement pour savoir comment définir ces autorisations.

    Enfin, vous passez l’enregistrement chemin d’accès à la `Save` méthode de la `WebImage` helper. Stocke l’image chargée sous son nouveau nom. L’enregistrement méthode ressemble à ceci : `photo.Save(@"~\" + imagePath)`. Le chemin d’accès complet est ajouté à `@"~\"`, qui est l’emplacement du site Web actuel. (Pour plus d’informations sur la `~` (opérateur), consultez [Introduction à ASP.NET Web Programming à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890#ID_WorkingWithFileAndFolderPaths).)

    Comme dans l’exemple précédent, le corps de la page contient un `<img>` élément pour afficher l’image. Si `imagePath` a été défini, le `<img>` élément est rendu et son `src` attribut est défini sur la `imagePath` valeur.
3. Exécutez la page dans un navigateur.
4. Télécharger une image et assurez-vous qu’il est affiché dans la page.
5. Dans votre site, ouvrez le *images* dossier. Vous voyez qu’un nouveau fichier a été ajouté dont le nom ressemble à ceci : 

    *45ea4527-7ddd-4965-b9ca-c6444982b342\_MyPhoto.png*

    Il s’agit de l’image que vous avez téléchargé avec un GUID comme préfixé le nom. (Votre propre fichier aura un GUID différent et probablement nommé autre chose que *MyPhoto.png*.)

> [!TIP] 
> 
> <a id="SB_AboutGUIDs"></a>
> ### <a name="about-guids"></a>Sur les GUID
> 
> Un GUID (identificateur global unique) est un identificateur qui est généralement rendu dans un format comme suit : `936DA01F-9ABD-4d9d-80C7-02AF85C822A8`. Les chiffres et lettres (de A à F) différent pour chaque GUID, mais elles suivent le modèle d’utilisation de groupes de 8-4-4-4-12 caractères. (Techniquement, un GUID est un nombre 16 octets/128 bits). Lorsque vous avez besoin d’un GUID, vous pouvez appeler un code spécialisé qui génère un GUID pour vous. L’idée derrière le GUID est entre la taille du nombre (3.4 x 10<sup>38</sup>) et l’algorithme pour générer, le nombre résultant est pratiquement garanti être un des types. Par conséquent, les GUID est un bon moyen de générer des noms pour les éléments lorsque vous devez vous assurer que vous n’utilisez pas le même nom à deux reprises. L’inconvénient, bien entendu, est que GUID ne sont pas particulièrement convivial, afin qu’ils ont tendance à être utilisé lorsque le nom est utilisé uniquement dans le code.


<a id="Resizing_an_Image"></a>
## <a name="resizing-an-image"></a>Redimensionnement d'une image

Si votre site Web accepte les images à partir d’un utilisateur, vous souhaiterez redimensionner les images avant d’afficher ou de les enregistrer. Vous pouvez utiliser à nouveau la `WebImage` auxiliaire pour cela.

Cette procédure montre comment redimensionner une image téléchargée pour créer une miniature, puis enregistrez des miniatures et l’image d’origine dans le site Web. Affichez la miniature sur la page, un lien hypertexte permet de rediriger les utilisateurs vers l’image en taille réelle.

![[image] ] (9-working-with-images/_static/image3.jpg "ch9images-3.jpg")

1. Ajouter une nouvelle page nommée *Thumbnail.cshtml*.
2. Dans le *images* dossier, créez un sous-dossier nommé *pouces*.
3. Remplacez le contenu existant dans la page avec les éléments suivants : 

    [!code-cshtml[Main](9-working-with-images/samples/sample5.cshtml)]

    Ce code est similaire au code de l’exemple précédent. La différence est que ce code enregistre l’image à deux reprises, une fois normalement et qu’une seule fois après avoir créé une copie miniature de l’image. Tout d’abord obtenir l’image chargée et l’enregistrer dans le *images* dossier. Puis, vous créez un nouveau chemin d’accès de l’image miniature. Pour créer la miniature, vous appelez le `WebImage` l’Assistant `Resize` méthode pour créer une image de 60-par 60 pixels. L’exemple montre comment vous conservez les proportions et comment vous pouvez empêcher l’image agrandi (au cas où la nouvelle taille est réellement agrandir l’image). Image redimensionnée est ensuite enregistré dans le *pouces* sous-dossier.

    À la fin du balisage, vous utilisez le même `<img>` élément avec la dynamique `src` attribut que vous l’avez vu dans les exemples précédents pour conditionnellement afficher l’image. Dans ce cas, vous Affrichez la miniature. Vous utilisez également un `<a>` élément pour créer un lien hypertexte vers la version grand de l’image. Comme avec la `src` attribut de la `<img>` élément, vous définissez la `href` attribut de la `<a>` élément dynamiquement à tout ce qui est dans `imagePath`. Pour vous assurer que le chemin d’accès peut être utilisé comme une URL, vous passez `imagePath` à la `Html.AttributeEncode` (méthode), qui convertit les caractères réservés dans le chemin d’accès à des caractères sont OK dans une URL.
4. Exécutez la page dans un navigateur.
5. Télécharger une photo et vérifiez que la miniature est affichée.
6. Cliquez sur la miniature pour afficher l’image en taille réelle.
7. Dans le *images* et *images/pouces*, notez que les nouveaux fichiers ont été ajoutés.

<a id="Rotating_and_Flipping"></a>
## <a name="rotating-and-flipping-an-image"></a>Rotation et retournement d’une Image

Le `WebImage` helper vous permet également de retourner et faire pivoter des images. Cette procédure montre comment obtenir une image à partir du serveur, retourner l’image de haut en bas (verticalement), enregistrez-le, puis afficher l’image renversée sur la page. Dans cet exemple, vous utilisez uniquement un fichier que vous avez déjà sur le serveur (*Photo2.jpg*). Dans une application réelle, vous serez probablement inverser une image dont le nom vous obtenez dynamiquement, comme vous l’avez fait dans les exemples précédents.

![[image] ] (9-working-with-images/_static/image4.jpg "ch9images-4.jpg")

1. Ajouter une nouvelle page nommée *FlipImage.cshtml*.
2. Remplacez le contenu existant dans la page avec les éléments suivants : 

    [!code-cshtml[Main](9-working-with-images/samples/sample6.cshtml)]

    Le code utilise le `WebImage` application d’assistance pour obtenir une image à partir du serveur. Vous créez le chemin d’accès à l’image à l’aide de la même technique que vous avez utilisé dans les exemples précédents pour l’enregistrement des images et que vous passez ce chemin d’accès lorsque vous créez une image à l’aide de `WebImage`:

    [!code-javascript[Main](9-working-with-images/samples/sample7.js)]

    Si une image est trouvée, vous construisez un nouveau chemin d’accès et nom de fichier, comme vous l’avez fait dans les exemples précédents. Pour retourner l’image, vous appelez le `FlipVertical` (méthode), puis enregistrez à nouveau l’image.

    L’image s’affiche à nouveau sur la page à l’aide de la `<img>` élément avec la `src` attribut la valeur `imagePath`.
3. Exécutez la page dans un navigateur. L’image pour *Photo2.jpg* est indiqué en haut en bas.
4. Actualisez la page ou demander la page pour voir que l’image est retournée à droite de nouveau.

Pour faire pivoter une image, vous utilisez le même code, à ceci près qu’au lieu d’appeler le `FlipVertical` ou `FlipHorizontal`, vous appelez `RotateLeft` ou `RotateRight`.

<a id="Adding_a_Watermark"></a>
## <a name="adding-a-watermark-to-an-image"></a>Ajout d’un filigrane à une Image

Lorsque vous ajoutez des images à votre site Web, vous souhaiterez ajouter un filigrane à l’image avant d’enregistrer ou afficher sur une page. Personnes utilisent souvent des filigranes pour ajouter les informations de copyright à une image ou pour annoncer leur nom de l’entreprise.

![[image] ] (9-working-with-images/_static/image5.jpg "ch9images-5.jpg")

1. Ajouter une nouvelle page nommée *Watermark.cshtml*.
2. Remplacez le contenu existant dans la page avec les éléments suivants : 

    [!code-cshtml[Main](9-working-with-images/samples/sample8.cshtml)]

    Ce code est semblable au code dans le *FlipImage.cshtml* page précédemment (bien que cette fois, elle utilise le *Photo3.jpg* fichier). Pour ajouter le filigrane, vous appelez le `WebImage` l’Assistant `AddTextWatermark` méthode avant d’enregistrer l’image. Dans l’appel à `AddTextWatermark`, vous passez le texte &quot;mon filigrane&quot;, définir la couleur de police en jaune et la valeur de la famille de polices Arial. (Bien qu’il n’est pas indiqué, le `WebImage` helper vous permet également de spécifier l’opacité, famille de polices et taille de police et la position du texte de filigrane.) Lorsque vous enregistrez l’image il ne doit pas être en lecture seule.

    Comme vous l’avez vu, l’image est affichée sur la page à l’aide de la `<img>` élément avec l’attribut src `@imagePath`.
3. Exécutez la page dans un navigateur. Notez le texte « Mon filigrane » dans le coin en bas à droite de l’image.

<a id="Using_an_Image_as_a_Watermark"></a>
## <a name="using-an-image-as-a-watermark"></a>À l’aide d’une Image en filigrane

Au lieu d’utiliser le texte de filigrane, vous pouvez utiliser une autre image. Personnes utilisent parfois des images comme un logo d’entreprise comme filigrane, ou ils utilisent une image en filigrane au lieu de texte pour les informations de copyright.

![[image] ] (9-working-with-images/_static/image6.jpg "ch9images-6.jpg")

1. Ajouter une nouvelle page nommée *ImageWatermark.cshtml*.
2. Ajouter une image à la *images* dossier que vous pouvez utiliser comme un logo et renommez l’image *MyCompanyLogo.jpg*. Cette image doit être une image que vous pouvez voir clairement quand il a la valeur 80 pixels de large et 20 pixels de hauteur.
3. Remplacez le contenu existant dans la page avec les éléments suivants : 

    [!code-cshtml[Main](9-working-with-images/samples/sample9.cshtml)]

    Il s’agit d’une autre variante sur le code dans les exemples précédents. Dans ce cas, vous appelez `AddImageWatermark` pour ajouter l’image en filigrane à l’image cible (*Photo3.jpg*) avant d’enregistrer l’image. Lorsque vous appelez `AddImageWatermark`, définissez sa largeur à 80 pixels et la hauteur à 20 pixels. Le *MyCompanyLogo.jpg* image est aligné horizontalement dans le centre et aligné verticalement en bas de l’image de la cible. L’opacité est définie sur 100 % et la marge intérieure est définie à 10 pixels. Si l’image en filigrane est plus grand que l’image cible, rien ne se produira. Si l’image en filigrane est plus grand que l’image cible et que vous définissez le remplissage de l’image en filigrane à zéro, le filigrane est ignoré.

    Comme avant, vous affichez l’image à l’aide de la `<img>` élément et un dynamique `src` attribut.
4. Exécutez la page dans un navigateur. Notez que l’image en filigrane apparaît au bas de l’image principale.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


[Utilisation des fichiers dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202896)

[Introduction à la programmation à l’aide de la syntaxe Razor ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkID=251587)
