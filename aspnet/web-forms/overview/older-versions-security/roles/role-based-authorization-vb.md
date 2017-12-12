---
uid: web-forms/overview/older-versions-security/roles/role-based-authorization-vb
title: "Autorisation basée sur les rôles (VB) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel commence par examiner comment le framework rôles associe des rôles d’un utilisateur à son contexte de sécurité. Il examine ensuite comment appliquer des URL basée sur un rôle..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/24/2008
ms.topic: article
ms.assetid: 83b4f5a4-4f5a-4380-ba33-f0b5c5ac6a75
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/roles/role-based-authorization-vb
msc.type: authoredcontent
ms.openlocfilehash: 973e5705fc36b13e5e6ec861dd2ca6adfc0f50fe
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="role-based-authorization-vb"></a>Autorisation basée sur les rôles (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/6/0/3/6032582f-360d-4739-b935-38721fdb86ea/VB.11.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/0/3/6032582f-360d-4739-b935-38721fdb86ea/aspnet_tutorial11_RoleAuth_vb.pdf)

> Ce didacticiel commence par examiner comment le framework rôles associe des rôles d’un utilisateur à son contexte de sécurité. Il examine ensuite comment appliquer des règles d’autorisation d’URL basée sur les rôles. À la suite, nous allons nous intéresser à l’aide et de programmation déclarative permettant de modifier les données affichées et les fonctionnalités offertes par une page ASP.NET.


## <a name="introduction"></a>Introduction

Dans le <a id="_msoanchor_1"> </a> [ *d’autorisation basée sur l’utilisateur* ](../membership/user-based-authorization-vb.md) didacticiel, nous avons vu comment utiliser l’autorisation d’URL pour spécifier quels utilisateurs peuvent visiter un ensemble particulier de pages. Avec un peu de balisage dans `Web.config`, nous pouvons également demander à ASP.NET pour autoriser uniquement les utilisateurs authentifiés à visiter une page. Ou bien, nous avons exigent que seuls les utilisateurs Tito et Bob étaient autorisées, ou indiquer que tous les utilisateurs authentifiés à l’exception de Sam étaient autorisées.

En plus de l’autorisation d’URL, nous avons également étudié déclaratives et par programme des techniques pour contrôler les données affichées et les fonctionnalités offertes par une page en fonction de l’utilisateur visite. En particulier, nous avons créé une page qui répertoriée le contenu du répertoire actuel. Toute personne peut visitez cette page, mais seuls les utilisateurs authentifiés peuvent afficher les contenus des dossiers et Tito seulement pu supprimer les fichiers.

Application des règles d’autorisation sur une base de l’utilisateur par l’utilisateur peut devenir un cauchemar de comptabilité. Une approche plus facile à gérer consiste à utiliser l’autorisation basée sur le rôle. La bonne nouvelle est que les outils à notre disposition pour appliquer des règles d’autorisation fonctionnent aussi bien avec les rôles de façon que pour les comptes d’utilisateur. Règles d’autorisation d’URL peuvent spécifier des rôles plutôt qu’aux utilisateurs. Le contrôle LoginView, qui restitue une sortie différente pour les utilisateurs authentifiés et anonymes, peut être configuré pour afficher un contenu différent en fonction des rôles de l’utilisateur connecté. Et l’API de rôles inclut des méthodes pour déterminer les rôles de l’utilisateur connecté.

Ce didacticiel commence par examiner comment le framework rôles associe des rôles d’un utilisateur à son contexte de sécurité. Il examine ensuite comment appliquer des règles d’autorisation d’URL basée sur les rôles. À la suite, nous allons nous intéresser à l’aide et de programmation déclarative permettant de modifier les données affichées et les fonctionnalités offertes par une page ASP.NET. C’est parti !

## <a name="understanding-how-roles-are-associated-with-a-users-security-context"></a>Comprendre comment les rôles sont associés avec un contexte de sécurité utilisateur

Chaque fois qu’une demande entre dans le pipeline ASP.NET, il est associé à un contexte de sécurité, qui inclut des informations permettant d’identifier le demandeur. Lorsque vous utilisez l’authentification par formulaire, un ticket d’authentification est utilisé comme un jeton d’identité. Comme expliqué dans la <a id="_msoanchor_2"> </a> [ *une vue d’ensemble de l’authentification par formulaire* ](../introduction/an-overview-of-forms-authentication-vb.md) et <a id="_msoanchor_3"> </a> [ *formulaires Configuration de l’authentification et les rubriques avancées* ](../introduction/forms-authentication-configuration-and-advanced-topics-vb.md) didacticiels, les `FormsAuthenticationModule` est responsable de la détermination de l’identité du demandeur, ce qu’il fait lors de la [ `AuthenticateRequest` d’événement](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.authenticaterequest.aspx).

Si un ticket d’authentification valide, non expiré est trouvé, le `FormsAuthenticationModule` décode afin de déterminer l’identité du demandeur. Il crée un nouveau `GenericPrincipal` de l’objet et affecte à la `HttpContext.User` objet. L’objectif d’une entité, comme `GenericPrincipal`, est utilisée pour identifier le nom de l’utilisateur authentifié et ce qu’elle appartient à des rôles. Cela est évident par le fait que tous les objets principales ont un `Identity` propriété et un `IsInRole(roleName)` (méthode). Le `FormsAuthenticationModule`, toutefois, n’est pas intéressé par l’enregistrement des informations sur les rôles et les `GenericPrincipal` objet il crée ne spécifie pas de tous les rôles.

Si l’infrastructure de rôles est activée, le [ `RoleManagerModule` ](https://msdn.microsoft.com/en-us/library/system.web.security.rolemanagermodule.aspx) HTTP Module les étapes après la `FormsAuthenticationModule` et identifie les rôles de l’utilisateur authentifié lors de la [ `PostAuthenticateRequest` événement](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.postauthenticaterequest.aspx), qui se déclenche après la `AuthenticateRequest` événement. Si la demande provient d’un utilisateur authentifié, le `RoleManagerModule` remplace le `GenericPrincipal` objet créé par le `FormsAuthenticationModule` et la remplace par un [ `RolePrincipal` objet](https://msdn.microsoft.com/en-us/library/system.web.security.roleprincipal.aspx). La `RolePrincipal` classe utilise l’API de rôles pour déterminer les rôles de l’utilisateur appartient.

La figure 1 illustre le flux de travail du pipeline ASP.NET lors de l’utilisation de l’authentification par formulaire et l’infrastructure de rôles. Le `FormsAuthenticationModule` s’exécute en premier, identifie l’utilisateur via son ticket d’authentification et crée un nouveau `GenericPrincipal` objet. Ensuite, le `RoleManagerModule` les étapes et remplace le `GenericPrincipal` de l’objet avec un `RolePrincipal` objet.

Si un utilisateur anonyme visite le site, ni le `FormsAuthenticationModule` ni le `RoleManagerModule` crée un objet principal.


[![Les événements du Pipeline ASP.NET pour un utilisateur authentifié lors de l’utilisation de l’authentification par formulaire et l’infrastructure de rôles](role-based-authorization-vb/_static/image2.png)](role-based-authorization-vb/_static/image1.png)

**Figure 1**: les événements de Pipeline ASP.NET pour une authentification utilisateur lorsque à l’aide de l’authentification par formulaire et l’infrastructure de rôles ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image3.png))


