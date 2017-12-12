---
uid: web-forms/overview/older-versions-security/membership/user-based-authorization-vb
title: "Autorisation basée sur l’utilisateur (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons examiner la limitation de l’accès aux pages et en limitant les fonctionnalités au niveau de la page par le biais de diverses techniques."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/18/2008
ms.topic: article
ms.assetid: bc937e9d-5c14-4fc4-aec7-440da924dd18
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/membership/user-based-authorization-vb
msc.type: authoredcontent
ms.openlocfilehash: 152b9a4b12f55bd999960568b11d08c96d342c03
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="user-based-authorization-vb"></a>Autorisation basée sur l’utilisateur (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/f/5/3f5a8605-c526-4b34-b3fd-a34167117633/ASPNET_Security_Tutorial_07_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/3/f/5/3f5a8605-c526-4b34-b3fd-a34167117633/aspnet_tutorial07_UserAuth_vb.pdf)

> Dans ce didacticiel, nous allons examiner la limitation de l’accès aux pages et en limitant les fonctionnalités au niveau de la page par le biais de diverses techniques.


## <a name="introduction"></a>Introduction

La plupart des applications web qui offrent des comptes d’utilisateur de le faire dans la partie de restreindre certains visiteurs d’accéder à certaines pages du site. Dans les sites messageboard plus en ligne, par exemple, tous les utilisateurs - anonymes ou authentifiés - sont en mesure d’afficher les publications de la messageboard, mais seuls les utilisateurs authentifiés peuvent visiter la page web pour créer une nouvelle publication. Et il peut y avoir des pages d’administration qui sont uniquement accessibles à un utilisateur particulier (ou un ensemble particulier d’utilisateurs). En outre, les fonctionnalités au niveau de la page peuvent être différent sur une base par utilisateur. Lorsque vous affichez une liste des publications, les utilisateurs authentifiés sont affichés une interface d’évaluation de chaque publication, tandis que cette interface n’est pas disponible pour les visiteurs anonymes.

ASP.NET rend facile de définir des règles d’autorisation basées sur l’utilisateur. Avec juste un peu de balisage dans `Web.config`, des pages web spécifiques ou des répertoires entiers peuvent être verrouillés afin qu’ils soient uniquement accessibles à un sous-ensemble spécifié d’utilisateurs. Les fonctionnalités au niveau des pages peuvent être activées ou désactivés en fonction de l’utilisateur actuellement connecté par des moyens déclaratives et par programmation.

Dans ce didacticiel, nous allons examiner la limitation de l’accès aux pages et en limitant les fonctionnalités au niveau de la page par le biais de diverses techniques. C’est parti !

## <a name="a-look-at-the-url-authorization-workflow"></a>Examiner le flux de travail d’autorisation URL

Comme indiqué dans le [ *une vue d’ensemble de l’authentification par formulaire* ](../introduction/an-overview-of-forms-authentication-vb.md) (didacticiel), lorsque le runtime ASP.NET traite une demande pour une ressource ASP.NET la demande déclenche un nombre d’événements pendant son cycle de vie. *Les Modules HTTP* sont des classes managées dont le code est exécuté en réponse à un événement spécifique dans le cycle de vie de demande. ASP.NET est fourni avec un nombre de Modules HTTP qui effectuent des tâches essentielles en arrière-plan.

Un tel HTTP Module est [ `FormsAuthenticationModule` ](https://msdn.microsoft.com/en-us/library/system.web.security.formsauthenticationmodule.aspx). Comme indiqué dans les didacticiels précédents, la fonction principale de la `FormsAuthenticationModule` consiste à déterminer l’identité de la requête actuelle. Cela s’effectue en inspectant le ticket d’authentification forms, qui est situé dans un cookie ou incorporé dans l’URL. Cette identification se produit pendant la [ `AuthenticateRequest` événement](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.authenticaterequest.aspx).

Un autre HTTP Module important est le [ `UrlAuthorizationModule` ](https://msdn.microsoft.com/en-us/library/system.web.security.urlauthorizationmodule.aspx), qui est déclenché en réponse à la [ `AuthorizeRequest` événement](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.authorizerequest.aspx) (ce qui se produit après la `AuthenticateRequest` événement). Le `UrlAuthorizationModule` examine le balisage de configuration dans `Web.config` pour déterminer si l’identité actuelle a autorité à visiter la page spécifiée. Ce processus est appelé *l’autorisation d’URL*.

Nous allons examiner la syntaxe pour les règles de l’autorisation d’URL à l’étape 1, mais tout d’abord nous allons examiner ce qui le `UrlAuthorizationModule` est selon si la demande est autorisée ou non. Si le `UrlAuthorizationModule` détermine que la demande est autorisée, puis elle ne fait rien et la demande se poursuit jusqu'à son cycle de vie. Toutefois, si la demande est *pas* autorisé, puis le `UrlAuthorizationModule` abandonne le cycle de vie et fait en sorte que le `Response` objet à retourner un [HTTP 401 non autorisé](http://www.checkupdown.com/status/E401.html) état. Lorsque vous utilisez l’authentification par formulaire cet état HTTP 401 n’est jamais retourné au client, car si le `FormsAuthenticationModule` détecte un HTTP 401 est de l’état modifie un [HTTP de redirection 302](http://www.checkupdown.com/status/E302.html) à la page de connexion.

La figure 1 illustre le flux de travail du pipeline ASP.NET, le `FormsAuthenticationModule`et le `UrlAuthorizationModule` lorsqu’une requête non autorisée arrive. En particulier, la Figure 1 illustre une demande émise par un visiteur anonyme pour `ProtectedPage.aspx`, qui est une page qui refuse l’accès aux utilisateurs anonymes. Étant donné que le visiteur est anonyme, le `UrlAuthorizationModule` interrompt la requête et retourne un état HTTP 401 non autorisé. Le `FormsAuthenticationModule` convertit ensuite l’état 401 une redirection 302 vers la page de connexion. Une fois que l’utilisateur est authentifié par la page de connexion, il est redirigé vers `ProtectedPage.aspx`. Cette fois le `FormsAuthenticationModule` identifie l’utilisateur en fonction de son ticket d’authentification. Maintenant que l’utilisateur est authentifié, le `UrlAuthorizationModule` permet l’accès à la page.


[![L’authentification par formulaire et le Workflow de l’autorisation d’URL](user-based-authorization-vb/_static/image2.png)](user-based-authorization-vb/_static/image1.png)

**Figure 1**: l’authentification de formulaires et de flux de travail URL d’autorisation ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image3.png))


La figure 1 illustre l’interaction qui se produit lorsqu’un visiteur anonyme tente d’accéder à une ressource qui n’est pas disponible aux utilisateurs anonymes. Dans ce cas, le visiteur anonyme est redirigé vers la page de connexion avec la page, qu'elle a tenté de visiter spécifié dans la chaîne de requête. Une fois que l’utilisateur se connecte, elle sera automatiquement redirigée vers la ressource, qu'elle a été initialement tentez de consulter.

