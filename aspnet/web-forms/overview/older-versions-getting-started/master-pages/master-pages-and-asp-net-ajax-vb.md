---
uid: web-forms/overview/older-versions-getting-started/master-pages/master-pages-and-asp-net-ajax-vb
title: "Les Pages maîtres et ASP.NET AJAX (VB) | Documents Microsoft"
author: rick-anderson
description: "Présente les options pour l’utilisation de pages maîtres et ASP.NET AJAX. Décrit l’utilisation de la classe ScriptManagerProxy ; Explique comment les différents fichiers JS sont chargés dependi..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/11/2008
ms.topic: article
ms.assetid: 0ee9318c-29bb-4d58-b1dc-94e575b8ae10
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/master-pages/master-pages-and-asp-net-ajax-vb
msc.type: authoredcontent
ms.openlocfilehash: b25234f82c46437d853d1ab5b240f8a688995ccc
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="master-pages-and-aspnet-ajax-vb"></a>Les Pages maîtres et ASP.NET AJAX (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/1/8/4/184e24fa-fcc8-47fa-ac99-4b6a52d41e97/ASPNET_MasterPages_Tutorial_08_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/e/b/4/eb4abb10-c416-4ba4-9899-32577715b1bd/ASPNET_MasterPages_Tutorial_08_VB.pdf)

> Présente les options pour l’utilisation de pages maîtres et ASP.NET AJAX. Décrit l’utilisation de la classe ScriptManagerProxy ; Explique comment les différents fichiers JS sont chargés selon que ScriptManager est utilisé dans le contrôleur de page ou la page de contenu.


## <a name="introduction"></a>Introduction

Sur plusieurs années, les applications web compatibles AJAX ont été création de plus en plus de développeurs. Un site Web compatibles AJAX utilise un nombre de technologies web connexes pour offrir une expérience utilisateur plus réactive. Créer des applications ASP.NET compatibles AJAX est étonnamment facile grâce au framework d’ASP.NET AJAX de Microsoft. ASP.NET AJAX est intégrée à ASP.NET 3.5 et Visual Studio 2008 ; Il est également disponible en téléchargement séparé pour les applications ASP.NET 2.0.

Lorsque vous créez des pages web compatibles AJAX avec l’infrastructure ASP.NET AJAX, vous devez ajouter précisément un contrôle ScriptManager à chaque page qui utilise l’infrastructure. Comme son nom l’indique, ScriptManager gère le script côté client utilisé dans les pages web compatibles AJAX. Au minimum, ScriptManager émet un code HTML qui indique au navigateur de télécharger les fichiers JavaScript utilisés Client ASP.NET AJAX Library. Il peut également être utilisé pour inscrire des fichiers JavaScript personnalisés, services web compatibles sur le script et fonctionnalités de service d’application personnalisée.

Si les pages de votre maître d’utilise de site (comme il le devrait), il est nécessairement inutile ajouter un contrôle ScriptManager à chaque page de contenu unique ; au lieu de cela, vous pouvez ajouter un contrôle ScriptManager à la page maître. Ce didacticiel montre comment ajouter le contrôle ScriptManager à la page maître. Il examine également comment utiliser le contrôle ScriptManagerProxy pour inscrire des scripts personnalisés et des services de script dans une page de contenu spécifique.

> [!NOTE]
> Ce didacticiel ne pas Explorer conception ou de la génération d’applications web compatibles AJAX avec l’infrastructure ASP.NET AJAX. Pour plus d’informations sur l’utilisation d’AJAX, consultez les vidéos ASP.NET AJAX et didacticiels, ainsi que les ressources répertoriées dans la section obtenir des informations supplémentaires à la fin de ce didacticiel.


## <a name="examining-the-markup-emitted-by-the-scriptmanager-control"></a>Examiner le balisage émis par le contrôle ScriptManager

Le contrôle ScriptManager émet une balise qui indique au navigateur pour télécharger les fichiers JavaScript utilisés Client ASP.NET AJAX Library. Il ajoute également un peu d’inline JavaScript à la page qui initialise cette bibliothèque. Le balisage suivant montre le contenu qui est ajouté à la sortie rendue d’une page qui inclut un contrôle ScriptManager :


[!code-html[Main](master-pages-and-asp-net-ajax-vb/samples/sample1.html)]