### <a name="caching-role-information-in-a-cookie"></a>La mise en cache des informations sur les rôles dans un Cookie

Le `RolePrincipal` l’objet `IsInRole(roleName)` les appels de méthode `Roles`.`GetRolesForUser` Pour obtenir les rôles pour l’utilisateur afin de déterminer si l’utilisateur est membre du *roleName*. Lorsque vous utilisez la `SqlRoleProvider`, cela entraîne une requête à la base de données du magasin de rôle. Lors de l’utilisation des règles d’autorisation URL basée sur les rôles du `RolePrincipal`de `IsInRole` méthode sera appelée à chaque demande à une page qui est protégée par les règles d’autorisation URL basée sur les rôles. Plutôt que d’avoir à rechercher les informations de rôle dans la base de données à chaque demande, le `Roles` framework inclut une option pour mettre en cache des rôles de l’utilisateur dans un cookie.

Si l’infrastructure de rôles est configuré pour mettre en cache des rôles de l’utilisateur dans un cookie, le `RoleManagerModule` crée le cookie au cours du pipeline ASP.NET [ `EndRequest` événement](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.endrequest.aspx). Ce cookie est utilisé dans les demandes suivantes dans le `PostAuthenticateRequest`, c'est-à-dire lorsque le `RolePrincipal` objet est créé. Si le cookie est valide et n’a pas expiré, les données dans le cookie sont analysées et utilisées pour remplir des rôles de l’utilisateur, ce qui l’enregistrement du `RolePrincipal` d’avoir à effectuer un appel à la `Roles` classe pour déterminer les rôles de l’utilisateur. La figure 2 illustre ce flux de travail.


[![Informations sur les rôles de l’utilisateur peuvent être stockées dans un Cookie pour améliorer les performances](role-based-authorization-vb/_static/image5.png)](role-based-authorization-vb/_static/image4.png)

**Figure 2**: rôle des informations peuvent être stockées l’utilisateur le dans un Cookie pour améliorer les performances ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image6.png))


