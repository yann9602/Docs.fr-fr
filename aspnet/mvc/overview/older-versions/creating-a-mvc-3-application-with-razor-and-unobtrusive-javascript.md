---
uid: mvc/overview/older-versions/creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript
title: "Création d’un MVC 3 Application avec Razor et JavaScript non Obstructif | Documents Microsoft"
author: microsoft
description: "L’exemple d’application web liste utilisateur montre combien il est simple de créer des applications ASP.NET MVC 3 à l’aide du moteur d’affichage Razor. Exemple application s..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 11/01/2010
ms.topic: article
ms.assetid: 658b149b-d770-46bf-8b4b-4e47cca242f3
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript
msc.type: authoredcontent
ms.openlocfilehash: 29b45c07b5498542abbf22c4c3001b1cee41edc9
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript"></a>Création d’un MVC 3 Application avec Razor et JavaScript non Obstrusif
====================
par [Microsoft](https://github.com/microsoft)

> L’exemple d’application web liste utilisateur montre combien il est simple de créer des applications ASP.NET MVC 3 à l’aide du moteur d’affichage Razor. L’exemple d’application montre comment utiliser le nouveau moteur d’affichage Razor avec ASP.NET MVC version 3 et Visual Studio 2010 pour créer un site Web liste utilisateur fictif qui inclut des fonctionnalités telles que la création, affichage, modification et suppression d’utilisateurs.
> 
> Ce didacticiel décrit les étapes qui ont été prises pour générer la liste des utilisateurs exemple ASP.NET MVC 3 d’application. Un projet Visual Studio avec code source c# et VB est disponible pour accompagner cette rubrique : [télécharger](https://code.msdn.microsoft.com/aspnetmvcsamples/Release/ProjectReleases.aspx?ReleaseId=5114). Si vous avez des questions à propos de ce didacticiel, veuillez la publier à le [forum MVC](https://forums.asp.net/1146.aspx).


## <a name="overview"></a>Vue d'ensemble

L’application que vous créez est un site Web de liste simple de l’utilisateur. Les utilisateurs peuvent entrer, afficher et mettre à jour les informations utilisateur.

![Exemple de site](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image1.png)

Vous pouvez télécharger le projet terminé VB et c# [ici](https://code.msdn.microsoft.com/Creating-a-MVC-3-28883c0f).

## <a name="creating-the-web-application"></a>Création de l’Application Web

Pour démarrer le didacticiel, ouvrez Visual Studio 2010 et créer un projet à l’aide du *ASP.NET MVC 3 Web Application* modèle. Nom de l’application &quot;Mvc3Razor&quot;.

[![Nouveau projet MVC 3](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image3.png)](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image2.png)

Dans le **nouveau projet ASP.NET MVC 3** boîte de dialogue, sélectionnez **Application Internet**, sélectionnez le moteur d’affichage Razor, puis cliquez sur **OK**.

![Boîte de dialogue Nouveau projet ASP.NET MVC 3](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image4.png)

Dans ce didacticiel vous ne serez pas utiliser le fournisseur d’appartenances ASP.NET, vous pouvez supprimer tous les fichiers associés d’ouverture de session et l’appartenance. Dans **l’Explorateur de solutions**, supprimez les fichiers et répertoires suivants :

- *Controllers\AccountController*
- *Models\AccountModels*
- *Views\Shared\\_LogOnPartial*
- *Views\Account* (et tous les fichiers dans ce répertoire)

![Soln Exp](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image5.png)

Modifier la  *\_Layout.cshtml* de fichier et remplacez la balise à l’intérieur de la `<div>` élément nommé `logindisplay` avec le message  *&quot;* connexion désactivé&quot;. L’exemple suivant montre le balisage :

[!code-cshtml[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample1.cshtml)]

## <a name="adding-the-model"></a>Ajout du modèle

Dans **l’Explorateur de solutions**, avec le bouton droit le *modèles* dossier, sélectionnez **ajouter**, puis cliquez sur **classe**.

![Nouvelle classe d’utilisateur Mdl](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image6.png)

Nommez la classe `UserModel`. Remplacez le contenu de la *UserModel* fichier avec le code suivant :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample2.cs)]

La `UserModel` classe représente les utilisateurs. Chaque membre de la classe est annoté avec le [requis](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx) attribut à partir de la [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) espace de noms. Les attributs dans le [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) espace de noms fournissent la validation côté client et serveur automatique pour les applications web.

Ouvrir le `HomeController` et ajouter un `using` directive afin que vous pouvez accéder à la `UserModel` et `Users` classes :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample3.cs)]

Juste après le `HomeController` déclaration, ajoutez le commentaire suivant et la référence à un `Users` classe :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample4.cs)]

La `Users` classe est un magasin de données simplifiée en mémoire que vous utiliserez dans ce didacticiel. Dans une application réelle, vous utiliseriez une base de données pour stocker les informations de l’utilisateur. Les premières lignes de la `HomeController` fichier sont affichés dans l’exemple suivant :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample5.cs)]

