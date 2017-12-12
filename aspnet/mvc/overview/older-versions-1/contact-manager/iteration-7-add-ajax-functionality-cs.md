---
uid: mvc/overview/older-versions-1/contact-manager/iteration-7-add-ajax-functionality-cs
title: "Itération #7 – des fonctionnalités Ajax d’ajouter (c#) | Documents Microsoft"
author: microsoft
description: "Dans l’itération septième, nous améliorer la réactivité et les performances de votre application en ajoutant la prise en charge d’Ajax."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2009
ms.topic: article
ms.assetid: f1b0809e-8909-444e-b6bb-a5cd1dea3f72
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/contact-manager/iteration-7-add-ajax-functionality-cs
msc.type: authoredcontent
ms.openlocfilehash: db313d12dfd6a146347f253dc3a1f4a889bee780
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="iteration-7--add-ajax-functionality-c"></a>Itération #7 – des fonctionnalités Ajax d’ajouter (c#)
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le Code](iteration-7-add-ajax-functionality-cs/_static/contactmanager_7_cs1.zip)

> Dans l’itération septième, nous améliorer la réactivité et les performances de votre application en ajoutant la prise en charge d’Ajax.


## <a name="building-a-contact-management-aspnet-mvc-application-c"></a>Création d’une Application ASP.NET MVC de gestion des contacts (c#)

Dans cette série de didacticiels, nous générer une application de gestion des contacts entière à partir du début à la fin. L’application Gestionnaire de contacts permet de vous permet de stocker les informations de contact (des noms, des numéros de téléphone et adresses de messagerie) pour obtenir la liste des personnes.

Nous générer l’application sur plusieurs itérations. Avec chaque itération, nous améliorer progressivement l’application. L’objectif de cette approche itération plusieurs est pour vous permettre de comprendre la raison de chaque modification.

- Itération #1 - créer l’application. Dans la première itération, nous créons le Gestionnaire de Contact de la façon la plus simple possible. Prise en charge pour les opérations de base de données : création, lecture, mise à jour et supprimer (CRUD).

- Itération #2 : rendre des applications attrayantes. Dans cette itération, nous améliorer l’apparence de l’application en modifiant la valeur par défaut de page maître de vue ASP.NET MVC en cascade de feuille de style.

- Itération #3 - ajouter la validation de formulaire. Dans la troisième itération, nous ajouter la validation de formulaire de base. Nous empêcher des personnes d’envoi d’un formulaire sans terminer les champs obligatoires. Nous avons également valider que les adresses de messagerie et les numéros de téléphone.

- Itération #4 : rendre des applications faiblement couplées. Dans cette troisième itération, nous tirer parti de plusieurs modèles de conception de logiciel pour le rendre plus facile à gérer et modifier l’application Gestionnaire de contacts. Par exemple, nous refactoriser notre application pour utiliser le modèle de référentiel et le modèle d’Injection de dépendances.

- Itération #5 - créer des tests unitaires. Dans l’itération cinquième, nous faciliter notre application mettre à jour et modifier en ajoutant les tests unitaires. Nous simuler nos classes de modèle de données et générer des tests unitaires pour nos contrôleurs et la logique de validation.

- Itération #6 - utiliser le développement piloté par test. Dans cette itération sixième, nous ajouter de nouvelles fonctionnalités à notre application en écrivant des tests unitaires tout d’abord et d’écrire du code pour les tests unitaires. Dans cette itération, nous ajouter des groupes de contacts.

- Itération #7 - ajouter des fonctionnalités Ajax. Dans l’itération septième, nous améliorer la réactivité et les performances de votre application en ajoutant la prise en charge d’Ajax.

## <a name="this-iteration"></a>Cette itération

Dans cette itération de l’application Gestionnaire de Contact, nous refactoriser notre application d’utiliser Ajax. En tirant parti d’Ajax, nous rendre votre application plus réactive. Nous pouvons éviter le rendu d’une page entière lorsque nous devons mettre à jour uniquement une région donnée dans une page.