Par défaut, le mécanisme de cookie de cache de rôle est désactivé. Il peut être activé via le `<roleManager>`; balisage de configuration dans `Web.config`. Nous l’avons vu à l’aide de la [ `<roleManager>` élément](https://msdn.microsoft.com/en-us/library/ms164660.aspx) pour spécifier des fournisseurs de rôles dans le <a id="_msoanchor_4"> </a> [ *création et gestion des rôles* ](creating-and-managing-roles-vb.md) (didacticiel), Vous devez disposent déjà de cet élément dans votre application `Web.config` fichier. Les paramètres de cookies de cache de rôle sont spécifiées en tant qu’attributs de la `<roleManager>`; élément et sont résumées dans le tableau 1.

> [!NOTE]
> Les paramètres de configuration répertoriées dans le tableau 1 spécifient les propriétés du cookie de cache de rôle qui en résulte. Pour plus d’informations sur les cookies, leur fonctionnement et leurs différentes propriétés, consultez [ce didacticiel Cookies](http://www.quirksmode.org/js/cookies.html).


| **Property** | **Description** |
| --- | --- |
| `cacheRolesInCookie` | Valeur booléenne qui indique si la mise en cache de cookie est utilisé. La valeur par défaut est `false`. |
| `cookieName` | Le nom du cookie de cache de rôle. La valeur par défaut ». ASPXROLES ». |
| `cookiePath` | Le chemin d’accès au cookie de rôles nom. L’attribut de chemin d’accès permet au développeur limiter l’étendue d’un cookie pour une hiérarchie de répertoires particulière. La valeur par défaut est « / », qui informe le navigateur à envoyer le cookie de ticket d’authentification pour toute demande adressée au domaine. |
| `cookieProtection` | Indique quelles techniques sont utilisées pour protéger le cookie de cache de rôle. Les valeurs autorisées sont : `All` (la valeur par défaut) ; `Encryption`; `None`; et `Validation`. Faire référence à l’étape 3 de la <a id="_anchor_5"> </a> [ *Configuration de l’authentification de formulaires et les rubriques avancées* ](../introduction/forms-authentication-configuration-and-advanced-topics-vb.md) didacticiel pour plus d’informations sur les niveaux de protection. |
| `cookieRequireSSL` | Valeur booléenne qui indique si une connexion SSL est requise pour transmettre le cookie d’authentification. La valeur par défaut est `false`. |
| `cookieSlidingExpiration` | Valeur booléenne qui indique si le délai d’expiration du cookie est réinitialisé chaque fois que l’utilisateur visite le site pendant une session unique. La valeur par défaut est `false`. Cette valeur est pertinente uniquement lorsque `createPersistentCookie` a la valeur `true`. |
| `cookieTimeout` | Spécifie la durée, en minutes, après laquelle le cookie de ticket d’authentification expire. La valeur par défaut est `30`. Cette valeur est pertinente uniquement lorsque `createPersistentCookie` a la valeur `true`. |
| `createPersistentCookie` | Valeur booléenne qui spécifie si le cookie de cache de rôle est un cookie de session ou un cookie persistant. Si `false` (la valeur par défaut), un cookie de session est utilisé, ce qui est supprimé lorsque le navigateur est fermé. Si `true`, un cookie persistant est utilisé ; il expire `cookieTimeout` nombre de minutes après qu’elle a été créée ou après la visite précédente, selon la valeur de `cookieSlidingExpiration`. |
| `domain` | Spécifie la valeur du cookie domaine. La valeur par défaut est une chaîne vide, ce qui entraîne le navigateur à utiliser le domaine à partir duquel il a été délivré (par exemple, www.yourdomain.com). Dans ce cas, le cookie sera **pas** envoyer lors de la fabrication des demandes à sous-domaines, telles que admin.yourdomain.com. Si vous souhaitez que le cookie doit être passé à tous les sous-domaines vous devez personnaliser le `domain` attribut, la valeur « votredomaine.com ». |
| `maxCachedResults` | Spécifie le nombre maximal de noms de rôles sont mis en cache dans le cookie. La valeur par défaut est 25. Le `RoleManagerModule` ne crée pas un cookie pour les utilisateurs qui appartiennent à plusieurs `maxCachedResults` rôles. Par conséquent, le `RolePrincipal` l’objet `IsInRole` méthode utilisera la `Roles` classe pour déterminer les rôles de l’utilisateur. La raison `maxCachedResults` existe est car de nombreux agents utilisateurs n’autorisent pas les cookies supérieurs à 4 096 octets. Par conséquent, ce cache est destiné à réduire le risque de dépasser cette limite de taille. Si vous avez des noms de rôles très longs, vous souhaiterez en spécifiant un plus petit `maxCachedResults` de valeur ; contrariwise, si vous avez des noms de rôles très courtes, vous pouvez probablement augmenter cette valeur. |

**Tableau 1**: les Options de Configuration de rôle du Cache Cookie

Permet de configurer notre application à utiliser les cookies du cache de rôle non persistant. Pour ce faire, mettez à jour le `<roleManager>` élément `Web.config` pour inclure les attributs de cookies suivants :

[!code-xml[Main](role-based-authorization-vb/samples/sample1.xml)]

J’ai mis à jour le `<roleManager>`; l’élément en ajoutant les trois attributs : `cacheRolesInCookie`, `createPersistentCookie`, et `cookieProtection`. En définissant `cacheRolesInCookie` à `true`, le `RoleManagerModule` sera désormais automatiquement en cache des rôles de l’utilisateur dans un cookie au lieu d’utiliser Rechercher des informations sur les rôles de l’utilisateur à chaque demande. Définir explicitement la `createPersistentCookie` et `cookieProtection` attributs `false` et `All`, respectivement. Techniquement, j’ai n’a pas besoin de spécifier les valeurs de ces attributs, car j’ai juste affectés à leurs valeurs par défaut, mais les placer ici pour le rendre explicitement effacer que je n’utilise des cookies persistants et que le cookie est chiffré et validé.

C’est tout cela ! Dorénavant, le framework rôles mettra en cache les rôles d’utilisateurs dans des cookies. Si le navigateur de l’utilisateur ne prend pas en charge les cookies, ou si les cookies sont supprimés ou perdus, une certaine manière, il n’est pas un problème – la `RolePrincipal` objet utilisera simplement la `Roles` classe dans les cas où aucun cookie (ou une version non valide ou a expiré) est disponible.

> [!NOTE]
> Les modèles de Microsoft &amp; groupe de pratiques déconseille l’utilisation de cookies du cache persistant de rôle. Étant donné que la possession du cookie de cache de rôle est suffisante pour prouver l’appartenance au rôle, si un pirate informatique d’une certaine manière accessibles d’un utilisateur valide qu’il peut emprunter l’identité de cet utilisateur. La probabilité de ce problème augmente si le cookie est rendu persistant sur le navigateur de l’utilisateur. Pour plus d’informations sur cette recommandation de sécurité, ainsi que d’autres problèmes de sécurité, reportez-vous à la [liste de questions de sécurité pour ASP.NET 2.0](https://msdn.microsoft.com/en-us/library/ms998375.aspx).


## <a name="step-1-defining-role-based-url-authorization-rules"></a>Étape 1 : Définition des règles d’autorisation URL basée sur un rôle

Comme indiqué dans le <a id="_msoanchor_6"> </a> [ *d’autorisation basée sur l’utilisateur* ](../membership/user-based-authorization-vb.md) (didacticiel), l’autorisation d’URL offre un moyen de restreindre l’accès à un ensemble de pages sur un utilisateur par l’utilisateur ou un rôle-par-role base. Les règles d’autorisation d’URL sont précisés dans `Web.config` à l’aide de la [ `<authorization>` élément](https://msdn.microsoft.com/en-us/library/8d82143t.aspx) avec `<allow>` et `<deny>` des éléments enfants. Outre les règles relatives à l’utilisateur une autorisation décrits dans les didacticiels précédents, chaque `<allow>` et `<deny>` élément enfant peut également inclure :

- Un rôle particulier
- Liste délimitée par des virgules des rôles

Par exemple, les règles d’autorisation d’URL accorder l’accès à ces utilisateurs dans les rôles des contrôleurs et des administrateurs, mais refusent l’accès à tous les autres :

[!code-xml[Main](role-based-authorization-vb/samples/sample2.xml)]

Le `<allow>` élément du balisage ci-dessus indique que les rôles des contrôleurs et des administrateurs sont autorisés ; les `<deny>`; élément qui fait en sorte que *tous les* refusé aux utilisateurs.

Permet de configurer votre application afin que le `ManageRoles.aspx`, `UsersAndRoles.aspx`, et `CreateUserWizardWithRoles.aspx` pages sont uniquement accessibles aux utilisateurs dans le rôle Administrateurs, alors que le `RoleBasedAuthorization.aspx` page reste accessible à tous les visiteurs.

Pour ce faire, commencez par ajouter un `Web.config` le fichier le `Roles` dossier.


[![Ajouter un fichier Web.config dans le répertoire de rôles](role-based-authorization-vb/_static/image8.png)](role-based-authorization-vb/_static/image7.png)

**Figure 3**: ajouter un `Web.config` de fichiers à la `Roles` active ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image9.png))


Ensuite, ajoutez le balisage suivant de la configuration à `Web.config`:

[!code-xml[Main](role-based-authorization-vb/samples/sample3.xml)]

Le `<authorization>` élément dans le `<system.web>` section indique que seuls les utilisateurs dans le rôle Administrateurs peuvent accéder aux ressources ASP.NET dans le `Roles` active. Le `<location>` élément définit un autre ensemble de règles d’autorisation d’URL pour le `RoleBasedAuthorization.aspx` page, ce qui permet de tous les utilisateurs à visiter la page.

Après avoir enregistré vos modifications dans `Web.config`, connectez-vous en tant qu’utilisateur qui n’est pas dans le rôle d’administrateur, puis recommencez à visiter une des pages protégées. Le `UrlAuthorizationModule` détecte que vous n’êtes pas autorisé à visiter la ressource demandée ; par conséquent, le `FormsAuthenticationModule` vous redirigera vers la page de connexion. La page de connexion puis vous redirige vers le `UnauthorizedAccess.aspx` page (voir Figure 4). Cette redirection finale à partir de la page de connexion à `UnauthorizedAccess.aspx` se produit en raison du code que nous avons ajouté à la page de connexion à l’étape 2 de la <a id="_msoanchor_7"> </a> [ *d’autorisation basée sur l’utilisateur* ](../membership/user-based-authorization-vb.md) didacticiel. En particulier, la page de connexion redirige automatiquement tous les utilisateurs authentifiés à `UnauthorizedAccess.aspx` si la chaîne de requête contienne un `ReturnUrl` paramètre, en tant que ce paramètre indique que l’utilisateur est arrivé à la page de connexion après avoir essayé d’afficher une page qu’il n’a pas été autorisé à afficher.


[![Seuls les utilisateurs dans le rôle Administrateurs peuvent afficher les Pages protégés](role-based-authorization-vb/_static/image11.png)](role-based-authorization-vb/_static/image10.png)

**Figure 4**: seuls les utilisateurs dans le rôle Administrateurs peuvent afficher les Pages de protégés ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image12.png))


Déconnectez-vous et connectez-vous en tant qu’utilisateur qui est membre du rôle Administrateurs. Maintenant, vous devez pouvoir afficher les trois pages protégés.


[![Tito peuvent visiter le UsersAndRoles.aspx Page car il est dans le rôle Administrateurs](role-based-authorization-vb/_static/image14.png)](role-based-authorization-vb/_static/image13.png)

**Figure 5**: Tito peuvent visiter le `UsersAndRoles.aspx` Page car il est dans le rôle Administrateurs ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image15.png))