Générez l’application afin que le modèle utilisateur seront disponible pour l’Assistant génération de modèles automatique à l’étape suivante.

## <a name="creating-the-default-view"></a>Création de la vue par défaut

L’étape suivante consiste à ajouter une méthode d’action et pour afficher les utilisateurs.

Supprimez les *Views\Home\Index* fichier. Vous allez créer un nouveau *Index* fichier pour afficher les utilisateurs.

Dans le `HomeController` de classe, remplacez le contenu de la `Index` méthode avec le code suivant :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample6.cs)]

Avec le bouton droit à l’intérieur de la `Index` (méthode), puis cliquez sur **ajouter une vue**.

![Ajouter une vue](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image7.png)

Sélectionnez le **créer une vue fortement typée** option. Pour **afficher la classe de données**, sélectionnez **Mvc3Razor.Models.UserModel**. (Si vous ne voyez pas **Mvc3Razor.Models.UserModel** dans les **afficher la classe de données** zone, vous devez générer le projet.) Assurez-vous que le moteur d’affichage est défini sur **Razor**. Définissez **afficher le contenu** à **liste** puis cliquez sur **ajouter**.

![Ajouter une vue d’Index](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image8.png)

La nouvelle vue micro-capsules automatiquement les données de l’utilisateur qui sont passées à la `Index` vue. Examinez généré récemment *Views\Home\Index* fichier. Le **créer un nouveau**, **modifier**, **détails**, et **supprimer** liens ne fonctionnent pas, mais le reste de la page est fonctionnel. Exécutez la page. Vous consultez une liste d’utilisateurs.

![Page d’index](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image9.png)

Ouvrez le *Index.cshtml* de fichiers et de remplacer le `ActionLink` balisage pour **modifier**, **détails**, et **supprimer** avec le code suivant :

[!code-cshtml[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample7.cshtml)]

Le nom d’utilisateur est utilisé comme ID pour rechercher l’enregistrement sélectionné dans le **modifier**, **détails**, et **supprimer** des liens.

## <a name="creating-the-details-view"></a>Création de la vue Détails

L’étape suivante consiste à ajouter un `Details` méthode d’action et d’affichage pour afficher les détails de l’utilisateur.

![Détails](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image10.png)

Ajoutez le code suivant `Details` méthode au contrôleur home :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample8.cs)]

Avec le bouton droit à l’intérieur de la `Details` (méthode), puis sélectionnez **ajouter une vue**. Vérifiez que le **afficher la classe de données** boîte contient **Mvc3Razor.Models.UserModel***.* Définissez **afficher le contenu** à **détails** puis cliquez sur **ajouter**.

![Ajouter la vue Détails](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image11.png)

Exécutez l’application et sélectionnez un lien de détails. La génération de modèles automatique montre chaque propriété dans le modèle.

![Détails](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image12.png)

## <a name="creating-the-edit-view"></a>Création de la vue de modification

Ajoutez le code suivant `Edit` méthode au contrôleur home.

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample9.cs)]

Ajouter une vue comme dans les étapes précédentes, mais définissez **afficher le contenu** à **modifier**.

![Ajouter une vue d’édition](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image13.png)

Exécutez l’application et modifier le nom et prénom de l’un des utilisateurs. Si vous ne respectez pas les `DataAnnotation` les contraintes qui ont été appliqués à la `UserModel` (classe), lorsque vous envoyez le formulaire, vous verrez des erreurs de validation qui sont produites par le code serveur. Par exemple, si vous modifiez le nom de la première &quot;Anne&quot; à &quot;A&quot;, lorsque vous envoyez le formulaire, l’erreur suivante s’affiche dans le formulaire :

`The field First Name must be a string with a minimum length of 3 and a maximum length of 8.`

Dans ce didacticiel, vous êtes en traitant le nom d’utilisateur comme clé primaire. Par conséquent, la propriété de nom d’utilisateur ne peut pas être modifiée. Dans le *Edit.cshtml* de fichiers, juste après la `Html.BeginForm` instruction, définissez le nom d’utilisateur à un champ masqué. Cela provoque la propriété devant être transmis dans le modèle. Le fragment de code suivant montre le placement de la `Hidden` instruction :

[!code-cshtml[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample10.cshtml)]

Remplacez le `TextBoxFor` et `ValidationMessageFor` un balisage pour le nom d’utilisateur avec un `DisplayFor` appeler. Le `DisplayFor` méthode affiche la propriété sous la forme d’un élément en lecture seule. L’exemple suivant montre le balisage complet. La version d’origine `TextBoxFor` et `ValidationMessageFor` appels sont mis en commentaire avec les caractères de commentaire begin et end-commentaire Razor (`@* *@`)

[!code-cshtml[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample11.cshtml)]

## <a name="enabling-client-side-validation"></a>L’activation de la Validation côté Client

Pour activer la validation côté client dans ASP.NET MVC 3, vous devez définir deux indicateurs et vous devez inclure les trois fichiers JavaScript.