Le `<script src="url"></script>` balises demander le navigateur pour télécharger et exécuter le fichier JavaScript à *url*. ScriptManager émet trois de ces balises. une référence au fichier `WebResource.axd`, tandis que les deux autres référencent le fichier `ScriptResource.axd`. Ces fichiers n’existent pas réellement en tant que fichiers dans votre site Web. Au lieu de cela, lorsqu’une demande pour l’un de ces fichiers arrive sur le serveur web, le moteur ASP.NET examine la chaîne de requête et retourne le contenu JavaScript approprié. Le script fourni par ces trois fichiers JavaScript externes constituent la bibliothèque cliente de l’infrastructure ASP.NET AJAX. L’autre `<script>` balises émis par ScriptManager incluent un script inline qui initialise cette bibliothèque.

Les références de script externe et le script inline émis par ScriptManager sont essentielles pour une page qui utilise l’infrastructure ASP.NET AJAX, mais il n’est pas nécessaire pour les pages qui n’utilisent pas de l’infrastructure. Par conséquent, vous pouvez motif qu’il est idéal pour uniquement ajouter un ScriptManager à ces pages qui utilisent l’infrastructure ASP.NET AJAX. Et cela est suffisant, mais si vous disposez de plusieurs pages qui utilisent l’infrastructure vous obtiendrez ajouter le contrôle ScriptManager toutes les pages : une tâche répétitive, pour n’en citer. Vous pouvez également ajouter un ScriptManager à la page maître, qui ensuite injecte ce script nécessaire dans toutes les pages de contenu. Avec cette approche, vous n’avez pas besoin de n’oubliez pas d’ajouter un ScriptManager à une nouvelle page qui utilise l’infrastructure ASP.NET AJAX, car il est déjà fourni par la page maître. Étape 1 présente à ajouter un ScriptManager à la page maître.

> [!NOTE]
> Si vous projetez d’inclure des fonctionnalités AJAX dans l’interface utilisateur de votre page maître, puis, vous devez en la matière : vous devez inclure le ScriptManager dans la page maître.


Un inconvénient de l’ajout de ScriptManager à la page maître est que le script ci-dessus est émis dans *chaque* page, indépendamment de si elle est nécessaire. Cela entraîne clairement la bande passante inutile pour les pages qui ont le ScriptManager inclus (via la page maître), mais n’utilisent pas toutes les fonctionnalités de l’infrastructure ASP.NET AJAX. Mais uniquement la quantité de perte de la bande passante ?

- Le contenu réel émis par ScriptManager (illustré ci-dessus) des totaux d’un peu plus de 1 Ko.
- Les trois fichiers de script externe référencés par le `<script>` élément, toutefois, comprendre environ 450 Ko de données non compressées ; dans un site Web qui utilise la compression gzip, la bande passante totale peut être réduite près de 100 Ko. Toutefois, ces fichiers de script sont mis en cache par le navigateur pendant un an, c'est-à-dire qu’ils ne doivent être téléchargé qu’une seule fois et puis peuvent être réutilisés dans d’autres pages sur le site.

Dans le meilleur des cas, puis, lorsque les fichiers de script sont mis en cache, le coût total est 1 Ko, qui est négligeable. Dans le pire des cas, cependant, c'est-à-dire lorsque les fichiers de script n’ont pas encore été téléchargées et le serveur web n’utilise pas toutes les formes de compression - le positionnement de la bande passante est environ 450 Ko, ce qui peut ajouter n’importe où à partir d’une seconde ou deux via une connexion haut débit à jusqu'à une minute pour  utilisateurs sur les modems d’accès à distance. La bonne nouvelle est que les fichiers de script externe sont mis en cache par le navigateur, ce scénario Pire des cas se produit rarement.

> [!NOTE]
> Si vous vous sentez toujours placer le contrôle ScriptManager dans la page maître, envisagez du formulaire Web (le `<form runat="server">` balisage dans la page maître). Chaque page ASP.NET qui utilise le modèle de publication (postback) doit inclure précisément un Web Form. Ajout d’un formulaire Web ajoute du contenu supplémentaire : un nombre de champs de formulaire masqué, le `<form>` balise lui-même, et, si nécessaire, une fonction JavaScript de l’initialisation d’une publication (postback) à partir du script. Cette balise n’est pas nécessaire pour les pages qui ne la publication. Ce balisage superflu peut être supprimé en supprimant le formulaire Web de la page maître et en l’ajoutant manuellement à chaque page de contenu qui nécessite. Toutefois, les avantages d’avoir le formulaire Web dans la page maître compensent les inconvénients de l’utilisation d’ajouter inutilement à certaines pages de contenu.