Nous allons refactoriser notre vue Index afin que nous n’avez pas besoin pour réafficher la page entière chaque fois qu’un utilisateur sélectionne un groupe de contacts. Au lieu de cela, lorsque l’utilisateur clique sur un groupe de contacts, nous simplement mettre à jour la liste de contacts et laisser le reste de la page uniquement.

Nous allons également modifier la façon de notre delete lien fonctionne. Au lieu d’afficher une page de confirmation distinct, nous affichons une boîte de dialogue de confirmation de JavaScript. Si vous confirmez que vous souhaitez supprimer un contact, une opération HTTP DELETE est effectuée sur le serveur pour supprimer l’enregistrement de contact à partir de la base de données.

En outre, nous tirera parti de jQuery pour ajouter des effets d’animation à notre vue d’Index. Nous allons afficher une animation lorsque la nouvelle liste de contacts est lue à partir du serveur.

Enfin, nous allons prendre parti de la prise en charge du framework ASP.NET AJAX pour gérer l’historique du navigateur. Nous allons créer de points d’historique à chaque fois que nous effectuons un appel Ajax pour mettre à jour la liste de contacts. De cette façon, le navigateur ascendante et descendante boutons va utiliser.

## <a name="why-use-ajax"></a>Pourquoi utiliser Ajax ?

L’utilisation d’Ajax présente de nombreux avantages. Tout d’abord, ajout de fonctionnalités Ajax à une application entraîne une meilleure expérience utilisateur. Dans une application web normal, l’ensemble de la page doit être validée sur le serveur chaque fois qu'un utilisateur effectue une action. Chaque fois que vous effectuez une action, les verrous de navigateur et l’utilisateur doivent patienter jusqu'à ce que la page entière est extraite et actualisée.

Il s’agit d’une expérience inacceptable dans le cas d’une application de bureau. Toutefois, en règle générale, nous avons eu avec cette expérience utilisateur incorrects dans le cas d’une application web, car nous ne connaissait pas que nous pouvions faire mieux. Nous pensions qu'a une limite d’applications web, en réalité, il était juste une limitation de notre imaginations.

Dans une application Ajax, vous n’avez pas besoin pour mettre l’expérience utilisateur arrête simplement mettre à jour une page. Au lieu de cela, vous pouvez effectuer une demande asynchrone en arrière-plan pour mettre à jour de la page. Vous ne force de t à l’utilisateur de patienter pendant que la partie de la page est mise à jour.

En tirant parti d’Ajax, vous pouvez également améliorer les performances de votre application. Pensez comment l’application Gestionnaire de contacts fonctionne maintenant sans fonctionnalités Ajax. Lorsque vous cliquez sur un groupe de contacts, la vue Index entier doit être actualisée. La liste de contacts et de la liste des groupes de contacts doivent être récupérées à partir du serveur de base de données. Toutes ces données doivent être passées via le réseau à partir du serveur web à un navigateur web.

Toutefois, lorsque nous ajoutons des fonctionnalités Ajax à notre application, nous pouvons éviter réaffichage de la page entière quand un utilisateur clique sur un groupe de contacts. Nous n’avez plus besoin de saisir les groupes de contact à partir de la base de données. Nous n’avez pas besoin pour transmettre toute la vue Index sur le câble également. En tirant parti d’Ajax, nous réduire la quantité de travail que notre serveur de base de données doit effectuer et nous la quantité de trafic réseau requis par votre application.

## <a name="don-t-be-afraid-of-ajax"></a>Ne pas être peur de Ajax

Certains développeurs évitent l’utilisation d’Ajax, car ils vous inquiétez pas sur les navigateurs de niveau inférieur. Ils veulent s’assurer que leurs applications web continueront de fonctionner lors de l’accès par un navigateur qui ne prend pas en charge JavaScript. Ajax dépendant de JavaScript, certains développeurs évitent l’utilisation d’Ajax.

