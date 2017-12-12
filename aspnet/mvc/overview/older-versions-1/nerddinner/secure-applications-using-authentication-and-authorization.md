---
uid: mvc/overview/older-versions-1/nerddinner/secure-applications-using-authentication-and-authorization
title: "Sécuriser des Applications à l’aide de l’authentification et autorisation | Documents Microsoft"
author: microsoft
description: "Étape 9 montre comment ajouter l’authentification et l’autorisation pour sécuriser notre application NerdDinner, afin que les utilisateurs ont besoin inscrire et de la connexion au site pour créer..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 9e4d5cac-b071-440c-b044-20b6d0c964fb
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/secure-applications-using-authentication-and-authorization
msc.type: authoredcontent
ms.openlocfilehash: a23b2cf4d1728624698c0db49c25ea7efd3af67d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="secure-applications-using-authentication-and-authorization"></a>Sécuriser des Applications à l’aide de l’authentification et autorisation
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 9 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 9 montre comment ajouter l’authentification et l’autorisation pour sécuriser notre application NerdDinner, afin que les utilisateurs ont besoin inscrire et de la connexion au site pour créer le nouveau préparés et seul l’utilisateur qui héberge un dîner peut le modifier ultérieurement.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-9-authentication-and-authorization"></a>De NerdDinner étape 9 : L’authentification et l’autorisation

Maintenant notre NerdDinner application accorde toute personne visitant le site de la possibilité de créer et modifier les détails de n’importe quel dinner. Nous allons maintenant changer cela afin que les utilisateurs ont besoin inscrire et de la connexion au site pour créer le nouveau préparés et ajouter une restriction afin que seul l’utilisateur qui héberge un dîner peut le modifier ultérieurement.

Pour ce faire, nous allons utiliser l’authentification et l’autorisation pour sécuriser votre application.

### <a name="understanding-authentication-and-authorization"></a>Autorisation et la compréhension de l’authentification

*Authentification* est le processus d’identification et de validation de l’identité d’un client de l’accès à une application. Plus simplement, il est sur l’identification « qui est « l’utilisateur final lorsqu’ils visitent un site Web. ASP.NET prend en charge plusieurs façons pour authentifier les utilisateurs du navigateur. Pour les applications web Internet, l’approche la plus courante d’authentification utilisée est appelée « Authentification de formulaires ». L’authentification par formulaire permet à un développeur créer un formulaire de connexion HTML dans leur application, puis valider le nom d’utilisateur/le mot de passe que l’utilisateur final envoie par rapport à une base de données ou une autre banque d’informations d’identification de mot de passe. Si la combinaison nom d’utilisateur/mot de passe est correcte, le développeur peut demander puis à émettre un cookie HTTP chiffré pour identifier l’utilisateur entre les futures demandes ASP.NET. Nous allons à l’aide de l’authentification par formulaire avec notre application NerdDinner.

*Autorisation* est le processus permettant de déterminer si un utilisateur authentifié est autorisé à accéder à une ressource d’URL/particulier ou pour effectuer une action. Par exemple, dans notre application NerdDinner nous vous souhaitez autoriser que seuls les utilisateurs qui sont connectés peuvent accéder à la *préparés/créer* URL et créer de nouveau préparés. Nous allons également ajouter une logique d’autorisation afin que seul l’utilisateur qui héberge un dîner modification – et de refuser l’accès en modification à tous les autres utilisateurs.

### <a name="forms-authentication-and-the-accountcontroller"></a>L’authentification par formulaire et la AccountController

Le modèle de projet Visual Studio par défaut pour ASP.NET MVC active automatiquement l’authentification par formulaire lors de la création de nouvelles applications ASP.NET MVC. Il ajoute également automatiquement une implémentation de page de connexion de compte avant génération au projet, ce qui le rend très facile d’intégrer la sécurité au sein d’un site.

La page maître Site.master de valeur par défaut affiche un lien « Session » à l’angle supérieur droit du site lorsque l’utilisateur qu’il n’est pas authentifié :