Lorsque la demande non autorisée est effectuée par un utilisateur anonyme, ce flux de travail est simple et facile pour le visiteur de comprendre ce qui est arrivé et pourquoi. Mais gardez à l’esprit que le `FormsAuthenticationModule` redirige *tout* non autorisé d’utilisateur de la page de connexion, même si la demande est effectuée par un utilisateur authentifié. Cela peut entraîner une expérience utilisateur à confusion si un utilisateur authentifié tente de visiter une page pour laquelle il ne dispose pas d’autorité.

Imaginez que le site Web contenait ses règles d’autorisation de URL configurées telles que la page ASP.NET `OnlyTito.aspx` était accessible uniquement aux Tito. Maintenant, imaginez que Sam visite le site, ouvre une session, puis tente de visiter `OnlyTito.aspx`. Le `UrlAuthorizationModule` arrêtera le cycle de vie de requête et retourner un état HTTP 401 non autorisé, ce qui le `FormsAuthenticationModule` détectera et puis rediriger Sam vers la page de connexion. Étant donné que Sam est déjà connecté, cependant, elle demandez peut-être pourquoi elle a été envoyée à la page de connexion. Elle peut la raison que ses informations d’identification ont été perdues une certaine manière, ou qu’elle entré les informations d’identification non valides. Si Sam entrent à nouveau dans ses informations d’identification à partir de la page de connexion qu’elle sera connectée (nouveau) et redirigée vers `OnlyTito.aspx`. Le `UrlAuthorizationModule` détectera que Sam ne peut pas visitez cette page, et elle s’affichera à la page de connexion.

La figure 2 illustre ce flux de travail à confusion.


[![Le flux de travail par défaut peut entraîner un Cycle à confusion](user-based-authorization-vb/_static/image5.png)](user-based-authorization-vb/_static/image4.png)

**Figure 2**: le par défaut du flux de travail peut entraîner un Cycle de confusion ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image6.png))


Le workflow illustré dans la Figure 2 peut befuddle rapidement même la plupart des ordinateurs expérimenté visiteur. Nous allons examiner comment éviter cela déroutant cycle à l’étape 2.

> [!NOTE]
> ASP.NET utilise deux mécanismes pour déterminer si l’utilisateur actuel peut accéder à une page web spécifique : l’autorisation d’URL et l’autorisation de fichier. L’autorisation de fichier est implémentée par le [ `FileAuthorizationModule` ](https://msdn.microsoft.com/en-us/library/system.web.security.fileauthorizationmodule.aspx), qui détermine l’autorité en consultant les ou les fichiers requis ACL. L’autorisation de fichier est plus couramment utilisée avec l’authentification Windows, car les ACL sont des autorisations qui s’appliquent aux comptes Windows. Lorsque vous utilisez l’authentification par formulaire, toutes les demandes de niveau système système d’exploitation et le fichier sont exécutées par le même compte Windows, quel que soit l’utilisateur visite le site. Étant donné que cette série de didacticiels se concentre sur l’authentification par formulaire, nous ne décrivons pas l’autorisation de fichier.


### <a name="the-scope-of-url-authorization"></a>L’étendue d’autorisation d’URL

Le `UrlAuthorizationModule` est du code managé qui fait partie du runtime ASP.NET. Avant la version 7 de Microsoft [Internet Information Services (IIS)](https://www.iis.net/) serveur web, il était une barrière distincte entre le pipeline HTTP de d’IIS et le pipeline du runtime ASP.NET. En résumé, dans IIS 6 et versions antérieures, ASP. De NET `UrlAuthorizationModule` s’exécute uniquement lorsqu’une demande est déléguée à partir de IIS à l’exécution d’ASP.NET. Par défaut, IIS traite le contenu statique lui-même - telles que des pages HTML et CSS, JavaScript et les fichiers image - et transmet uniquement les demandes pour le runtime ASP.NET lorsqu’une page avec une extension `.aspx`, `.asmx`, ou `.ashx` est demandé.

IIS 7, toutefois, permet intégré IIS et ASP.NET pipelines. Avec quelques paramètres de configuration, vous pouvez configurer IIS 7 pour appeler le `UrlAuthorizationModule` pour *tous les* demandes, c'est-à-dire que les règles d’autorisation d’URL peuvent être définies pour les fichiers de n’importe quel type. En outre, IIS 7 inclut son propre moteur d’autorisation d’URL. Pour plus d’informations sur l’intégration d’ASP.NET et les fonctionnalités d’autorisation URL native d’IIS 7, consultez [comprendre l’autorisation d’URL IIS7](https://www.iis.net/articles/view.aspx/IIS7/Managing-IIS7/Configuring-Security/URL-Authorization/Understanding-IIS7-URL-Authorization). Pour une plus approfondies sur l’intégration d’ASP.NET et IIS 7, ils récupèrent une copie du livre de Shahram Khosravi *Professionnel IIS 7 et ASP.NET intégré programmation* (ISBN : 978-0470152539).

En résumé, dans les versions antérieures d’IIS 7, règles d’autorisation d’URL sont appliquées uniquement aux ressources gérées par le runtime ASP.NET. Mais avec IIS 7, il est possible d’utiliser native fonctionnalité de l’autorisation d’URL d’IIS ou intégrer ASP. De NET `UrlAuthorizationModule` dans le pipeline HTTP d’IIS ainsi étendre cette fonctionnalité pour toutes les demandes.

> [!NOTE]
> Il existe des différences subtiles néanmoins importantes dans la manière dont ASP. Du NET `UrlAuthorizationModule` et la fonctionnalité de l’autorisation d’URL IIS 7 traiter les règles d’autorisation. Ce didacticiel n’examine pas les fonctionnalités d’autorisation d’URL IIS 7 ou les différences dans la façon dont il analyse les règles d’autorisation par rapport à la `UrlAuthorizationModule`. Pour plus d’informations sur ces sujets, reportez-vous à la documentation de IIS 7 sur MSDN ou à [www.iis.net](https://www.iis.net/).


## <a name="step-1-defining-url-authorization-rules-inwebconfig"></a>Étape 1 : Définir des règles d’autorisation d’URL dans`Web.config`

Le `UrlAuthorizationModule` détermine s’il faut accorder ou refuser l’accès à une ressource demandée pour une identité particulière en fonction des règles d’autorisation URL définis dans la configuration de l’application. Les règles d’autorisation sont précisés dans le [ `<authorization>` élément](https://msdn.microsoft.com/en-us/library/8d82143t.aspx) sous la forme de `<allow>` et `<deny>` des éléments enfants. Chaque `<allow>` et `<deny>` élément enfant peut spécifier :

- Un utilisateur particulier
- Une liste délimitée par des virgules des utilisateurs
- Tous les utilisateurs anonymes, représentées par un point d’interrogation ( ?)
- Tous les utilisateurs, signalés par un astérisque (\*)

Le balisage suivant illustre comment utiliser les règles d’autorisation d’URL pour autoriser les utilisateurs Tito et Scott et refuser tous les autres :

[!code-xml[Main](user-based-authorization-vb/samples/sample1.xml)]

Le `<allow>` élément définit ce que les utilisateurs sont autorisés - Tito et Scott - tandis que le `<deny>` élément qui fait en sorte que *tous les* refusé aux utilisateurs.

> [!NOTE]
> Le `<allow>` et `<deny>` éléments peuvent également spécifier des règles d’autorisation pour les rôles. Nous allons examiner l’autorisation basée sur les rôles dans un didacticiel futures.


Le paramètre suivant accorde l’accès à une autre personne que Sam (y compris les visiteurs anonymes) :

[!code-xml[Main](user-based-authorization-vb/samples/sample2.xml)]

Pour autoriser uniquement les utilisateurs authentifiés, utilisez la configuration suivante, qui refuse l’accès à tous les utilisateurs anonymes :

[!code-xml[Main](user-based-authorization-vb/samples/sample3.xml)]

Les règles d’autorisation sont définis dans le `<system.web>` élément `Web.config` et s’appliquent à toutes les ressources dans l’application web ASP.NET. Souvent, une application possède des règles d’autorisation différentes pour différentes sections. Par exemple, sur un site de commerce électronique, tous les visiteurs peuvent consulter les produits, consultez les évaluations de produits, le catalogue de recherche et ainsi de suite. Toutefois, seuls les utilisateurs authentifiés peuvent atteindre la récupération ou les pages pour gérer l’historique de l’envoi. En outre, il peut y avoir des portions du site qui sont uniquement accessibles par sélectionner les utilisateurs, telles que les administrateurs de site.

ASP.NET rend facile de définir des règles d’autorisation différentes pour différents fichiers et dossiers sur le site. Les règles d’autorisation spécifiés dans le dossier racine `Web.config` fichier s’appliquent à toutes les ressources ASP.NET dans le site. Toutefois, ces paramètres d’autorisation par défaut peuvent être substitués pour un dossier spécifique en ajoutant un `Web.config` avec un `<authorization>` section.

Nous allons mettre à jour notre site Web afin que seuls les utilisateurs authentifiés peuvent visiter les pages ASP.NET dans le `Membership` dossier. Pour cela nous devons ajouter une `Web.config` le fichier le `Membership` dossier et définissez ses paramètres d’autorisation Refuser à des utilisateurs anonymes. Cliquez sur le `Membership` dossier dans l’Explorateur de solutions, choisissez le menu Ajouter un nouvel élément dans le menu contextuel, ajoutez un nouveau fichier de Configuration Web nommé `Web.config`.


[![Ajouter un fichier Web.config dans le dossier de l’appartenance](user-based-authorization-vb/_static/image8.png)](user-based-authorization-vb/_static/image7.png)

**Figure 3**: ajouter un `Web.config` de fichiers à la `Membership` dossier ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image9.png))


À ce stade votre projet doit contenir deux `Web.config` fichiers : un dans le répertoire racine et l’autre dans le `Membership` dossier.


[![Votre Application doit maintenant contenir deux fichiers Web.config](user-based-authorization-vb/_static/image11.png)](user-based-authorization-vb/_static/image10.png)

**Figure 4**: votre Application doit maintenant contenir deux `Web.config` fichiers ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image12.png))