Toutefois, si vous êtes attention lors de la procédure d’implémentation Ajax vous pouvez générer les applications qui fonctionnent avec les navigateurs de niveau supérieur et de niveau inférieur. Notre application Contact Manager fonctionne avec les navigateurs qui prennent en charge JavaScript et les navigateurs qui ne le font pas.

Si vous utilisez l’application Gestionnaire de contacts avec un navigateur qui prend en charge JavaScript vous aura une meilleure expérience utilisateur. Par exemple, lorsque vous cliquez sur un groupe de contacts, seulement la région de la page qui affiche les contacts sera mis à jour.

Si, en revanche, vous utilisez l’application Gestionnaire de contacts avec un navigateur qui ne prend pas en charge JavaScript (ou dont JavaScript est désactivé) vous disposez d’une expérience utilisateur légèrement moins recommandée. Par exemple, lorsque vous cliquez sur un groupe de contacts, toute la vue Index doit être publiée sur le navigateur pour afficher la liste de contacts de correspondance.

## <a name="adding-the-required-javascript-files"></a>Ajout de fichiers JavaScript requis

Nous devrons trois fichiers JavaScript permet d’ajouter des fonctionnalités Ajax à notre application. Les trois de ces fichiers sont inclus dans le dossier de Scripts d’une application ASP.NET MVC.

Si vous envisagez d’utiliser Ajax dans plusieurs pages de votre application puis il judicieux d’inclure les fichiers JavaScript requis dans votre page maître de vue s d’une application. De cette façon, les fichiers JavaScript sont automatiquement inclus dans toutes les pages dans votre application.

Ajoutez le code JavaScript suivant inclut à l’intérieur de la &lt;head&gt; balise de votre page maître de vue :

[!code-html[Main](iteration-7-add-ajax-functionality-cs/samples/sample1.html)]

## <a name="refactoring-the-index-view-to-use-ajax"></a>La vue Index pour utiliser Ajax de refactorisation

Permettent de commencer par modifier la vue d’Index afin qu’en cliquant sur un groupe de contacts met à jour uniquement pour la région de la vue qui affiche les contacts s. La zone rouge dans la Figure 1 contient la région que vous souhaitez mettre à jour.


[![Mise à jour uniquement les contacts](iteration-7-add-ajax-functionality-cs/_static/image1.jpg)](iteration-7-add-ajax-functionality-cs/_static/image1.png)

**Figure 01**: mise à jour uniquement les contacts ([cliquez pour afficher l’image en taille réelle](iteration-7-add-ajax-functionality-cs/_static/image2.png))


La première étape consiste à séparer la partie de la vue que vous souhaitez mettre à jour de façon asynchrone dans un partiel distinct (contrôle utilisateur). La section de la vue de l’Index qui affiche la table des contacts a été déplacée dans partielle dans la liste 1.

**La liste 1 - Views\Contact\ContactList.ascx**

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample2.aspx)]

Remarquez qu’un modèle différent de la vue Index partielle dans la liste 1. Le *Inherits* d’attribut dans le &lt;% @ Page %&gt; directive spécifie que partielle hérite le ViewUserControl&lt;groupe&gt; classe.

La vue Index mis à jour est contenue dans la liste 2.

**Listing 2 - Views\Contact\Index.aspx**

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample3.aspx)]

Il existe deux éléments que vous devez remarquer sur la vue de mises à jour dans la liste 2. Tout d’abord, notez que tout le contenu déplacé dans partielle est remplacé par un appel à Html.RenderPartial(). La méthode Html.RenderPartial() est appelée lors de la première requête de la vue Index afin d’afficher l’ensemble initial des contacts.

En second lieu, notez que le Html.ActionLink() utilisée pour afficher les groupes de contact a été remplacée par une Ajax.ActionLink(). Le Ajax.ActionLink() est appelée avec les paramètres suivants :

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample4.aspx)]

