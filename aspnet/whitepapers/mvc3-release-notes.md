---
uid: whitepapers/mvc3-release-notes
title: ASP.NET MVC 3 | Documents Microsoft
author: rick-anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/06/2010
ms.topic: article
ms.assetid: f44c166e-7e91-48a0-a6f8-d9285f3594e5
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/mvc3-release-notes
msc.type: content
ms.openlocfilehash: c1fa5d31f68b44bfdfda61c870a6825eeba18647
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-mvc-3"></a>ASP.NET MVC 3
====================
- [Vue d’ensemble](#overview)
- [Notes d’installation](#installation-notes)
- [Configuration logicielle requise](#software-requirements)
- [Documentation](#documentation)
- [Support](#support)
- [La mise à niveau d’un projet ASP.NET MVC 2 vers ASP.NET MVC 3 Tools mettre à jour les](#upgrading)
- [ASP.NET MVC 3 Tools Update (12 avril 2011)](#tu-changes)

    - [Boîte de dialogue « Ajouter un contrôleur » peut structurez maintenant les contrôleurs avec code d’accès aux données et de vues](#tu-AddControllerDialog)
    - [Améliorations apportées à la « ASP.NET MVC 3 nouveau projet « boîte de dialogue](#tu-ImprovementsNewDialogBox)
    - [Modèles de projet incluent à présent Modernizr 1.7](#tu-Modernizr)
    - [Modèles de projet incluent des versions mises à jour de jQuery, jQuery UI et jQuery Validation](#tu-UpdatedJQuery)
    - [Modèles de projet incluent à présent ADO.NET Entity Framework 4.1 comme package NuGet préinstallé](#tu-EF)
    - [Modèles de projet incluent des bibliothèques JavaScript comme packages NuGet préinstallés](#tu-JavaScriptLibsNuget)
    - [Problèmes connus](#tu-KI)
- [ASP.NET MVC 3 RTM (13 janvier 2011)](#MVC3RTM)

    - [Modification : Mise à jour la version de l’interface utilisateur jQuery pour 1.8.7](#RTM-1)
    - [Modification : Affecté la valeur par défaut ModelMetadataProvider au DataAnnotationsModelMetadataProvider](#RTM-2)
    - [Problème résolu : Collez une partie d’une expression de Razor qui contient les résultats d’un espace blanc qu’il contient est inversé](#RTM-3)
    - [Problème résolu : Renommer un fichier Razor qui est ouvert dans l’éditeur désactive la coloration de syntaxe et IntelliSense](#RTM-4)
    - [Problèmes connus](#RTM-KI)
    - [Modifications avec rupture](#RTM-BC)
- [ASP.NET MVC 3 version Release Candidate 2 (10 décembre 2010)](#_Toc2)

    - [Modèles de projet modifié pour inclure 1.4.4 de jQuery, jQuery Validation 1.7 et jQuery UI 1.8.6y UI 1.8.6](#_Toc2_1)
    - [Classe d’ajout de « AdditionalMetadataAttribute »](#_Toc2_2)
    - [Structure de la vue améliorée](#_Toc2_3)
    - [Méthode Html.Raw ajoutée](#_Toc2_3)
    - [Les propriétés renommé « Controller.ViewModel » et « Vue » à « ViewBag »](#_Toc2_4)
    - [Classe renommé « ControllerSessionStateAttribute » à « SessionStateAttribute »](#_Toc2_5)
    - [Propriété RemoteAttribute renommé « Champs » « AdditionalFields »](#_Toc2_6)
    - [Renamed "SkipRequestValidationAttribute" to "AllowHtmlAttribute"](#_Toc2_7)
    - [Méthode modifiées « Html.ValidationMessage » pour afficher le premier Message d’erreur utiles](#_Toc2_8)
    - [Fixe @model déclaration de ne pas ajouter un espace blanc au Document](#_Toc2_9)
    - [Propriété ajoutée « FileExtensions » pour les moteurs d’affichage pour prendre en charge les noms de fichiers de moteur spécifique](#_Toc2_10)
    - [Application d’assistance LabelFor « fixe » pour émettre la valeur correcte pour l’attribut « Pour »](#_Toc2_11)
    - [Méthode RenderAction « fixe » pour donner la priorité des valeurs explicites lors de la liaison de modèle](#_Toc2_12)
    - [Modifications avec rupture](#_Toc2_BC)
    - [Problèmes connus](#_Toc2_KI)
- [ASP.NET MVC 3 version Release Candidate (9 novembre 2010)](#TOC_ASP_NET_3_RC)

    - [Nouvelles fonctionnalités dans ASP.NET MVC 3 RC](#_Toc276711785)
    - [Gestionnaire de Package NuGet](#_Toc276711786)
    - [Amélioration de « Nouveau projet » boîte de dialogue](#_Toc276711787)
    - [Contrôleurs sessionless](#_Toc276711788)
    - [Nouveaux attributs de Validation](#_Toc276711789)
    - [Nouvelles surcharges pour « LabelFor » et « LabelForModel » méthodes](#_Toc276711790)
    - [Action enfant mise en cache de sortie](#_Toc276711791)
    - [Améliorations de boîte de dialogue « Ajouter une vue »](#_Toc276711792)
    - [Validation de la demande granulaire](#_Toc276711793)
    - [Modifications avec rupture](#_Toc276711794)
    - [Problèmes connus](#_Toc276711795)
- [ASP. Notes de version bêta MVC 3 (6 octobre 2010)](#TOC_ASP_NET_3_Beta)

    - [Nouvelles fonctionnalités de la version bêta d’ASP.NET MVC 3](#0.1__Toc274034215)
    - [Gestionnaire de Package NuPack](#0.1__Toc274034216)
    - [Boîte de dialogue Nouveau projet améliorée](#0.1__Toc274034217)
    - [Un moyen simple pour spécifier fortement typée modèles dans les vues Razor](#0.1__Toc274034218)
    - [Prise en charge de nouvelles méthodes d’assistance de Pages Web ASP.NET](#0.1__Toc274034219)
    - [Prise en charge d’Injection de dépendance supplémentaire](#0.1__Toc274034220)
    - [La prise en charge Ajax de base jQuery discrète](#0.1__Toc274034221)
    - [Prise en charge de jQuery non obstructive Validation](#0.1__Toc274034222)
    - [Nouveaux indicateurs de l’Application pour la validation côté Client et du JavaScript discret](#0.1__Toc274034223)
    - [Nouvelle prise en charge pour le Code qui s’exécute avant l’exécution de vues](#0.1__Toc274034224)
    - [Nouvelle prise en charge pour la syntaxe Razor VBHTML](#0.1__Toc274034225)
    - [Contrôle plus granulaire sur ValidateInputAttribute](#0.1__Toc274034226)
    - [Programmes d’assistance pour convertir des traits de soulignement des traits d’union pour les noms d’attribut HTML spécifiés à l’aide d’objets anonymes](#0.1__Toc274034227)
    - [Correctifs de bogues](#0.1__Toc274034228)
    - [Modifications avec rupture](#0.1__Toc274034229)
    - [Problèmes connus](#0.1__Toc274034230)
- [Exclusion de responsabilité](#0.1__Toc274034231)

<a id="overview"></a>
## <a name="overview"></a>Vue d'ensemble

Ce document décrit la version d’ASP.NET MVC 3 RTM pour Visual Studio 2010. ASP.NET MVC est une infrastructure pour le développement d’applications Web qui utilise le modèle Model-View-Controller (MVC). Le programme d’installation d’ASP.NET MVC 3 inclut les composants suivants :

- Composants d’exécution ASP.NET MVC 3
- Outils ASP.NET MVC 3 Visual Studio 2010
- Composants d’exécution des Pages Web ASP.NET
- Outils de Visual Studio 2010 de Pages Web ASP.NET
- Gestionnaire de Package de Microsoft pour .NET (NuGet)
- Une mise à jour pour Visual Studio 2010 qui permet la prise en charge de la syntaxe Razor. (Pour plus d’informations, consultez l’article de la base de connaissances 2483190.)

Vous trouverez l’ensemble des notes de publication pour chaque version préliminaire d’ASP.NET MVC 3 sur le site Web d’ASP.NET à l’adresse suivante :

https://www.asp.net/learn/whitepapers/mvc3-release-notes

<a id="installation-notes"></a>
## <a name="installation-notes"></a>Notes d’installation

Pour installer ASP.NET MVC 3 RTM à l’aide de Web Platform Installer (Web PI), visitez la page suivante :

[https://www.microsoft.com/web/gallery/install.aspx?appid=MVC3](https://www.microsoft.com/web/gallery/install.aspx?appid=MVC3)

Vous pouvez également télécharger le programme d’installation pour ASP.NET MVC 3 RTM pour Visual Studio 2010 à partir de la page suivante :

https://go.microsoft.com/fwlink/?LinkID=208140

ASP.NET MVC 3 peut être installé et qu’il peut s’exécuter côte à côte avec ASP.NET MVC 2.

<a id="software-requirements"></a>
## <a name="software-requirements"></a>Configuration logicielle

Les composants d’exécution ASP.NET MVC 3 nécessitent les logiciels suivants :

- .NET framework version 4. 

    Outils ASP.NET MVC 3 Visual Studio 2010 requièrent le logiciel suivant :
- Visual Studio 2010 ou Visual Web Developer 2010 Express.

<a id="documentation"></a>
## <a name="documentation"></a>Documentation

Documentation pour ASP.NET MVC est disponible sur le site Web MSDN à l’adresse suivante :

[https://go.microsoft.com/fwlink/?LinkId=205717](https://go.microsoft.com/fwlink/?LinkId=205717)

Didacticiels et autres informations sur ASP.NET MVC sont disponibles sur la page MVC du site Web ASP.NET à l’adresse suivante :

[https://www.asp.net/mvc/](../mvc/index.md)

<a id="support"></a>
## <a name="support"></a>Assistance

Il s’agit d’une version entièrement prise en charge. Vous trouverez des informations sur l’obtention de support technique au [site Web de Support technique de Microsoft](https://support.microsoft.com/).

Vous pouvez également poser des questions sur cette version sur le forum ASP.NET MVC, où les membres de la Communauté ASP.NET peuvent souvent fournir un support informel :

[https://forums.asp.net/1146.aspx](https://forums.asp.net/1146.aspx)

<a id="upgrading"></a>
## <a name="upgrading-an-aspnet-mvc-2-project-to-aspnet-mvc-3-tools-update"></a>La mise à niveau d’un projet ASP.NET MVC 2 vers ASP.NET MVC 3 Tools mettre à jour les

ASP.NET MVC 3 peut être installé côte à côte avec ASP.NET MVC 2 sur le même ordinateur, ce qui vous donne la souplesse dans le choix quand mettre à niveau une application ASP.NET MVC 2 vers ASP.NET MVC 3.

Pour mettre à niveau une application ASP.NET MVC 2 existante vers la version 3, procédez comme suit :

1. Créer un nouveau projet ASP.NET MVC 3 vide sur votre ordinateur. Ce projet contient des fichiers qui sont requis pour la mise à niveau.
2. Copiez les fichiers suivants à partir du projet ASP.NET MVC 3 dans l’emplacement correspondant de votre projet ASP.NET MVC 2. Vous devez mettre à jour toutes les références à la bibliothèque jQuery pour prendre en compte le nouveau nom de fichier (jQuery-1.5.1.js) : 

    - /Views/Web.config
    - /packages.config
    - /scripts/\*.js
    - /Contenu/thèmes/\*.\*
3. Copie le *packages* dossier à la racine de la solution du projet ASP.NET MVC 3 vide dans la racine de votre solution, qui se trouve dans le répertoire où se trouve le fichier de solution .sln.
4. Si votre projet ASP.NET MVC 2 contient des domaines, copiez le fichier /Views/Web.config le *vues* dossier de chaque zone.
5. Dans les deux fichiers Web.config dans le projet ASP.NET MVC 2, globalement rechercher et remplacer la version d’ASP.NET MVC. Recherchez la chaîne suivante : 

    [!code-console[Main](mvc3-release-notes/samples/sample1.cmd)]

    Remplacer par les éléments suivants :

    [!code-console[Main](mvc3-release-notes/samples/sample2.cmd)]
6. Dans l’Explorateur de solutions, supprimez la référence à *System.Web.Mvc* (qui pointe vers la DLL de la version 2), puis ajoutez une référence à *System.Web.Mvc* (v3.0.0.0).
7. Ajouter une référence à System.Web.WebPages.dll et à System.Web.Helpers.dll. Ces assemblys se trouvent dans les dossiers suivants : 

    - %ProgramFiles%\ Microsoft ASP.NET\ASP.NET MVC 3\Assemblies
    - %ProgramFiles%\ Microsoft ASP.NET\ASP.NET Web Pages\v1.0\Assemblies
8. Dans l’Explorateur de solutions, cliquez sur le nom du projet et sélectionnez Décharger le projet. Cliquez à nouveau sur le nom du projet, puis sélectionnez Modifier *nom_projet*.csproj.
9. Recherchez le *ProjectTypeGuids* élément et remplacez {F85E285D-A4E0-4152-9332-AB1D724D3325} par {E53F8FEA-EAE0-44A6-8774-FFD645390401}.
10. Enregistrer les modifications, cliquez sur le projet, puis recharger le projet.
11. Dans le fichier Web.config de racine de l’application, ajoutez les paramètres suivants pour le *assemblys* section. 

    [!code-xml[Main](mvc3-release-notes/samples/sample3.xml)]
12. Si le projet fait référence à des bibliothèques tierces qui sont compilés à l’aide d’ASP.NET MVC 2, ajoutez ce qui suit en surbrillance *bindingRedirect* élément dans le fichier Web.config dans la racine de l’application sous le  *configuration* section : 

    [!code-xml[Main](mvc3-release-notes/samples/sample4.xml)]

<a id="tu-changes"></a>
## <a name="changes-in-aspnet-mvc-3-tools-update"></a>Modifications dans ASP.NET MVC 3 Tools Update

Cette section décrit les modifications apportées dans la version d’ASP.NET MVC 3 Tools Update depuis la version RTM d’ASP.NET MVC 3.

<a id="tu-AddControllerDialog"></a>
### <a name="add-controller-dialog-box-can-now-scaffold-controllers-with-views-and-data-access-code"></a>Boîte de dialogue « Ajouter un contrôleur » peut structurez maintenant les contrôleurs avec code d’accès aux données et de vues

Génération de modèles automatique est un moyen de générer rapidement un contrôleur et les vues de votre application. Une fois que le code a été généré, vous pouvez le modifier pour répondre aux exigences de votre projet.

Pour lancer le *ajouter un contrôleur* droit de la boîte de dialogue dans ASP.NET MVC 3, le *contrôleurs* dossier *l’Explorateur de solutions*, cliquez sur *ajouter*, puis cliquez sur *contrôleur*. La boîte de dialogue a été améliorée pour offrir des options de génération de modèles automatique supplémentaires.

![](mvc3-release-notes/_static/image1.png)

Il existe trois modèles de génération de modèles automatique par défaut.

#### <a name="empty-controller"></a>Contrôleur vide

Ce modèle génère un fichier de contrôleur vide. Ce modèle équivaut à ne pas cocher *ajouter des actions pour créer, modifier, détails, supprimez les scénarios* dans les versions précédentes d’ASP.NET MVC. Si vous choisissez cette option, aucune des options supplémentaires ne sont disponibles.

#### <a name="controller-with-empty-readwrite-actions"></a>Contrôleur avec actions en lecture/écriture vides

Ce modèle génère un fichier de contrôleur qui a toutes les méthodes d’action requise, mais aucun code de mise en œuvre dans les méthodes. Ce modèle est équivalent à la vérification *ajouter des actions pour créer, modifier, détails, supprimez les scénarios* dans les versions précédentes d’ASP.NET MVC. Si vous choisissez cette option, aucune des options supplémentaires ne sont disponibles.

#### <a name="controller-with-readwrite-actions-and-views-using-entity-framework"></a>Contrôleur avec actions en lecture/écriture et de vues, utilisant Entity Framework

Ce modèle vous permet de créer rapidement une interface utilisateur de saisie de données travail. Il génère un code qui gère une plage des exigences courants et des scénarios, tels que les éléments suivants :

- *Accès aux données*. Le code généré lit et écrit des entités dans une base de données. Il fonctionne avec l’approche Entity Framework Code First si vous choisissez une classe de contexte de données existante ou si vous laissez le modèle pour générer un nouveau *DbContext* classe. Elle fonctionne également avec l’approche Entity Framework Database First ou Model First si vous choisissez un existant *ObjectContext* classe.
- *Validation*. Le code généré utilise la liaison de modèle ASP.NET MVC et les fonctions de métadonnées afin que les envois de formulaire sont validés conformément aux règles déclarées sur votre classe de modèle. Cela inclut des règles de validation intégrées, telles que la *requis* et *StringLength* attributs et les règles de validation personnalisées.
- *Les relations un-à-plusieurs*. Si vous définissez des relations de clé étrangère un-à-plusieurs entre vos classes de modèle, le code généré produira des listes déroulantes pour sélectionner des entités associées. Par exemple, vous pouvez définir les classes du modèle suivant suit les conventions d’Entity Framework Code First : 

    [!code-csharp[Main](mvc3-release-notes/samples/sample5.cs)]

    Lorsque vous structurez puis un contrôleur pour le *produit* (classe), ses vues permettent aux utilisateurs de choisir un *catégorie* objet pour chaque *produit* instance.

    Ce modèle active des options supplémentaires dans le *ajouter un contrôleur* boîte de dialogue. Pour *classe de modèle*, vous pouvez choisir n’importe quelle classe de modèle dans votre solution, qui détermine le type de données que les utilisateurs pourront créer ou modifier :
- Si vous souhaitez utiliser Entity Framework Code First, vous pouvez choisir n’importe quelle classe de modèle.
- Si vous utilisez Entity Framework Database First ou Entity Framework Model First, veillez à choisir une classe d’entité définie dans le modèle conceptuel.

Pour *classe du contexte de données*, vous pouvez rendre ces choix :

- Si vous souhaitez utiliser un Code First et n’ont aucun contexte de données existante de classe, choisissez  *&lt;nouveau contexte de données... &gt;*". Une classe de contexte de données est alors générée pour vous.
- Si vous souhaitez utiliser un Code First et disposent d’une classe de contexte de données existante, cliquez ici. Elle sera mise à jour pour conserver la classe de modèle que vous avez sélectionné.
- Si vous utilisez la base de données First ou Model First, choisissez votre classe de contexte d’objet.

Pour les vues, choisissez le moteur d’affichage que vous souhaitez utiliser, ou aucun si vous ne souhaitez pas structurez toutes les vues.

Vous pouvez sélectionner des options d’avancées spécifier d’autres options pour les vues générées. Par exemple, vous pouvez choisir la disposition ou la page maître à utiliser.

<a id="tu-ImprovementsNewDialogBox"></a>
### <a name="improvements-to-the-aspnet-mvc-3-new-project-dialog-box"></a>Améliorations apportées à la « ASP.NET MVC 3 nouveau projet « boîte de dialogue

La boîte de dialogue que vous permet de créer des projets ASP.NET MVC 3 comprend plusieurs améliorations, comme indiqué ci-dessous.

![](mvc3-release-notes/_static/image2.png)

#### <a name="new-intranet-project-template"></a>Nouveau modèle de « Projet Intranet »

La liste des modèles de projet inclut un nouveau modèle Application Intranet. Ce modèle contient des paramètres pour la création d’une application web à l’aide de l’authentification Windows au lieu de l’authentification par formulaire. Comme une application intranet requiert certains paramètres IIS qui ne peut pas être encapsulés dans un modèle de projet, le modèle inclut un fichier Lisez-moi des instructions sur la façon de rendre le modèle de projet de travail dans IIS. Documentation pour l’un nouveau modèle Application Intranet est disponible sur le site Web MSDN à l’adresse suivante :

[https://msdn.microsoft.com/library/gg703322(VS.98).aspx](https://msdn.microsoft.com/library/gg703322(VS.98).aspx)

#### <a name="project-templates-are-now-html5-enabled"></a>Modèles de projet sont maintenant HTML5 activé

La boîte de dialogue Nouveau projet contient maintenant une option pour ajouter des fonctionnalités HTML5 aux modèles de projet. En sélectionnant l’option entraîne la génération des vues qui contiennent la nouvelle HTML5  *&lt;en-tête&gt;*,  *&lt;pied de page&gt;*, et  *&lt;navigation&gt;*  éléments.

Notez que des versions antérieures des navigateurs ne prennent pas en charge les balises HTML5. Pour résoudre cette limitation, les modèles de projet HTML5 incluent une référence à la bibliothèque Modernizr. (Consultez la section suivante.)

<a id="tu-Modernizr"></a>
### <a name="project-templates-now-include-modernizr-17"></a>Modèles de projet incluent à présent Modernizr 1.7

Modernizr est une bibliothèque JavaScript qui permet la prise en charge de CSS 3 et HTML5 dans les navigateurs qui ne prennent pas encore en charge ces fonctionnalités. Cette bibliothèque est incluse comme package NuGet préinstallé dans les modèles pour les projets ASP.NET MVC 3. Pour plus d’informations sur Modernizr, consultez [http://www.modernizr.com/](http://www.modernizr.com/).

<a id="tu-UpdatedJQuery"></a>
### <a name="project-templates-include-updated-versions-of-jquery-jquery-ui-and-jquery-validation"></a>Modèles de projet incluent des versions mises à jour de jQuery, jQuery UI et jQuery Validation

Les modèles de projet incluent désormais les versions suivantes des scripts jQuery :

- jQuery 1.5.1
- jQuery Validation 1.8
- jQuery UI 1.8.11

Ces bibliothèques sont incluses comme packages NuGet préinstallés.

<a id="tu-EF"></a>
### <a name="project-templates-now-include-adonet-entity-framework-41-as-a-pre-installed-nuget-package"></a>Modèles de projet incluent à présent ADO.NET Entity Framework 4.1 comme package NuGet préinstallé

ADO.NET Entity Framework 4.1 inclut la fonctionnalité Code First. Code First est un nouveau modèle de développement pour ADO.NET Entity Framework qui fournit une alternative aux modèles Database First et Model First existants.

Code First consiste à définir votre modèle à l’aide de classes POCO (« plain old CLR objects ») écrites en Visual Basic ou c#. Ces classes peuvent être mappées à une base de données existante ou être utilisés pour générer un schéma de base de données. Une configuration supplémentaire peut être fournie à l’aide de *DataAnnotations* attributs ou à l’aide des API fluent.

Documentation sur l’utilisation de Code Firstwith ASP.NET MVC est disponible sur le site Web ASP.NET les URL suivantes :

[https://www.asp.net/mvc/tutorials/getting-started-with-mvc3-part1-cs](../mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) [https://www.asp.net/entity-framework/tutorials/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application](../mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)

<a id="tu-JavaScriptLibsNuget"></a>
### <a name="project-templates-include-javascript-libraries-as-pre-installed-nuget-packages"></a>Modèles de projet incluent des bibliothèques JavaScript comme packages NuGet préinstallés

Lorsque vous créez un nouveau projet ASP.NET MVC 3, le projet inclut les fichiers JavaScript mentionnés précédemment (par exemple, la bibliothèque Modernizr) en les installant à l’aide de NuGet au lieu d’ajouter directement des scripts dans le dossier Scripts dans le modèle de projet contenu. Cela vous permet d’utiliser NuGet pour les scripts de mise à jour vers la dernière version lorsque de nouvelles versions des scripts.

Par exemple, étant donné la fréquence des nouvelles versions de jQuery, la version de jQuery incluse dans le modèle de projet à un moment donné sera obsolète. Toutefois, étant donné que jQuery est fourni comme un package NuGet installé, vous seront avertis dans la boîte de dialogue NuGet lorsque de nouvelles versions de jQuery sont disponibles.

Étant donné que jQuery inclut le numéro de version dans le nom de fichier, la mise à jour de jQuery vers la dernière version nécessite également la mise à jour la  *&lt;script&gt;*  balise qui fait référence au fichier jQuery pour utiliser le nouveau nom de fichier. Autres bibliothèques de scripts fournies n’incluent pas le numéro de version dans le nom du script, ils peuvent être plus facilement mises à jour leurs versions les plus récentes.

<a id="tu-KI"></a>
## <a name="known-issues"></a>Problèmes connus

- Dans certains cas, l’installation peut échouer avec l’erreur message « Échec de l’Installation avec le code d’erreur (0 x 80070643) ». Pour plus d’informations sur la façon de contourner ce problème, consultez [article de la base de connaissances 2531566](https://support.microsoft.com/kb/2531566).
- La génération de modèles automatique pour l’ajout d’un contrôleur de ne pas structurez des entités qui tirent parti de la prise en charge de l’héritage des entités dans Entity Framework. Par exemple, prenons une base de *personne* classe est héritée par un *Student* génération de modèles automatique de la classe le *étudiant* classe entraîne un code généré qui ne se compile pas.
- Création d’un projet ASP.NET MVC 3 dans un dossier de solution entraîne une *NullReferenceException* erreur. La solution de contournement consiste à créer le projet ASP.NET MVC 3 à la racine de la solution, puis le déplacer dans le dossier de solution.
- IntelliSense pour la syntaxe Razor ne fonctionne pas lorsque ReSharper est installé. Si vous ReSharper est installé et que vous souhaitez tirer parti de la prise en charge de Razor IntelliSense dans ASP.NET MVC 3, consultez l’entrée [Razor Intellisense and ReSharper](http://blogs.jetbrains.com/dotnet/2010/11/razor-intellisense-and-resharper/) sur le blog de hadi, qui explique comment les utiliser aujourd'hui ensemble.
- Pendant l’installation, la boîte de dialogue d’acceptation CLUF affiche les termes du contrat de licence dans une fenêtre plus petite que prévue.
- Lorsque vous modifiez une vue Razor (.cshtml ou. *vbhtml* fichier), des vues. ASP.NET MVC 3 n’inclut pas les extraits de code pour les vues Razor... aspxselecting un extrait de code pour ASP.NET MVC affichera des extraits de code pour
- Si vous installez ASP.NET MVC 3 pour Visual Web Developer Express sur un ordinateur où Visual Studio n’est pas installé, installez Visual Studio ultérieurement, vous devez réinstaller ASP.NET MVC 3. Visual Studio et Visual Web Developer Express partagent des composants qui sont mis à niveau par le programme d’installation d’ASP.NET MVC 3. Le même problème s’applique si vous installez ASP.NET MVC 3 pour Visual Studio sur un ordinateur qui n’ont pas Visual Web Developer Express et installer ultérieurement Visual Web Developer Express.

<a id="MVC3RTM"></a>
## <a name="changes-in-aspnet-mvc-3-rtm"></a>Modifications apportées à la version RTM d’ASP.NET MVC 3

Cette section décrit les modifications et des correctifs de bogues apportées dans la version RTM d’ASP.NET MVC 3 depuis la version RC2.

<a id="RTM-1"></a>
### <a name="change-updated-the-version-of-jquery-ui-to-187"></a>Modification : Mise à jour la version de l’interface utilisateur jQuery pour 1.8.7

Les modèles de projet ASP.NET MVC pour Visual Studio ont été mis à jour pour inclure la version la plus récente de la bibliothèque jQuery UI. Les modèles incluent également l’ensemble minimal des fichiers de ressources requises par l’interface utilisateur, telles que le CSS associés et les fichiers image jQuery.

<a id="RTM-2"></a>
### <a name="change-changed-the-default-modelmetadataprovider-back-to-dataannotationsmodelmetadataprovider"></a>Modification : Affecté la valeur par défaut ModelMetadataProvider au DataAnnotationsModelMetadataProvider

La version RC2 d’ASP.NET MVC 3 introduit un *CachedDataAnnotationsMetadataProvider* classe que la mise en cache au-dessus existants fourni *DataAnnotationsModelMetadataProvider* classe comme un amélioration des performances. Toutefois, certains bogues ont été signalées avec cette implémentation, la modification a été annulée et déplacée dans le projet MVC perspectives, ce qui est disponible à l’adresse [ASP.NET WebStack](https://github.com/aspnet/AspNetWebStack).

<a id="RTM-3"></a>
### <a name="fixed-pasting-part-of-a-razor-expression-that-contains-whitespace-results-in-it-being-reversed"></a>Problème résolu : Collez une partie d’une expression de Razor qui contient les résultats d’un espace blanc qu’il contient est inversé

Dans les versions préliminaires d’ASP.NET MVC 3, lorsque vous collez une partie d’une expression Razor qui contient un espace blanc dans un fichier Razor, l’expression obtenue est inversée. Par exemple, considérez le bloc de code Razor suivant :

[!code-cshtml[Main](mvc3-release-notes/samples/sample6.cshtml)]

Si vous sélectionnez le texte « premier param » dans la première méthode et collez en tant qu’argument dans la deuxième méthode, le résultat est comme suit :

[!code-cshtml[Main](mvc3-release-notes/samples/sample7.cshtml)]

Le comportement correct est que l’opération de collage doit avoir les conséquences suivantes :

[!code-cshtml[Main](mvc3-release-notes/samples/sample8.cshtml)]

Ce problème a été résolu dans la version RTM, afin que l’expression est correctement conservée pendant l’opération de collage.

<a id="RTM-4"></a>
### <a name="fixed-renaming-a-razor-file-that-is-opened-in-the-editor-disables-syntax-colorization-and-intellisense"></a>Problème résolu : Renommer un fichier Razor qui est ouvert dans l’éditeur désactive la coloration de syntaxe et IntelliSense

Renommer un fichier Razor à l’aide de l’Explorateur de solutions pendant que le fichier est ouvert dans la fenêtre Éditeur entraîne la mise en surbrillance de syntaxe et IntelliSense pour cesser de fonctionner pour ce fichier. Ce problème a été corrigé afin que la mise en surbrillance et IntelliSense sont conservées après un changement de nom.

<a id="RTM-KI"></a>
## <a name="known-issues"></a>Problèmes connus

- Si vous fermez Visual Studio 2010 SP1 bêta alors que la Console du Gestionnaire de Package NuGet est ouverte, Visual Studio se bloque et tente de redémarrer. Ce problème sera résolu dans la version RTM de Visual Studio 2010 SP1.
- Le programme d’installation d’ASP.NET MVC 3 n’est pas en mesure d’installer une version initiale du Gestionnaire de package NuGet. Après avoir installé la version initiale, NuGet peut être installé et mis à jour de l’aide du Gestionnaire d’extensions Visual Studio. Si vous avez déjà installé de NuGet, accédez à la galerie Visual Studio Extension mise à jour vers la version la plus récente de NuGet.
- Création d’un projet ASP.NET MVC 3 dans un dossier de solution entraîne une *NullReferenceException* erreur. La solution de contournement consiste à créer le projet ASP.NET MVC 3 à la racine de la solution, puis le déplacer dans le dossier de solution.
- Le programme d’installation peut prendre beaucoup plus de temps que les versions antérieures d’ASP.NET MVC pour terminer. Il s’agit, car elle met à jour des composants de Visual Studio 2010.
- IntelliSense pour la syntaxe Razor ne fonctionne pas lorsque ReSharper est installé. Si vous ReSharper est installé et que vous souhaitez tirer parti de la prise en charge de Razor IntelliSense dans ASP.NET MVC 3, consultez l’entrée [Razor Intellisense and ReSharper](http://blogs.jetbrains.com/dotnet/2010/11/razor-intellisense-and-resharper/) sur le blog de hadi, qui explique comment les utiliser aujourd'hui ensemble.
- Les vues CCSHTML et VBHTML créés avec la version bêta d’ASP.NET MVC 3 n’ont pas leur action de génération définie correctement, ce qui permet d’afficher ces types sont omis lorsque le projet est publié. La valeur de l’Action de génération de ces fichiers doit être définie à « Contenu ». ASP.NET MVC 3 RTM résout ce problème pour les nouveaux fichiers, mais ne corrige pas le paramètre pour les fichiers existants pour un projet créé avec les versions préliminaires.
- ![](mvc3-release-notes/_static/image3.png)
- Pendant l’installation, la boîte de dialogue d’acceptation CLUF affiche les termes du contrat de licence dans une fenêtre plus petite que prévu. / li&gt;
- Lorsque vous modifiez une vue Razor (fichier .cshtml), l’élément de menu contrôleur Go dans Visual Studio ne sera pas disponible, et il en existe aucun extrait de code.
- Si vous installez ASP.NET MVC 3 pour Visual Web Developer Express sur un ordinateur où Visual Studio n’est pas installé, installez Visual Studio ultérieurement, vous devez réinstaller ASP.NET MVC 3. Visual Studio et Visual Web Developer Express partagent des composants qui sont mis à niveau par le programme d’installation d’ASP.NET MVC 3. Le même problème s’applique si vous installez ASP.NET MVC 3 pour Visual Studio sur un ordinateur qui n’ont pas Visual Web Developer Express et installer ultérieurement Visual Web Developer Express.

<a id="RTM-BC"></a>
## <a name="breaking-changes"></a>Modifications avec rupture

- Dans les versions précédentes d’ASP.NET MVC, action, les filtres sont créer par la demande, sauf dans certains cas. Ce comportement n’a jamais été un comportement de garantie, mais simplement un détail d’implémentation et le contrat pour les filtres était à prendre en compte sans état. Dans ASP.NET MVC 3, les filtres sont mis en cache en plus efficacement. Par conséquent, tous les filtres d’action personnalisée qui incorrectement stocker l’état de l’instance peuvent être interrompues.
- L’ordre d’exécution pour les filtres d’exception a été modifiée pour les filtres d’exception qui ont le même *commande* valeur. Dans ASP.NET MVC 2 et versions antérieures, les filtres d’exception sur le contrôleur qui ont le même *commande* valeur que ceux qui sont sur une méthode d’action sont exécutés avant les filtres d’exception sur la méthode d’action. Cela serait généralement le cas lorsque les filtres d’exception sont appliquées sans spécifier *commande* valeur. Dans ASP.NET MVC 3, cette commande a été inversée afin que le Gestionnaire d’exceptions plus spécifique s’exécute en premier. Comme dans les versions antérieures, si la *commande* propriété est explicitement spécifiée, les filtres sont exécutés dans l’ordre spécifié.
- Une nouvelle propriété nommée *FileExtensions* a été ajouté à la *VirtualPathProviderViewEngine* classe de base. Lorsque ASP.NET présente une vue par le chemin d’accès (pas par nom), seules les vues avec une extension de fichier contenues dans la liste spécifiée par cette nouvelle propriété sont considérées comme. Il s’agit d’une modification avec rupture dans les applications où un fournisseur de générations personnalisées est inscrit afin d’activer une extension de fichier personnalisés pour les vues du formulaire Web et le fournisseur fait référence à ces vues à l’aide d’un chemin d’accès complet, plutôt qu’un nom. La solution de contournement consiste à modifier la valeur de la *FileExtensions* propriété à inclure l’extension de fichier personnalisé.
- Les implémentations de fabrique de contrôleur personnalisé qui implémentent directement le *IControllerFactory* interface doit fournir une implémentation de la nouvelle *GetControllerSessionBehavior* méthode qui a été ajouté à l’interface dans cette version. En règle générale, il est recommandé de ne pas implémenter cette interface directement et à la place de dériver votre classe de *DefaultControllerFactory*.

<a id="_Toc2"></a>
## <a name="changes-in-aspnet-mvc-3-rc2"></a>Modifications dans ASP.NET MVC 3 RC2

Cette section décrit les modifications (nouvelles fonctionnalités et des correctifs de bogues) dans la version d’ASP.NET MVC 3 RC2 depuis la version RC.

<a id="_Toc2_1"></a>
### <a name="project-templates-changed-to-include-jquery-144-jquery-validation-17-and-jquery-ui-186"></a>Modèles de projet modifié pour inclure 1.4.4 de jQuery, jQuery Validation 1.7 et jQuery UI 1.8.6

Les modèles de projet ASP.NET MVC 3 incluent désormais les dernières versions de jQuery et jQuery Validation jQuery UI. jQuery UI est une nouveauté dans les modèles de projet et fournit des widgets d’interface utilisateur utile. Pour plus d’informations sur l’interface utilisateur jQuery, visitez leur page d’accueil : [http://jqueryui.com/](http://jqueryui.com/).

<a id="_Toc2_2"></a>
### <a name="added-additionalmetadataattribute-class"></a>Classe d’ajout de « AdditionalMetadataAttribute »

Vous pouvez utiliser la *AdditionalMetadataAttribute* classe pour remplir la *ModelMetadata.AdditionalValues* dictionnaire pour une propriété de modèle.

Par exemple, qu'un modèle de vue a des propriétés qui doivent être affichées uniquement à un administrateur. Ce modèle peut être annoté avec le nouvel attribut à l’aide de AdminOnly en tant que la clé et la valeur true comme valeur, comme dans l’exemple suivant :

[!code-csharp[Main](mvc3-release-notes/samples/sample9.cs)]

Ces métadonnées sont rendue disponible à tous les modèles d’affichage ou de l’éditeur lors du rendu d’un modèle d’affichage de produit. Il vous incombe en tant que développeur d’applications pour interpréter les informations de métadonnées.

<a id="_Toc2_3"></a>
### <a name="improved-view-scaffolding"></a>Structure de la vue améliorée

Les modèles T4 utilisés pour les vues de la génération de modèles automatique maintenant génèrent des appels aux méthodes d’assistance de modèle tel que *EditorFor* au lieu de programmes d’assistance telles que *TextBoxFor*. Cette modification améliore la prise en charge des métadonnées sur le modèle sous la forme d’attributs d’annotations de données lorsque la boîte de dialogue Ajouter une vue génère une vue.

La génération de modèles automatique ajouter une vue inclut également l’amélioration de la détection et de l’utilisation des informations de clé primaire sur le modèle, en fonction de la convention. Par exemple, la boîte de dialogue Ajouter une vue utilise ces informations pour vous assurer que la valeur de clé primaire n’est pas structurée comme un champ de formulaire modifiable.

La valeur par défaut modifier et la création de modèles d’inclure des références aux scripts jQuery requis pour la validation client.

<a id="_Toc2_4"></a>
### <a name="added-htmlraw-method"></a>Méthode Html.Raw ajoutée

Par défaut, l’affichage Razor moteur encode au format HTML toutes les valeurs. Par exemple, l’extrait de code suivant encode le code HTML à l’intérieur de la variable de salutations afin qu’elle est affichée dans la page en tant que &amp;lt ; fort&amp;gt ; Salut tout le monde ! &amp;lt ; /strong&amp;gt ;.

[!code-cshtml[Main](mvc3-release-notes/samples/sample10.cshtml)]

La nouvelle *Html.Raw* méthode offre un moyen simple d’afficher le code HTML lorsque le contenu est connu pour être sûr. L’exemple suivant affiche la même chaîne, mais la chaîne est rendue en tant que balisage :

[!code-cshtml[Main](mvc3-release-notes/samples/sample11.cshtml)]

<a id="_Toc2_5"></a>
### <a name="renamed-controllerviewmodel-property-and-the-view-property-to-viewbag"></a>Les propriétés renommé « Controller.ViewModel » et « Vue » à « ViewBag »

Auparavant, le *ViewModel* propriété de *contrôleur* correspondaient à la *vue* propriété d’une vue. Ces deux propriétés permettent d’accéder aux valeurs de la *ViewDataDictionary* de l’objet à l’aide de la syntaxe de l’accesseur de propriété dynamique. Les deux propriétés ont été renommées pour être identiques afin d’éviter toute confusion et plus cohérent.

<a id="_Toc2_6"></a>
### <a name="renamed-controllersessionstateattribute-class-to-sessionstateattribute"></a>Classe renommé « ControllerSessionStateAttribute » à « SessionStateAttribute »

Le *ControllerSessionStateAttribute* classe a été introduite dans la version RC d’ASP.NET MVC 3. La propriété a été renommée pour être plus concise.

<a id="_Toc2_7"></a>
### <a name="renamed-remoteattribute-fields-property-to-additionalfields"></a>Propriété RemoteAttribute renommé « Champs » « AdditionalFields »

Le *RemoteAttribute* de classe *champs* propriété a provoqué une certaine confusion parmi les utilisateurs. Modification du nom de cette propriété sur *AdditionalFields* clarifie son intention.

<a id="_Toc2_8"></a>
### <a name="renamed-skiprequestvalidationattribute-to-allowhtmlattribute"></a>Renommé « SkipRequestValidationAttribute » à « AllowHtmlAttribute »

Le *SkipRequestValidationAttribute* attribut a été renommé en *AllowHtmlAttribute* afin de mieux représenter son utilisation prévue.

<a id="_Toc2_9"></a>
### <a name="changed-htmlvalidationmessage-method-to-display-the-first-useful-error-message"></a>Méthode modifiées « Html.ValidationMessage » pour afficher le premier Message d’erreur utiles

Le *Html.ValidationMessage* méthode a été résolue pour afficher le premier message d’erreur utiles au lieu d’afficher simplement de la première erreur.

Lors de la liaison de modèle, le *ModelState* dictionnaire peut être rempli à partir de plusieurs sources avec un message d’erreur concernant la propriété, y compris à partir du modèle lui-même (s’il implémente *IValidatableObject* ), les attributs de validation appliqués à la propriété et d’exceptions levées pendant que la propriété est accessible.

Lorsque le *Html.ValidationMessage* méthode affiche un message de validation, il ignore les entrées d’état de modèle qui incluent une exception, car celles-ci ne sont généralement pas prévues pour l’utilisateur final. Au lieu de cela, la méthode recherche le premier message de validation qui n’est pas associé à une exception et affiche ce message. Si aucun message n’est trouvée, la valeur par défaut est un message d’erreur générique qui est associé à la première exception.

<a id="_Toc2_10"></a>
### <a name="fixed-model-declaration-to-not-add-whitespace-to-the-document"></a>Fixe @model déclaration de ne pas ajouter un espace blanc au Document

Dans les versions antérieures, le  *@model*  déclaration en haut d’une vue ajouté une ligne vide à la sortie HTML à restituer. Ce problème a été corrigé afin que la déclaration n’introduit pas d’espace blanc.

<a id="_Toc2_11"></a>
### <a name="added-fileextensions-property-to-view-engines-to-support-engine-specific-file-names"></a>Propriété ajoutée « FileExtensions » pour les moteurs d’affichage pour prendre en charge les noms de fichiers de moteur spécifique

Un moteur d’affichage peut retourner une vue à l’aide d’un chemin d’accès de la vue explicite comme dans l’exemple suivant :

[!code-csharp[Main](mvc3-release-notes/samples/sample12.cs)]

Le moteur d’affichage tente toujours de restituer la vue. Par défaut, le moteur d’affichage Web Forms est le premier moteur d’affichage ; Étant donné que le moteur de Web Forms ne peut pas restituer une vue Razor, une erreur se produit. Moteurs d’affichage ont maintenant un *FileExtensions* propriété qui est utilisée pour spécifier les extensions de fichier prises en charge. Cette propriété est vérifiée lorsque ASP.NET détermine si un moteur d’affichage peut restituer un fichier. Il s’agit d’une modification avec rupture et plus de détails sont inclus dans le [modifications avec rupture](#_Toc2_BC) section de ce document.

<a id="_Toc2_12"></a>
### <a name="fixed-labelfor-helper-to-emit-the-correct-value-for-the-for-attribute"></a>Application d’assistance LabelFor « fixe » pour émettre la valeur correcte pour l’attribut « Pour »

Un bogue a été résolu où le *LabelFor* méthode rendu un *pour* attribut qui correspond à la *d’entrée* l’élément *nom* d’attribut à la place de son ID. Selon le W3C, le *pour* attribut doit correspondre à la *d’entrée* ID de. l’élément

<a id="_Toc2_13"></a>
### <a name="fixed-renderaction-method-to-give-explicit-values-precedence-during-model-binding"></a>Méthode RenderAction « fixe » pour donner la priorité des valeurs explicites lors de la liaison de modèle

Dans les versions antérieures, les valeurs explicites qui ont été passés à la *RenderAction* méthode a été ignoré en faveur de la valeur actuelle du formulaire lors de la liaison de modèle à l’intérieur d’une action enfant. Le correctif garantit que les valeurs explicites sont prioritaires pendant la liaison du modèle.

<a id="_Toc2_BC"></a>
## <a name="breaking-changes"></a>Modifications avec rupture

- Dans les versions précédentes d’ASP.NET MVC, les filtres d’action ont été créés par la demande, sauf dans certains cas. Ce comportement n’a jamais été un comportement de garantie, mais simplement un détail d’implémentation et le contrat pour les filtres était à prendre en compte sans état. Dans ASP.NET MVC 3, les filtres sont mis en cache en plus efficacement. Par conséquent, tous les filtres d’action personnalisée qui incorrectement stocker l’état de l’instance peuvent être interrompues.
- L’ordre d’exécution pour les filtres d’exception a été modifiée pour les filtres d’exception qui ont le même *commande* valeur. Dans ASP.NET MVC 2 et versions antérieures, les filtres d’exception sur le contrôleur ayant le même *commande* valeur que ceux qui sont sur une méthode d’action étaient exécutés avant les filtres d’exception sur la méthode d’action. Cela serait généralement le cas lorsque les filtres d’exception étaient appliqués sans spécifier *commande* valeur. Dans ASP.NET MVC 3, cette commande a été inversée afin que le Gestionnaire d’exceptions plus spécifique s’exécute en premier. Comme dans les versions antérieures, si la *commande* propriété est explicitement spécifiée, les filtres sont exécutés dans l’ordre spécifié.
- Une nouvelle propriété nommée *FileExtensions* a été ajouté à la *VirtualPathProviderViewEngine* classe de base. Lorsque ASP.NET présente une vue par le chemin d’accès (pas par nom), seules les vues avec une extension de fichier contenues dans la liste spécifiée par cette nouvelle propriété sont considérées comme. Il s’agit d’une modification avec rupture dans les applications où un fournisseur de générations personnalisées est inscrit afin d’activer une extension de fichier personnalisés pour les vues du formulaire Web et le fournisseur fait référence à ces vues à l’aide d’un chemin d’accès complet, plutôt qu’un nom. La solution de contournement consiste à modifier la valeur de la *FileExtensions* propriété à inclure l’extension de fichier personnalisé.
- Les implémentations de fabrique de contrôleur personnalisé qui implémentent directement le *IControllerFactory* interface doit fournir une implémentation de la nouvelle *GetControllerSessionBehavior ** méthode qui a été ajouté à la interface dans cette version*. En règle générale, il est recommandé de ne pas implémenter cette interface directement et à la place de dériver votre classe de *DefaultControllerFactory*.

<a id="_Toc2_KI"></a>
## <a name="known-issues"></a>Problèmes connus

- Le programme d’installation d’ASP.NET MVC 3 n’est pas en mesure d’installer une version initiale du Gestionnaire de package NuGet. Après avoir installé la version initiale, NuGet peut être installé et mis à jour de l’aide du Gestionnaire d’extensions Visual Studio. Si vous avez déjà installé de NuGet, accédez à la galerie Visual Studio Extension mise à jour vers la version la plus récente de NuGet.
- Création d’un projet ASP.NET MVC 3 dans un dossier de solution entraîne une *NullReferenceException* erreur. La solution de contournement consiste à créer le projet ASP.NET MVC 3 à la racine de la solution, puis le déplacer dans le dossier de solution.
- Le programme d’installation peut prendre beaucoup plus de temps que les versions antérieures d’ASP.NET MVC pour terminer. Il s’agit, car elle met à jour des composants de Visual Studio 2010.
- IntelliSense pour la syntaxe Razor ne fonctionne pas lorsque ReSharper est installé. Si vous ReSharper est installé et que vous souhaitez tirer parti de la prise en charge de Razor IntelliSense dans ASP.NET MVC 3 RC2, consultez l’entrée [Razor Intellisense and ReSharper](http://blogs.jetbrains.com/dotnet/2010/11/razor-intellisense-and-resharper/) sur le blog de hadi, qui explique comment les utiliser aujourd'hui ensemble.
- Les vues CSHTML et VBHTML créés avec la version bêta d’ASP.NET MVC 3 n’ont pas leur action de génération définie correctement, ce qui permet d’afficher ces types sont omis lorsque le projet est publié. Le *Action de génération* valeur pour ces fichiers doivent être définies pour le contenu ». ASP.NET MVC 3 RC2 résout ce problème pour les nouveaux fichiers, mais ne corrige pas le paramètre pour les fichiers existants pour un projet créé avec la version bêta.![](mvc3-release-notes/_static/image4.png)
- Pendant l’installation, la boîte de dialogue d’acceptation CLUF affiche les termes du contrat de licence dans une fenêtre plus petite que prévue.
- Lorsque vous modifiez une vue Razor (fichier .cshtml), l’élément de menu contrôleur Go dans Visual Studio ne sera pas disponible, et il en existe aucun extrait de code.
- Si vous installez ASP.NET MVC 3 pour Visual Web Developer Express sur un ordinateur où Visual Studio n’est pas installé, installez Visual Studio ultérieurement, vous devez réinstaller ASP.NET MVC 3. Visual Studio et Visual Web Developer Express partagent des composants qui sont mis à niveau par le programme d’installation d’ASP.NET MVC 3. Le même problème s’applique si vous installez ASP.NET MVC 3 pour Visual Studio sur un ordinateur qui n’ont pas Visual Web Developer Express et installer ultérieurement Visual Web Developer Express.
- L’installation d’ASP.NET MVC 3 RC 2 n’est pas actualisée NuGet si vous avez déjà installé. Pour mettre à niveau NuGet, accédez au Gestionnaire de Extension Visual Studio et il doit s’afficher en tant qu’une mise à jour disponible. Vous pouvez mettre à niveau NuGet vers la dernière version à partir de là.

<a id="TOC_ASP_NET_3_RC"></a>
## <a name="aspnet-mvc-3-release-candidate"></a>ASP.NET MVC 3 version Release Candidate

ASP.NET MVC version finale a été publiée le 9 novembre 2010.

<a id="_Toc276711785"></a>
## <a name="new-features-in-aspnet-mvc-3-rc"></a>Nouvelles fonctionnalités dans ASP.NET MVC 3 RC

Cette section décrit les fonctionnalités qui ont été introduites dans la version d’ASP.NET MVC 3 RC depuis la version bêta.

<a id="_Toc276711786"></a>
### <a name="nuget-package-manager"></a>Gestionnaire de package NuGet

ASP.NET MVC 3 inclut le Gestionnaire de Package NuGet (anciennement NuPack), qui est un outil de gestion de package intégré pour ajouter des bibliothèques et des outils pour les projets Visual Studio. Cet outil automatise les étapes que les développeurs utiliser dès aujourd'hui pour obtenir une bibliothèque dans l’arborescence de leur source.

Vous pouvez travailler avec NuGet comme un outil de ligne de commande, comme une fenêtre de console intégrée à l’intérieur de Visual Studio 2010, dans le menu contextuel de Visual Studio et comme un ensemble d’applets de commande PowerShell.

Pour plus d’informations sur NuGet, consultez le [Documentation de Nuget](https://docs.microsoft.com/nuget/).

<a id="_Toc276711787"></a>
### <a name="improved-new-project-dialog-box"></a>Amélioration de « Nouveau projet » boîte de dialogue

Lorsque vous créez un nouveau projet, la boîte de dialogue Nouveau projet vous permet désormais de spécifier le moteur d’affichage, ainsi qu’un type de projet ASP.NET MVC.

![](mvc3-release-notes/_static/image5.png)

Prise en charge de la modification de la liste de modèles et de vue moteurs répertoriée dans la boîte de dialogue est inclus dans cette version.

Les modèles par défaut sont les suivants :

Vide. Contient un ensemble minimal de fichiers pour un projet ASP.NET MVC, y compris la structure de répertoires par défaut pour les projets ASP.NET MVC, un fichier Site.css qui contient la valeur par défaut, QU'ASP.NET MVC styles et un répertoire de Scripts qui contient les fichiers JavaScript.

Application Internet. Contient des fonctionnalités d’exemple qui montre comment utiliser le fournisseur d’appartenances avec ASP.NET MVC.

La liste des modèles de projet qui s’affiche dans la boîte de dialogue est spécifiée dans le Registre Windows.

<a id="_Toc276711788"></a>
### <a name="sessionless-controllers"></a>Contrôleurs sessionless

La nouvelle *ControllerSessionStateAttribute* vous donne davantage de contrôle sur le comportement de l’état de session pour les contrôleurs en spécifiant un [System.Web.SessionState.SessionStateBehavior](https://msdn.microsoft.com/library/system.web.sessionstate.sessionstatebehavior.aspx) valeur d’énumération.

L’exemple suivant montre comment désactiver l’état de session pour toutes les demandes à un contrôleur.

[!code-csharp[Main](mvc3-release-notes/samples/sample13.cs)]

L’exemple suivant montre comment définir l’état de session en lecture seule pour toutes les demandes à un contrôleur.

[!code-csharp[Main](mvc3-release-notes/samples/sample14.cs)]

<a id="_Toc276711789"></a>
### <a name="new-validation-attributes"></a>Nouveaux attributs de Validation

#### <a name="compareattribute"></a>CompareAttribute

La nouvelle *CompareAttribute* attribut de validation vous permet de comparer les valeurs des deux propriétés différentes d’un modèle. Dans l’exemple suivant, la *ComparePassword* propriété doit correspondre à la *mot de passe* champ pour être valides.

[!code-csharp[Main](mvc3-release-notes/samples/sample15.cs)]

#### <a name="remoteattribute"></a>RemoteAttribute

La nouvelle *RemoteAttribute* attribut de validation tire parti de validateur à distance jQuery Validation du plug-in, ce qui active la validation côté client appeler une méthode sur le serveur qui exécute la logique de validation réelle.

Dans l’exemple suivant, la *nom d’utilisateur* la propriété a la *RemoteAttribute* appliqué. Lorsque vous modifiez cette propriété dans une vue d’édition, la validation client appelle une action nommée *UserNameAvailable* sur la *UsersController* classe afin de valider ce champ.

[!code-csharp[Main](mvc3-release-notes/samples/sample16.cs)]

L’exemple suivant montre le contrôleur correspondant.

[!code-csharp[Main](mvc3-release-notes/samples/sample17.cs)]

Par défaut, le nom de propriété, l’attribut est appliqué est envoyé à la méthode d’action comme paramètre de chaîne de requête.

<a id="_Toc276711790"></a>
### <a name="new-overloads-for-labelfor-and-labelformodel-methods"></a>Nouvelles surcharges pour « LabelFor » et « LabelForModel » méthodes

Nouvelles surcharges ont été ajoutés pour la *LabelFor* et *LabelForModel* les méthodes qui vous permettent de spécifient le texte d’étiquette. L’exemple suivant montre comment utiliser ces surcharges.

[!code-cshtml[Main](mvc3-release-notes/samples/sample18.cshtml)]

<a id="_Toc276711791"></a>
### <a name="child-action-output-caching"></a>Action enfant mise en cache de sortie

Le *OutputCacheAttribute* prend en charge la sortie mise en cache des actions enfants qui sont appelées à l’aide de la *Html.RenderAction* ou *Html.Action* méthodes d’assistance. L’exemple suivant montre une vue qui appelle une autre action.

[!code-cshtml[Main](mvc3-release-notes/samples/sample19.cshtml)]

Le *GetDate* action est annotée avec le *OutputCacheAttribute*:

[!code-csharp[Main](mvc3-release-notes/samples/sample20.cs)]

Lorsque ce code s’exécute, le résultat de l’appel à Html.Action("GetDate") est mis en cache pendant 100 secondes.

<a id="_Toc276711792"></a>
### <a name="add-view-dialog-box-improvements"></a>Améliorations de boîte de dialogue « Ajouter une vue »

Lorsque vous ajoutez une vue fortement typée, la boîte de dialogue Ajouter une vue filtre désormais d’autres types non applicable que dans les versions précédentes, telles que de nombreux types du .NET Framework core. En outre, la liste est maintenant triée par nom de la classe et non par le nom de type qualifié complet, ce qui le rend plus facile à trouver des types. Par exemple, le nom de type est maintenant affiché comme dans l’exemple suivant :

ClassName (espace de noms)

Dans les versions antérieures, cela aurait affiché comme suit :

Namespace.ClassName

<a id="_Toc276711793"></a>
### <a name="granular-request-validation"></a>Validation de la demande granulaire

Le *exclure* propriété du *ValidateInputAttribute* n’existe plus. Au lieu de cela, pour que la validation de la demande ignorée pour les propriétés spécifiques d’un modèle lors de la liaison de modèle, utilisez la nouvelle *SkipRequestValidationAttribute*.

Par exemple, qu'une méthode d’action permet de modifier un billet de blog :

[!code-csharp[Main](mvc3-release-notes/samples/sample21.cs)]

L’exemple suivant montre le modèle d’affichage d’un billet de blog.

[!code-csharp[Main](mvc3-release-notes/samples/sample22.cs)]

Lorsqu’un utilisateur soumet des balises pour la propriété de Description, liaison de modèle échoue en raison de la validation de la demande. Pour désactiver la validation de la demande lors de la liaison de modèle pour la Description du billet de blog, appliquer la *SkipRequpestValidationAttribute* à la propriété, comme illustré dans cet exemple :.

[!code-csharp[Main](mvc3-release-notes/samples/sample23.cs)]

Vous pouvez également appliquer pour désactiver la validation de la demande pour chaque propriété du modèle, *ValidateInputAttribute* avec la valeur *false* à la méthode d’action :

[!code-csharp[Main](mvc3-release-notes/samples/sample24.cs)]

<a id="_Toc276711794"></a>
## <a name="breaking-changes"></a>Modifications avec rupture

- L’ordre d’exécution pour les filtres d’exception a été modifiée pour les filtres d’exception qui ont le même *commande* valeur. Dans ASP.NET MVC 2 et versions antérieures, les filtres d’exception sur le contrôleur ayant le même *commande* comme ceux qui sont sur une méthode d’action étaient exécutés avant les filtres d’exception sur la méthode d’action. Cela serait généralement le cas lorsque les filtres d’exception étaient appliqués sans spécifier *commande* valeur. Dans ASP.NET MVC 3, cette commande a été inversée afin que le Gestionnaire d’exceptions plus spécifique s’exécute en premier. Comme dans les versions antérieures, si la *commande* propriété est explicitement spécifiée, les filtres sont exécutés dans l’ordre spécifié.
- Ajouter une nouvelle propriété nommée *FileExtensions* à la *VirtualPathProviderViewEngine* classe de base. Lorsque vous recherchez une vue par le chemin d’accès (et non pas par nom), seules les vues avec une extension de fichier contenues dans la liste spécifiée par cette nouvelle propriété est considérée comme. Ceci est une modification avec rupture pour les personnes inscrites personnalisé fournisseur pour activer une extension de fichier personnalisés pour les modes de formulaire web de build et et font référence à ces vues à l’aide d’un chemin d’accès complet, plutôt qu’un nom. La solution de contournement consiste à modifier la valeur de la *FileExtensions* propriété à inclure l’extension de fichier personnalisé.

<a id="_Toc276711795"></a>
## <a name="known-issues"></a>Problèmes connus

- Le programme d’installation peut prendre beaucoup plus de temps que les versions antérieures d’ASP.NET MVC pour terminer, car elle met à jour des composants de Visual Studio 2010.
- L’ajout de vue génération de modèles automatique lors de la sélection astrongly typée afficher squelettes les propriétés en écriture seule. Ceux-ci doivent toujours être ignorées par la génération de modèles automatique. La boîte de dialogue Ajouter une vue micro-capsules également des propriétés en lecture seule lors de la génération d’une vue « Modifier » ou « Créer ». Propriétés en lecture seule doivent uniquement être structurées pour les affichages de liste et d’affichage.
- Le débogage ne fonctionne pas lorsque ASP.NET MVC 3 est installé en même temps que le Async CTP. ASP.NET MVC 3 ne peut pas être installé côte à côte avec le CTP de Async. Désinstallez le Async CTP pour réparer le débogage. Pour plus d’informations, consultez [ce billet de blog](http://drew-prog.blogspot.com/2010/11/how-to-uninstall-microsoft-aspnet-mvc-3.html) sur la désinstallation de tous les éléments d’ASP.NET MVC 3 RC.
- Razor Intellisense ne fonctionne pas lorsque Resharper est installé. Si vous ReSharper est installé et que vous souhaitez tirer parti de la prise en charge de Razor intellisense dans ASP.NET MVC 3 RC, veuillez lire [ce billet de blog](http://blogs.jetbrains.com/dotnet/2010/11/razor-intellisense-and-resharper/) de JetBrains qui explique les moyens d’utiliser ensemble aujourd'hui.
- CSHTML et VBHTML vues créées avec la version bêta d’ASP.NET MVC 3 n’ont pas leur action de génération correctement qui omet les à partir de la publication. Le *Action de génération* pour ces fichiers doivent être définies sur « Contenu ». ASP.NET MVC 3 RC résout ce problème pour les nouveaux fichiers, mais ne corrige pas le paramètre pour les fichiers existants pour un projet créé avec la version bêta.
- Le programme d’installation peut prendre beaucoup plus de temps que les versions antérieures d’ASP.NET MVC pour terminer, car elle met à jour des composants de Visual Studio 2010.
- L’ajouter une vue génération de modèles automatique lorsqu’en sélectionnant un « Edit » fortement typée vue squelettes propriétés en lecture seule. De même, les propriétés en écriture seule sont structurées pour les vues « Affichage ».
- Pendant l’installation, la boîte de dialogue d’acceptation CLUF affiche les termes du contrat de licence dans une fenêtre plus petite que prévue.
- L’installation de le [Visual Studio Async CTP](https://www.microsoft.com/downloads/en/details.aspx?FamilyID=18712f38-fcd2-4e9f-9028-8373dc5732b2&amp;displaylang=en) provoque un conflit avec la mise à jour Razor est inclus dans le cadre de l’installation des outils ASP.NET MVC 3. Assurez-vous que vous n’essayez pas d’installer le Visual Studio Async CTP et la version de Razor sur le même ordinateur.
- Lorsque vous modifiez une vue Razor (fichier .cshtml), l’élément de menu contrôleur Go dans Visual Studio ne sera pas disponible, et il en existe aucun extrait de code.

<a id="TOC_ASP_NET_3_Beta"></a>
## <a name="aspnet-mvc-3-beta"></a>Version bêta d’ASP.NET MVC 3

Version bêta d’ASP.NET MVC 3 a été publiée le 6 octobre 2010. Les remarques suivantes sont spécifiques à la version bêta et sont soumis à toutes les mises à jour ou modifications référencées dans la section d’ASP.NET MVC 3 Release Candidate ci-dessus.

## <a id="0.1__Toc274034215"></a>Nouveau bêta d’ASP.NET MVC 3 Featuresin

<a id="0.1__Default_validation_system"></a>Cette section décrit les fonctionnalités qui ont été introduites dans la version de la version bêta d’ASP.NET MVC 3.

### <a id="0.1__Toc274034216"></a>Gestionnaire de Package NuGet

ASP.NET MVC 3 inclut le Gestionnaire de Package NuGet, qui est un outil de gestion intégrée de package pour ajouter les bibliothèques et outils pour les projets Visual Studio. Dans la plupart des cas, il automatise les étapes que les développeurs prennent aujourd'hui pour obtenir une bibliothèque dans leur arborescence source.

Vous pouvez travailler avec NuGet comme un outil de ligne de commande, comme une fenêtre de console intégrée à l’intérieur de Visual Studio 2010, dans le menu contextuel de Visual Studio et en tant que jeu d’applets de commande PowerShell.

Pour plus d’informations sur NuGet, consultez le [Documentation de NuGet](https://docs.microsoft.com/nuget/).

### <a id="0.1__Toc274034217"></a>Boîte de dialogue Nouveau projet améliorée

Lorsque vous créez un nouveau projet, la boîte de dialogue Nouveau projet vous permet désormais de spécifier le moteur d’affichage, ainsi qu’un type de projet ASP.NET MVC.

![](mvc3-release-notes/_static/image6.png)

Prise en charge de la modification de la liste de modèles et de vue moteurs répertoriée dans la boîte de dialogue n’est pas inclus dans cette version.

Les modèles par défaut sont les suivants :

Vide. Contient un ensemble minimal de fichiers pour un projet ASP.NET MVC, y compris la structure de répertoires par défaut pour les projets ASP.NET MVC, un petit fichier Site.css qui contient la valeur par défaut, QU'ASP.NET MVC styles et un répertoire de Scripts qui contient les fichiers JavaScript.

Application Internet. Contient des fonctionnalités d’exemple qui montre comment utiliser le fournisseur d’appartenances dans ASP.NET MVC.

### <a id="0.1__Toc274034218"></a>Un moyen simple pour spécifier fortement typée modèles dans les vues Razor

La façon de spécifier le type de modèle pour les vues Razor fortement typées a été simplifiée à l’aide de la nouvelle @model directive pour les vues CSHTML et @ModelType directive pour les vues VBHTML. Dans les versions antérieures d’ASP.NET MVC, vous devez spécifier qu'un modèle fortement typé pour Razor vues de cette façon :

[!code-cshtml[Main](mvc3-release-notes/samples/sample25.cshtml)]

Dans cette version, vous pouvez utiliser la syntaxe suivante :

[!code-cshtml[Main](mvc3-release-notes/samples/sample26.cshtml)]

### <a id="0.1__Toc274034219"></a>Prise en charge de nouvelles méthodes d’assistance de Pages Web ASP.NET

La nouvelle technologie de Pages Web ASP.NET inclut un ensemble de méthodes d’assistance qui sont utiles pour ajouter des fonctionnalités couramment utilisées pour les vues et les contrôleurs. ASP.NET MVC 3 prend en charge à l’aide de ces méthodes d’assistance au sein des contrôleurs et vues (le cas échéant). Ces méthodes sont contenus dans l’assembly System.Web.Helpers. Le tableau suivant répertorie quelques exemples de méthodes d’assistance ASP.NET Web Pages.

| **Application d’assistance** | **Description** |
| --- | --- |
| Graphique | Affiche un graphique dans une vue. Contient des méthodes telles que Chart.ToWebImage, Chart.Save et Chart.Write. |
| Chiffrement | Utilise le hachage d’algorithmes pour créer correctement salé et hacher les mots de passe. |
| WebGrid | Affiche une collection d’objets (en règle générale, les données à partir d’une base de données) sous la forme d’une grille. Prend en charge la pagination et le tri. |
| WebImage | Restitue une image. |
| Messagerie Web | Envoie un message électronique. |

Une rubrique de référence rapide qui répertorie les programmes d’assistance et la syntaxe de base est disponible en tant que partie de la documentation de la syntaxe ASP.NET Razor à l’URL suivante :

[https://www.asp.net/webmatrix/tutorials/asp-net-web-pages-api-reference](../web-pages/overview/api-reference/asp-net-web-pages-api-reference.md)

### <a id="0.1__Toc274034220"></a>Prise en charge d’Injection de dépendance supplémentaire

S’appuyant sur la version d’ASP.NET MVC 3 Preview 1, la version actuelle inclut la prise en charge supplémentaire pour les deux nouveaux services et quatre services existants et la prise en charge améliorée pour la résolution de dépendance et Common Service Locator.

#### <a name="new-icontrolleractivator-interface-for-fine-grained-controller-instantiation"></a>Nouvelle Interface IControllerActivator lors de l’instanciation du contrôleur de granularité fine

La nouvelle interface IControllerActivator fournit un contrôle plus affiné sur la façon dont les contrôleurs sont instanciés par injection de dépendances. L’exemple suivant montre l’interface :

[!code-csharp[Main](mvc3-release-notes/samples/sample27.cs)]

Comparez cela au rôle de la fabrique de contrôleurs. Une fabrique de contrôleur est une implémentation de l’interface IControllerFactory, qui est chargé à la fois pour la localisation d’un type de contrôleur et d’instanciation d’une instance de ce type de contrôleur.

ACTIVATEURS sont uniquement responsables de l’instanciation d’une instance d’un type de contrôleur. Ne pas effectuer la recherche du type de contrôleur. Après avoir localisé d’un type de contrôleur approprié, les fabriques de contrôleurs doivent déléguer à une instance de IControllerActivator pour gérer l’instanciation réelle du contrôleur.

La classe DefaultControllerFactory a un nouveau constructeur qui accepte une instance de IControllerFactory. Cela vous permet d’appliquer l’Injection de dépendances pour gérer cet aspect de création du contrôleur sans avoir à remplacer le comportement de recherche de type de contrôleur par défaut.

#### <a name="iservicelocator-interface-replaced-with-idependencyresolver"></a>Interface de IServiceLocator remplacé par IDependencyResolver

En fonction de commentaires de la Communauté, la version de la version bêta d’ASP.NET MVC 3 a remplacé l’utilisation de l’interface IServiceLocator avec une interface de IDependencyResolver épurée spécifique aux besoins d’ASP.NET MVC. L’exemple suivant montre la nouvelle interface :

[!code-csharp[Main](mvc3-release-notes/samples/sample28.cs)]

Dans le cadre de cette modification, la classe ServiceLocator a été également remplacée par la classe DependencyResolver. L’inscription d’un résolveur de dépendance est similaire aux versions antérieures d’ASP.NET MVC :

[!code-csharp[Main](mvc3-release-notes/samples/sample29.cs)]

Les implémentations de cette interface doivent déléguer simplement pour le conteneur d’injection de dépendance sous-jacente pour fournir le service inscrit pour le type demandé.

Lorsqu’il n’y a aucun service inscrit du type demandé, ASP.NET MVC attend des implémentations de cette interface pour retourner une valeur null à partir de la méthode GetService et pour retourner une collection vide de GetServices.

La nouvelle classe DependencyResolver vous permet d’enregistrer des classes qui implémentent l’interface Common Service Locator (IServiceLocator) ou la nouvelle interface IDependencyResolver. Pour plus d’informations sur Common Service Locator, consultez [CommonServiceLocator sur GitHub](https://github.com/unitycontainer/commonservicelocator).

<a id="0.1__Breaking_Changes"></a>

#### <a name="new-iviewactivator-interface-for-fine-grained-view-page-instantiation"></a>Nouvelle Interface IViewActivator lors de l’instanciation de Page de vue affinées

La nouvelle interface IViewPageActivator fournit un contrôle plus affiné sur la manière dont les pages de vue sont instanciés via l’injection de dépendances. Cela s’applique aux instances de WebFormView et RazorView instances. L’exemple suivant montre la nouvelle interface :

[!code-csharp[Main](mvc3-release-notes/samples/sample30.cs)]

Ces classes désormais acceptent un argument de constructeur IViewPageActivator, qui vous permet de vous utilisez l’injection de dépendances pour contrôler la manière dont les types ViewPage, ViewUserControl et WebViewPage sont instanciés.

#### <a name="new-dependency-resolver-support-for-existing-services"></a>Nouvelle prise en charge d’outil de résolution des dépendances pour les Services existants

La nouvelle version inclut la prise en charge la résolution de dépendances pour les services suivants :

- Fournisseurs de validation de modèle. Les classes qui implémentent ModelValidatorProvider peuvent être inscrits dans le résolveur de dépendance et le système de les utiliser pour prendre en charge la validation côté client et côté serveur.
- Fournisseur de métadonnées de modèle. Une seule classe qui implémente ModelMetadataProvider peut être inscrits dans le résolveur de dépendance et le système utilisera pour fournir des métadonnées pour les systèmes de création de modèles et de validation.
- Fournisseurs de valeurs. Les classes qui implémentent ValueProviderFactory peuvent être inscrits dans le résolveur de dépendance et le système de les utiliser pour créer des fournisseurs de valeurs qui sont consommés par le contrôleur et lors de la liaison de modèle.
- Classeurs de modèles. Les classes qui implémentent IModelBinderProvider peuvent être inscrits dans le résolveur de dépendance et le système de les utiliser pour créer des classeurs de modèles qui sont utilisés par le système de liaison de modèle.

### <a id="0.1__Toc274034221"></a>La prise en charge Ajax de base jQuery discrète

ASP.NET MVC inclut des méthodes d’assistance Ajax tels que les éléments suivants :

- Ajax.ActionLink
- Ajax.RouteLink
- Ajax.BeginForm
- Ajax.BeginRouteForm

Ces méthodes utilisent JavaScript pour appeler une méthode d’action sur le serveur au lieu d’utiliser une publication (postback) complète. Cette fonctionnalité a été mis à jour pour tirer parti de jQuery de manière discrète. Au lieu d’intrusive générant des scripts de clients en ligne, ces méthodes d’assistance séparent le comportement à partir du balisage en émettant des attributs HTML5 à l’aide de la *données ajax* préfixe. Comportement est ensuite appliqué à la balise en référençant les fichiers JavaScript appropriés. Assurez-vous que les fichiers JavaScript suivants sont référencés :

- jquery-1.4.1.js
- jquery.unobtrusive.ajax.js

Cette fonctionnalité est activée par défaut dans le fichier Web.config dans les modèles de projet ASP.NET MVC 3 nouveaux, mais est désactivée par défaut pour les projets existants. Pour plus d’informations, consultez [ajouté des indicateurs de l’application pour la validation côté client et du JavaScript discret](#0.1_AddedApplicationWideFlagsForClientValida) plus loin dans ce document.

### <a id="0.1__Toc274034222"></a>Prise en charge de jQuery non obstructive Validation

Par défaut, la version bêta d’ASP.NET MVC 3 utilise jQuery validation de manière discrète pour effectuer la validation côté client. Pour activer la validation client non obstructive, effectuer un appel à ce qui suit dans une vue à partir de :

[!code-csharp[Main](mvc3-release-notes/samples/sample31.cs)]

Cela nécessite que ViewContext.UnobtrusiveJavaScriptEnabled propriété est définie sur true, ce que vous pouvez faire en effectuant l’appel suivant :

[!code-csharp[Main](mvc3-release-notes/samples/sample32.cs)]

Assurez-vous également que les fichiers JavaScript suivants sont référencés.

- jquery-1.4.1.js
- jquery.validate.js
- jquery.validate.unobtrusive.js

Cette fonctionnalité est activée sur par défaut dans le fichier Web.config dans les modèles de projet ASP.NET MVC 3 nouveaux, mais est désactivée par défaut pour les projets existants. Pour plus d’informations, consultez [nouveaux indicateurs de l’application pour la validation côté client et du JavaScript discret](#0.1_AddedApplicationWideFlagsForClientValida) plus loin dans ce document.

<a id="0.1__Toc274034223"></a>

### <a id="0.1_AddedApplicationWideFlagsForClientValida"></a>Nouveaux indicateurs de l’Application pour la validation côté Client et du JavaScript discret

Vous pouvez activer ou désactiver la validation côté client et du JavaScript discret globalement à l’aide des membres statiques de la classe HtmlHelper, comme dans l’exemple suivant :

[!code-csharp[Main](mvc3-release-notes/samples/sample33.cs)]

Les modèles de projet par défaut activent JavaScript non obstructif par défaut. Vous pouvez également activer ou désactiver ces fonctionnalités dans le fichier Web.config racine de votre application en utilisant les paramètres suivants :

[!code-xml[Main](mvc3-release-notes/samples/sample34.xml)]

Étant donné que vous pouvez activer ces fonctionnalités par défaut, les nouvelles surcharges ont été introduites dans la classe HtmlHelper qui vous permettre de remplacer les paramètres par défaut, comme indiqué dans les exemples suivants :

[!code-csharp[Main](mvc3-release-notes/samples/sample35.cs)]

Pour la compatibilité descendante, ces deux fonctionnalités sont désactivées par défaut.

### <a id="0.1__Toc274034224"></a>Nouvelle prise en charge pour le Code qui s’exécute avant l’exécution de vues

Vous pouvez à présent placer un fichier nommé \_viewstart.cshtml (ou \_viewstart.vbhtml) dans le répertoire Views et ajouter du code qui est partagé entre plusieurs vues dans ce répertoire et ses sous-répertoires. Par exemple, vous pouvez placer le code suivant dans le \_page viewstart.cshtml dans le dossier ~/Views :

[!code-cshtml[Main](mvc3-release-notes/samples/sample36.cshtml)]

Cela définit la page de disposition pour chaque vue dans le dossier Views et tous ses sous-dossiers de manière récursive. Lorsqu’une vue est rendu, le code dans le \_viewstart.cshtml fichier s’exécute avant que le code de la vue s’exécute. Le \_viewstart.cshtml code s’applique à chaque vue dans ce dossier.

Par défaut, le code dans le \_viewstart.cshtml fichier s’applique également aux vues dans les sous-dossiers. Toutefois, les sous-dossiers individuels peuvent avoir leur propre version de la \_viewstart.cshtml fichier ; dans ce cas, la version locale est prioritaire. Par exemple, pour exécuter le code commun à toutes les vues pour le fichier HomeController, placez un \_fichier viewstart.cshtml dans le dossier ~/Views/Home.

### <a id="0.1__Toc274034225"></a>Nouvelle prise en charge pour la syntaxe Razor VBHTML

L’aperçu d’ASP.NET MVC précédente est pris en charge pour les vues à l’aide de la syntaxe Razor basée sur c#. Ces vues utilisent l’extension de fichier .cshtml. Dans le cadre d’un travail en cours pour prendre en charge de Razor, la version bêta d’ASP.NET MVC 3 introduit la prise en charge de la syntaxe Razor dans Visual Basic, qui utilise l’extension de fichier .vbhtml.

Pour une introduction à l’aide de la syntaxe Visual Basic dans les pages VBHTML, consultez le didacticiel à l’URL suivante :

[https://www.asp.net/webmatrix/tutorials/asp-net-web-pages-visual-basic](../web-pages/overview/getting-started/introducing-razor-syntax-vb.md)

### <a id="0.1__Toc274034226"></a>Contrôle plus granulaire sur ValidateInputAttribute

ASP.NET MVC est toujours inclus la classe ValidateInputAttribute, qui appelle l’infrastructure de validation de demande ASP.NET core pour vous assurer que la demande entrante ne contient pas d’entrées potentiellement malveillantes. Par défaut, la validation d’entrée est activée. Il est possible de désactiver la validation de la demande à l’aide de l’attribut ValidateInputAttribute, comme dans l’exemple suivant :

[!code-csharp[Main](mvc3-release-notes/samples/sample37.cs)]

Toutefois, de nombreuses applications web ont des champs de formulaire individuels qui doivent autoriser l’HTML, tandis que les champs restants ne doivent pas. La classe ValidateInputAttribute permet désormais de spécifier une liste de champs qui ne doivent pas être inclus dans la validation de la demande.

Par exemple, si vous développez un moteur de blog, vous souhaiterez autoriser le balisage dans les champs de résumé et de corps. Ces champs peuvent être représentées par deux éléments d’entrée, chacun avec un attribut de nom correspondant au nom de propriété (« Body » et « Résumé »). Pour désactiver la demande de validation pour ces champs, spécifiez les noms (séparées par des virgules) dans la propriété de l’exclusion de la classe ValidateInput, comme dans l’exemple suivant :

[!code-csharp[Main](mvc3-release-notes/samples/sample38.cs)]

### <a id="0.1__Toc274034227"></a>Programmes d’assistance pour convertir des traits de soulignement des traits d’union pour les noms d’attribut HTML spécifiés à l’aide d’objets anonymes

Méthodes d’assistance vous permettent de spécifier les paires nom/valeur d’attribut à l’aide d’un objet anonyme, comme dans l’exemple suivant :

[!code-csharp[Main](mvc3-release-notes/samples/sample39.cs)]

Cette approche ne vous permettre d’utiliser des traits d’union dans le nom d’attribut, car un trait d’union ne peut pas être utilisé pour un nom de propriété dans ASP.NET. Toutefois, des traits d’union sont importants pour les attributs HTML5 personnalisés ; par exemple, HTML5 utilise le préfixe « données- ».

En même temps, des traits de soulignement ne peut pas être utilisés pour les noms d’attribut dans le code HTML, mais sont valides dans les noms de propriété. Par conséquent, si vous spécifiez des attributs à l’aide d’un objet anonyme, et si les noms d’attribut incluent le trait de soulignement, des méthodes d’assistance convertira les traits de soulignement des traits d’union. Par exemple, la syntaxe d’assistance suivante utilise un trait de soulignement :

[!code-csharp[Main](mvc3-release-notes/samples/sample40.cs)]

L’exemple précédent restitue le balisage suivant lors de l’exécution de l’application d’assistance :

[!code-html[Main](mvc3-release-notes/samples/sample41.html)]

## <a id="0.1__Toc274034228"></a>Correctifs de bogues

Le modèle d’objet par défaut pour les programmes d’assistance de modèle EditorFor et DisplayFor prend désormais en charge le classement spécifié dans la propriété DisplayAttribute.Order. (Dans les versions précédentes, le paramètre de commande a été pas utilisé.)

Validation du client maintenant en charge la validation de propriétés remplacées qui ont des attributs de validation appliquées.

JsonValueProviderFactory est maintenant inscrit par défaut.

## <a id="0.1__Toc274034229"></a>Modifications avec rupture

L’ordre d’exécution pour les filtres d’exception a changé pour les filtres d’exception qui ont la même valeur d’ordre. Dans ASP.NET MVC 2 et versions antérieures, filtres d’exception sur le contrôleur avec le même ordre que ceux qui sont sur une méthode d’action étaient exécutés avant les filtres d’exception sur la méthode d’action. Cela serait généralement le cas lorsque les filtres d’exceptions ont été appliquées sans une valeur d’ordre spécifiée. Dans ASP.NET MVC 3, cette commande a été inversée afin que le Gestionnaire d’exceptions plus spécifique s’exécute en premier. Comme dans les versions antérieures, si la propriété d’ordre est spécifiée explicitement, les filtres sont exécutés dans l’ordre spécifié.

## <a id="0.1__Toc274034230"></a>Problèmes connus

Pendant l’installation, la boîte de dialogue d’acceptation CLUF affiche les termes du contrat de licence dans une fenêtre plus petite que prévue.

Vues Razor n’ont pas de prise en charge IntelliSense, ni la mise en surbrillance de syntaxe. Il est prévu que la prise en charge de la syntaxe Razor dans Visual Studio sera inclus dans le cadre d’une version ultérieure.

Lorsque vous modifiez une vue Razor (fichier CSHTML), le <a id="0.1__Toc224729061"> </a> <a id="0.1__Toc238051347"> </a> élément de menu de contrôleur Go dans Visual Studio ne sera pas disponible, et il en existe aucun extrait de code.

Lorsque vous utilisez la @model permet d’afficher la syntaxe pour spécifier un CSHTML fortement typée, spécifique à la langue des raccourcis pour les types ne sont pas reconnues. Par exemple, @model int ne fonctionnera pas, mais @model Int32 fonctionnera. La solution de contournement pour ce bogue est d’utiliser le nom de type réel lorsque vous spécifiez le type de modèle.

Lorsque vous utilisez la @model syntaxe permettant de spécifier une vue fortement typée de CSHTML (ou @ModelType pour spécifier une vue fortement typée de VBHTML), les types nullable et les déclarations de tableau ne sont pas pris en charge. Par exemple, @model int ? n’est pas pris en charge. Au lieu de cela, utilisez @model Nullable&lt;Int32&gt;. La syntaxe @model string [] est également pas prise en charge ; utilisez plutôt @model IList&lt;chaîne&gt;.

Lorsque vous mettez à niveau un projet ASP.NET MVC 2 vers ASP.NET MVC 3, veillez à ajoutez le code suivant à la section appSettings du fichier Web.config :

[!code-xml[Main](mvc3-release-notes/samples/sample42.xml)]

Il existe un problème connu qui provoque l’authentification par formulaire pour toujours rediriger les utilisateurs non authentifiés à ~/Account/connexion, en ignorant le paramètre d’authentification forms utilisé dans le fichier Web.config. La solution consiste à ajouter le paramètre d’application.

[!code-xml[Main](mvc3-release-notes/samples/sample43.xml)]

## <a id="0.1__Toc274034231"></a>Exclusion de responsabilité

© 2011 Microsoft Corporation. Tous droits réservés. Ce document est fourni « en tant que-est. » Informations et opinions exprimées dans ce document, y compris les URL et autres références à des sites Web Internet, peuvent changer sans préavis. Vous assumez tous les risques liés à leur utilisation.

Ce document ne vous donne aucun droit légal de propriété intellectuelle quant aux produits Microsoft. Vous pouvez copier et utiliser ce document à titre de référence interne.
