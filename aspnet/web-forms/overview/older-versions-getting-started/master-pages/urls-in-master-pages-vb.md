---
uid: web-forms/overview/older-versions-getting-started/master-pages/urls-in-master-pages-vb
title: "URL dans les Pages maîtres (VB) | Documents Microsoft"
author: rick-anderson
description: "Adresses comment URL dans la page maître peuvent provoquer des problèmes en raison du fichier de page maître en cours dans un autre répertoire relatif à la page de contenu. Examine la relocalisation..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2008
ms.topic: article
ms.assetid: 43d1e83c-0092-4dcf-977c-e709c4dce7c3
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/master-pages/urls-in-master-pages-vb
msc.type: authoredcontent
ms.openlocfilehash: 8aa0ed2fbf385e4b8dbb7e7a3bdb152f1e016e67
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="urls-in-master-pages-vb"></a>URL dans les Pages maîtres (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/e/e/f/eef369f5-743a-4a52-908f-b6532c4ce0a4/ASPNET_MasterPages_Tutorial_04_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/8/f/6/8f6349e4-6554-405a-bcd7-9b094ba5089a/ASPNET_MasterPages_Tutorial_04_VB.pdf)

> Adresses comment URL dans la page maître peuvent provoquer des problèmes en raison du fichier de page maître en cours dans un autre répertoire relatif à la page de contenu. Examine l’URL via la relocalisation ~ dans la syntaxe déclarative et à l’aide de ResolveUrl et ResolveClientUrl par programme. (Également consulter


## <a name="introduction"></a>Introduction

Dans tous les exemples de nous l’avons vu jusqu'à présent, que les pages maîtres et de contenu sont placées dans le même dossier (du site Web racine). Mais il n’existe aucune raison pourquoi les pages maîtres et de contenu doivent être dans le même dossier. Vous pouvez certainement créer les pages de contenu dans les sous-dossiers. De même, vous pouvez créer un `~/MasterPages/` dossier où vous placez les pages maîtres de votre site.

Un problème potentiel avec le placement des pages maîtres et de contenu dans des dossiers différents implique URL rompues. Si la page maître contient les URL relatives dans les liens hypertexte, des images ou autres éléments, le lien n’est pas valide pour les pages de contenu qui se trouvent dans un dossier différent. Dans ce didacticiel, nous examinons la source de ce problème, ainsi que les solutions de contournement.

## <a name="the-problem-with-relative-urls"></a>Le problème avec des URL relatives

Une URL sur une page web est dite un *URL relative* si l’emplacement de la ressource vers laquelle il pointe est par rapport à l’emplacement de la page web dans la structure de dossiers du site Web. N’importe quelle URL qui ne commence pas par une barre oblique (`/`) ou un protocole (tel que `http://`) est relatif, car il est résolu par le navigateur basé sur l’emplacement de la page web qui contient l’URL.

Par exemple, notre site Web a un `~/Images/` dossier avec un seul fichier image, `PoweredByASPNET.gif`. Le fichier de page maître `Site.master` a un `<img>` élément dans le `footerContent` région avec le balisage suivant :


[!code-html[Main](urls-in-master-pages-vb/samples/sample1.html)]

Le `src` attribut la valeur dans la `<img>` élément est une URL relative, car il ne commence pas par `/` ou `http://`. En bref, la `src` valeur de l’attribut indique au navigateur pour rechercher dans le `Images` sous-dossier pour un fichier nommé `PoweredByASPNET.gif`.

Lorsque vous visitez une page de contenu, le balisage ci-dessus est envoyé directement au navigateur. Prenez un moment pour visiter `About.aspx` et afficher la source HTML envoyée au navigateur. Vous constaterez que la balise même exacte dans la page maître a été envoyée au navigateur.


[!code-html[Main](urls-in-master-pages-vb/samples/sample2.html)]

Si la page de contenu se trouve dans le dossier racine (comme est `About.aspx`) tout fonctionne comme prévu, car il existe un `Images` sous-dossier relatif au dossier racine. Toutefois, panne vers le bas si la page de contenu se trouve dans un dossier autre que celui de la page maître. Pour illustrer cela, créez un sous-dossier nommé `Admin`. Ensuite, ajoutez une page de contenu nommée `Default.aspx` à la `Admin` dossier, en veillant à lier la nouvelle page à la `Site.master` page maître.

> [!NOTE]
> Dans le [ *spécifiant le titre, les balises Meta et les autres en-têtes HTML dans la Page maître* ](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-vb.md) didacticiel, nous avons créé une classe de page de base personnalisée nommée `BasePage` qui définir automatiquement le contenu du titre de page (si elle a été pas explicitement affectée). N’oubliez pas d’avoir classe code-behind de la page nouvellement créé dérivent `BasePage` afin qu’il peut utiliser cette fonctionnalité.


Une fois que vous avez créé cette page de contenu, l’Explorateur de solutions doit ressembler à la Figure 1.


![Un nouveau dossier et une Page ASP.NET ont été ajoutés au projet](urls-in-master-pages-vb/_static/image1.png)

**Figure 01**: un nouveau dossier et une Page ASP.NET ont été ajoutés au projet


Ensuite, mettez à jour le `Web.sitemap` pour inclure un nouveau fichier `<siteMapNode>` entrée pour cette leçon. Le code XML suivant montre l’intégralité `Web.sitemap` balisage, ce qui inclut à présent d’un tiers `<siteMapNode>` élément.


[!code-xml[Main](urls-in-master-pages-vb/samples/sample3.xml)]

Nouvellement créé `Default.aspx` page doit comporter quatre contrôles de contenu correspondant aux quatre ContentPlaceHolders dans `Site.master`. Ajouter du texte dans le contrôle de contenu faisant référence à la `MainContent` ContentPlaceHolder, puis vous accédez à la page via un navigateur. Comme le montre la Figure 2, le navigateur ne peut pas trouver le `PoweredByASPNET.gif` fichier image. Qu'est-ce qui se passe ?

Le `~/Admin/Default.aspx` page de contenu est envoyée le même code HTML pour le `footerContent` région tel qu’il a été le `About.aspx` page :


[!code-html[Main](urls-in-master-pages-vb/samples/sample4.html)]

Étant donné que la `<img>` l’élément `src` attribut est une URL relative, le navigateur tente de rechercher un `Images` dossier par rapport à l’emplacement du dossier de la page web. En d’autres termes, le navigateur recherche le fichier image `Admin/Images/PoweredByASPNET.gif`.


[![Impossible de trouver le fichier Image de PoweredByASPNET.gif](urls-in-master-pages-vb/_static/image3.png)](urls-in-master-pages-vb/_static/image2.png)

**Figure 02**: le `PoweredByASPNET.gif` Image fichier est introuvable ([cliquez pour afficher l’image en taille réelle](urls-in-master-pages-vb/_static/image4.png))


### <a name="replacing-relative-urls-with-absolute-urls"></a>Remplacement des URL relatives avec une URL absolue

L’inverse d’une URL relative est un *URL absolue*, qui est une tâche qui commence par une barre oblique (`/`) ou un protocole comme `http://`. Comme une URL absolue Spécifie l’emplacement d’une ressource à partir d’un point fixe connu, la même URL absolue est valide dans une page web, indépendamment de l’emplacement de la page web dans la structure de dossiers du site Web.

Pour remédier à l’image rompue indiqué dans la Figure 2, nous devons mettre à jour le `<img>` l’élément `src` afin qu’il utilise une URL absolue au lieu d’une relative d’attribut. Pour déterminer la bonne URL absolue, reportez-vous à l’un des pages web dans votre site Web et examinez la barre d’adresses. Comme le montre la barre d’adresse dans la Figure 2, le chemin d’accès complet à l’application web est `http://localhost:3908/ASPNET_MasterPages_Tutorial_04_VB/`. Par conséquent, nous pouvons mettre à jour le `<img>` l’élément `src` d’attribut à un des deux URL absolues suivantes :

- `/ASPNET_MasterPages_Tutorial_04_VB/Images/PoweredByASPNET.gif`
- `http://localhost:3908/ASPNET_MasterPages_Tutorial_04_VB/Images/PoweredByASPNET.gif`

Prenez un moment pour mettre à jour le `<img>` l’élément `src` d’attribut à une URL absolue à l’aide d’une des formes ci-dessus, puis visitez le `~/Admin/Default.aspx` page via un navigateur. Cette fois, le navigateur sera correctement rechercher et afficher le `PoweredByASPNET.gif` fichier image (voir Figure 3).


[![Le PoweredByASPNET.gif Image est maintenant affichée](urls-in-master-pages-vb/_static/image6.png)](urls-in-master-pages-vb/_static/image5.png)

**Figure 03**: le `PoweredByASPNET.gif` Image est maintenant affichée ([cliquez pour afficher l’image en taille réelle](urls-in-master-pages-vb/_static/image7.png))


Alors que fonctionne de manière irréversible dans l’URL absolue, il associe étroitement votre code HTML pour les serveurs du site Web et l’emplacement de dossier, qui peut-être changer. À l’aide d’une URL absolue de la forme `http://localhost:3908/...` est fragile, car le numéro de port qui précède localhost est sélectionné automatiquement chaque démarrage d’un serveur Web de développement intégré ASP.NET de Visual Studio. De même, la `http://localhost` partie est valide uniquement lorsque vous testez localement. Une fois le code est déployé sur un serveur de production, la base d’URL changera autre chose, tel que `http://www.yourserver.com`. L’URL absolue dans le formulaire `/ASPNET_MasterPages_Tutorial_04_VB/...` souffre également la même fragilité, car il est souvent ce chemin d’accès de l’application est différent entre les serveurs de développement et de production.

La bonne nouvelle est que ASP.NET propose une méthode pour générer une URL relative valide lors de l’exécution.

## <a name="usingandresolveclienturl"></a>À l’aide de`~`et`ResolveClientUrl`

Plutôt que de coder en dur une URL absolue, ASP.NET permet aux développeurs de page à utiliser le tilde (`~`) pour indiquer la racine de l’application web. Par exemple, plus haut dans ce didacticiel, j’ai utilisé la notation `~/Admin/Default.aspx` dans le texte pour faire référence à la `Default.aspx` page dans le `Admin` dossier. Le `~` indique que le `Admin` dossier est un sous-dossier de la racine de l’application web.

Le `Control` de classe [ `ResolveClientUrl` méthode](https://msdn.microsoft.com/library/system.web.ui.control.resolveclienturl.aspx) accepte une URL et le modifie pour une URL relative appropriée pour la page web sur lequel réside le contrôle. Par exemple, l’appel `ResolveClientUrl("~/Images/PoweredByASPNET.gif")` de `About.aspx` retourne `Images/PoweredByASPNET.gif`. Appel de `~/Admin/Default.aspx`, cependant, retourne `../Images/PoweredByASPNET.gif`.

> [!NOTE]
> Étant donné que tous les contrôles de serveur ASP.NET dérivent de la `Control` (classe), tous les contrôles serveur ont accès à la `ResolveClientUrl` (méthode). Même la `Page` classe dérive de la `Control` (classe), c'est-à-dire que vous pouvez utiliser cette méthode directement à partir de classes de code-behind de vos pages ASP.NET.


### <a name="usingin-the-declarative-markup"></a>À l’aide de`~`dans le balisage déclaratif

Plusieurs contrôles Web ASP.NET incluent les propriétés URL : le contrôle de lien hypertexte a un `NavigateUrl` propriété ; l’Image de contrôle a une `ImageUrl` propriété ; et ainsi de suite. Lors du rendu, ces contrôles transmettent les valeurs de propriété d’URL pour `ResolveClientUrl`. Par conséquent, si ces propriétés contiennent un `~` pour indiquer la racine de l’application web, l’URL sera modifié pour une URL relative valide.

N’oubliez pas que seuls des contrôles serveur ASP.NET transforment le `~` dans leurs propriétés de l’URL. Si un `~` apparaît dans le balisage HTML statique, tel que `<img src="~/Images/PoweredByASPNET.gif" />`, le moteur ASP.NET envoie le `~` dans le navigateur avec le reste du contenu HTML. Le navigateur suppose que le `~` fait partie de l’URL. Par exemple, si le navigateur reçoit le balisage `<img src="~/Images/PoweredByASPNET.gif" />` il suppose qu’il existe un sous-dossier nommé `~` avec un sous-dossier `Images` qui contient le fichier image `PoweredByASPNET.gif`.

Pour corriger le balisage de l’image dans `Site.master`, remplacer la `<img>` élément avec un contrôle ASP.NET Image Web. Définir le contrôle d’Image Web `ID` à `PoweredByImage`, ses `ImageUrl` propriété `~/Images/PoweredByASPNET.gif`et son `AlternateText` propriété « Optimisé par ASP.NET ! »


[!code-aspx[Main](urls-in-master-pages-vb/samples/sample5.aspx)]

Après avoir apporté cette modification à la page maître, vous devez revoir la `~/Admin/Default.aspx` page à nouveau. Cette fois le `PoweredByASPNET.gif` fichier image apparaît dans la page (voir Figure 3). Lorsque le Web de l’Image de contrôle est restitué il utilise le `ResolveClientUrl` méthode pour résoudre son `ImageUrl` valeur de propriété. Dans `~/Admin/Default.aspx` le `ImageUrl` est convertie en une URL relative appropriée, comme l’extrait suivant de la source HTML affiche :


[!code-html[Main](urls-in-master-pages-vb/samples/sample6.html)]

> [!NOTE]
> En plus utilisée dans les propriétés des contrôles Web basés sur des URL, le `~` peut également être utilisé lors de l’appel du `Response.Redirect` et `Server.MapPath` méthodes, entre autres. En outre, le `ResolveClientUrl` méthode peut être appelée directement à partir d’ASP.NET ou balisage déclaratif de la page maître, si nécessaire, consultez [Fritz Onion](https://www.pluralsight.com/blogs/fritz/)d’entrée de blog [Using `ResolveClientUrl` dans le balisage](https://www.pluralsight.com/blogs/fritz/archive/2006/02/06/18596.aspx).


## <a name="fixing-the-master-pages-remaining-relative-urls"></a>Résolution de la Page maître restant des URL relatives

Outre la `<img>` élément dans le `footerContent` que nous avons résolu simplement, la page maître contient une URL relative plus nécessitant votre attention. Le `topContent` région inclut le lien « Master Pages didacticiels » qui pointe vers `Default.aspx`.


[!code-html[Main](urls-in-master-pages-vb/samples/sample7.html)]

Étant donné que cette URL est relative, il envoie à l’utilisateur le `Default.aspx` page dans le dossier de la page de contenu qu’ils visitent. Pour que ce lien toujours pointer vers `Default.aspx` dans le dossier racine, nous devons remplacer le `<a>` élément avec un site Web de lien hypertexte de contrôle afin que nous pouvons utiliser le `~` notation.

Supprimer le `<a>` balisage d’élément et ajouter un contrôle de lien hypertexte à la place. Valeur du lien hypertexte `ID` à `lnkHome`, ses `NavigateUrl` propriété `~/Default.aspx`et son `Text` propriété « Didacticiels de Pages maître ».


[!code-aspx[Main](urls-in-master-pages-vb/samples/sample8.aspx)]

C’est tout ! À ce stade, toutes les URL dans notre page maître reposent correctement lors du rendu à une page de contenu, quelle que soit les dossiers de la page maître et la page de contenu sont situés dans.

### <a name="automatic-url-resolution-in-theheadsection"></a>Résolution des URL automatique dans le`<head>`Section

Dans le [ *création d’une disposition à l’échelle du Site à l’aide des Pages maîtres* ](creating-a-site-wide-layout-using-master-pages-vb.md) didacticiel, nous avons ajouté un `<link>` à la `Styles.css` de fichiers dans le `<head>` région :


[!code-aspx[Main](urls-in-master-pages-vb/samples/sample9.aspx)]

Alors que le `<link>` l’élément `href` attribut est relatif, il est automatiquement converti en un chemin d’accès approprié lors de l’exécution. Comme expliqué dans la [ *spécifiant le titre, les balises Meta et les autres en-têtes HTML dans la Page maître* ](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-vb.md) (didacticiel), le `<head>` région est en fait un contrôle côté serveur, ce qui lui permet de modifier le contenu de ses contrôles internes lorsqu’il est restitué.

Pour cela, vous devez revoir la `~/Admin/Default.aspx` page et afficher la source HTML envoyée au navigateur. Comme l’illustre l’extrait de code ci-dessous, le `<link>` l’élément `href` attribut a été modifié automatiquement vers une URL relative appropriée, `../Styles.css`.


[!code-html[Main](urls-in-master-pages-vb/samples/sample10.html)]

## <a name="summary"></a>Récapitulatif

Les pages maîtres sont très souvent des liens, des images et autres ressources externes qui doivent être spécifiés via une URL. Étant donné que la page maître et les pages de contenu ne peuvent pas exister dans le même dossier, il est important abstenir d’à l’aide des URL relatives. S’il est possible d’utiliser des URL absolues codée en dur, faisant ainsi étroitement associe l’URL absolue à l’application web. Si les modifications de l’URL absolue - comme il le fait souvent lors du déplacement ou de déploiement d’une application web - vous devrez n’oubliez pas de revenir en arrière et mettre à jour l’URL absolues.

L’approche idéale consiste à utiliser le tilde (`~`) pour indiquer la racine de l’application. Mappent les contrôles Web ASP.NET qui contient les propriétés URL le `~` à la racine de l’application lors de l’exécution. En interne, les contrôles Web utilisent la `Control` de classe `ResolveClientUrl` méthode permettant de générer une URL relative valide. Cette méthode est publique et disponible à partir de chaque contrôle de serveur (y compris la `Page` classe), vous pouvez l’utiliser par programme à partir de vos classes de code-behind, si nécessaire.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Pages maîtres dans ASP.NET](http://www.odetocode.com/Articles/419.aspx)
- [Relocalisation des URL dans une Page maître](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/masterpages/default.aspx#urls)
- [À l’aide de `ResolveClientUrl` dans le balisage](https://www.pluralsight.com/blogs/fritz/archive/2006/02/06/18596.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de plusieurs manuels ASP/ASP.NET et de créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 3.5 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Scott peut être atteint à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com).

>[!div class="step-by-step"]
[Précédent](specifying-the-title-meta-tags-and-other-html-headers-in-the-master-page-vb.md)
[Suivant](control-id-naming-in-content-pages-vb.md)