Le premier paramètre représente le texte à afficher pour le lien, le deuxième paramètre représente les valeurs d’itinéraire et le troisième paramètre représente les options Ajax. Dans ce cas, nous utilisons l’option UpdateTargetId Ajax pour pointer vers le code HTML &lt;div&gt; balise que vous souhaitez mettre à jour une fois la requête Ajax terminée. Vous souhaitez mettre à jour le &lt;div&gt; balise avec la nouvelle liste de contacts.

La méthode Index() mis à jour du contrôleur de Contact est contenue dans la liste 3.

**La liste 3 - Controllers\ContactController.cs (méthode d’Index)**

[!code-csharp[Main](iteration-7-add-ajax-functionality-cs/samples/sample5.cs)]

L’action Index() mis à jour retourne conditionnelle l’une des deux choses. Si l’action Index() est appelée par une requête Ajax le contrôleur retourne un partiel. Sinon, l’action Index() retourne une vue entière.

Notez que l’action Index() n’a pas besoin renvoyer les données lorsqu’elle est appelée par une requête Ajax. Dans le contexte d’une demande normale, l’action Index retourne une liste de tous les groupes de contact et le groupe de contact sélectionné. Dans le contexte d’une requête Ajax, l’action Index() retourne uniquement le groupe sélectionné. AJAX signifie moins de travail sur votre serveur de base de données.

Notre vue Index modifiée fonctionne dans le cas de niveau supérieur et inférieur. Si vous cliquez sur un groupe, et que votre navigateur prend en charge JavaScript, seulement la région de la vue qui contient la liste de contacts est mis à jour. Si, en revanche, votre navigateur ne prend pas en charge JavaScript, la totalité de la vue est mise à jour.


Notre vue mise à jour d’Index a un problème. Lorsque vous cliquez sur un groupe, le groupe sélectionné n’est pas mis en surbrillance. Étant donné que la liste des groupes s’affiche en dehors de la région qui est mis à jour pendant une requête Ajax, le groupe de droite ne pas obtenir mis en surbrillance. Ce problème sera résolu dans la section suivante.


## <a name="adding-jquery-animation-effects"></a>Ajout d’effets d’Animation jQuery

En règle générale, lorsque vous cliquez sur un lien dans une page web, vous pouvez utiliser la barre de progression du navigateur pour déterminer si le navigateur activement extrait le contenu mis à jour. Lorsque vous effectuez une requête Ajax, en revanche, la barre de progression du navigateur ne ne montre aucune évolution. Cela peut rendre les utilisateurs nerveux. Comment savoir si le navigateur a figée ?

Il existe plusieurs méthodes que vous pouvez indiquer à un utilisateur dont le travail est exécuté lors de l’exécution d’une requête Ajax. Une approche consiste à afficher une animation simple. Par exemple, vous pouvez réduire une région quand une requête Ajax commence et fondu dans la région à l’issue de la demande.

Nous allons utiliser la bibliothèque jQuery qui est incluse dans le framework Microsoft ASP.NET MVC, pour créer les effets d’animation. La vue Index mis à jour est contenue dans la liste 4.

**La liste 4 - Views\Contact\Index.aspx**

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample6.aspx)]

Notez que la vue Index mis à jour contient trois nouvelles fonctions JavaScript. Les deux premières fonctions permet jQuery fondu et fondu dans la liste de contacts lorsque vous cliquez sur un groupe de contacts. La troisième fonction affiche un message d’erreur lorsque les résultats de la demande Ajax dans une erreur (par exemple, délai d’expiration réseau).

La première fonction se charge aussi de mettre en surbrillance le groupe sélectionné. Une classe = attribut sélectionné est ajouté à l’élément parent (l’élément LI) de l’élément que vous cliquez sur. Là encore, jQuery facilite la sélection de l’élément approprié et ajouter la classe CSS.