> [!NOTE]
> Lorsque vous spécifiez des règles d’autorisation URL – pour les rôles ou d’utilisateurs : il est important de garder à l’esprit que les règles sont analysée individuellement, à partir du haut vers le bas. Dès qu’une correspondance est trouvée, l’utilisateur est accordé ou refusé, selon que la correspondance a été trouvée dans un `<allow>` ou `<deny>` élément. **Si aucune correspondance n’est trouvée, l’utilisateur est autorisé à accéder.** Par conséquent, si vous souhaitez restreindre l’accès à un ou plusieurs comptes d’utilisateur, il est impératif d’utiliser un `<deny>` élément comme dernier élément dans la configuration de l’autorisation d’URL. **Si vos règles d’autorisation d’URL n’incluent pas une**`<deny>`**élément, tous les utilisateurs auront accès.** Pour une discussion plus détaillée sur la façon dont les règles d’autorisation d’URL sont analysées, faire référence à la « observez comment la `UrlAuthorizationModule` utilise les règles d’autorisation pour accorder ou refuser l’accès « section de la <a id="_msoanchor_8"> </a> [  *Autorisation basée sur l’utilisateur* ](../membership/user-based-authorization-vb.md) didacticiel.


## <a name="step-2-limiting-functionality-based-on-the-currently-logged-in-users-roles"></a>Étape 2 : Limitant les fonctionnalités basée sur les rôles de l’utilisateur actuellement connecté

Permet de d’autorisation d’URL faciles à spécifient grossiers d’autorisation règles cet état, les identités est autorisées et celles qui est refusées à partir de l’affichage d’une page spécifique (ou toutes les pages dans un dossier et ses sous-dossiers). Toutefois, dans certains cas, nous pouvons adopter autoriser tous les utilisateurs à accéder à une page, mais limiter les fonctionnalités de la page basée sur les rôles de l’utilisateur visite. Cela peut entraîner l’affichage ou masquage de données basé sur le rôle de l’utilisateur ou de l’offre des fonctionnalités supplémentaires pour les utilisateurs qui appartiennent à un rôle particulier.

Ces règles d’autorisation basée sur le rôle de granularité fine peuvent être implémentés de façon déclarative ou par programme (ou via une combinaison des deux). Dans la section suivante, nous verrons comment implémenter l’autorisation de granularité fine déclarative via le contrôle LoginView. Après cela, nous allons examiner les techniques de programmation. Avant que nous pouvons examiner d’appliquer des règles d’autorisation de granularité fine, toutefois, nous devez d’abord créer une page dont la fonctionnalité varie selon le rôle de l’utilisateur à visiter.

Nous allons créer une page qui répertorie tous les comptes d’utilisateur dans le système dans un GridView. Le contrôle GridView inclura les nom d’utilisateur de chaque utilisateur, adresse de messagerie, date de la dernière ouverture de session et commentaires concernant l’utilisateur. En plus d’afficher les informations de chaque utilisateur, le contrôle GridView inclut modifier et supprimer des fonctionnalités. Initialement nous créer cette page avec le modifier et supprimer des fonctionnalités disponibles pour tous les utilisateurs. Dans les sections « À l’aide du contrôle LoginView » et « Limiter par programme des fonctionnalités », nous verrons comment activer ou désactiver ces fonctionnalités en fonction de la visite du rôle utilisateur.

> [!NOTE]
> Nous sommes sur le point de créer la page ASP.NET utilise un contrôle GridView pour afficher les comptes d’utilisateur. Depuis ce didacticiel série se concentre sur l’authentification par formulaire, l’autorisation, les comptes d’utilisateurs et rôles, je ne souhaite pas passer trop de temps d’aborder le fonctionnement interne du contrôle GridView. Alors que ce didacticiel fournit des instructions spécifiques pour la configuration de cette page, il ne pas étudier en détail pourquoi certains choix ont été apportées, ou ce qui ont les propriétés particulières d’effet sur la sortie rendue. Pour un examen approfondi du contrôle GridView, passez en revue mon  *[utilisation des données dans ASP.NET 2.0](../../data-access/index.md)*  série de didacticiels.


Commencez par ouvrir le `RoleBasedAuthorization.aspx` page dans le `Roles` dossier. Faites glisser un contrôle GridView à partir de la page dans le concepteur et un ensemble de ses `ID` à `UserGrid`. Dans un instant nous allez écrire du code qui appelle la `Membership`.`GetAllUsers` méthode et lie résultant `MembershipUserCollection` objet au GridView. Le `MembershipUserCollection` contient un `MembershipUser` objet pour chaque compte d’utilisateur dans le système ; `MembershipUser` objets ont des propriétés comme `UserName`,`Email`,`LastLoginDate` et ainsi de suite.

Avant de nous écrire le code qui lie les comptes d’utilisateur à la grille, nous allons définir les champs de GridView. À partir de la balise de GridView, cliquez sur le lien « Modifier les colonnes » pour lancer la boîte de dialogue champs zone (voir Figure 6). À ce stade, désactivez la case à cocher « Générer automatiquement des champs » dans l’angle inférieur gauche. Comme nous voulons que ce contrôle GridView à inclure la modification et suppression de fonctionnalités, ajoutez un CommandField et définissez son `ShowEditButton` et `ShowDeleteButton` propriétés à la valeur True. Ensuite, ajoutez quatre champs pour l’affichage de la `UserName`, `Email`, `LastLoginDate`, et `Comment` propriétés. Utiliser un BoundField pour les deux propriétés en lecture seule (`UserName` et `LastLoginDate`) et TemplateField pour les deux champs modifiables (`Email` et `Comment`).

Affiche de BoundField la première la `UserName` propriété ; définir son `HeaderText` et `DataField` propriétés « UserName ». Ce champ n’est pas modifiables, par conséquent, définissez son `ReadOnly` True à la propriété. Configurer le `LastLoginDate` BoundField en définissant son `HeaderText` à la « Dernière connexion » et ses `DataField` à « LastLoginDate ». Nous allons mettre en forme la sortie de cette BoundField afin que la date est affichée (au lieu de la date et l’heure). Pour ce faire, affectez ce BoundField `HtmlEncode` propriété sur False et que son `DataFormatString` propriété « {0 : d} ». Définissez également la `ReadOnly` True à la propriété.

Définir le `HeaderText` propriétés de la deux TemplateField « Email » et « Commentaire ».


[![Les champs du GridView peuvent être configurés via la boîte de dialogue champs](role-based-authorization-vb/_static/image17.png)](role-based-authorization-vb/_static/image16.png)

**Figure 6**: champs peut être configuré via la boîte du GridView de dialogue champs ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image18.png))


Nous devons maintenant définir la `ItemTemplate` et `EditItemTemplate` pour le « Email » et « Commentaire » TemplateField. Ajouter un contrôle Web Label dans chaque le `ItemTemplates` et lier leurs `Text` propriétés pour le `Email` et `Comment` propriétés, respectivement.

Pour le TemplateField « Email », ajoutez une zone de texte nommée `Email` à son `EditItemTemplate` et lier sa `Text` propriété le `Email` propriété à l’aide de la liaison de données bidirectionnelle. Ajouter un RequiredFieldValidator et un RegularExpressionValidator à la `EditItemTemplate` pour vous assurer qu’un visiteur de modification de la propriété de messagerie a entré une adresse de messagerie valide. Pour le TemplateField « Commentaire », ajoutez une zone de texte multiligne nommé `Comment` à son `EditItemTemplate`. Définir la zone de texte `Columns` et `Rows` propriétés à 40 et 4, respectivement, puis de lier son `Text` propriété le `Comment` propriété à l’aide de la liaison de données bidirectionnelle.