Ouvrez l’application *Web.config* fichier. Vérifiez `that ClientValidationEnabled` et `UnobtrusiveJavaScriptEnabled` ont la valeur true dans les paramètres d’application. Le fragment suivant à partir de la racine *Web.config* fichier montre les paramètres appropriés :

[!code-xml[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample12.xml)]

Paramètre `UnobtrusiveJavaScriptEnabled` true Ajax discrète d’Active et la validation client non obstructive. Lorsque vous utilisez la validation discrète, les règles de validation sont transformées en attributs HTML5. Noms d’attributs HTML5 peuvent comprendre uniquement des lettres minuscules, des chiffres et des tirets.

Paramètre `ClientValidationEnabled` à true active la validation côté client. En définissant ces clés dans l’application *Web.config* fichier, vous activez la validation côté client et du JavaScript discret pour toute l’application. Vous pouvez également activer ou désactiver ces paramètres dans des vues ou des méthodes de contrôleur à l’aide du code suivant :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample13.cs)]

Vous devez également inclure plusieurs fichiers JavaScript dans l’affichage. Un moyen simple pour inclure le code JavaScript dans toutes les vues consiste à les ajouter à la *Views\Shared\\_Layout.cshtml* fichier. Remplacez le `<head>` élément de la  *\_Layout.cshtml* fichier avec le code suivant :

[!code-cshtml[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample14.cshtml)]

Les deux premiers scripts jQuery sont hébergés par le contenu remise réseau CDN Microsoft Ajax (). En tirant parti du CDN Microsoft Ajax, vous pouvez considérablement améliorer les performances du premier accès de vos applications.

Exécutez l’application et cliquez sur un lien d’édition. Afficher le code source dans le navigateur. La source du navigateur présente beaucoup d’attributs de la forme `data-val` (pour la validation de données). Lors de la validation côté client et le JavaScript non obstrusif est activé, les champs d’entrée avec une règle de validation côté client contient le `data-val="true"` attribut pour déclencher la validation client non obstructive. Par exemple, le `City` champ dans le modèle a été décoré avec le [requis](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx) attribut, ce qui génère le code HTML indiqué dans l’exemple suivant :

[!code-cshtml[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample15.cshtml)]

Pour chaque règle de validation côté client, un attribut est ajouté qui présente sous la forme `data-val-rulename="message"`. À l’aide de la `City` champ exemple illustré précédemment, la règle de validation côté client nécessaire génère le `data-val-required` attribut et le message &quot;champ de la ville est obligatoire&quot;. Exécutez l’application, modifiez un des utilisateurs et désactivez la `City` champ. Lorsque vous changez le champ, vous voyez un message d’erreur de validation côté client.

![Ville requis](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image14.png)

De même, pour chaque paramètre dans la règle de validation côté client, un attribut est ajouté qui présente sous la forme `data-val-rulename-paramname=paramvalue`. Par exemple, le `FirstName` propriété est annotée avec le [StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) d’attribut et spécifie une longueur minimale de 3 et une longueur maximale de 8. La règle de validation de données nommée `length` porte le nom de paramètre `max` et la valeur du paramètre 8. L’exemple suivant montre le code HTML qui est généré pour le `FirstName` champ lorsque vous modifiez un des utilisateurs :

[!code-cshtml[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample16.cshtml)]

Pour plus d’informations sur la validation client non obstructive, consultez l’entrée [discrète de Validation Client dans ASP.NET MVC 3](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html) dans le blog de Brad Wilson.

> [!NOTE]
> Dans la version bêta d’ASP.NET MVC 3, vous devez parfois envoyer le formulaire afin de démarrer la validation côté client. Cela peut être modifié pour la version finale.


## <a name="creating-the-create-view"></a>Création de la vue de créer

L’étape suivante consiste à ajouter un `Create` méthode d’action et de la vue afin de permettre à l’utilisateur créer un nouvel utilisateur. Ajoutez le code suivant `Create` méthode au contrôleur home :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample17.cs)]

Ajouter une vue comme dans les étapes précédentes, mais définissez **afficher le contenu** à **créer**.

![Créer une vue](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image15.png)

Exécutez l’application, sélectionnez le **créer** lier et ajouter un nouvel utilisateur. Le `Create` méthode tire automatiquement parti de la validation côté client et côté serveur. Essayez d’entrer un nom d’utilisateur qui contient un espace blanc, tel que &quot;Ben X&quot;. Lorsque vous changez le champ nom d’utilisateur, une erreur de validation côté client (`White space is not allowed`) s’affiche.

## <a name="add-the-delete-method"></a>Ajoutez la méthode de suppression

Pour exécuter ce didacticiel, ajoutez le code suivant `Delete` méthode au contrôleur home :

[!code-csharp[Main](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/samples/sample18.cs)]

Ajouter un `Delete` vue comme dans les étapes précédentes, en définissant **afficher le contenu** à **supprimer**.

![Supprimer la vue](creating-a-mvc-3-application-with-razor-and-unobtrusive-javascript/_static/image16.png)

Vous disposez maintenant d’une application ASP.NET MVC 3 simple mais entièrement fonctionnelle avec la validation.