Ces scripts sont liés aux liens de groupe à l’aide du paramètre Ajax.ActionLink() AjaxOptions. L’appel de méthode Ajax.ActionLink() mis à jour ressemble à ceci :

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample7.aspx)]

## <a name="adding-browser-history-support"></a>Ajout de prise en charge de l’historique des navigateurs

En règle générale, lorsque vous cliquez sur un lien vers une page de mise à jour, l’historique de votre navigateur est mis à jour. De cette façon, vous pouvez cliquer sur le bouton précédent du navigateur pour revenir dans le temps à l’état précédent de la page. Par exemple, si vous cliquez sur le groupe de contacts de vos amis et puis cliquez sur le groupe de contacts professionnels, cliquez sur le bouton précédent du navigateur pour revenir à l’état de la page lorsque le groupe de contact amis a été sélectionné.

Malheureusement, effectuer une requête Ajax ne pas automatiquement à jour l’historique de navigation. Si vous cliquez sur un groupe, et la liste des contacts correspondants est extraites par une requête Ajax, l’historique du navigateur n’est pas actualisé. Vous ne pouvez pas utiliser le bouton précédent du navigateur pour revenir à un groupe de contacts après avoir sélectionné un groupe de contacts.

Si vous souhaitez que les utilisateurs soient en mesure d’utiliser le navigateur précédent bouton après avoir effectué les requêtes Ajax vous devez effectuer un peu plus de travail. Vous avez besoin tirer parti de la fonctionnalité de gestion de l’historique de navigateur intégrée dans l’infrastructure ASP.NET AJAX.

L’historique du navigateur ASP.NET AJAX, vous devez effectuer trois actions :

1. Activer l’historique de navigation en définissant la propriété enableBrowserHistory sur true.
2. Enregistrer les points d’historique lorsque l’état d’une vue est modifiée en appelant la méthode addHistoryPoint().
3. Reconstruire l’état de la vue lorsque l’événement de navigation est déclenché.

La vue Index mis à jour est contenue dans la liste 5.

**La liste 5 - Views\Contact\Index.aspx**

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample8.aspx)]

Dans la liste 5, l’historique de navigation est activée dans la fonction pageInit(). La fonction pageInit() est également utilisée pour configurer le Gestionnaire d’événements pour l’événement de navigation. L’événement de navigation est déclenché chaque fois que le navigateur vers l’avant ou arrière provoque l’état de la page à modifier.

La méthode beginContactList() est appelée lorsque vous cliquez sur un groupe de contacts. Cette méthode crée un nouveau point d’historique en appelant la méthode addHistoryPoint(). L’id d’un clic sur le groupe de contacts est ajouté à l’historique.

L’id de groupe est récupérée à partir de l’attribut expando sur le lien du groupe de contacts. Le lien est restitué avec l’appel suivant à Ajax.ActionLink().

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample9.aspx)]

Le dernier paramètre passé à la Ajax.ActionLink() ajoute un attribut expando nommé groupid à la liaison (minuscule pour la compatibilité XHTML).

Lorsqu’un utilisateur accède à l’arrière ou navigateur vers l’avant, l’événement de navigation est déclenché et la méthode navigate() est appelée. Cette méthode met à jour les contacts affichés dans la page en fonction de l’état de la page qui correspond au point d’historique navigateur passé à la méthode navigate.

## <a name="performing-ajax-deletes"></a>L’exécution de Ajax supprime

Actuellement, pour supprimer un contact, vous devez cliquer sur le lien Supprimer, puis cliquez sur le bouton Supprimer affiché dans la page de confirmation de suppression (voir Figure 2). Il semble que de nombreuses demandes de page pour faire quelque chose de simple comme la suppression d’un enregistrement de base de données.


[![La page de confirmation de suppression](iteration-7-add-ajax-functionality-cs/_static/image2.jpg)](iteration-7-add-ajax-functionality-cs/_static/image3.png)