Mettre à jour le fichier de configuration dans le `Membership` dossier afin qu’il interdit l’accès aux utilisateurs anonymes.

[!code-xml[Main](user-based-authorization-vb/samples/sample4.xml)]

C’est tout cela !

Pour tester cette modification, visitez la page d’accueil dans un navigateur et vérifiez que vous êtes connecté. Étant donné que le comportement par défaut d’une application ASP.NET consiste à autoriser tous les visiteurs, et étant donné que nous n’avez pas apporter de modifications d’autorisation à la racine du `Web.config` fichier, nous sommes en mesure de consulter les fichiers dans le répertoire racine en tant qu’un visiteur anonyme.

Cliquez sur le lien de création de comptes d’utilisateur trouvé dans la colonne de gauche. Vous accéderez à la `~/Membership/CreatingUserAccounts.aspx`. Étant donné que la `Web.config` de fichiers dans le `Membership` dossier définit des règles d’autorisation pour interdire l’accès anonyme, la `UrlAuthorizationModule` interrompt la requête et retourne un état HTTP 401 non autorisé. Le `FormsAuthenticationModule` modifie cet aspect à un état de redirection 302, nous envoyer à la page de connexion. Notez que la page nous étions tente d’accéder à (`CreatingUserAccounts.aspx`) est passée à la page de connexion via le `ReturnUrl` paramètre querystring.


[![Depuis l’URL d’autorisation règles interdire l’accès anonyme, nous allons redirigés vers la Page de connexion](user-based-authorization-vb/_static/image14.png)](user-based-authorization-vb/_static/image13.png)

**Figure 5**: étant donné que l’autorisation d’URL règles interdire l’accès anonyme, nous allons redirigés vers la Page de connexion ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image15.png))


Lors de la connexion avec succès, nous allons redirigés vers le `CreatingUserAccounts.aspx` page. Cette fois le `UrlAuthorizationModule` permet l’accès à la page, car nous ne sont plus anonymes.

### <a name="applying-url-authorization-rules-to-a-specific-location"></a>Application des règles d’autorisation d’URL à un emplacement spécifique