Après avoir configuré ces TemplateField, leur balisage déclaratif doit ressembler à ce qui suit :

[!code-aspx[Main](role-based-authorization-vb/samples/sample4.aspx)]

Lors de la modification ou la suppression d’un compte d’utilisateur que nous devons savoir de cet utilisateur `UserName` valeur de propriété. Définir le contrôle de GridView `DataKeyNames` propriété « Nom_utilisateur » afin que ces informations sont disponibles par le biais du GridView `DataKeys` collection.

Enfin, ajoutez un contrôle ValidationSummary à la page et définissez ses `ShowMessageBox` True à la propriété et sa `ShowSummary` propriété sur False. Avec ces paramètres, le contrôle ValidationSummary affiche une alerte côté client si l’utilisateur tente de modifier un compte d’utilisateur avec une adresse de messagerie manquant ou non valide.

[!code-aspx[Main](role-based-authorization-vb/samples/sample5.aspx)]

Nous avons terminé le balisage déclaratif de cette page. La tâche suivante consiste à lier de l’ensemble des comptes d’utilisateur pour le contrôle GridView. Ajoutez une méthode nommée `BindUserGrid` à la `RoleBasedAuthorization.aspx` classe code-behind de la page qui lie la `MembershipUserCollection` retourné par `Membership.GetAllUsers` à la `UserGrid` GridView. Appelez cette méthode à partir de la `Page_Load` Gestionnaire d’événements sur la première visite de page.

[!code-vb[Main](role-based-authorization-vb/samples/sample6.vb)]

Avec ce code en place, visitez la page via un navigateur. Comme le montre la Figure 7, vous devez voir un GridView répertoriant des informations sur chaque compte d’utilisateur dans le système.


[![Le contrôle UserGrid GridView répertorie des informations sur chaque utilisateur dans le système](role-based-authorization-vb/_static/image20.png)](role-based-authorization-vb/_static/image19.png)

**Figure 7**: le `UserGrid` GridView répertorie des informations sur chaque utilisateur dans le système ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image21.png))


> [!NOTE]
> Le `UserGrid` GridView répertorie tous les utilisateurs dans une interface de réserve non paginée. Cette interface grille simple n’est pas appropriée pour les scénarios où il existe plusieurs dizaines ou de plusieurs utilisateurs. Une option consiste à configurer le contrôle GridView pour activer la pagination. Le `Membership.GetAllUsers` méthode possède deux surcharges : un qui n’accepte aucun paramètre d’entrée et retourne tous les utilisateurs et l’autre qui accepte les valeurs entières pour les index et la taille de la page et retourne uniquement le sous-ensemble spécifié d’utilisateurs. La deuxième surcharge peut être utilisé pour parcourir plus efficacement les utilisateurs, car elle retourne uniquement le sous-ensemble précis de comptes d’utilisateur au lieu de *tous les* d'entre eux. Si vous avez des milliers de comptes d’utilisateur, vous souhaiterez une interface en fonction de filtre, qui affiche uniquement les utilisateurs dont nom d’utilisateur commence par un caractère sélectionné, par exemple. Le [ `Membership.FindUsersByName` méthode](https://msdn.microsoft.com/en-us/library/system.web.security.membership.findusersbyname.aspx) est idéal pour la création d’une interface utilisateur basée sur le filtre. Nous allons nous intéresser à la création d’une telle interface dans un didacticiel futures.


Le contrôle GridView offre intégrée de modification et de suppression de prise en charge lorsque le contrôle est lié à un contrôle de source de données correctement configuré, telles que le SqlDataSource ou ObjectDataSource. Le `UserGrid` GridView, toutefois, a ses données liées par programmation ; par conséquent, nous devons écrire du code pour effectuer ces deux tâches. En particulier, nous devons créer des gestionnaires d’événements pour le contrôle du GridView `RowEditing`, `RowCancelingEdit`, `RowUpdating`, et `RowDeleting` les événements qui sont déclenchés lorsqu’un visiteur clique du GridView modifier Annuler, mise à jour, ou supprimer des boutons.

Commencez par créer les gestionnaires d’événements pour le contrôle du GridView `RowEditing`, `RowCancelingEdit`, et `RowUpdating` événements, puis ajoutez le code suivant :

[!code-vb[Main](role-based-authorization-vb/samples/sample7.vb)]

Le `RowEditing` et `RowCancelingEdit` gestionnaires d’événements définissent simplement de GridView `EditIndex` propriété et la reliaison puis la liste des utilisateurs de comptes à la grille. La plus intéressante dans le `RowUpdating` Gestionnaire d’événements. Ce gestionnaire d’événements démarre en veillant à ce que les données ne sont valides et qu’il extrait ensuite le `UserName` valeur du compte d’utilisateur modifié à partir de la `DataKeys` collection. Le `Email` et `Comment` zones de texte dans les deux TemplateField `EditItemTemplate` s puis référencés par programmation. Leur `Text` propriétés contiennent l’adresse de messagerie modifiée et un commentaire.