**Figure 02**: la page de confirmation de suppression ([cliquez pour afficher l’image en taille réelle](iteration-7-add-ajax-functionality-cs/_static/image4.png))


Il est tentant d’ignorer la page de confirmation de suppression et de supprimer un contact directement à partir de la vue Index. Vous devez éviter de tentation car cette approche ouvre votre application à des failles de sécurité. En règle générale, ne pas souhaitez exécuter une opération HTTP GET lors de l’appel d’une action qui modifie l’état de votre application web. Lorsque vous effectuez une suppression, que vous souhaitez effectuer une requête HTTP POST, ou mieux encore, une opération HTTP DELETE.

Le lien Supprimer est contenu dans le ContactList partielle. Une version mise à jour de la ContactList partielle est contenue dans la liste 6.

**La liste 6 - Views\Contact\ContactList.ascx**

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample10.aspx)]

Le lien Supprimer est rendu avec l’appel suivant à la méthode Ajax.ImageActionLink() :

[!code-aspx[Main](iteration-7-add-ajax-functionality-cs/samples/sample11.aspx)]

> [!NOTE] 
> 
> Le Ajax.ImageActionLink() n’est pas une partie standard de l’infrastructure ASP.NET MVC. Le Ajax.ImageActionLink() est des méthodes d’assistance personnalisé inclus dans le projet de gestionnaire de contacts.


Le paramètre AjaxOptions possède deux propriétés. Tout d’abord, la propriété de confirmation est utilisée pour afficher une boîte de dialogue contextuelle JavaScript confirmation. En second lieu, la propriété HttpMethod est utilisée pour effectuer une opération HTTP DELETE.

La liste 7 contient une nouvelle action AjaxDelete() qui a été ajoutée au contrôleur de Contact.

**La liste 7 - Controllers\ContactController.cs (AjaxDelete)**

[!code-csharp[Main](iteration-7-add-ajax-functionality-cs/samples/sample12.cs)]

L’action AjaxDelete() est décorée avec un attribut AcceptVerbs. Cet attribut empêche l’action invoquée à l’exception par l’opération HTTP autre qu’une opération HTTP DELETE. En particulier, vous ne pouvez pas appeler cette opération avec un verbe HTTP GET.

Après avoir supprimé les enregistrements de base de données, vous devez afficher la liste de mises à jour des contacts qui ne contient-elle pas l’enregistrement supprimé. La méthode AjaxDelete() retourne le ContactList partielle et la liste de mises à jour des contacts.

## <a name="summary"></a>Résumé

Dans cette itération, nous avons ajouté des fonctionnalités Ajax à notre application Gestionnaire de contacts. Nous avons utilisé Ajax pour améliorer la réactivité et les performances de notre application.

Tout d’abord, nous avons remanié la vue Index afin qu’en cliquant sur un groupe de contacts ne met pas à jour la vue d’ensemble. Au lieu de cela, cliquez sur un groupe de contacts met à jour uniquement la liste des contacts.

Ensuite, nous avons utilisé animations jQuery fondu et fondu dans la liste de contacts. Ajout d’une animation à une application Ajax peut être utilisé pour fournir aux utilisateurs de l’application avec l’équivalent d’une barre de progression du navigateur.

Nous avons également ajouté des navigateurs pris en charge de l’historique à notre application Ajax. Nous avons activé les utilisateurs de cliquer sur le navigateur de retour et de boutons pour modifier l’état de la vue Index suivante.

Enfin, nous avons créé un lien de suppression qui prend en charge les opérations HTTP DELETE. Effectuez les suppressions Ajax, nous permettre aux utilisateurs de supprimer des enregistrements de la base de données sans que l’utilisateur demander une page de confirmation de suppression supplémentaires.

>[!div class="step-by-step"]
[Précédent](iteration-6-use-test-driven-development-cs.md)
[Suivant](iteration-1-create-the-application-vb.md)