Les paramètres d’autorisation définis dans le `<system.web>` section de `Web.config` s’appliquent à toutes les ressources ASP.NET dans ce répertoire et ses sous-répertoires (jusqu'à ce que sinon substituée par un autre `Web.config` fichier). Cependant, dans certains cas, nous voulons que toutes les ressources ASP.NET dans un répertoire ayant une configuration d’autorisation spécifique à l’exception d’une ou deux pages spécifiques. Cela peut être obtenue en ajoutant un `<location>` élément `Web.config`, qu’il pointe vers le fichier dont les règles d’autorisation sont différents et définir ses règles d’autorisation unique dans celui-ci.

Pour illustrer l’utilisation de la `<location>` élément remplacent les paramètres de configuration pour une ressource spécifique, nous allons personnaliser les paramètres d’autorisation afin que seuls Tito peut visiter `CreatingUserAccounts.aspx`. Pour ce faire, ajoutez un `<location>` élément à la `Membership` du dossier `Web.config` de fichiers et de mettre à jour de son balisage afin qu’il ressemble à ce qui suit :

[!code-xml[Main](user-based-authorization-vb/samples/sample5.xml)]

Le `<authorization>` élément `<system.web>` définit les règles d’autorisation URL par défaut pour les ressources ASP.NET dans le `Membership` dossier et ses sous-dossiers. Le `<location>` élément permet de remplacer ces règles pour une ressource particulière. Dans le balisage ci-dessus le `<location>` références de l’élément le `CreatingUserAccounts.aspx` page et spécifie son autorisation règles telle que Tito, mais refuser tout le monde.

Pour tester cette modification d’autorisation, démarrez en visitant le site Web en tant qu’utilisateur anonyme. Si vous essayez de visiter une page dans le `Membership` dossier, tel que `UserBasedAuthorization.aspx`, le `UrlAuthorizationModule` refuse la demande et vous allez être redirigé vers la page de connexion. Une fois connecté, comme par exemple, Scott, vous pouvez visiter n’importe quelle page dans le `Membership` dossier *sauf* pour `CreatingUserAccounts.aspx`. Tentative de visiter `CreatingUserAccounts.aspx` connecté en tant que tout le monde mais Tito entraîne une tentative d’accès non autorisé, vous rediriger vers la page de connexion.

> [!NOTE]
> Le `<location>` doit apparaître en dehors de la configuration du `<system.web>` élément. Vous devez recourir à un `<location>` élément pour chaque ressource dont vous souhaitez remplacer les paramètres d’autorisation.


### <a name="a-look-at-how-theurlauthorizationmoduleuses-the-authorization-rules-to-grant-or-deny-access"></a>Regardez comment la`UrlAuthorizationModule`utilise les règles d’autorisation pour accorder ou refuser l’accès

Le `UrlAuthorizationModule` détermine si pour autoriser une identité particulière d’une URL particulière en analysant l’autorisation d’URL des règles à la fois, en commençant à partir du premier et l’utilisation de sa manière vers le bas. Dès qu’une correspondance est trouvée, l’utilisateur est accordé ou refusé, selon que la correspondance a été trouvée dans un `<allow>` ou `<deny>` élément. **Si aucune correspondance n’est trouvée, l’utilisateur est autorisé à accéder.** Par conséquent, si vous souhaitez restreindre l’accès, il est impératif d’utiliser un `<deny>` élément comme dernier élément dans la configuration de l’autorisation d’URL. **Si vous omettez un ***`<deny>`*** élément, tous les utilisateurs auront accès.**

Pour mieux comprendre le processus utilisé par le `UrlAuthorizationModule` pour déterminer l’autorité, prenons l’exemple de règles d’autorisation d’URL, nous avons étudié plus haut dans cette étape. La première règle est un `<allow>` élément qui permet d’accéder à Tito et Scott. Les règles des deuxième est un `<deny>` élément qui refuse l’accès à tout le monde. Si la visite d’un utilisateur anonyme, le `UrlAuthorizationModule` commence par la demande, est anonyme Scott ou Tito ? La réponse est évidemment, non, il se poursuit pour la deuxième règle. Est anonyme dans l’ensemble de tout le monde ? Depuis la réponse est Oui, le `<deny>` règle est placée en vigueur et de l’utilisateur est redirigé vers la page de connexion. De même, si la visite de Jisun, le `UrlAuthorizationModule` commence par la demande, est le Jisun Scott ou Tito ? Dans la mesure où elle n’est pas, la `UrlAuthorizationModule` passe à la deuxième question, est de Jisun dans l’ensemble de tout le monde ? Elle est, elle, trop, l’accès est refusée. Enfin, si Tito visite, la première question posé par la `UrlAuthorizationModule` étant une réponse affirmative, Tito est autorisé à accéder.

Étant donné que la `UrlAuthorizationModule` processus de l’autorisation de règles à partir du haut vers le bas, l’arrêt à n’importe quel type de correspondance, il est important que les règles les plus spécifiques précéder les moins spécifiques. Autrement dit, pour définir des règles d’autorisation qui interdit pas Jisun et les utilisateurs anonymes, mais autorisent tous les autres utilisateurs authentifiés, vous démarrer avec la règle la plus spécifique - Jisun ayant un effet sur un seul - et alors les règles les moins spécifiques - celles permettant de tous les autres les utilisateurs authentifiés, mais le refus de tous les utilisateurs anonymes. Les règles d’autorisation URL suivants implémente cette stratégie en refus Jisun d’abord, puis en refuser tout utilisateur anonyme. Tout utilisateur authentifié que Jisun est autorisé, car aucune de ces `<deny>` instructions correspondra.

[!code-xml[Main](user-based-authorization-vb/samples/sample6.xml)]

## <a name="step-2-fixing-the-workflow-for-unauthorized-authenticated-users"></a>Étape 2 : Résolution du flux de travail pour les utilisateurs non autorisés, authentifiées

Comme expliqué précédemment dans ce didacticiel dans le A Examinez la section de Workflow de l’autorisation d’URL, à tout moment une requête non autorisée apparaît, le `UrlAuthorizationModule` interrompt la requête et retourne un état HTTP 401 non autorisé. Cet état 401 est modifié par le `FormsAuthenticationModule` dans un 302 rediriger l’état qui envoie à l’utilisateur à la page de connexion. Ce flux de travail se produit sur toute demande non autorisée, même si l’utilisateur est authentifié.

Retour d’un utilisateur authentifié à la page de connexion est susceptible de les confondre, car ils ont déjà connecté au système. Avec un peu de travail, nous pouvons améliorer ce flux de travail en redirigeant les utilisateurs authentifiés, qui effectuent des demandes non autorisées à une page qui explique qu’ils ont essayé d’accéder à une page à accès restreint.

Commencez par créer une nouvelle page ASP.NET dans le dossier racine de l’application web nommé `UnauthorizedAccess.aspx`; n’oubliez pas d’associer cette page avec le `Site.master` page maître. Après la création de cette page, supprimez le contrôle de contenu qui fait référence à la `LoginContent` ContentPlaceHolder afin que la page maître contenu par défaut s’affichera. Ensuite, ajoutez un message qui explique la situation, à savoir que l’utilisateur a tenté d’accéder à une ressource protégée. Après avoir ajouté un tel message, le `UnauthorizedAccess.aspx` balisage déclaratif de la page doit ressembler à ce qui suit :

[!code-aspx[Main](user-based-authorization-vb/samples/sample7.aspx)]

Nous devons maintenant modifier le flux de travail afin que si une requête non autorisée est effectuée par un utilisateur authentifié, elles sont envoyées à la `UnauthorizedAccess.aspx` page au lieu de la page de connexion. La logique qui redirige les demandes non autorisées vers la page de connexion est comprise dans une méthode privée de la `FormsAuthenticationModule` classe, afin que nous ne pouvons pas personnaliser ce comportement. Ce que nous pouvons faire, toutefois, consiste à ajouter un notre propre logique pour la page de connexion qui redirige l’utilisateur à `UnauthorizedAccess.aspx`, si nécessaire.

Lorsque le `FormsAuthenticationModule` redirige un visiteur non autorisé à la page de connexion, il ajoute l’URL demandée, non autorisé à la chaîne de requête avec le nom `ReturnUrl`. Par exemple, si un utilisateur non autorisé a tenté de visiter `OnlyTito.aspx`, le `FormsAuthenticationModule` redirige vers `Login.aspx?ReturnUrl=OnlyTito.aspx`. Par conséquent, si la page de connexion a été atteint par un utilisateur authentifié avec une chaîne de requête qui inclut la `ReturnUrl` paramètre, puis nous savoir que cet utilisateur non authentifié a tenté de simplement à visiter une page, elle n’est pas autorisée à afficher. Dans ce cas, nous souhaitons lui permet de rediriger `UnauthorizedAccess.aspx`.

Pour ce faire, ajoutez le code suivant à la page de connexion `Page_Load` Gestionnaire d’événements :

[!code-vb[Main](user-based-authorization-vb/samples/sample8.vb)]

Le code ci-dessus redirige les utilisateurs authentifiés, non autorisés au `UnauthorizedAccess.aspx` page. Pour voir cette logique dans action, visitez le site en tant qu’un visiteur anonyme, puis cliquez sur le lien de création de comptes utilisateur dans la colonne de gauche. Vous accéderez à la `~/Membership/CreatingUserAccounts.aspx` page qui, à l’étape 1, nous avons configuré uniquement autoriser l’accès à Tito. Étant donné que les utilisateurs anonymes sont interdites, le `FormsAuthenticationModule` nous redirige vers la page de connexion.

À ce stade, nous sont anonymes, de sorte que `Request.IsAuthenticated` retourne `False` et nous ne sommes pas redirigés vers `UnauthorizedAccess.aspx`. Au lieu de cela, la page de connexion s’affiche. Ouvrez une session en tant qu’utilisateur autre que Tito, telles que Bruce. Après avoir entré les informations d’identification appropriées, la page de connexion nous redirige vers `~/Membership/CreatingUserAccounts.aspx`. Toutefois, étant donné que cette page est uniquement accessible aux Tito, nous sont non autorisés pour l’afficher et sont directement retournés à la page de connexion. Cette fois, toutefois, `Request.IsAuthenticated` retourne `True` (et `ReturnUrl` paramètre querystring existe), de sorte que nous sommes redirigés vers le `UnauthorizedAccess.aspx` page.


[![Authentifié, les utilisateurs non autorisés sont redirigées vers UnauthorizedAccess.aspx](user-based-authorization-vb/_static/image17.png)](user-based-authorization-vb/_static/image16.png)

**Figure 6**: authentifié, les utilisateurs non autorisés sont redirigées vers `UnauthorizedAccess.aspx` ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image18.png))