Pour mettre à jour d’un compte d’utilisateur via l’API d’appartenance que vous devez tout d’abord obtenir les informations de l’utilisateur, ce que nous faire via un appel à `Membership.GetUser(userName)`. Retourné `MembershipUser` l’objet `Email` et `Comment` sont ensuite mises à jour avec les valeurs entrées dans les deux zones de texte à partir de l’interface de modification. Enfin, ces modifications sont enregistrées par un appel à [ `Membership.UpdateUser` ](https://msdn.microsoft.com/en-us/library/system.web.security.membership.updateuser.aspx). Le `RowUpdating` Gestionnaire d’événements se termine en rétablissant le contrôle GridView à son interface de modification préalable.

Ensuite, créez le `RowDeleting` RowDeleting Gestionnaire d’événements, puis ajoutez le code suivant :

[!code-vb[Main](role-based-authorization-vb/samples/sample8.vb)]

Le Gestionnaire d’événements ci-dessus démarre en saisissant la `UserName` valeur à partir du contrôle de GridView `DataKeys` collection ; cette `UserName` valeur est ensuite passée à la classe d’appartenance [ `DeleteUser` méthode](https://msdn.microsoft.com/en-us/library/system.web.security.membership.deleteuser.aspx). Le `DeleteUser` méthode supprime le compte d’utilisateur à partir du système, y compris les données d’appartenance connexes (telles que les rôles de cet utilisateur appartient). Après la suppression de l’utilisateur, la grille `EditIndex` a la valeur -1 (au cas où l’utilisateur a cliqué sur Supprimer alors qu’une autre ligne était en mode édition) et le `BindUserGrid` méthode est appelée.

> [!NOTE]
> Ce bouton ne nécessite pas de confirmation de l’utilisateur avant de supprimer le compte d’utilisateur quelconque. Je vous encourage à ajouter une forme de confirmation de l’utilisateur pour réduire le risque d’un compte en cours de suppression par inadvertance. Une des méthodes plus simples pour confirmer une action est via une boîte de dialogue de confirmation du côté client. Pour plus d’informations sur cette technique, consultez [Ajout côté Client Confirmation lors de la suppression de](https://asp.net/learn/data-access/tutorial-42-vb.aspx).


Vérifiez que cette page fonctionne comme prévu. Vous devez être en mesure de modifier l’adresse de messagerie d' un utilisateur et le commentaire, ainsi que de supprimer n’importe quel compte d’utilisateur. Étant donné que le `RoleBasedAuthorization.aspx` page est accessible à tous les utilisateurs, tout utilisateur – même anonymes visiteurs – visitez cette page et modifier et supprimer des comptes d’utilisateur ! Nous allons mettre à jour cette page afin que seuls les utilisateurs dans les rôles superviseurs et les administrateurs peuvent modifier adresse de messagerie d’un utilisateur et le commentaire, et seuls les administrateurs peuvent supprimer un compte d’utilisateur.

La section « À l’aide du contrôle LoginView » présente à l’aide du contrôle LoginView pour afficher les instructions spécifiques au rôle de l’utilisateur. Si une personne dans le rôle Administrateurs visite cette page, nous allons montrer des instructions sur la façon de modifier et supprimer des utilisateurs. Si un utilisateur dans le rôle superviseurs atteint cette page, nous allons montrer des instructions sur la modification d’utilisateurs. Et si le visiteur est anonyme ou n’est pas dans le rôle Administrateurs soit par exemple, nous affichera un message qui explique qu’ils ne peuvent pas modifier ou supprimer les informations de compte d’utilisateur. Dans la section « Limitation par programme des fonctionnalités » nous allez écrire du code qui affiche ou masque les boutons Modifier et supprimer basés sur le rôle de l’utilisateur par programme.

### <a name="using-the-loginview-control"></a>L’utilisation du contrôle LoginView

Comme nous l’avons vu dans ces didacticiels, le contrôle LoginView est utile pour afficher les interfaces différentes pour les utilisateurs authentifiés et anonymes, mais le contrôle LoginView peut également être utilisé pour afficher différentes balises basée sur les rôles de l’utilisateur. Nous allons utiliser un contrôle LoginView pour afficher des instructions différentes selon le rôle de l’utilisateur visite.

Démarrage en ajoutant un LoginView ci-dessus le `UserGrid` GridView. Comme nous l’avons indiqué précédemment, le contrôle LoginView a deux modèles intégrés : `AnonymousTemplate` et `LoggedInTemplate`. Entrez un bref message dans ces deux modèles qui informe l’utilisateur qu’ils ne peuvent pas modifier ou supprimer des informations utilisateur.

[!code-aspx[Main](role-based-authorization-vb/samples/sample9.aspx)]

Outre la `AnonymousTemplate` et `LoggedInTemplate`, le contrôle LoginView peut inclure *RoleGroups*, qui sont des modèles de rôle spécifique. Chaque RoleGroup contient une seule propriété, `Roles`, qui spécifie quels rôles le RoleGroup s’applique à. Le `Roles` propriété peut être définie à un rôle unique (par exemple, « administrateurs ») ou à une liste délimitée par des virgules des rôles (comme « administrateurs, par exemple »).

Pour gérer les RoleGroups, cliquez sur le lien « Modifier les RoleGroups » à partir de balise du contrôle active pour afficher l’éditeur de collections RoleGroup. Ajoutez deux RoleGroups de nouveau. Valeur de la première RoleGroup `Roles` à la propriété « Administrateurs » et la seconde à « Exemple ».


[![Gérer les modèles de spécifiques au rôle de le LoginView via l’éditeur de collections RoleGroup.](role-based-authorization-vb/_static/image23.png)](role-based-authorization-vb/_static/image22.png)

**Figure 8**: gestion spécifiques au rôle modèles via l’éditeur de la LoginView de collections RoleGroup ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image24.png))


Cliquez sur OK pour fermer l’éditeur de collections RoleGroup ; Cela met à jour le balisage déclaratif de la LoginView à inclure un `<RoleGroups>` section avec un `<asp:RoleGroup>` dans l’éditeur de collections RoleGroup défini par l’élément enfant pour chaque RoleGroup. En outre, la liste déroulante « Vues » liste dans les balises actives de la LoginView - qui initialement répertoriées uniquement le `AnonymousTemplate` et `LoggedInTemplate` – inclut désormais les RoleGroups ajoutés également.

Modifier les RoleGroups afin que les utilisateurs du rôle superviseurs sont les instructions affichées sur la façon de modifier les comptes d’utilisateur, tandis que les utilisateurs du rôle Administrateurs sont des instructions pour la modification et suppression. Après avoir apporté ces modifications, la balise déclarative de votre LoginView doit ressembler à ce qui suit.

[!code-aspx[Main](role-based-authorization-vb/samples/sample10.aspx)]

Après avoir apporté ces modifications, enregistrez la page, puis vous accédez il via un navigateur. Tout d’abord, visitez la page en tant qu’utilisateur anonyme. Vous devez s’afficher le message, « vous n’êtes pas connecté au système. Par conséquent, vous ne peut pas modifier ni supprimer toutes les informations utilisateur. » Connectez-vous en tant qu’un utilisateur authentifié, mais qui n’est ni dans le rôle superviseurs ni les administrateurs. Cette fois, vous devez voir le message « vous n’êtes pas membre des rôles superviseurs ou les administrateurs. Par conséquent, vous ne peut pas modifier ni supprimer toutes les informations utilisateur. »

Ensuite, connectez-vous en tant qu’utilisateur qui est membre du rôle superviseurs. Cette fois, vous devez voir les superviseurs spécifiques au rôle de message (voir la Figure 9). Et si vous vous connectez en tant qu’utilisateur dans les administrateurs de rôle, vous devez voir les administrateurs spécifiques au rôle de message (voir la Figure 10).


[![Bruce est affiché le Message spécifiques au rôle superviseurs](role-based-authorization-vb/_static/image26.png)](role-based-authorization-vb/_static/image25.png)

**Figure 9**: Bruce est affiché le Message spécifiques au rôle superviseurs ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image27.png))


[![Tito est affiché le Message spécifiques au rôle Administrateurs](role-based-authorization-vb/_static/image29.png)](role-based-authorization-vb/_static/image28.png)

**La figure 10**: Tito est affiché le Message spécifiques au rôle Administrateurs ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image30.png))


Comme les captures d’écran dans les Figures 9 et 10 afficher, le LoginView restitue uniquement un modèle, même si plusieurs modèles s’appliquent. Bruce et Tito sont à la fois les utilisateurs connectés, mais le LoginView affiche uniquement la correspondance RoleGroup et non le `LoggedInTemplate`. En outre, Tito appartient aux rôles à la fois les administrateurs et les superviseurs, mais le contrôle LoginView restitue le modèle de spécifiques au rôle Administrateurs au lieu des superviseurs une.

La figure 11 illustre le flux de travail permet de déterminer quel modèle de rendu par le contrôle LoginView. Notez que s’il existe plusieurs RoleGroup spécifié, le modèle LoginView restitue le *premier* RoleGroup correspondant. En d’autres termes, si nous avions placé le RoleGroup superviseurs en tant que la première RoleGroup et les administrateurs en tant que le second, puis lorsque Tito visité cette page il visualiserez le message par exemple.


