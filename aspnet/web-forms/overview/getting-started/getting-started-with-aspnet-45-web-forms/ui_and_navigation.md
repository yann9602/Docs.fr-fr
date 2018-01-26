---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/ui_and_navigation
title: "L’interface utilisateur et la Navigation | Documents Microsoft"
author: Erikre
description: "Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/08/2014
ms.topic: article
ms.assetid: 5c76891d-e515-4885-b576-76bd2c494efe
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/ui_and_navigation
msc.type: authoredcontent
ms.openlocfilehash: 7f1d8a1a473820a7c8da4c8086904cc41c86fd2a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="ui-and-navigation"></a>L’interface utilisateur et la Navigation
====================
Par [Erik Reitan](https://github.com/Erikre)

[Télécharger Wingtip Toys exemple de projet (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [télécharger des livres (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

> Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour le Web. Un Visual Studio 2013 [projet avec le code source c#](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) est disponible pour accompagner cette série de didacticiels.


Dans ce didacticiel, vous allez modifier l’interface utilisateur de l’application Web par défaut pour prendre en charge les fonctionnalités de l’application avant de magasin Wingtip Toys. En outre, vous allez ajouter simple et navigation lié aux données. Ce didacticiel s’appuie sur le didacticiel précédent « Créer la couche d’accès aux données » et fait partie de la série de didacticiels Wingtip Toys.

## <a name="what-youll-learn"></a>Ce que vous allez apprendre :

- Comment modifier l’interface utilisateur pour prendre en charge les fonctionnalités de l’application avant de magasin Wingtip Toys.
- Comment configurer un élément HTML5 pour inclure la navigation entre les pages.
- Comment créer un contrôle piloté par les données pour accéder aux données de produit spécifique.
- Comment afficher les données d’une base de données créé à l’aide d’Entity Framework Code First.

ASP.NET Web Forms permettent de créer du contenu dynamique pour votre application Web. Chaque page Web ASP.NET est créé de manière similaire à une page HTML Web statique (une page qui n’inclut pas de traitement serveur), mais la page Web ASP.NET inclut les éléments supplémentaires qu’ASP.NET reconnaît et traite pour générer le code HTML lors de l’exécution de la page.

Avec une page HTML statique (*.htm* ou *.html* fichier), le serveur répond à un `Web` demande par la lecture du fichier et de les envoyer en tant que-au navigateur. En revanche, quand un utilisateur demande une page Web ASP.NET (*.aspx* fichier), la page s’exécute en tant que programme sur le serveur Web. Alors que la page est en cours d’exécution, il peut effectuer toute tâche qui nécessite de votre site Web, y compris le calcul de valeurs, la lecture ou l’écriture des informations de base de données ou appel d’autres programmes. En tant que sa sortie, la page génère le balisage (par exemple, des éléments au format HTML) et envoie cette sortie dynamique au navigateur dynamiquement.

## <a name="modifying-the-ui"></a>Modification de l’interface utilisateur

Vous allez continuer cette série de didacticiels en modifiant le *Default.aspx* page. Vous allez modifier l’interface utilisateur qui est déjà établie par le modèle par défaut utilisé pour créer l’application. Le type de modifications que vous allez effectuer sont par défaut lors de la création d’une application Web Forms. Vous devez effectuer cette opération en modifiant le titre, le remplacement du contenu et suppression du contenu de la valeur par défaut inutiles.

1. Ouvrez ou basculez vers le *Default.aspx* page.
2. Si la page s’affiche dans **conception** afficher, basculez vers **Source** vue.
3. En haut de la page dans le `@Page` directive, modifier le `Title` l’attribut « Accueil », comme indiqué en surbrillance en jaune ci-dessous. 

    [!code-aspx[Main](ui_and_navigation/samples/sample1.aspx?highlight=1)]
4. Également dans le *Default.aspx* page, remplacez tout le contenu par défaut contenu dans le `<asp:Content>` marquer afin que le balisage apparaît sous la forme ci-dessous. 

    [!code-aspx[Main](ui_and_navigation/samples/sample2.aspx)]
5. Enregistrer le *Default.aspx* page en sélectionnant **enregistrer de Default.aspx** à partir de la **fichier** menu.

 Résultant *Default.aspx* page s’affiche comme suit : 

[!code-aspx[Main](ui_and_navigation/samples/sample3.aspx)]

Dans l’exemple, vous avez défini le `Title` attribut de la `@Page` la directive. Lorsque le code HTML s’affiche dans un navigateur, le code serveur `<%: Page.Title %>` résout le contenu de la `Title` attribut.

L’exemple de page inclut les éléments de base qui constituent une page Web ASP.NET. La page contient du texte statique comme vous avez peut-être dans une page HTML, ainsi que les éléments qui sont spécifiques à ASP.NET. Le contenu de la *Default.aspx* page sera intégrée avec le contenu de la page maître, qui est expliqué plus loin dans ce didacticiel.

### <a name="page-directive"></a>@PageDirective

ASP.NET Web Forms contiennent généralement des directives qui vous permettent de spécifier les informations de page de propriétés et la configuration de la page. Les directives sont utilisées par ASP.NET en tant qu’instructions pour savoir comment le processus de la page, mais ils ne sont pas rendus dans le cadre de la balise qui est envoyée au navigateur.

La directive couramment utilisée est le `@Page` directive, qui vous permet de spécifier de nombreuses options de configuration de la page, notamment les suivantes :

1. Le serveur de langage de programmation pour le code de la page, tels que c#.
2. Indique si la page est une page avec un code serveur directement dans la page, ce qui est appelé une page à fichier unique, ou s’il s’agit d’une page de code dans un fichier de classe distincte, qui est appelé une page code-behind.
3. Indique si la page est une page maître associée et doit donc être traitée comme une page de contenu.
4. Le débogage et les options de suivi.

Si vous n’incluez pas une `@Page` directive dans la page, ou si la directive n’inclut pas un paramètre spécifique, un paramètre est hérité de la *Web.config* fichier de configuration ou à partir de la *Machine.config* fichier de configuration. Le *Machine.config* fichier fournit des paramètres de configuration supplémentaires à toutes les applications en cours d’exécution sur un ordinateur.

> [!NOTE] 
> 
> Le *Machine.config* fournit également des détails sur tous les paramètres de configuration possible.


### <a name="web-server-controls"></a>Contrôles serveur Web

Dans la plupart des applications ASP.NET Web Forms, vous allez ajouter des contrôles permettant aux utilisateurs d’interagir avec la page, tels que des boutons, zones de texte, listes et ainsi de suite. Ces contrôles serveur Web sont semblables aux boutons HTML et des éléments d’entrée. Toutefois, elles sont traitées sur le serveur, ce qui vous permet d’utiliser le code serveur pour définir leurs propriétés. Ces contrôles déclenchent également des événements que vous pouvez gérer dans le code serveur.

Contrôles serveur utilisent une syntaxe spéciale qu’ASP.NET reconnaît lorsque la page s’exécute. Le nom de balise des contrôles serveur ASP.NET commence par un `asp:` préfixe. Cela permet à ASP.NET de reconnaître et de traiter ces contrôles serveur. Le préfixe peut être différent si le contrôle ne fait pas partie du .NET Framework. Outre la `asp:` préfixe, les contrôles serveur ASP.NET incluent également le `runat="server"` attribut et un `ID` que vous pouvez utiliser pour référencer le contrôle dans le code serveur.

Lorsque la page s’exécute, ASP.NET identifie les contrôles serveur et exécute le code qui est associé à ces contrôles. De nombreux contrôles rendent du HTML ou un autre balisage dans la page lorsqu’il est affiché dans un navigateur.

### <a name="server-code"></a>Code serveur

La plupart des applications Web Forms ASP.NET inclure du code qui s’exécute sur le serveur lors du traitement de la page. Comme indiqué ci-dessus, code serveur peut être utilisé pour effectuer diverses opérations, telles que l’ajout de données à un contrôle ListView. ASP.NET prend en charge de nombreux langages pour s’exécuter sur le serveur, notamment c#, Visual Basic, J# et autres.

ASP.NET prend en charge deux modèles pour l’écriture de code serveur pour une page Web. Dans le modèle de fichier unique, le code de la page est dans un élément de script dans lequel la balise d’ouverture comprend le `runat="server"` attribut. Vous pouvez également créer le code de la page dans un fichier de classe distincte, qui est appelé par le modèle code-behind. Dans ce cas, la page Web Forms ASP.NET ne contient généralement aucun code serveur. Au lieu de cela, le `@Page` directive inclut des informations qui lie la *.aspx* page avec son fichier code-behind associé.

Le `CodeBehind` attribut contenu dans le `@Page` directive spécifie le nom du fichier de classe distinct et le `Inherits` attribut spécifie le nom de la classe dans le fichier code-behind qui correspond à la page.

### <a name="updating-the-master-page"></a>Mise à jour de la Page maître

Dans ASP.NET Web Forms, pages maîtres permettent de créer une disposition cohérente pour les pages dans votre application. Une seule page maître définit l’apparence et le comportement standard que vous souhaitez pour toutes les pages (ou un groupe de pages) de votre application. Vous pouvez ensuite créer des pages de contenu individuelles qui contiennent le contenu que vous souhaitez afficher, comme expliqué ci-dessus. Lorsque des utilisateurs demandent les pages de contenu, ASP.NET les fusionne avec la page maître pour produire une sortie qui associe la disposition de la page maître avec le contenu à partir de la page de contenu.

Le nouveau site a besoin d’un logo unique à afficher sur chaque page. Pour ajouter ce logo, vous pouvez modifier le code HTML de la page maître.

1. Dans **l’Explorateur de solutions**, recherchez et ouvrez le **Site.Master** page.
2. Si la page est en **conception** afficher, basculez vers **Source** vue.
3. Mettre à jour de la page maître par **modifiant ou en ajoutant** le balisage mis en surbrillance en jaune : 

    [!code-aspx[Main](ui_and_navigation/samples/sample4.aspx?highlight=9,49,76-81,87)]

Ce code HTML affiche l’image nommée *fichier logo.jpg* à partir de la *Images* dossier de l’application Web, vous ajouterez ultérieurement. Lorsqu’une page qui utilise la page maître est affichée dans un navigateur, le logo sera affiché. Si un utilisateur clique sur le logo, l’utilisateur navigue vers la *Default.aspx* page. La balise d’ancrage HTML `<a>` encapsule le contrôle serveur image et permet à l’image à inclure dans le cadre de la liaison. Le `href` pour la balise d’ancrage, spécifie la racine d’attribut «`~/`» du site Web en tant que l’emplacement du lien. Par défaut, le *Default.aspx* page s’affiche lorsque l’utilisateur accède à la racine du site Web. Le **Image** `<asp:Image>` contrôle serveur inclut des propriétés de l’addition, tels que `BorderStyle`, qui génèrent un rendu au format HTML lors de l’afficher dans un navigateur.

### <a name="master-pages"></a>Pages maîtres

Une page maître est un fichier ASP.NET avec l’extension .master (par exemple, *Site.Master*) avec une disposition prédéfinie qui peut inclure un texte statique, des éléments HTML et des contrôles serveur. La page maître est identifiée par un spécial `@Master` directive qui remplace le `@Page` directive est utilisée pour ordinaire *.aspx* pages.

Outre la `@Master` directive, la page maître contient également tous les éléments HTML de niveau supérieur d’une page, comme `html`, `head`, et `form`. Par exemple, sur la page maître ajouté précédemment, vous utilisez HTML `table` pour la mise en page, un `img` , élément pour le logo de société, texte statique et des contrôles de serveur pour gérer l’appartenance commune pour votre site. Vous pouvez utiliser le code HTML et des éléments d’ASP.NET dans le cadre de votre page maître.

En plus de texte statique et les contrôles qui apparaissent sur toutes les pages, la page maître inclut également un ou plusieurs **ContentPlaceHolder** contrôles. Ces contrôles réservés définissent des régions où le contenu remplaçable apparaîtra. À son tour, le contenu remplaçable est défini dans les pages de contenu, tel que *Default.aspx*, à l’aide du **contenu** contrôle serveur.

#### <a name="adding-image-files"></a>Ajout de fichiers Image

L’image du logo est mentionnée ci-dessus, ainsi que toutes les images de produit doit être ajouté à l’application Web afin qu’elles peuvent être affichées lorsque le projet s’affiche dans un navigateur.

#### <a name="download-from-msdn-samples-site"></a>Télécharger à partir du site d’exemples MSDN :

[Prise en main 4.5 Web Forms ASP.NET et Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (c#)

Le téléchargement inclut des ressources dans les *WingtipToys-composants* dossier qui sont utilisées pour créer l’exemple d’application.

1. Si vous n’avez pas déjà fait, téléchargez les fichiers de compressé exemple utilisant le lien ci-dessus à partir du site MSDN Samples.
2. Une fois téléchargé, ouvrez le fichier .zip et copiez le contenu vers un dossier local sur votre ordinateur.
3. Recherchez et ouvrez le *WingtipToys-composants* dossier.
4. Par glisser -déplacer, copier le *catalogue* dossier à partir de votre dossier local à la racine du projet d’application Web dans le **l’Explorateur de solutions** de Visual Studio. 

    ![Navigation - copier les fichiers et l’interface utilisateur](ui_and_navigation/_static/image1.png)
5. Ensuite, créez un dossier nommé *Images* en cliquant sur le **WingtipToys** projet **l’Explorateur de solutions** et en sélectionnant **ajouter**  - &gt; **Nouveau dossier**.
6. Copie le *fichier logo.jpg* de fichiers à partir de la *WingtipToys-composants* dossier dans **l’Explorateur de fichiers** à la *Images* dossier de l’application Web projet **l’Explorateur de solutions** de Visual Studio.
7. Cliquez sur le **afficher tous les fichiers** option en haut de **l’Explorateur de solutions** pour mettre à jour la liste des fichiers si vous ne voyez pas les nouveaux fichiers.  
  
    **L’Explorateur de solutions** affiche maintenant les fichiers de projet mis à jour. 

    ![Navigation - Explorateur de solutions et l’interface utilisateur](ui_and_navigation/_static/image2.png)

### <a name="adding-pages"></a>Ajout de Pages

Avant d’ajouter la navigation à l’application Web, vous allez d’abord ajouter deux nouvelles pages que vous allez accéder à. Plus loin dans cette série de didacticiels, vous allez afficher les produits et les détails du produit sur ces nouvelles pages.

1. Dans **l’Explorateur de solutions**, avec le bouton droit **WingtipToys**, cliquez sur **ajouter**, puis cliquez sur **un nouvel élément**.   
 La boîte de dialogue **Ajouter un nouvel élément** s’affiche.
2. Sélectionnez le **Visual C#**  - &gt; **Web** groupe de modèles sur la gauche. Ensuite, sélectionnez **Web Form avec Page maître** à partir du milieu de liste et nommez-le *ProductList.aspx*. 

    ![Interface utilisateur et Navigation - ajouter la boîte de dialogue Nouvel élément](ui_and_navigation/_static/image3.png)
3. Sélectionnez **Site.Master** pour attacher la page maître à la nouvelle *.aspx* page. 

    ![Interface utilisateur et Navigation - sélectionner la Page maître](ui_and_navigation/_static/image4.png)
4. Ajouter une page supplémentaire nommée *ProductDetails.aspx* en suivant ces mêmes étapes.

### <a name="updating-bootstrap"></a>Mise à jour des données d’amorçage

Utilisent des modèles de projet Visual Studio 2013 [Bootstrap](http://getbootstrap.com/), une infrastructure de mise en page et des thèmes créée par Twitter. Programme d’amorçage utilise CSS3 pour fournir une conception réactive, ce qui signifie que les dispositions peuvent s’adapter dynamiquement aux tailles de fenêtre de navigateur différents. Vous pouvez également utiliser la fonctionnalité de thèmes du programme d’amorçage pour facilement effectuer un changement dans l’apparence de l’application. Par défaut, le modèle d’Application Web ASP.NET dans Visual Studio 2013 inclut Bootstrap comme package NuGet.

Dans ce didacticiel, vous allez modifier l’apparence de l’application Wingtip Toys en remplaçant les fichiers de programme d’amorçage CSS.

1. Dans **l’Explorateur de solutions**, ouvrez le *contenu* dossier.
2. Avec le bouton droit le *bootstrap.css* de fichiers et de le renommer *original.css-bootstrap*.
3. Renommer le *bootstrap.min.css* à *original.min.css-bootstrap*.
4. Dans **l’Explorateur de solutions**, cliquez sur le *contenu* et sélectionnez **ouvrir le dossier dans l’Explorateur de fichiers**.  
 L’Explorateur de fichiers s’affichera. Vous allez enregistrer un fichier CSS de démarrage téléchargé à cet emplacement.
5. Dans votre navigateur, accédez à [http://Bootswatch.com](http://bootswatch.com/).
6. Faites défiler la fenêtre du navigateur jusqu'à ce que vous voyiez le thème Cerulean. 

    ![Navigation - Cerulean de thème et l’interface utilisateur](ui_and_navigation/_static/image5.png)
7. Télécharger à la fois le *bootstrap.css* fichier et le *bootstrap.min.css* de fichiers à la *contenu* dossier. Utilisez le chemin d’accès au dossier de contenu qui est affiché dans le **l’Explorateur de fichiers** fenêtre que vous avez précédemment ouvert.
8. Dans **Visual Studio** en haut de **l’Explorateur de solutions**, sélectionnez le **afficher tous les fichiers** option pour afficher les nouveaux fichiers dans le dossier de contenu. 

    ![Navigation - Explorateur de solutions et l’interface utilisateur](ui_and_navigation/_static/image6.png)

 Vous verrez deux nouveaux fichiers CSS dans le **contenu** dossier, mais notez que l’icône en regard de chaque nom de fichier est grisée. Cela signifie que le fichier n’a pas encore été ajouté au projet.
9. Avec le bouton droit le *bootstrap.css* et *bootstrap.min.css* fichiers et sélectionnez **inclure dans le projet**.   
 Lorsque vous exécutez l’application Wingtip Toys plus loin dans ce didacticiel, la nouvelle interface utilisateur s’affichera.

> [!NOTE] 
> 
> Le modèle d’Application Web ASP.NET utilise le *Bundle.config* fichier à la racine du projet pour stocker le chemin d’accès des fichiers CSS de programme d’amorçage.


### <a name="modifying-the-default-navigation"></a>Modification de la Navigation par défaut

Le volet de navigation par défaut pour chaque page de l’application peut être modifié en modifiant l’élément de liste non triée de navigation qui se trouve dans le *Site.Master* page.

1. Dans **l’Explorateur de solutions**, recherchez et ouvrez le *Site.Master* page.
2. Ajouter le lien de navigation supplémentaires mis en surbrillance en jaune dans la liste non triée indiquée ci-dessous :   

    [!code-html[Main](ui_and_navigation/samples/sample5.html?highlight=5)]

Comme vous pouvez le voir dans le code HTML ci-dessus, vous avez modifié l’élément de chaque ligne `<li>` contenant une balise d’ancrage `<a>` avec un lien `href` attribut. Chaque `href` pointe vers une page dans l’application Web. Dans le navigateur, lorsqu’un utilisateur clique sur l’un de ces liens (tel que **produits**), leur permet d’accéder à la page contenue dans le `href` (tel que **ProductList.aspx**). Vous allez exécuter l’application à la fin de ce didacticiel.

> [!NOTE] 
> 
> Le tilde (`~`) caractère est utilisé pour spécifier que le `href` chemin d’accès commence à la racine du projet.


### <a name="adding-a-data-control-to-display-navigation-data"></a>Ajout d’un contrôle de données pour afficher les données de Navigation

Ensuite, vous allez ajouter un contrôle pour afficher toutes les catégories à partir de la base de données. Chaque catégorie agit comme un lien vers le *ProductList.aspx* page. Lorsqu’un utilisateur clique sur un lien de la catégorie dans le navigateur, ils seront accédez à la page produits et voir uniquement les produits associés à la catégorie sélectionnée.

Vous allez utiliser un **ListView** contrôle pour afficher toutes les catégories contenues dans la base de données. Pour ajouter un **ListView** contrôle à la page maître :

1. Dans le *Site.Master* page, ajoutez le code suivant mis en surbrillance `<div>` élément **après** le `<div>` élément contenant le `id="TitleContent"` que vous avez ajouté précédemment :  

    [!code-aspx[Main](ui_and_navigation/samples/sample6.aspx?highlight=7-21)]

Ce code affiche toutes les catégories à partir de la base de données. Le **ListView** contrôle affiche chaque nom de catégorie sous forme de texte de lien et inclut un lien vers le *ProductList.aspx* page avec une valeur de chaîne de requête contenant le `ID` de la catégorie. En définissant le `ItemType` propriété dans le **ListView** contrôler, l’expression de liaison de données `Item` est disponible dans le `ItemTemplate` nœud et le contrôle devient fortement typés. Vous pouvez sélectionner les détails de la `Item` à l’aide d’IntelliSense, telles que la spécification de l’objet le `CategoryName`. Ce code est contenu dans le conteneur `<%#: %>` qui marque une expression de liaison de données. En ajoutant le ( :) à la fin de la `<%#` préfixe, le résultat de l’expression de liaison de données est codée en HTML. Lorsque le résultat est codée en HTML, votre application est mieux protégée contre entre sites (XSS) de l’injection de code et les attaques par injection HTML de script.

> [!NOTE] 
> 
> **Tip**
> 
> Lorsque vous ajoutez du code en tapant pendant le développement, vous pouvez être certain qu’un membre valide d’un objet est trouvé, car il est fortement typée les contrôles de données affichent les membres disponibles en fonction de IntelliSense. IntelliSense offre des choix de code selon le contexte comme type de code, tels que les objets, méthodes et propriétés.


Dans l’étape suivante, vous allez implémenter le `GetCategories` méthode pour récupérer des données.

### <a name="linking-the-data-control-to-the-database"></a>Lier le contrôle de données à la base de données

Avant que vous pouvez afficher des données dans le contrôle de données, vous devez lier le contrôle de données à la base de données. Pour établir le lien, vous pouvez modifier le code-behind de la *Site.Master.cs* fichier.

1. Dans **l’Explorateur de solutions**, cliquez sur le *Site.Master* page, puis cliquez sur **afficher le Code**. Le *Site.Master.cs* fichier est ouvert dans l’éditeur.
2. Près du début de la *Site.Master.cs* , ajoutez les deux espaces de noms supplémentaires pour que tous les espaces de noms inclus apparaissent comme suit :  

    [!code-csharp[Main](ui_and_navigation/samples/sample7.cs?highlight=8-9)]
3. Ajouter la mise en surbrillance `GetCategories` méthode après le `Page_Load` Gestionnaire d’événements comme suit :  

    [!code-csharp[Main](ui_and_navigation/samples/sample8.cs?highlight=6-11)]

Le code ci-dessus est exécuté lorsque n’importe quelle page qui utilise la page maître est chargé dans le navigateur. Le `ListView` contrôle (nommé « categoryList ») que vous avez ajouté précédemment dans ce didacticiel utilise la liaison de modèle pour sélectionner les données. Dans le balisage de la `ListView` régler du contrôle `SelectMethod` propriété le `GetCategories` méthode, illustrée ci-dessus. Le `ListView` appels de contrôle le `GetCategories` méthode au moment approprié dans la vie de la page cycle et lie automatiquement les données retournées. Vous en apprendrez davantage sur la liaison de données dans le didacticiel suivant.

### <a name="running-the-application-and-creating-the-database"></a>Exécution de l’Application et la création de la base de données

Plus haut dans cette série de didacticiels vous avez créé une classe de l’initialiseur (nommée « ProductDatabaseInitializer ») et spécifié cette classe dans le *global.asax.cs* fichier. Entity Framework génère la base de données lorsque l’application est exécutée la première fois, car le `Application_Start` méthode contenu dans le *global.asax.cs* fichier appelle la classe de l’initialiseur. La classe initialiseur utilise les classes du modèle (`Category` et `Product`) que vous avez ajouté précédemment dans cette série de didacticiels pour créer la base de données.

1. Dans **l’Explorateur de solutions**, avec le bouton droit le *Default.aspx* page et sélectionnez **définir comme Page de démarrage**.
2. Dans le Presse de Visual Studio **F5**.   
 Il prendra un peu de temps pour configurer tous les éléments au cours de cette première exécution.   
    ![Navigation - fenêtres du navigateur et l’interface utilisateur](ui_and_navigation/_static/image7.png)  
 Lorsque vous exécutez l’application, l’application est compilée et la base de données nommée *wingtiptoys.mdf* sera créé dans le *application\_données* dossier. Dans le navigateur, vous verrez un menu de navigation de catégorie. Ce menu a été généré en récupérant les catégories à partir de la base de données. Dans l’étape suivante du didacticiel, vous allez implémenter le volet de navigation.
3. Fermez le navigateur pour arrêter l’application en cours d’exécution.

### <a name="reviewing-the-database"></a>Vérification de la base de données

Ouvrez le *Web.config* de fichier et examinez la section de chaîne de connexion. Vous pouvez voir que la `AttachDbFilename` valeur dans la chaîne de connexion pointe vers le `DataDirectory` pour le projet d’application Web. La valeur `|DataDirectory|` est une valeur réservée qui représente le *application\_données* dossier dans le projet. Ce dossier est où se trouve la base de données qui a été créé à partir de vos classes d’entité.

[!code-xml[Main](ui_and_navigation/samples/sample9.xml)]

> [!NOTE] 
> 
> Si le *application\_données* dossier n’est pas visible, ou si le dossier est vide, sélectionnez le **Actualiser** icône, puis le **afficher tous les fichiers** icône en haut de la **L’Explorateur de solutions** fenêtre. Développement de la largeur de la **l’Explorateur de solutions** windows peuvent être nécessaire pour afficher toutes les icônes disponibles.


Maintenant vous pouvez inspecter les données contenues dans le *wingtiptoys.mdf* fichier de base de données à l’aide de la **l’Explorateur de serveurs** fenêtre.

1. Développez le *application\_données* dossier. Si le *application\_données* dossier n’est pas visible, voir la Remarque ci-dessus.
2. Si le *wingtiptoys.mdf* fichier de base de données n’est pas visible, sélectionnez le **Actualiser** icône, puis le **afficher tous les fichiers** icône en haut de la **l’Explorateur de solutions**  fenêtre.
3. Cliquez sur le *wingtiptoys.mdf* fichier de base de données, puis sélectionnez **ouvrir**.  
    **L’Explorateur de serveurs** s’affiche. 

    ![Navigation - Explorateur de serveurs et l’interface utilisateur](ui_and_navigation/_static/image8.png)
4. Développez le *Tables* dossier.
5. Avec le bouton droit le **produits**de table et sélectionnez **afficher les données de Table**.  
 Le **produits** table s’affiche. 

    ![Interface utilisateur et Navigation - Table Products](ui_and_navigation/_static/image9.png)
6. Ce mode vous permet d’afficher et modifier les données dans le **produits** table manuellement.
7. Fermer le **produits** fenêtre de la table.
8. Dans le **l’Explorateur de serveurs**, avec le bouton droit le **produits** à nouveau la table et sélectionnez **ouvrir la définition de Table**.  
 Les données de conception pour la **produits** table s’affiche. 

    ![Navigation - conception de produits et l’interface utilisateur](ui_and_navigation/_static/image10.png)
9. Dans le **T-SQL** onglet, vous verrez l’instruction SQL DDL qui a été utilisée pour créer la table. Vous pouvez également utiliser l’interface utilisateur de la **conception** onglet pour modifier le schéma.
10. Dans le **l’Explorateur de serveurs**, avec le bouton droit **WingtipToys** de base de données et sélectionnez **fermer la connexion**.   
 En détachant la base de données à partir de Visual Studio, le schéma de base de données pourront être modifiés ultérieurement dans cette série de didacticiels.
11. Retour à **l’Explorateur de solutions**en sélectionnant le **l’Explorateur de solutions** onglet en bas de la **l’Explorateur de serveurs** fenêtre.

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel de la série, vous avez ajouté une interface utilisateur de base, des graphiques, pages et navigation. En outre, vous avez exécuté l’application Web, ce qui a créé la base de données à partir des classes de données que vous avez ajouté dans le didacticiel précédent. Vous avez affiché également le contenu de la *produits* table de la base de données en consultant la base de données directement. Dans l’étape suivante du didacticiel, vous allez afficher les éléments de données et des détails de la base de données.

## <a name="additional-resources"></a>Ressources supplémentaires

[Introduction à la programmation des Pages Web ASP.NET](https://msdn.microsoft.com/library/ms178125.aspx)   
[Vue d’ensemble des contrôles serveur Web ASP.NET](https://msdn.microsoft.com/library/zsyt68f1.aspx)   
[Didacticiel CSS](http://www.w3schools.com/css/default.asp)

>[!div class="step-by-step"]
[Précédent](create_the_data_access_layer.md)
[Suivant](display_data_items_and_details.md)