Ce flux de travail personnalisé présente une expérience utilisateur plus simple et pratique court-circuit le cycle mentionné dans la Figure 2.

## <a name="step-3-limiting-functionality-based-on-the-currently-logged-in-user"></a>Étape 3 : Limitant les fonctionnalités en fonction de l’utilisateur actuellement connecté

Autorisation d’URL rend facile de spécifier des règles d’autorisation grossière. Comme nous l’avons vu à l’étape 1, avec l’autorisation d’URL nous pouvons succincte les identités sont autorisées et ceux non autorisés à afficher une page spécifique ou toutes les pages dans un dossier. Toutefois, dans certains scénarios, nous pouvons adopter autoriser tous les utilisateurs à visiter une page, mais limiter les fonctionnalités de la page en fonction de l’utilisateur à visiter.

Prenons le cas d’un site Web de commerce électronique qui permet des visiteurs authentifiés passer en revue leurs produits. Lorsqu’un utilisateur anonyme visite la page d’un produit, ils visualiserez uniquement les informations de produit et la possibilité de laisser une revue pas est données. Toutefois, un utilisateur authentifié sur la même page verriez l’interface de révision. Si l’utilisateur authentifié n’a pas encore consulté ce produit, l’interface leur permettant de soumettre une révision ; dans le cas contraire, il serait les afficher leur révision précédemment soumis. Pour prendre ce scénario en outre, la page du produit peut afficher des informations supplémentaires et offrent des fonctionnalités étendues pour les utilisateurs qui travaillent pour la société de commerce électronique. Par exemple, la page du produit peut répertorier l’inventaire en stock et incluent des options pour modifier le prix du produit et une description lorsque visité par un employé.

Ces règles d’autorisation de granularité fine peuvent être implémentés de façon déclarative ou par programme (ou via une combinaison des deux). Dans la section suivante, nous verrons comment implémenter l’autorisation de granularité fine via le contrôle LoginView. Après cela, nous allons examiner les techniques de programmation. Avant que nous pouvons examiner d’appliquer des règles d’autorisation de granularité fine, toutefois, nous devez d’abord créer une page dont la fonctionnalité dépend de l’utilisateur à visiter.

Nous allons créer une page qui répertorie les fichiers dans un répertoire particulier dans un GridView. En même temps que l’affichage de la liste nom de chaque fichier, de taille et d’autres informations, le contrôle GridView inclut deux colonnes de LinkButton : un intitulé vue et supprimez intitulé. Si un clic sur le bouton de lien de vue, le contenu du fichier sélectionné s’affichera ; Si l’utilisateur clique sur le bouton de lien Supprimer, le fichier sera supprimé. Nous allons créer initialement cette page de telle sorte que ses fonctionnalités d’affichage et de suppression ne soient disponible pour tous les utilisateurs. Dans la les sections de contrôle LoginView et limitant par programme les fonctionnalités que nous allons voir comment activer ou désactiver ces fonctionnalités en fonction de l’utilisateur sur la page.

> [!NOTE]
> Nous sommes sur le point de créer la page ASP.NET utilise un contrôle GridView pour afficher une liste de fichiers. Depuis ce didacticiel série se concentre sur l’authentification par formulaire, l’autorisation, les comptes d’utilisateurs et rôles, je ne souhaite pas passer trop de temps d’aborder le fonctionnement interne du contrôle GridView. Alors que ce didacticiel fournit des instructions spécifiques pour la configuration de cette page, il ne pas étudier en détail pourquoi certains choix ont été apportées, ou ce qui ont les propriétés particulières d’effet sur la sortie rendue. Pour un examen approfondi du contrôle GridView, consultez Mes  *[utilisation des données dans ASP.NET 2.0](../../data-access/index.md)*  série de didacticiels.


Commencez par ouvrir le `UserBasedAuthorization.aspx` de fichiers dans le `Membership` dossier et l’ajout d’un contrôle GridView à la page nommée `FilesGrid`. À partir de la balise de GridView, cliquez sur le lien Modifier les colonnes pour ouvrir la boîte de dialogue champs. À ce stade, désactivez les générer automatiquement des champs dans l’angle inférieur gauche. Ensuite, ajoutez un bouton de sélection et deux BoundFields un bouton Supprimer à partir de l’angle supérieur gauche (les boutons Sélectionner et supprimer se trouve dans le type CommandField). Définir le bouton de sélection `SelectText` à la vue et de la première BoundField `HeaderText` et `DataField` propriétés nom. Définir le BoundField deuxième `HeaderText` propriété à la taille en octets, son `DataField` propriété de longueur, son `DataFormatString` propriété {0 : N0} et son `HtmlEncode` propriété sur False.

Après avoir configuré les colonnes du contrôle de GridView, cliquez sur OK pour fermer la boîte de dialogue champs. À partir de la fenêtre Propriétés, définissez du GridView `DataKeyNames` propriété `FullName`. À ce stade balisage déclaratif du GridView doit se présenter comme suit :

[!code-aspx[Main](user-based-authorization-vb/samples/sample9.aspx)]

Avec une balise de GridView créé, nous sommes prêts à écrire le code qui Récupère les fichiers dans un répertoire particulier et les lier au contrôle GridView. Ajoutez le code suivant à la page `Page_Load` Gestionnaire d’événements :

[!code-vb[Main](user-based-authorization-vb/samples/sample10.vb)]