## <a name="step-1-adding-a-scriptmanager-control-to-the-master-page"></a>Étape 1 : Ajout d’un contrôle ScriptManager à la Page maître

Chaque page web qui utilise l’infrastructure ASP.NET AJAX doit contenir précisément un contrôle ScriptManager. En raison de cette exigence, il est généralement judicieux placer un seul contrôle ScriptManager sur la page maître afin que toutes les pages de contenu ont le contrôle ScriptManager inclus automatiquement. En outre, ScriptManager doit figurer avant les contrôles serveur ASP.NET AJAX, telles que les contrôles UpdatePanel et UpdateProgress. Par conséquent, il est préférable de placer le ScriptManager avant tous les contrôles ContentPlaceHolder dans le formulaire Web.

Ouvrez le `Site.master` page maître et ajouter un contrôle ScriptManager à la page dans le formulaire Web, mais avant que le `<div id="topContent">` élément (voir Figure 1). Si vous utilisez Visual Web Developer 2008 ou Visual Studio 2008, le contrôle ScriptManager se trouve dans la boîte à outils dans l’onglet Extensions AJAX. Si vous utilisez Visual Studio 2005, vous devez d’abord installer l’infrastructure ASP.NET AJAX et ajouter des contrôles à la boîte à outils. Visitez la page de téléchargement d’ASP.NET AJAX pour obtenir de l’infrastructure ASP.NET 2.0.

Après avoir ajouté le ScriptManager à la page, modifier son `ID` de `ScriptManager1` à `MyManager`.


[![Ajouter un ScriptManager à la Page maître](master-pages-and-asp-net-ajax-vb/_static/image2.png)](master-pages-and-asp-net-ajax-vb/_static/image1.png)

**Figure 01**: ajouter un ScriptManager à la Page maître ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image3.png))


## <a name="step-2-using-the-aspnet-ajax-framework-from-a-content-page"></a>Étape 2 : À l’aide de l’infrastructure ASP.NET AJAX à partir d’une Page de contenu

Avec le contrôle ScriptManager ajouté à la page maître, nous pouvons maintenant ajouter des fonctionnalités ASP.NET AJAX framework à n’importe quelle page de contenu. Nous allons créer une nouvelle page ASP.NET qui affiche un produit sélectionné de façon aléatoire à partir de la base de données Northwind. Nous allons utiliser le contrôle Timer de l’infrastructure ASP.NET AJAX pour mettre à jour l’affichage toutes les 15 secondes, affichant un nouveau produit.

Commencez par créer une nouvelle page dans le répertoire racine nommé `ShowRandomProduct.aspx`. N’oubliez pas de lier cette nouvelle page afin du `Site.master` page maître.


[![Ajouter une nouvelle Page ASP.NET pour le site Web](master-pages-and-asp-net-ajax-vb/_static/image5.png)](master-pages-and-asp-net-ajax-vb/_static/image4.png)

**Figure 02**: ajouter une nouvelle Page ASP.NET pour le site Web ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image6.png))


N’oubliez pas que dans la spécification le titre, les balises Meta et les autres en-têtes HTML dans le didacticiel sur la Page maître [SKM1] nous avons créé une classe de page de base personnalisée nommée `BasePage` qui a généré le titre de la page si elle n’a pas été explicitement définie. Accédez à la `ShowRandomProduct.aspx` code-behind de la page de classe et de la dériver de `BasePage` (au lieu d’à partir de `System.Web.UI.Page`).

Enfin, mettre à jour le `Web.sitemap` fichier à inclure une entrée pour cette leçon. Ajoutez le balisage suivant sous la `<siteMapNode>` pour le masque leçon d’Interaction de la Page contenu :


[!code-xml[Main](master-pages-and-asp-net-ajax-vb/samples/sample2.xml)]

L’ajout de ce `<siteMapNode>` élément est reflété dans les leçons liste (voir Figure 5).

### <a name="displaying-a-randomly-selected-product"></a>Affichage d’un produit sélectionné de façon aléatoire