![](secure-applications-using-authentication-and-authorization/_static/image1.png)

En cliquant sur le lien « Session » amène un utilisateur vers le */Account / d’ouverture de session* URL :

![](secure-applications-using-authentication-and-authorization/_static/image2.png)

Les visiteurs qui n’ont pas inscrits peuvent le faire en cliquant sur le lien « Register » – qui mène à la */Account/Register* URL et les autoriser à entrer les détails du compte :

![](secure-applications-using-authentication-and-authorization/_static/image3.png)

En cliquant sur le bouton « Enregistrer » crée un nouvel utilisateur dans le système d’appartenance d’ASP.NET et authentifier l’utilisateur sur le site à l’aide de l’authentification par formulaire.

Lorsqu’un utilisateur est connecté, la page Site.master change haut à droite de la page vers la sortie d’un « Bienvenue [username] ! » message et restitue une « session » lier au lieu d’une « session » un. En cliquant sur le lien « Session » déconnecte l’utilisateur :

![](secure-applications-using-authentication-and-authorization/_static/image4.png)

La fonctionnalité de connexion, de déconnexion et d’enregistrement ci-dessus est implémentée dans la classe AccountController qui a été ajoutée à notre projet par Visual Studio lors de sa création du projet. L’interface utilisateur pour le AccountController est implémentée à l’aide de modèles d’affichage dans le répertoire \Views\Account :

![](secure-applications-using-authentication-and-authorization/_static/image5.png)

La classe AccountController utilise le système d’authentification par formulaire ASP.NET pour émettre des cookies d’authentification chiffrées et l’API d’appartenance ASP.NET pour stocker et de valider les noms d’utilisateur/mots de passe. L’API de l’appartenance ASP.NET est extensible et permet de n’importe quel magasin d’informations d’identification de mot de passe à utiliser. ASP.NET est fourni avec les implémentations de fournisseurs d’appartenances intégrés qui stockent le nom d’utilisateur/mots de passe dans une base de données SQL, ou dans Active Directory.

Nous pouvons configurer le fournisseur d’appartenances notre application de NerdDinner doit utiliser par l’ouverture du fichier « web.config » à la racine du projet et en recherchant le &lt;appartenance&gt; section qu’il contient. Web.config par défaut ajoutés lorsque le projet a été créé inscrit le fournisseur d’appartenances SQL et le configure pour utiliser une chaîne de connexion nommée « ApplicationServices » pour spécifier l’emplacement de la base de données.

La chaîne de connexion par défaut « ApplicationServices » (qui est spécifiée dans le &lt;connectionStrings&gt; section du fichier web.config) est configuré pour utiliser SQL Express. Il pointe vers une base de données SQL Express, nommé « ASPNETDB. MDF » sous l’application « application\_données » active. Si cette base de données n’existe pas la première utilisation de l’API de l’appartenance au sein de l’application, ASP.NET sera automatiquement créer la base de données et configurer le schéma de base de données d’appartenance appropriés qu’il contient :

![](secure-applications-using-authentication-and-authorization/_static/image6.png)

Si au lieu d’utiliser SQL Express nous souhaitons utiliser une instance de SQL Server complète (ou se connecter à une base de données distante), tout ce que nous devez action consiste à mettre à jour la chaîne de connexion « ApplicationServices » dans le fichier web.config et assurez-vous que le schéma d’adhésion appropriées a été ajouté à la base de données à laquelle sur qu'il pointe. Vous pouvez exécuter le « aspnet\_regsql.exe « utilitaire dans le répertoire \Windows\Microsoft.NET\Framework\v2.0.50727\ pour ajouter le schéma approprié pour l’appartenance et les autres services d’application ASP.NET pour une base de données.

### <a name="authorizing-the-dinnerscreate-url-using-the-authorize-filter"></a>Autoriser l’URL préparés/créer en utilisant le filtre [Authorize]

Nous n’aviez pas à écrire du code pour activer une authentification sécurisée et l’implémentation de gestion de compte pour l’application de NerdDinner. Les utilisateurs peuvent inscrire de nouveaux comptes avec notre application et la connexion/déconnexion du site.