[![Flux de travail du contrôle LoginView pour déterminer quel modèle de rendu](role-based-authorization-vb/_static/image32.png)](role-based-authorization-vb/_static/image31.png)

**Figure 11**: flux de travail du contrôle LoginView le pour déterminer quel modèle rendu ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image33.png))


### <a name="programmatically-limiting-functionality"></a>Limitant les fonctionnalités par programme

Tandis que le contrôle LoginView affiche des instructions différentes en fonction du rôle de l’utilisateur sur la page, les boutons Modifier et Annuler restent visibles par tous. Nous devons masquer par programmation les boutons Modifier et supprimer pour les visiteurs anonymes et les utilisateurs figurant dans le rôle Administrateurs ni superviseurs. Nous devons masquer le bouton Supprimer pour tous les utilisateurs n’est pas un administrateur. Pour ce faire, nous allons écrire un peu de code qui fait référence à par programmation de la CommandField modifier supprimer un LinkButton et jeux de leurs `Visible` propriétés `False`, si nécessaire.

Le moyen le plus simple pour référencer par programme des contrôles dans un CommandField doit tout d’abord de le convertir en un modèle. Pour ce faire, cliquez sur le lien « Modifier les colonnes » à partir de la balise active du GridView, sélectionner le CommandField à partir de la liste de champs actuels, cliquez sur le lien « Convertir ce champ en TemplateField ». Cela active le CommandField en TemplateField avec un `ItemTemplate` et `EditItemTemplate`. Le `ItemTemplate` contient le modifier et supprimer un LinkButton lors de la `EditItemTemplate` héberge la mise à jour et annuler le LinkButton.


[![Convertir le CommandField en TemplateField](role-based-authorization-vb/_static/image35.png)](role-based-authorization-vb/_static/image34.png)

**Figure 12**: convertir CommandField dans TemplateField un ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image36.png))


Mettre à jour le modifier et supprimer un LinkButton dans la `ItemTemplate`, le paramètre leurs `ID` les valeurs de propriétés `EditButton` et `DeleteButton`, respectivement.

[!code-aspx[Main](role-based-authorization-vb/samples/sample11.aspx)]

Chaque fois que les données sont liées au GridView, le contrôle GridView énumère les enregistrements dans son `DataSource` propriété et génère un correspondant `GridViewRow` objet. Comme chaque `GridViewRow` objet est créé, le `RowCreated` événement est déclenché. Pour masquer les boutons Modifier et supprimer des utilisateurs non autorisés, nous devons créer un gestionnaire d’événements pour cet événement et de référencer par programme le modifier et supprimer LinkButton, définir leurs `Visible` propriétés en conséquence.

Créer un gestionnaire d’événements le `RowCreated` événement, puis ajoutez le code suivant :

[!code-vb[Main](role-based-authorization-vb/samples/sample12.vb)]

N’oubliez pas que le `RowCreated` événement est déclenché pour *tous les* des lignes GridView, y compris l’en-tête, le pied de page, l’interface du récepteur de radiomessagerie et ainsi de suite. Nous voulons uniquement référencer par programme le modifier et supprimer un LinkButton si nous parlons d’une ligne de données, pas en mode d’édition (étant donné que la ligne en mode édition comporte des boutons de la mise à jour et annuler au lieu de modifier et supprimer). Cette vérification est gérée par le `If` instruction.

Si nous avons affaire à une ligne de données qui n’est pas en mode édition, le modifier et supprimer un LinkButton sont référencé et leurs `Visible` propriétés sont définies en fonction des valeurs booléennes retournées par le `User` l’objet `IsInRole(roleName)` (méthode). Le `User` objet fait référence à l’entité de sécurité créée par le `RoleManagerModule`; par conséquent, le `IsInRole(roleName)` méthode utilise l’API de rôles pour déterminer si l’utilisateur actuel appartient à *roleName*.

> [!NOTE]
> Nous pourrions avoir utilisé la classe des rôles directement, en remplaçant l’appel à `User.IsInRole(roleName)` avec un appel à la [ `Roles.IsUserInRole(roleName)` méthode](https://msdn.microsoft.com/en-us/library/system.web.security.roles.isuserinrole.aspx). J’ai décidé d’utiliser l’objet principal `IsInRole(roleName)` méthode dans cet exemple, car il est plus efficace que l’utilisation de l’API de rôles directement. Plus haut dans ce didacticiel, nous avons configuré le Gestionnaire de rôles pour mettre en cache des rôles de l’utilisateur dans un cookie. Cette mise en cache des données de cookie est utilisé uniquement lorsque l’entité de sécurité `IsInRole(roleName)` méthode est appelée ; les appels directs à l’API de rôles impliquent toujours un aller-retour vers le magasin de rôles. Même si les rôles ne sont pas mises en cache dans un cookie, l’appel de l’objet principal `IsInRole(roleName)` méthode est généralement plus efficace car lorsqu’elle est appelée pour la première fois pendant une demande il met en cache les résultats. L’API de rôles, en revanche, n’effectue aucune mise en cache. Étant donné que la `RowCreated` événement est déclenché une fois pour chaque ligne dans le GridView, à l’aide de `User.IsInRole(roleName)` implique qu’un seul voyage dans le magasin de rôle tandis que `Roles.IsUserInRole(roleName)` requiert *N* allers-retours, où *N* est le nombre de comptes d’utilisateur affiché dans la grille.


Le bouton de modification `Visible` est définie sur `True` si l’utilisateur visite de cette page est dans le rôle Administrateurs ou superviseurs ; sinon, elle a la valeur `False`. Le bouton de suppression `Visible` est définie sur `True` uniquement si l’utilisateur est dans le rôle Administrateurs.

Testez cette page via un navigateur. Si vous visitez la page sous la forme d’un visiteur anonyme ou un utilisateur qui n’est ni un superviseur ni un administrateur, le CommandField est vide. Il en existe, mais comme un éclat dynamique sans le modifier ou supprimer des boutons.

> [!NOTE]
> Il est possible de masquer le CommandField complètement quand un non-responsable et les non administrateurs visite à la page. J’ai laisser ce champ comme un exercice pour le lecteur.


[![La modifier et supprimer des boutons sont masqués pour Non-superviseurs et par les non-administrateurs](role-based-authorization-vb/_static/image38.png)](role-based-authorization-vb/_static/image37.png)

**Figure 13**: supprimer des boutons et modifier le sont masqués pour les non-administrateurs et les Non-par exemple ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image39.png))


Si un utilisateur qui appartient au rôle superviseurs (mais pas au rôle Administrateurs) visite, il voit uniquement le bouton Modifier.


[![Alors que le bouton Modifier est disponible pour par exemple, le bouton Supprimer est masqué.](role-based-authorization-vb/_static/image41.png)](role-based-authorization-vb/_static/image40.png)

**La figure 14**: alors que le bouton Modifier est disponible pour par exemple, le bouton Supprimer est masqué ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image42.png))


Et si un administrateur visite, elle a accès à ces deux boutons Modifier et supprimer.