Retour à `ShowRandomProduct.aspx`. Dans le concepteur, faites glisser un contrôle UpdatePanel à partir de la boîte à outils dans le `MainContent` contrôle de contenu et définir son `ID` propriété `ProductPanel`. UpdatePanel représente une région de l’écran qui peut être mis à jour en mode asynchrone via une publication de page partielle.

Notre première tâche consiste à afficher des informations sur un produit sélectionné de façon aléatoire dans UpdatePanel. Commencez par faire glisser un contrôle DetailsView dans UpdatePanel. Définir le contrôle de DetailsView `ID` propriété `ProductInfo` et effacer son `Height` et `Width` propriétés. Développez la balise active du DetailsView et, dans la liste déroulante Choisir la Source de données, choisissez de lier le contrôle DetailsView à un nouveau contrôle SqlDataSource nommé `RandomProductDataSource`.


[![Lier le contrôle DetailsView à un nouveau contrôle SqlDataSource](master-pages-and-asp-net-ajax-vb/_static/image8.png)](master-pages-and-asp-net-ajax-vb/_static/image7.png)

**Figure 03**: lier le contrôle DetailsView à un nouveau contrôle SqlDataSource ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image9.png))


Configurer le contrôle SqlDataSource pour se connecter à la base de données Northwind via le `NorthwindConnectionString` (que nous avons créé dans l’interaction avec la Page maître à partir du didacticiel de la Page de contenu [SKM2]). Lorsque la configuration de l’instruction select choisir de spécifier une instruction SQL personnalisée, puis entrez la requête suivante :


[!code-sql[Main](master-pages-and-asp-net-ajax-vb/samples/sample3.sql)]

Le `TOP 1` mot clé dans le `SELECT` clause retourne uniquement le premier enregistrement retourné par la requête. Le `NEWID()` fonction génère une nouvelle valeur d’identificateur global unique (GUID) et peut être utilisée dans un `ORDER BY` clause pour retourner les enregistrements de la table dans un ordre aléatoire.


[![Configurer le SqlDataSource pour retourner un enregistrement unique, sélectionné aléatoirement](master-pages-and-asp-net-ajax-vb/_static/image11.png)](master-pages-and-asp-net-ajax-vb/_static/image10.png)

**Figure 04**: configurer le SqlDataSource pour retourner un seul enregistrement de sélectionné au hasard ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image12.png))


Après la fin de l’Assistant, Visual Studio crée un BoundField pour les deux colonnes retournées par la requête ci-dessus. À ce stade balisage déclaratif de votre page doit ressembler à ce qui suit :


[!code-aspx[Main](master-pages-and-asp-net-ajax-vb/samples/sample4.aspx)]

La figure 5 illustre le `ShowRandomProduct.aspx` page lorsqu’ils sont affichés via un navigateur. Cliquez sur le bouton d’actualisation de votre navigateur pour recharger la page. Vous devez voir le `ProductName` et `UnitPrice` valeurs pour un nouvel enregistrement sélectionné de façon aléatoire.


[![Nom et le prix d’un produit aléatoire s’affiche.](master-pages-and-asp-net-ajax-vb/_static/image14.png)](master-pages-and-asp-net-ajax-vb/_static/image13.png)

**Figure 05**: nom et le prix du produit A aléatoire est affiché ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image15.png))


### <a name="automatically-displaying-a-new-product-every-15-seconds"></a>Afficher automatiquement un nouveau produit toutes les 15 secondes

L’infrastructure ASP.NET AJAX inclut un contrôle Timer qui effectue une publication (postback) à une heure spécifiée ; de publication (postback) de la minuterie `Tick` événement est déclenché. Si le contrôle Timer est placé dans un UpdatePanel déclenche une publication de page partielle, au cours de laquelle nous pouvons relier les données au contrôle DetailsView pour afficher un nouveau produit sélectionné de façon aléatoire.

Pour ce faire, faites glisser un minuteur à partir de la boîte à outils et déposez-le dans UpdatePanel. Modification de la minuterie `ID` de `Timer1` à `ProductTimer` et son `Interval` propriété 60000 15000. Le `Interval` propriété indique le nombre de millisecondes écoulées entre publications (postback) ; la valeur 15000 provoque le minuteur déclencher une publication de page partielle toutes les 15 secondes. À ce stade balisage déclaratif de votre minuterie doit ressembler à ce qui suit :