Maintenant, nous pouvons ajouter la logique d’autorisation à l’application et permet de contrôler ce qu’ils peuvent et ne peuvent pas faire dans le site de l’état de l’authentification et le nom d’utilisateur de visiteurs. Nous allons commencer en ajoutant une logique d’autorisation pour les méthodes d’action « Créer » de notre classe DinnersController. Plus précisément, nous nécessite que les utilisateurs accédant à la *préparés/créer* URL doit être enregistré dans. Si elles ne sont pas consignées dans nous allons les rediriger vers la page de connexion afin qu’ils peuvent se connecter au.

L’implémentation de cette logique est assez facile. Il suffit d’action consiste à ajouter un attribut de filtre [Authorize] à nos méthodes d’action Créer comme suit :

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample1.cs)]

ASP.NET MVC prend en charge la capacité de créer des « filtres d’action » qui peuvent être utilisés pour implémenter la logique réutilisable qui peut être appliquée de façon déclarative aux méthodes d’action. Le filtre [Authorize] est un des filtres d’action intégrés fournis par ASP.NET MVC, et il permet à un développeur de façon déclarative appliquer des règles d’autorisation pour les méthodes d’action et les classes de contrôleur.

En cas d’application sans paramètres (comme ci-dessus) le filtre [Authorize] impose que l’utilisateur qui effectue la requête de méthode d’action doit être connecté, et il sera automatiquement rediriger le navigateur vers l’URL de connexion qui ne sont pas. Cette redirection de l’URL demandée à l’origine est passé comme argument de chaîne de requête lors de l’exécution (par exemple : / Account / d’ouverture de session ? ReturnUrl = % 2fDinners % 2fCreate). Le AccountController puis redirige l’utilisateur vers l’URL demandée à l’origine une fois qu’il se connecte.

Le filtre [Authorize] éventuellement prend en charge la possibilité de spécifier une propriété « Utilisateurs » ou « Rôles » qui peut être utilisée pour exiger que l’utilisateur est connecté et dans une liste d’utilisateurs autorisés ou un membre d’un rôle de sécurité autorisées. Par exemple, le code ci-dessous permet uniquement de deux utilisateurs spécifiques, « scottgu » et « billg » accéder à l’URL préparés/Create :

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample2.cs)]

Incorporation des noms d’utilisateur spécifique dans du code a tendance à être assez non facile à gérer si. Une meilleure approche consiste à définir de niveau supérieur « rôles » que le code vérifie, puis de mapper les utilisateurs dans le rôle à l’aide d’une base de données ou le système Active Directory (l’activation de la liste de mappage de l’utilisateur réel à stocker en externe à partir du code). ASP.NET inclut une API de gestion de rôle intégré ainsi que d’un ensemble prédéfini de fournisseurs de rôles (tels que SQL et Active Directory) qui peuvent aider à effectuer ce mappage de rôle d’utilisateur. Nous avons ensuite mettre à jour le code pour autoriser uniquement les utilisateurs dans un rôle spécifique « admin » accéder à l’URL préparés/Create :

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample3.cs)]

### <a name="using-the-useridentityname-property-when-creating-dinners"></a>À l’aide de la propriété User.Identity.Name lors de la création préparés

Nous pouvons récupérer le nom d’utilisateur de l’utilisateur actuellement connecté en tant d’une demande à l’aide de la propriété User.Identity.Name exposée sur la classe de base du contrôleur.

Antérieure mise en œuvre de la version HTTP-POST de la méthode d’action Create() nous avons dû codé en dur la propriété « HostedBy » de la dîner à une chaîne statique. Nous pouvons maintenant mettre à jour de ce code pour utiliser à la place de la propriété User.Identity.Name, mais aussi ajouter automatiquement une réponse pour l’hôte de créer le dîner :

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample4.cs)]