Le code ci-dessus utilise la [ `DirectoryInfo` classe](https://msdn.microsoft.com/en-us/library/system.io.directoryinfo.aspx) pour obtenir une liste des fichiers dans le dossier racine de l’application. Le [ `GetFiles()` méthode](https://msdn.microsoft.com/en-us/library/system.io.directoryinfo.getfiles.aspx) retourne tous les fichiers dans le répertoire sous forme de tableau de [ `FileInfo` objets](https://msdn.microsoft.com/en-us/library/system.io.fileinfo.aspx), qui est ensuite lié au GridView. Le `FileInfo` objet possède un large éventail de propriétés, telles que `Name`, `Length`, et `IsReadOnly`, entre autres. Comme vous pouvez le voir à partir de son balisage déclaratif, le contrôle GridView affiche simplement la `Name` et `Length` propriétés.

> [!NOTE]
> Le `DirectoryInfo` et `FileInfo` classes se trouvent dans le [ `System.IO` espace de noms](https://msdn.microsoft.com/en-us/library/system.io.aspx). Par conséquent, vous aurez besoin de faire précéder les noms de classe par leurs noms d’espace de noms ou disposer de l’espace de noms importé dans le fichier de classe (via `Imports System.IO`).


Prenez un moment pour consulter cette page via un navigateur. Il affiche la liste des fichiers se trouvant dans le répertoire racine de l’application. Cliquez simplement sur la vue ou de supprimer le LinkButton provoque une publication (postback), mais aucune action ne se produit, car nous avons encore pour créer les gestionnaires d’événements nécessaires.


[![Le contrôle GridView répertorie les fichiers dans le répertoire racine de l’Application Web](user-based-authorization-vb/_static/image20.png)](user-based-authorization-vb/_static/image19.png)

**Figure 7**: le contrôle GridView répertorie les fichiers dans le répertoire racine de l’Application Web ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image21.png))


Nous avons besoin d’un moyen d’afficher le contenu du fichier sélectionné. Revenez à Visual Studio et ajoutez une zone de texte nommée `FileContents` ci-dessus le GridView. Définir son `TextMode` propriété `MultiLine` et son `Columns` et `Rows` propriétés à 95 % et 10, respectivement.

[!code-aspx[Main](user-based-authorization-vb/samples/sample11.aspx)]

Ensuite, créez un gestionnaire d’événements pour le contrôle du GridView [ `SelectedIndexChanged` événements](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.selectedindexchanged.aspx) et ajoutez le code suivant :

[!code-vb[Main](user-based-authorization-vb/samples/sample12.vb)]

Ce code utilise le contrôle de GridView `SelectedValue` propriété pour déterminer le nom de fichier complet du fichier sélectionné. En interne, le `DataKeys` collection est référencée afin d’obtenir le `SelectedValue`, il est impératif que vous définissez de GridView `DataKeyNames` propriété par nom, comme décrit précédemment dans cette étape. Le [ `File` classe](https://msdn.microsoft.com/en-us/library/system.io.file.aspx) est utilisé pour lire le contenu du fichier sélectionné dans une chaîne, qui est ensuite assigné à la `FileContents` la zone de texte `Text` propriété, ce qui affiche le contenu du fichier sélectionné dans la page.


[![Contenu du fichier sélectionné est affiché dans la zone de texte](user-based-authorization-vb/_static/image23.png)](user-based-authorization-vb/_static/image22.png)

**Figure 8**: contenu du fichier sélectionné est affiché dans la zone de texte ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image24.png))


> [!NOTE]
> Si vous affichez le contenu d’un fichier qui contient le balisage HTML, puis essayez d’afficher ou supprimer un fichier, vous recevrez un `HttpRequestValidationException` erreur. Cela se produit, car lors de la publication le contenu de la zone de texte est envoyés sur le serveur web. Par défaut, ASP.NET déclenche un `HttpRequestValidationException` erreur chaque fois que le contenu de publication (postback) potentiellement dangereux, telles que des balises HTML, est détecté. Pour désactiver cette erreur ne se produise, désactiver la validation de la demande pour la page en ajoutant `ValidateRequest="false"` à le `@Page` la directive. Pour plus d’informations sur les avantages de la validation de la demande comme ainsi que les précautions, vous devez prendre lorsque la désactivation, lire [Validation de la demande - empêche les attaques de Script](https://asp.net/learn/whitepapers/request-validation/).


Enfin, ajoutez un gestionnaire d’événements par le code suivant pour le contrôle du GridView [ `RowDeleting` événement](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.gridview.rowdeleting.aspx):

[!code-vb[Main](user-based-authorization-vb/samples/sample13.vb)]

Le code affiche simplement le nom complet du fichier à supprimer dans le `FileContents` zone de texte *sans* réellement supprimer le fichier.


[![En cliquant sur le bouton de suppression ne supprime pas réellement le fichier](user-based-authorization-vb/_static/image26.png)](user-based-authorization-vb/_static/image25.png)

**Figure 9**: sur la suppression bouton ne supprime ne pas réellement le fichier ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image27.png))


À l’étape 1, nous avons configuré les règles d’autorisation URL pour interdire aux utilisateurs anonymes à partir de l’affichage des pages dans le `Membership` dossier. Afin de mieux présentent l’authentification de granularité fine, nous allons autoriser les utilisateurs anonymes à visiter le `UserBasedAuthorization.aspx` page, mais avec des fonctionnalités limitées. Pour ouvrir cette page accessible par tous les utilisateurs, ajoutez le code suivant `<location>` élément à la `Web.config` de fichiers dans le `Membership` dossier :

[!code-xml[Main](user-based-authorization-vb/samples/sample14.xml)]

Après avoir ajouté ce `<location>` élément, tester les règles d’autorisation de nouvelles URL en ouvrant une session sur le site. En tant qu’utilisateur anonyme vous devez être autorisées à visiter le `UserBasedAuthorization.aspx` page.

Actuellement, tout utilisateur authentifié ou anonyme peut visiter le `UserBasedAuthorization.aspx` page et d’afficher ou de supprimer des fichiers. Nous allons faire en sorte que seuls les utilisateurs authentifiés peuvent afficher le contenu d’un fichier et Tito uniquement peut supprimer un fichier. Ces règles de d’autorisation de granularité fine peuvent être appliquées de manière déclarative, par programme ou par une combinaison des deux méthodes. Nous allons utiliser l’approche déclarative pour limiter qui peut afficher le contenu d’un fichier. Nous allons utiliser l’approche programmatique pour limiter les utilisateurs permettre supprimer un fichier.

### <a name="using-the-loginview-control"></a>L’utilisation du contrôle LoginView

Comme nous l’avons vu dans ces didacticiels, le contrôle LoginView est utile pour afficher différentes interfaces pour les utilisateurs authentifiés et anonymes et offre un moyen aisé de masquer des fonctionnalités qui ne sont pas accessibles aux utilisateurs anonymes. Étant donné que les utilisateurs anonymes ne peuvent pas afficher ou supprimer des fichiers, nous avons besoin afficher uniquement les `FileContents` zone de texte lorsque la page est visitée par un utilisateur authentifié. Pour ce faire, ajoutez un contrôle LoginView à la page, nommez-le `LoginViewForFileContentsTextBox`et déplacer le `FileContents` balisage déclaratif de la zone de texte dans le contrôle de LoginView `LoggedInTemplate`.

[!code-aspx[Main](user-based-authorization-vb/samples/sample15.aspx)]

Les contrôles Web dans les modèles de la LoginView ne sont plus directement accessibles à partir de la classe code-behind. Par exemple, le `FilesGrid` du GridView `SelectedIndexChanged` et `RowDeleting` gestionnaires d’événements font actuellement référencent le `FileContents` telles que le contrôle de zone de texte avec le code :