[!code-aspx[Main](master-pages-and-asp-net-ajax-vb/samples/sample5.aspx)]

Créer un gestionnaire d’événements pour de la minuterie `Tick` événement. Dans ce gestionnaire d’événements, nous devons lier de nouveau les données au contrôle DetailsView en appelant de DetailsView `DataBind` (méthode). Cela fait en sorte que le contrôle DetailsView à récupérer de nouveau les données à partir de son contrôle de source de données, qui sélectionne et afficher de façon aléatoire un nouveau enregistrement sélectionné (à l’instar lorsque recharger la page en cliquant sur le bouton d’actualisation du navigateur).


[!code-vb[Main](master-pages-and-asp-net-ajax-vb/samples/sample6.vb)]

C’est tout cela ! Modifier la page via un navigateur. Au départ, les informations d’un produit aléatoire s’affiche. Si vous recherchez patiemment à l’écran, vous remarquerez que, au bout de 15 secondes, plus d’informations sur un nouveau produit remplace par magie l’affichage existant.

Pour mieux voir ce qui se passe ici, vous allez ajouter un contrôle Label pour UpdatePanel qui affiche l’heure de que dernière mise à jour de l’affichage. Ajouter un contrôle Web Label dans UpdatePanel, définissez son `ID` à `LastUpdateTime`, désactivez sa `Text` propriété. Ensuite, créez un gestionnaire d’événements pour de UpdatePanel `Load` événement et afficher l’heure actuelle dans l’étiquette. (De UpdatePanel `Load` événement est déclenché à chaque publication de page partiel ou complet.)


[!code-vb[Main](master-pages-and-asp-net-ajax-vb/samples/sample7.vb)]

Cette modification est terminée, la page inclut l’heure qu'affiché produit a été chargé. La figure 6 illustre la page visitée en premier. Figure 7 montre à la page 15 secondes plus tard, une fois que le contrôle Timer a « cochée » et UpdatePanel a été actualisée pour afficher des informations sur un nouveau produit.


[![Un produit sélectionné au hasard s’affiche sur le chargement de la Page](master-pages-and-asp-net-ajax-vb/_static/image17.png)](master-pages-and-asp-net-ajax-vb/_static/image16.png)

**Figure 06**: aléatoirement sélectionné produit A est affiché sur le chargement de la Page ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image18.png))


[![Toutes les 15 secondes un nouveau aléatoirement sélectionné produit s’affiche.](master-pages-and-asp-net-ajax-vb/_static/image20.png)](master-pages-and-asp-net-ajax-vb/_static/image19.png)

**Figure 07**: toutes les 15 secondes, un nouveau aléatoirement sélectionné produit s’affiche ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image21.png))


## <a name="step-3-using-the-scriptmanagerproxy-control"></a>Étape 3 : Utiliser le contrôle ScriptManagerProxy

Avec, notamment le script nécessaire pour l’infrastructure ASP.NET AJAX bibliothèque cliente, ScriptManager peut également inscrire des fichiers JavaScript personnalisés, des références à des Services Web en charge les scripts et d’authentification personnalisée, l’autorisation et des services de profil. En règle générale, ces personnalisations sont spécifiques à une page donnée. Toutefois, si les fichiers de script personnalisés, références de Service Web, ou l’authentification, l’autorisation ou des services de profil sont référencés dans ScriptManager dans la page maître, ils sont inclus dans toutes les pages du site Web.

Pour ajouter des personnalisations liées au ScriptManager sur une base de page par page utilisent le contrôle ScriptManagerProxy. Vous pouvez ajouter un ScriptManagerProxy à une page de contenu et puis enregistrez le fichier JavaScript personnalisé, référence de Service Web, ou l’authentification, l’autorisation ou service de profil à partir de la ScriptManagerProxy ; Cela a pour effet d’inscription de ces services pour la page de contenu particulier.

> [!NOTE]
> Une page ASP.NET peut uniquement posséder pas plus d’un contrôle ScriptManager présent. Par conséquent, vous ne peut pas ajouter un contrôle ScriptManager à une page de contenu si le contrôle ScriptManager est déjà défini dans la page maître. Le seul but du ScriptManagerProxy est d’offrir un moyen aux développeurs de définir le ScriptManager dans la page maître, tout en ayant la possibilité d’ajouter des personnalisations de ScriptManager sur une base de page par page.