Étant donné que nous avons ajouté un attribut [Authorize] à la méthode Create(), ASP.NET MVC permet de s’assurer que la méthode d’action s’exécute uniquement si l’utilisateur visite de l’URL préparés/créer s’est connecté sur le site. Par conséquent, la valeur de propriété User.Identity.Name contient toujours un nom d’utilisateur valide.

### <a name="using-the-useridentityname-property-when-editing-dinners"></a>À l’aide de la propriété User.Identity.Name lors de la modification préparés

Ajoutons maintenant une logique d’autorisation qui limite les utilisateurs afin qu’ils puissent modifier uniquement des propriétés de préparés qu'eux-mêmes hébergent.

Pour résoudre cela, nous allons tout d’abord ajouter une méthode d’assistance de « IsHostedBy(username) » à notre objet Dinner (au sein de la classe partielle Dinner.cs que nous avons créé précédemment). Cette méthode d’assistance retourne true ou false selon si un nom d’utilisateur fourni correspond à la propriété Dinner HostedBy et encapsule la logique nécessaire pour effectuer une comparaison de chaînes de casse d'entre eux :

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample5.cs)]

Nous allons ensuite ajouter un attribut [Authorize] pour les méthodes d’action Edit() au sein de notre classe DinnersController. Cela garantit que les utilisateurs doivent être connectés à la demande un */Dinners/modification / [id]* URL.

Nous pouvons ensuite ajouter le code à nos méthodes de modification qui utilise la méthode d’assistance Dinner.IsHostedBy(username) pour vérifier que l’utilisateur connecté correspond à l’hôte de Dinner. Si l’utilisateur n’est pas l’ordinateur hôte, nous afficher une vue « InvalidOwner » et mettre fin à la demande. Le code se présente comme ci-dessous :

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample6.cs)]

Nous pouvons ensuite avec le bouton droit sur le répertoire \Views\Dinners et choisissez Ajouter -&gt;afficher la commande de menu pour créer une nouvelle vue « InvalidOwner ». Nous allons la remplir avec le message d’erreur ci-après :

[!code-aspx[Main](secure-applications-using-authentication-and-authorization/samples/sample7.aspx)]

Et maintenant lorsqu’un utilisateur tente de modifier un dîner, qu'ils ne possèdent pas, ils obtenez un message d’erreur :

![](secure-applications-using-authentication-and-authorization/_static/image7.png)

Nous pouvons Répétez ces étapes pour les méthodes d’action Delete() dans notre contrôleur verrouiller autorisation pour supprimer également les préparés et vous assurer que seul l’hôte d’un dîner peut le supprimer.

### <a name="showinghiding-edit-and-delete-links"></a>Affichage/masquage modifier et les liens de suppression

Nous sommes liaison à la méthode d’action de modification et suppression de notre classe DinnersController à partir de notre URL détails :

![](secure-applications-using-authentication-and-authorization/_static/image8.png)

Actuellement, nous montrons les modifier et supprimer des liens d’action que l’ordinateur hôte de le dinner soit le visiteur à l’URL de détails. Nous allons modifier afin que les liens sont affichés uniquement si l’utilisateur visite est le propriétaire de la dîner.

La méthode d’action Details() dans notre DinnersController récupère un objet Dinner et puis le passe en tant que l’objet de modèle à notre modèle de vue :

[!code-csharp[Main](secure-applications-using-authentication-and-authorization/samples/sample8.cs)]

Nous pouvons mettre à jour notre modèle d’affichage pour conditionnellement afficher/masquer les liens modifier et supprimer à l’aide de la Dinner.IsHostedBy() méthode d’assistance comme ci-dessous :

[!code-aspx[Main](secure-applications-using-authentication-and-authorization/samples/sample9.aspx)]

#### <a name="next-steps"></a>Étapes suivantes

Nous allons maintenant voir comment nous pouvons activer les utilisateurs authentifiés à RSVP pour préparés à l’aide d’AJAX.

>[!div class="step-by-step"]
[Précédent](implement-efficient-data-paging.md)
[Suivant](use-ajax-to-deliver-dynamic-updates.md)