[!code-aspx[Main](user-based-authorization-vb/samples/sample16.aspx)]

Toutefois, ce code n’est plus valide. En déplaçant le `FileContents` zone de texte dans le `LoggedInTemplate` la zone de texte ne sont pas accessibles directement. Au lieu de cela, nous devons utiliser la `FindControl("controlId")` méthode référencer le contrôle par programme. Mise à jour le `FilesGrid` gestionnaires d’événements pour faire référence à la zone de texte comme suit :

[!code-vb[Main](user-based-authorization-vb/samples/sample17.vb)]

Après le déplacement de la zone de texte pour le LoginView `LoggedInTemplate` et mettre à jour le code de la page à la zone de texte à l’aide de référence du `FindControl("controlId")` de modèle, consultez la page en tant qu’utilisateur anonyme. Comme le montre la Figure 10, la `FileContents` zone de texte n’est pas affichée. Toutefois, la vue LinkButton reste affiché.


[![Le contrôle LoginView affiche uniquement la zone de texte FileContents pour les utilisateurs authentifiés](user-based-authorization-vb/_static/image29.png)](user-based-authorization-vb/_static/image28.png)

**La figure 10**: le LoginView contrôle uniquement restitue le `FileContents` zone de texte pour les utilisateurs authentifiés ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image30.png))


Une pour masquer le bouton d’affichage pour les utilisateurs anonymes consiste à convertir le champ GridView en TemplateField. Cette opération génère un modèle qui contient le balisage déclaratif pour le bouton de lien de vue. Nous pouvons ensuite ajouter un contrôle LoginView au TemplateField et placer le LinkButton au sein de la LoginView `LoggedInTemplate`, ce qui le masquage le bouton d’affichage des visiteurs anonymes. Pour ce faire, cliquez sur le lien Modifier les colonnes à partir de balise du contrôle de GridView active pour lancer la boîte de dialogue champs. Ensuite, sélectionnez le bouton de sélection dans la liste dans l’angle inférieur gauche et puis cliquez sur le convertir ce champ en TemplateField lien. Cela modifiera le balisage déclaratif du champ à partir de :

[!code-aspx[Main](user-based-authorization-vb/samples/sample18.aspx)]

 À : 

[!code-aspx[Main](user-based-authorization-vb/samples/sample19.aspx)]

 À ce stade, nous pouvons ajouter un LoginView pour le TemplateField. Le balisage suivant affiche la vue LinkButton uniquement pour les utilisateurs authentifiés. 

[!code-aspx[Main](user-based-authorization-vb/samples/sample20.aspx)]

Comme le montre la Figure 11, le résultat final n’est pas que plutôt que la vue colonne est toujours affichée même si la vue LinkButton dans la colonne sont masqués. Nous allons examiner comment masquer la colonne entière de GridView (et pas seulement le LinkButton) dans la section suivante.


[![Le contrôle LoginView masque la vue LinkButton pour les visiteurs anonymes](user-based-authorization-vb/_static/image32.png)](user-based-authorization-vb/_static/image31.png)

**Figure 11**: le contrôle LoginView masque la vue LinkButton pour les visiteurs anonymes ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image33.png))


### <a name="programmatically-limiting-functionality"></a>Limitant les fonctionnalités par programme

Dans certains cas, les techniques déclaratives sont insuffisantes pour limitant les fonctionnalités à une page. Par exemple, la disponibilité de certaines fonctionnalités de page peut être dépendante de critères au-delà de si l’utilisateur visite de la page est anonymes ou authentifiés. Dans ce cas, les différents éléments d’interface utilisateur peuvent être affichés ou masqués moyens par programme.

Pour limiter les fonctionnalités par programme, nous devons effectuer deux tâches :

1. Déterminer si l’utilisateur visite de la page peut accéder à la fonctionnalité, et
2. Modifier par programmation l’interface utilisateur selon si l’utilisateur a accès à la fonctionnalité en question.

Pour illustrer l’application de ces deux tâches, nous allons autoriser Tito supprimer des fichiers du contrôle GridView. Notre première tâche, est ensuite, pour déterminer si elle est Tito sur la page. Une fois que qui a été déterminé, nous devons masquer (ou afficher) supprimer une colonne de GridView. Les colonnes du GridView sont accessibles via son `Columns` propriété ; une colonne est affichée uniquement si son `Visible` est définie sur `True` (la valeur par défaut).

Ajoutez le code suivant à la `Page_Load` Gestionnaire d’événements avant la liaison des données au contrôle GridView :

[!code-vb[Main](user-based-authorization-vb/samples/sample21.vb)]

Comme expliqué dans la [ *une vue d’ensemble de l’authentification par formulaire* ](../introduction/an-overview-of-forms-authentication-vb.md) didacticiel, `User.Identity.Name` retourne le nom de l’identité. Cela correspond au nom d’utilisateur entré dans le contrôle de connexion. Si elle est Tito visite de la page, deuxième colonne du GridView `Visible` est définie sur `True`; sinon, elle est définie sur `False`. Il en résulte que lorsque quelqu'un d’autre que Tito visite de la page, soit un autre utilisateur authentifié ou un utilisateur anonyme, la colonne de suppression n’est restituée (voir Figure 12) ; Toutefois, lorsque Tito visite de la page, la colonne de suppression est présente (voir la Figure 13).


[![La colonne de supprimer n’est pas rendue lorsque visité par quelqu'un d’autre que Tito (par exemple, Bruce)](user-based-authorization-vb/_static/image35.png)](user-based-authorization-vb/_static/image34.png)

**Figure 12**: la colonne à supprimer est pas rendu lorsque visité par quelqu'un d’autre que Tito (par exemple, Bruce) ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image36.png))


[![La suppression d’une colonne est rendue pour Tito](user-based-authorization-vb/_static/image38.png)](user-based-authorization-vb/_static/image37.png)

**Figure 13**: la colonne à supprimer est rendue pour Tito ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image39.png))


## <a name="step-4-applying-authorization-rules-to-classes-and-methods"></a>Étape 4 : Application des règles d’autorisation à des Classes et méthodes

À l’étape 3, nous interdit les utilisateurs anonymes d’afficher du contenu d’un fichier et interdit tous les utilisateurs mais Tito à partir de la suppression de fichiers. Cela a été accompli en masquant les éléments d’interface utilisateur associé pour les visiteurs non autorisés par le biais des techniques déclaratives et par programme. Pour notre exemple, correctement masquer les éléments d’interface utilisateur était simple, mais qu’en est-il des sites plus complexes où il peut y avoir différentes façons d’effectuer les mêmes fonctionnalités ? Limiter cette fonctionnalité aux utilisateurs non autorisés, que se passe-t-il si nous oubliez pas de masquer ou de désactiver tous les éléments d’interface utilisateur applicable ?