Pour afficher le contrôle ScriptManagerProxy en action, nous allons augmenter UpdatePanel dans `ShowRandomProduct.aspx` d’inclure un bouton qui utilise un script côté client pour suspendre ou reprendre le contrôle Timer. Le contrôle Timer a trois méthodes côté client que nous pouvons utiliser pour obtenir cette fonctionnalité souhaitée :

- `_startTimer()`-démarre le contrôle Timer
- `_raiseTick()`-provoque le contrôle Timer « cycles », ce qui publication et déclencher son événement Tick sur le serveur
- `_stopTimer()`-arrête le contrôle Timer

Nous allons créer un fichier JavaScript avec une variable nommée `timerEnabled` et une fonction nommée `ToggleTimer`. Le `timerEnabled` variable indique si le contrôle Timer est actuellement activé ou désactivé ; la valeur par défaut est true. Le `ToggleTimer` fonction accepte deux paramètres d’entrée : une référence pour la côté client et le bouton de Pause/reprise `id` valeur du contrôle. Cette fonction active ou désactive la valeur de `timerEnabled`, obtient une référence au contrôle Timer, démarre ou arrête le minuteur (selon la valeur de `timerEnabled`) et met à jour le texte d’affichage du bouton « Pause » ou « Resume ». Cette fonction est appelée chaque fois qu’un clic sur le bouton Pause/reprise.

Commencez par créer un nouveau dossier dans le site Web nommé `Scripts`. Ensuite, ajoutez un nouveau fichier dans le dossier Scripts nommé `TimerScript.js` de type fichier JScript.


[![Ajouter un nouveau fichier JavaScript dans le dossier de Scripts](master-pages-and-asp-net-ajax-vb/_static/image23.png)](master-pages-and-asp-net-ajax-vb/_static/image22.png)

**Figure 08**: ajouter un nouveau fichier JavaScript à la `Scripts` dossier ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image24.png))


[![Un nouveau fichier JavaScript a été ajouté au site Web](master-pages-and-asp-net-ajax-vb/_static/image26.png)](master-pages-and-asp-net-ajax-vb/_static/image25.png)

**Figure 09**: un fichier JavaScript a été ajouté au site Web ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image27.png))


Ensuite, ajoutez le script suivant à la `TimerScript.js` fichier :


[!code-csharp[Main](master-pages-and-asp-net-ajax-vb/samples/sample8.cs)]

Nous devons maintenant inscrire ce fichier JavaScript personnalisé dans `ShowRandomProduct.aspx`. Retour à `ShowRandomProduct.aspx` et ajouter un contrôle ScriptManagerProxy à la page ; définir son `ID` à `MyManagerProxy`. Pour inscrire un code JavaScript personnalisé fichier sélectionner le contrôle ScriptManagerProxy dans le concepteur et passez à la fenêtre Propriétés. Une des propriétés est intitulée Scripts. Cette propriété affiche l’éditeur de collections ScriptReference indiqué dans la Figure 10. Cliquez sur le bouton Ajouter pour inclure une nouvelle référence de script et puis entrez le chemin d’accès au fichier de script dans la propriété de chemin d’accès : `~/Scripts/TimerScript.js`.


[![Ajouter une référence de Script pour le contrôle ScriptManagerProxy](master-pages-and-asp-net-ajax-vb/_static/image29.png)](master-pages-and-asp-net-ajax-vb/_static/image28.png)

**La figure 10**: ajouter une référence de Script pour le contrôle ScriptManagerProxy ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image30.png))


Après l’ajout de la référence de script le contrôle ScriptManagerProxy's déclarative balisage est mis à jour pour inclure un `<Scripts>` collection avec une seule `ScriptReference` entrée, l’extrait de balisage suivant illustre :


[!code-aspx[Main](master-pages-and-asp-net-ajax-vb/samples/sample9.aspx)]

Le `ScriptReference` entrée indique le ScriptManagerProxy pour inclure une référence au fichier JavaScript dans le balisage de son rendu. Autrement dit, en inscrivant personnalisé de script dans le ScriptManagerProxy le `ShowRandomProduct.aspx` sortie de rendu de la page inclut désormais une autre `<script src="url"></script>` balise : `<script src="Scripts/TimerScript.js" type="text/javascript"></script>`.