[![La modifier et supprimer des boutons sont disponibles uniquement pour les administrateurs](role-based-authorization-vb/_static/image44.png)](role-based-authorization-vb/_static/image43.png)

**Figure 15**: modifier l’et supprimer des boutons sont disponibles uniquement pour les administrateurs ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image45.png))


## <a name="step-3-applying-role-based-authorization-rules-to-classes-and-methods"></a>Étape 3 : Application des règles d’autorisation basées sur le rôle aux Classes et méthodes

À l’étape 2, que nous avons limité modifier les fonctionnalités pour les utilisateurs dans les rôles superviseurs et les administrateurs et supprimer des fonctionnalités pour les administrateurs uniquement. Cela a été accompli en masquant les éléments d’interface utilisateur associé pour les utilisateurs non autorisés par le biais des techniques de programmation. Ces mesures ne garantissent pas qu’un utilisateur non autorisé ne pourront pas effectuer une action privilégiée. Il existe peut-être des éléments d’interface utilisateur qui sont ajoutées plus tard ou que nous avons oublié masquer des utilisateurs non autorisés. Ou bien, un pirate peut détecter une autre façon d’obtenir la page ASP.NET pour exécuter la méthode de votre choix.

Un moyen simple pour vous assurer qu’une partie spécifique des fonctionnalités ne sont pas accessibles par un utilisateur non autorisé consiste à décorer cette classe ou une méthode avec le [ `PrincipalPermission` attribut](https://msdn.microsoft.com/en-us/library/system.security.permissions.principalpermissionattribute.aspx). Lorsque le runtime .NET utilise une classe ou exécute l’une de ses méthodes, il vérifie que le contexte de sécurité actuel possède l’autorisation. Le `PrincipalPermission` attribut fournit un mécanisme par lequel nous pouvons définir ces règles.

Nous avons examiné à l’aide de la `PrincipalPermission` d’attribut dans le <a id="_msoanchor_9"> </a> [ *d’autorisation basée sur l’utilisateur* ](../membership/user-based-authorization-vb.md) didacticiel. Plus précisément, nous avons vu comment décorer du GridView `SelectedIndexChanged` et `RowDeleting` Gestionnaire d’événements afin qu’ils pourraient être exécutées uniquement par les utilisateurs authentifiés et Tito, respectivement. Le `PrincipalPermission` attribut fonctionne aussi bien avec les rôles.

Nous allons illustrent l’utilisation de la `PrincipalPermission` attribut dans le contrôle de GridView `RowUpdating` et `RowDeleting` gestionnaires d’événements pour interdire l’exécution pour les utilisateurs non autorisés. Il suffit de faire est ajouter l’attribut approprié en haut de chaque définition de fonction :

[!code-vb[Main](role-based-authorization-vb/samples/sample13.vb)]

L’attribut pour le `RowUpdating` Gestionnaire d’événements indique que seuls les utilisateurs aux rôles Administrateurs ou les superviseurs peuvent exécuter le Gestionnaire d’événements, alors que l’attribut sur le `RowDeleting` Gestionnaire d’événements limite l’exécution pour les utilisateurs dans les administrateurs rôle.


> [!NOTE]
> Le `PrincipalPermission` attribut est représenté comme une classe dans le `System.Security.Permissions` espace de noms. Veillez à ajouter un `Imports System.Security.Permissions` instruction en haut de votre fichier de classe code-behind pour importer cet espace de noms.


Si une certaine manière, un utilisateur non-administrateur tente d’exécuter le `RowDeleting` Gestionnaire d’événements ou si un non-superviseur ou non administrateur tente d’exécuter le `RowUpdating` Gestionnaire d’événements, le runtime .NET déclenchera un `SecurityException`.


[![Si le contexte de sécurité n’est pas autorisé à exécuter la méthode, une exception SecurityException est levée.](role-based-authorization-vb/_static/image47.png)](role-based-authorization-vb/_static/image46.png)

**Figure 16**: si le contexte de sécurité n’est pas autorisé à exécuter la méthode, un `SecurityException` est levée ([cliquez pour afficher l’image en taille réelle](role-based-authorization-vb/_static/image48.png))


En plus de pages ASP.NET, de nombreuses applications ont également une architecture qui inclut des différentes couches, telles que la logique métier et les couches d’accès aux données. Ces couches sont généralement implémentées en tant que bibliothèques de classes et offrent des classes et méthodes permettant d’effectuer des fonctionnalités d’entreprise logique et les données liées. Le `PrincipalPermission` attribut est utile pour appliquer des règles d’autorisation ainsi ces couches.

Pour plus d’informations sur l’utilisation de la `PrincipalPermission` attribut pour définir des règles d’autorisation sur les classes et méthodes, reportez-vous à [Scott Guthrie](https://weblogs.asp.net/scottgu/)d’entrée de blog [Ajout de règles d’autorisation à l’entreprise et les couches de données à l’aide de `PrincipalPermissionAttributes` ](https://weblogs.asp.net/scottgu/archive/2006/10/04/Tip_2F00_Trick_3A00_-Adding-Authorization-Rules-to-Business-and-Data-Layers-using-PrincipalPermissionAttributes.aspx).

## <a name="summary"></a>Résumé

Dans ce didacticiel, nous avons examiné comment spécifient grossiers et règles d’autorisation de granularité fine basée sur les rôles de l’utilisateur. ASP. La fonctionnalité d’autorisation de NET URL permet à un développeur de page spécifier les identités sont autorisées ou refuser l’accès à quelles pages. Comme nous l’avons vu dans la <a id="_msoanchor_10"> </a> [ *d’autorisation basée sur l’utilisateur* ](../membership/user-based-authorization-vb.md) (didacticiel), l’autorisation d’URL règles peuvent être appliquées sur une base par utilisateur. Il peuvent également être appliqués à la fois par rôle de rôle, comme nous l’avons vu à l’étape 1 de ce didacticiel.

Règles d’autorisation de granularité fine peuvent être appliquées de façon déclarative ou par programme. À l’étape 2, nous avons étudié à l’aide de la fonctionnalité de RoleGroups du contrôle LoginView pour afficher la sortie de différents basée sur les rôles de l’utilisateur visite. Nous avons également des méthodes pour déterminer par programme si un utilisateur appartient à un rôle spécifique et comment ajuster les fonctionnalités de la page en conséquence.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Ajout de règles d’autorisation à l’entreprise et les couches de données à l’aide`PrincipalPermissionAttributes`](https://weblogs.asp.net/scottgu/archive/2006/10/04/Tip_2F00_Trick_3A00_-Adding-Authorization-Rules-to-Business-and-Data-Layers-using-PrincipalPermissionAttributes.aspx)
- [Examen de ASP.NET de 2.0 l’appartenance, rôles et profil : utilisation des rôles](http://aspnet.4guysfromrolla.com/articles/121405-1.aspx)
- [Liste de questions de sécurité pour ASP.NET 2.0](https://msdn.microsoft.com/en-us/library/ms998375.aspx)
- [Documentation technique pour le `<roleManager>` élément](https://msdn.microsoft.com/en-us/library/ms164660.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements particuliers à...

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Réviseurs tête pour ce didacticiel incluent Suchi Banerjee et Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](assigning-roles-to-users-vb.md)