Un moyen simple pour vous assurer qu’une partie spécifique des fonctionnalités ne sont pas accessibles par un utilisateur non autorisé consiste à décorer cette classe ou une méthode avec le [ `PrincipalPermission` attribut](https://msdn.microsoft.com/en-us/library/system.security.permissions.principalpermissionattribute.aspx). Lorsque le runtime .NET utilise une classe ou exécute l’une de ses méthodes, il vérifie que le contexte de sécurité actuel est autorisé à utiliser la classe ou d’exécuter la méthode. Le `PrincipalPermission` attribut fournit un mécanisme par lequel nous pouvons définir ces règles.

Nous allons illustrent l’utilisation de la `PrincipalPermission` attribut dans le contrôle de GridView `SelectedIndexChanged` et `RowDeleting` gestionnaires d’événements pour interdire l’exécution par les utilisateurs anonymes et les utilisateurs, hormis Tito, respectivement. Il suffit de faire est ajouter l’attribut approprié en haut de chaque définition de fonction :

[!code-vb[Main](user-based-authorization-vb/samples/sample22.vb)]

L’attribut pour le `SelectedIndexChanged` détermine de gestionnaire d’événements que seuls les utilisateurs authentifiés peut exécuter le Gestionnaire d’événements, alors que l’attribut sur le `RowDeleting` Gestionnaire d’événements limite l’exécution à Tito.

> [!NOTE]
> Un attribut peut être appliqué à une classe, méthode, propriété ou l’événement. Lorsque vous ajoutez un attribut, il doit faire partie de la classe, une méthode, une propriété ou une instruction de déclaration d’événement. Étant donné que Visual Basic utilise des sauts de ligne comme séparateurs d’instruction, les attributs doivent apparaître sur la même ligne que la déclaration ou juste au-dessus de lui avec un caractère de continuation de ligne (le caractère de soulignement). Dans l’extrait de code ci-dessus, le caractère de continuation de ligne est utilisé pour placer l’attribut sur une ligne et la déclaration de méthode sur un autre.


Si une certaine manière, un utilisateur autre que Tito tente d’exécuter le `RowDeleting` Gestionnaire d’événements ou un utilisateur non authentifié tente d’exécuter le `SelectedIndexChanged` Gestionnaire d’événements, le runtime .NET génère un `SecurityException`.


[![Si le contexte de sécurité n’est pas autorisé à exécuter la méthode, une exception SecurityException est levée.](user-based-authorization-vb/_static/image41.png)](user-based-authorization-vb/_static/image40.png)

**La figure 14**: si le contexte de sécurité n’est pas autorisé à exécuter la méthode, un `SecurityException` est levée ([cliquez pour afficher l’image en taille réelle](user-based-authorization-vb/_static/image42.png))


> [!NOTE]
> Pour permettre à plusieurs contextes de sécurité accéder à une classe ou une méthode, décorez la classe ou une méthode avec un `PrincipalPermission` attribut pour chaque contexte de sécurité. Autrement dit, pour autoriser Tito et Bruce d’exécuter le `RowDeleting` Gestionnaire d’événements, ajoutez *deux* `PrincipalPermission` attributs :


[!code-vb[Main](user-based-authorization-vb/samples/sample23.vb)]

En plus de pages ASP.NET, de nombreuses applications ont également une architecture qui inclut des différentes couches, telles que la logique métier et les couches d’accès aux données. Ces couches sont généralement implémentées en tant que bibliothèques de classes et offrent des classes et méthodes permettant d’effectuer des fonctionnalités d’entreprise logique et les données liées. Le `PrincipalPermission` attribut est utile pour appliquer des règles d’autorisation à ces couches.

Pour plus d’informations sur l’utilisation de la `PrincipalPermission` attribut pour définir des règles d’autorisation sur les classes et méthodes, reportez-vous à [Scott Guthrie](https://weblogs.asp.net/scottgu/)d’entrée de blog [Ajout de règles d’autorisation à l’entreprise et les couches de données à l’aide de `PrincipalPermissionAttributes` ](https://weblogs.asp.net/scottgu/archive/2006/10/04/Tip_2F00_Trick_3A00_-Adding-Authorization-Rules-to-Business-and-Data-Layers-using-PrincipalPermissionAttributes.aspx).

## <a name="summary"></a>Résumé

Dans ce didacticiel, nous avons étudié comment appliquer des règles d’autorisation basées sur l’utilisateur. Nous avons commencé par un examen ASP. Structure de NET URL d’autorisation. Sur chaque demande, le moteur ASP.NET `UrlAuthorizationModule` inspecte les règles de l’autorisation d’URL définis dans la configuration de l’application pour déterminer si l’identité est autorisée à accéder à la ressource demandée. En bref, l’autorisation d’URL rend facile de spécifier des règles d’autorisation pour une page spécifique ou pour toutes les pages dans un répertoire particulier.

L’infrastructure de l’autorisation d’URL applique des règles d’autorisation sur une base de page par page. Avec autorisation d’URL, soit l’identité du demandeuse est autorisée à accéder à une ressource particulière ou non. Beaucoup de scénarios, cependant, appeler pour plusieurs règles d’autorisation de granularité fine. Au lieu de la définition qui est autorisé à accéder à une page, nous pouvons vous devez laisser tout le monde à accéder à une page, mais pour afficher des données différentes ou offrent des fonctionnalités différentes en fonction de l’utilisateur sur la page. Autorisation au niveau de la page implique généralement le masquage des éléments d’interface utilisateur spécifique afin d’empêcher les utilisateurs non autorisés d’accéder aux fonctionnalités interdites. En outre, il est possible d’utiliser des attributs pour restreindre l’accès aux classes et l’exécution de ses méthodes pour certains utilisateurs.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Ajout de règles d’autorisation à l’entreprise et les couches de données à l’aide`PrincipalPermissionAttributes`](https://weblogs.asp.net/scottgu/archive/2006/10/04/Tip_2F00_Trick_3A00_-Adding-Authorization-Rules-to-Business-and-Data-Layers-using-PrincipalPermissionAttributes.aspx)
- [Autorisation ASP.NET](https://msdn.microsoft.com/en-us/library/wce3kxhd.aspx)
- [Modifications apportées entre la sécurité IIS6 et IIS7](https://www.iis.net/articles/view.aspx/IIS7/Managing-IIS7/Configuring-Security/Changes-between-IIS6-and-IIS7-Security)
- [Configuration des fichiers et sous-répertoires](https://msdn.microsoft.com/en-us/library/6hbkh9s7.aspx)
- [Limitant les fonctionnalités de Modification de données en fonction de l’utilisateur](../../data-access/editing-inserting-and-deleting-data/limiting-data-modification-functionality-based-on-the-user-vb.md)
- [Démarrages rapides du contrôle LoginView](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/ctrlref/login/loginview.aspx)
- [Présentation de l’autorisation d’URL IIS7](https://www.iis.net/articles/view.aspx/IIS7/Managing-IIS7/Configuring-Security/URL-Authorization/Understanding-IIS7-URL-Authorization)
- [`UrlAuthorizationModule`Documentation technique](https://msdn.microsoft.com/en-us/library/system.web.security.urlauthorizationmodule.aspx)
- [Utilisation des données dans ASP.NET 2.0](../../data-access/index.md)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4GuysFromRolla.com).

>[!div class="step-by-step"]
[Précédent](validating-user-credentials-against-the-membership-user-store-vb.md)
[Suivant](storing-additional-user-information-vb.md)