Nous pouvons maintenant appeler le `ToggleTimer` fonction définie dans `TimerScript.js` à partir du script client dans le `ShowRandomProduct.aspx` page. Ajoutez le code HTML suivant dans UpdatePanel :


[!code-aspx[Main](master-pages-and-asp-net-ajax-vb/samples/sample10.aspx)]

Cela permet d’afficher un bouton avec le texte « Suspendre ». Chaque fois que vous cliquez dessus, la fonction JavaScript `ToggleTimer` est appelée, en passant une référence au bouton et le `id` valeur du contrôle Timer (`ProductTimer`). Notez la syntaxe pour obtenir le `id` la valeur du contrôle. `<%=ProductTimer.ClientID%>`émet la valeur de la `ProductTimer` du contrôle Timer `ClientID` propriété. Dans le contrôle ID d’affectation de noms dans le didacticiel de Pages de contenu [SKM3] évoqué les différences entre le côté serveur `ID` valeur et la côté client qui en résulte `id` valeur et comment `ClientID` retourne la côté client `id`.

La figure 11 illustre cette page lors de la première visite via un navigateur. La minuterie est en cours d’exécution et met à jour les informations de produit affiché toutes les 15 secondes. Figure 12 montre l’écran après que le bouton Pause a été utilisé. En cliquant sur le bouton Pause arrête le minuteur et met à jour le texte du bouton pour « Resume ». Les informations de produit seront Actualiser (et continuer à actualiser toutes les 15 secondes) une fois que l’utilisateur clique sur Reprendre.


[![Cliquez sur le bouton Pause pour interrompre le contrôle Timer](master-pages-and-asp-net-ajax-vb/_static/image32.png)](master-pages-and-asp-net-ajax-vb/_static/image31.png)

**Figure 11**: cliquez sur le bouton Pause pour interrompre le contrôle Timer ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image33.png))


[![Cliquez sur le bouton Resume pour redémarrer la minuterie](master-pages-and-asp-net-ajax-vb/_static/image35.png)](master-pages-and-asp-net-ajax-vb/_static/image34.png)

**Figure 12**: cliquez sur le bouton Resume pour redémarrer la minuterie ([cliquez pour afficher l’image en taille réelle](master-pages-and-asp-net-ajax-vb/_static/image36.png))


## <a name="summary"></a>Résumé

Lors de la création d’applications web compatibles AJAX à l’aide de l’infrastructure ASP.NET AJAX, il est impératif que chaque page web compatibles AJAX inclut un contrôle ScriptManager. Pour faciliter ce processus, nous pouvons ajouter un ScriptManager à la page maître, plutôt que d’avoir à mémoriser ajouter un ScriptManager à chaque page de contenu. Étape 1 a montré comment ajouter un ScriptManager à la page maître lors de l’étape 2 est examiné pour implémenter les fonctionnalités AJAX dans une page de contenu.

Si vous devez ajouter des scripts personnalisés, des références à des Services Web en charge les scripts ou d’authentification personnalisés, l’autorisation ou des services de profil à une page de contenu particulier, ajoutez un contrôle ScriptManagerProxy à la page de contenu puis configurez le il les personnalisations. Étape 3 examiné comment le ScriptManagerProxy permet d’enregistrer un fichier JavaScript personnalisé dans une page de contenu spécifique.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Infrastructure ASP.NET AJAX](../../../../ajax/index.md)
- [ASP.NET AJAX didacticiels](../aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax.md)
- [ASP.NET AJAX vidéos](../../../videos/aspnet-ajax/index.md)
- [Interface utilisateur Interactive de construction avec Microsoft ASP.NET AJAX](http://aspnet.4guysfromrolla.com/articles/101007-1.aspx)
- [Utilisation de NEWID pour trier les enregistrements de façon aléatoire](http://www.sqlteam.com/article/using-newid-to-randomly-sort-records)
- [Utilisation du contrôle Timer](http://aspnet.4guysfromrolla.com/articles/061808-1.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de plusieurs manuels ASP/ASP.NET et de créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 3.5 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672329972/4guysfromrollaco). Scott peut être atteint à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](interacting-with-the-content-page-from-the-master-page-vb.md)
[Suivant](specifying-the-master-page-programmatically-vb.md)
