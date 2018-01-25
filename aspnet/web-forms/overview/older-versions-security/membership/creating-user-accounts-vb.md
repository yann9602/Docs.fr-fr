---
uid: web-forms/overview/older-versions-security/membership/creating-user-accounts-vb
title: "Création de comptes d’utilisateur (VB) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons examiner à l’aide de l’infrastructure d’appartenance (via SqlMembershipProvider) pour créer de nouveaux comptes d’utilisateur. Nous allons voir comment créer de nouveaux nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/18/2008
ms.topic: article
ms.assetid: 9ef3e893-bebe-4b13-9fe5-8b71720dd85e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/membership/creating-user-accounts-vb
msc.type: authoredcontent
ms.openlocfilehash: 61621ffaae98ac74c16b2ff014ba9d85c2c10b3a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="creating-user-accounts-vb"></a>Création de comptes d’utilisateur (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/f/5/3f5a8605-c526-4b34-b3fd-a34167117633/ASPNET_Security_Tutorial_05_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/3/f/5/3f5a8605-c526-4b34-b3fd-a34167117633/aspnet_tutorial05_CreatingUsers_vb.pdf)

> Dans ce didacticiel, nous allons examiner à l’aide de l’infrastructure d’appartenance (via SqlMembershipProvider) pour créer de nouveaux comptes d’utilisateur. Nous allons voir comment créer de nouveaux utilisateurs par programme et via les pages ASP. Contrôle intégré CreateUserWizard du réseau.


## <a name="introduction"></a>Introduction

Dans le <a id="_msoanchor_1"> </a> [didacticiel précédent](creating-the-membership-schema-in-sql-server-vb.md) nous installé le schéma de services d’application dans une base de données ajouté les tables, vues et requises par des procédures stockées de la `SqlMembershipProvider` et `SqlRoleProvider`. Cela créé l’infrastructure que nous aurons besoin pour le reste des didacticiels de cette série. Dans ce didacticiel, nous allons examiner à l’aide de l’infrastructure d’appartenance (via la `SqlMembershipProvider`) pour créer de nouveaux comptes d’utilisateur. Nous allons voir comment créer de nouveaux utilisateurs par programme et via les pages ASP. Contrôle intégré CreateUserWizard du réseau.

En plus de découvrir comment créer de nouveaux comptes d’utilisateur, vous devrez également mettre à jour le site Web démonstration que nous avons créés dans le  *<a id="_msoanchor_2"> </a> [une vue d’ensemble de l’authentification par formulaire](../introduction/an-overview-of-forms-authentication-vb.md)*  didacticiel et puis améliorée dans les  *<a id="_msoanchor_3"> </a> [Configuration de l’authentification de formulaires et les rubriques avancées](../introduction/forms-authentication-configuration-and-advanced-topics-vb.md)*  didacticiel. Notre exemple d’application web possède une page de connexion qui valide les informations d’identification des utilisateurs contre les paires codées en dur nom d’utilisateur/mot de passe. En outre, `Global.asax` inclut du code qui crée personnalisé `IPrincipal` et `IIdentity` objets pour les utilisateurs authentifiés. Nous mettrons à jour la page de connexion pour valider les informations d’identification des utilisateurs par rapport à l’infrastructure d’appartenance et de supprimer la logique personnalisée de principal et identity.

C’est parti !

## <a name="the-forms-authentication-and-membership-checklist"></a>L’authentification par formulaire et la liste de vérification de l’appartenance

Avant de commencer à travailler avec l’infrastructure de l’appartenance, prenons un moment pour passer en revue les étapes importantes, que nous avons pris pour atteindre ce point. Lors de l’utilisation de l’infrastructure d’appartenance avec le `SqlMembershipProvider` dans un scénario de l’authentification basée sur les formulaires, les étapes suivantes doivent être effectuées avant d’implémenter des fonctionnalités d’appartenance dans votre application web :

1. **Activer l’authentification basée sur des formulaires.** Comme expliqué dans  *<a id="_msoanchor_4"> </a> [une vue d’ensemble de l’authentification par formulaire](../introduction/an-overview-of-forms-authentication-vb.md)*, l’authentification par formulaire est activée en modifiant `Web.config` et en définissant le `<authentication>` l’élément `mode` attribut `Forms`. Avec l’authentification par formulaire activée, chaque demande entrante est examinée par un *ticket d’authentification de formulaires*, qui, le cas échéant, identifie le demandeur.
2. **Ajouter le schéma de services d’application à la base de données approprié.** Lorsque vous utilisez la `SqlMembershipProvider` nous devons installer le schéma de services d’application à une base de données. Généralement, ce schéma est ajouté à la même base de données qui contient le modèle de données de l’application. Le  *<a id="_msoanchor_5"> </a> [création du schéma de l’appartenance dans SQL Server](creating-the-membership-schema-in-sql-server-vb.md)*  didacticiel examiné à l’aide de la `aspnet_regsql.exe` outil pour y parvenir.
3. **Personnaliser les paramètres de l’Application Web pour faire référence à la base de données de l’étape 2.** Le *création du schéma de l’appartenance dans SQL Server* didacticiel ont montré de deux façons de configurer l’application web afin que les `SqlMembershipProvider` utiliserait la base de données sélectionnée à l’étape 2 : en modifiant le `LocalSqlServer` nom de chaîne de connexion ; ou en ajoutant un nouveau fournisseur inscrit à la liste des fournisseurs d’appartenances framework et en personnalisant ce nouveau fournisseur pour utiliser la base de données à partir de l’étape 2.

Lorsque vous créez une application web qui utilise le `SqlMembershipProvider` et l’authentification basée sur les formulaires, vous devez effectuer ces trois étapes avant d’utiliser la `Membership` classe ou les contrôles Web d’ouverture de session ASP.NET. Étant donné que nous avons déjà effectué ces étapes dans les didacticiels précédents, nous sommes prêts à démarrer à l’aide de l’infrastructure d’appartenance !

## <a name="step-1-adding-new-aspnet-pages"></a>Étape 1 : Ajout de nouvelles Pages ASP.NET

Dans ce didacticiel et les trois suivants, nous étudierons différents d’appartenance des fonctions et fonctionnalités. Nous devons une série de pages ASP.NET pour implémenter les rubriques examinées dans l’ensemble de ces didacticiels. Nous allons créer ces pages, puis un fichier de mappage de site `(Web.sitemap)`.

Commencez par créer un nouveau dossier dans le projet nommé `Membership`. Ensuite, ajoutez cinq nouvelles pages ASP.NET à la `Membership` dossier, lier chaque page avec le `Site.master` page maître. Nommez les pages :

- `CreatingUserAccounts.aspx`
- `UserBasedAuthorization.aspx`
- `EnhancedCreateUserWizard.aspx`
- `AdditionalUserInfo.aspx`
- `Guestbook.aspx`

À ce stade l’Explorateur de votre projet de solutions doit ressembler à l’écran : WordGame indiqué dans la Figure 1.


[![Cinq nouvelles Pages ont été ajoutés dans le dossier de l’appartenance](creating-user-accounts-vb/_static/image2.png)](creating-user-accounts-vb/_static/image1.png)

**Figure 1**: cinq nouvelles Pages ont été ajoutés à la `Membership` dossier ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image3.png))


Chaque page doit avoir à ce stade, les deux contrôles de contenu, une pour chaque ContentPlaceHolders de la page maître : `MainContent` et `LoginContent`.

[!code-aspx[Main](creating-user-accounts-vb/samples/sample1.aspx)]

N’oubliez pas que le `LoginContent` balisage de valeur par défaut du ContentPlaceHolder affiche un lien pour se connecter ou se déconnecter du site, en fonction de l’utilisateur authentifié. La présence de la `Content2` contrôle de contenu, toutefois, substitue balisage par défaut de la page maître. Comme expliqué dans  *<a id="_msoanchor_6"> </a> [une vue d’ensemble de l’authentification par formulaire](../introduction/an-overview-of-forms-authentication-vb.md)*  (didacticiel), cela est utile dans les pages où nous ne souhaitez pas afficher les options de connexion dans la colonne de gauche.

Pour ces cinq pages, toutefois, nous souhaitons afficher les marques de valeur par défaut de la page maître pour la `LoginContent` ContentPlaceHolder. Par conséquent, supprimez le balisage déclaratif pour la `Content2` contrôle de contenu. Après cela, chaque du balisage de la page cinq doit contenir qu’un contrôle de contenu.

## <a name="step-2-creating-the-site-map"></a>Étape 2 : Création du mappage de Site

Toutefois, les sites Web insignifiant devoir implémenter une certaine forme d’une interface utilisateur de navigation. L’interface utilisateur de navigation peut être une simple liste de liens vers les différentes sections du site. Vous pouvez également ces liens peuvent être réorganisées dans les menus ou les vues de l’arborescence. Les développeurs de pages, création de l’interface utilisateur de navigation est seulement la moitié de l’article. Nous devons également un moyen de définir la structure du site logique de manière facile à gérer et mettre à jour. Que de nouvelles pages sont ajoutés ou supprimés des pages existantes, vous voulez être en mesure de mettre à jour une seule source - le plan de site - et répercuter les modifications sur l’interface d’utilisateur de navigation du site.

Ces deux tâches - définissant le plan de site et l’implémentation d’une interface utilisateur de navigation selon le plan de site - sont faciles à accomplir grâce à l’infrastructure de plan de Site et la Navigation Web contrôle ajouté dans ASP.NET version 2.0. L’infrastructure de plan de Site permet à un développeur pour définir un plan de site, puis à y accéder via une API de programmation (le [ `SiteMap` classe](https://msdn.microsoft.com/library/system.web.sitemap.aspx)). Inclure des contrôles de Navigation Web intégrés un [contrôle Menu](https://msdn.microsoft.com/library/bz09dy46.aspx), le [contrôle TreeView](https://msdn.microsoft.com/library/3eafky27.aspx)et le [contrôle SiteMapPath](https://msdn.microsoft.com/library/3eafky27.aspx).

Comme les infrastructures d’appartenance et les rôles, l’infrastructure de plan de Site est construit sur le [modèle de fournisseur](http://aspnet.4guysfromrolla.com/articles/101905-1.aspx). Le travail de la classe de fournisseur de plan de Site consiste à générer la structure de la mémoire utilisée par le `SiteMap` classe à partir d’un magasin de données persistantes, comme un fichier XML ou une table de base de données. Le .NET Framework est fourni avec un fournisseur de plan de Site par défaut qui lit les données de plan de site à partir d’un fichier XML ([`XmlSiteMapProvider`](https://msdn.microsoft.com/library/system.web.xmlsitemapprovider.aspx)), et il s’agit du fournisseur, nous utiliserons dans ce didacticiel. Pour certaines implémentations de fournisseur autre plan de Site, consultez la section Documentation supplémentaire à la fin de ce didacticiel.

Le fournisseur de plan de Site par défaut attend un fichier XML correctement mis en forme nommé `Web.sitemap` existe le répertoire racine. Étant donné que nous utilisons ce fournisseur par défaut, nous devons ajouter ce type de fichier et de définir la structure de la carte de site dans le format XML approprié. Pour ajouter le fichier, avec le bouton droit sur le nom du projet dans l’Explorateur de solutions et choisissez Ajouter un nouvel élément. À partir de la boîte de dialogue Choisir d’ajouter un fichier de type de plan de Site nommé `Web.sitemap`.


[![Ajoutez un fichier nommé Web.sitemap vers le répertoire du projet racine](creating-user-accounts-vb/_static/image5.png)](creating-user-accounts-vb/_static/image4.png)

**Figure 2**: ajouter un fichier nommé `Web.sitemap` vers le répertoire du projet racine ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image6.png))


Le fichier de mappage de site XML définit la structure du site Web comme une hiérarchie. Cette relation hiérarchique est modélisée dans le fichier XML via l’ascendance de la `<siteMapNode>` éléments. Le `Web.sitemap` doit commencer par une `<siteMap>` nœud parent qui a précisément un `<siteMapNode>` enfant. Ce niveau supérieur `<siteMapNode>` élément représente la racine de la hiérarchie et peut avoir un nombre arbitraire de nœuds descendants. Chaque `<siteMapNode>` élément doit inclure un `title` d’attribut et peut éventuellement inclure `url` et `description` attributs, entre autres ; chaque vide `url` attribut doit être unique.

Entrez le code XML suivant dans le `Web.sitemap` fichier :

[!code-xml[Main](creating-user-accounts-vb/samples/sample2.xml)]

Le balisage de mappage de site ci-dessus définit la hiérarchie affichée dans la Figure 3.


[![Le plan de Site représente une Structure hiérarchique de navigation](creating-user-accounts-vb/_static/image8.png)](creating-user-accounts-vb/_static/image7.png)

**Figure 3**: le plan de Site représente une Structure hiérarchique de navigation ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image9.png))


## <a name="step-3-updating-the-master-page-to-include-a-navigational-user-interface"></a>Étape 3 : Mise à jour de la Page maître pour inclure une Interface utilisateur de navigation

ASP.NET inclut un nombre de contrôles Web navigation pour la conception d’une interface utilisateur. Celles-ci incluent le Menu, TreeView et les contrôles SiteMapPath. Les contrôles Menu et TreeView restituent respectivement, la structure du plan de site dans un menu ou une arborescence, tandis que le contrôle SiteMapPath affiche une barre de navigation qui affiche le nœud actuel qui est visité, ainsi que ses ancêtres. Les données de plan de site peut être lié à d’autres données à l’aide de SiteMapDataSource de contrôles Web et sont accessibles par programme le `SiteMap` classe.

Une discussion approfondie de l’infrastructure de plan de Site et les contrôles de Navigation étant dépasse le cadre de cette série de didacticiels, plutôt que de passer du temps élaborer votre propre interface utilisateur de navigation nous allons à la place emprunter celui utilisé dans mon  *[ Utilisation des données dans ASP.NET 2.0](../../data-access/index.md)*  série de didacticiels, qui utilise un contrôle de répéteur pour afficher une liste à puces de profondeur à deux des liens de navigation, comme illustré dans la Figure 4.

### <a name="adding-a-two-level-list-of-links-in-the-left-column"></a>Ajout d’une liste de deux niveaux de liens dans la colonne de gauche

Pour créer cette interface, ajoutez le balisage déclaratif suivant à la `Site.master` colonne gauche de la page maître où le texte TODO : Menu sera insérée ici... réside actuellement.

[!code-aspx[Main](creating-user-accounts-vb/samples/sample3.aspx)]

Le balisage ci-dessus lie un contrôle de répéteur nommé `menu` à un SiteMapDataSource, qui retourne la hiérarchie de plan de site définie dans `Web.sitemap`. Depuis le contrôle de SiteMapDataSource [ `ShowStartingNode` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.sitemapdatasource.showstartingnode.aspx) est définie sur False, il commence à renvoyer la hiérarchie du plan de site en commençant par les descendants du nœud d’accueil. Répéteur affiche chacun de ces nœuds (actuellement uniquement appartenance) dans un `<li>` élément. Répéteur interne et un autre affiche alors les enfants du nœud actuel dans une liste non triée imbriquée.

La figure 4 montre la sortie de rendu du balisage ci-dessus avec la structure du plan de site que nous avons créé à l’étape 2. Répéteur restitue le balisage de la liste non triée vanille ; les règles de feuille de style en cascade définies dans `Styles.css` sont chargés de la mise en page esthétiques. Pour obtenir une description plus détaillée du fonctionne de la balise ci-dessus, reportez-vous à la [les Pages maîtres et Navigation du Site](https://asp.net/learn/data-access/tutorial-03-vb.aspx) didacticiel.


[![L’Interface utilisateur de navigation est restitué à l’aide d’imbriqué non triée de listes](creating-user-accounts-vb/_static/image11.png)](creating-user-accounts-vb/_static/image10.png)

**Figure 4**: l’Interface utilisateur de navigation est restitué à l’aide d’imbriqué non ordonnée des listes ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image12.png))


### <a name="adding-breadcrumb-navigation"></a>Ajout d’une Navigation de la barre de navigation

En plus de la liste de liens dans la colonne de gauche, nous allons également avoir chaque affichage de la page un [fil d’Ariane](http://en.wikipedia.org/wiki/Breadcrumb_%28navigation%29). Une barre de navigation est un élément d’interface utilisateur de navigation qui vous indique rapidement les utilisateurs à leur position actuelle dans la hiérarchie du site. Le contrôle SiteMapPath utilise l’infrastructure de plan de Site pour déterminer l’emplacement de la page active dans le plan de site, puis affiche une barre de navigation en fonction de ces informations.

En particulier, ajoutez un `<span>` élément à l’en-tête de la page maître `<div>` élément et définir le nouveau `<span>` l’élément `class` d’attribut à la barre de navigation. (La `Styles.css` classe contient une règle pour une classe de barre de navigation.) Ensuite, ajoutez un SiteMapPath à ce nouvel `<span>` élément.

[!code-aspx[Main](creating-user-accounts-vb/samples/sample4.aspx)]

La figure 5 illustre la sortie de la SiteMapPath lors de la visite `~/Membership/CreatingUserAccounts.aspx`.


[![La barre de navigation affiche la Page actuelle et de mappent de ses ancêtres du site](creating-user-accounts-vb/_static/image14.png)](creating-user-accounts-vb/_static/image13.png)

**Figure 5**: la barre de navigation affiche la Page actuelle et ses ancêtres dans le plan de Site ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image15.png))


## <a name="step-4-removing-the-custom-principal-and-identity-logic"></a>Étape 4 : Suppression de l’objet Principal personnalisé et la logique de l’identité

Dans le  *<a id="_msoanchor_7"> </a> [Configuration de l’authentification de formulaires et les rubriques avancées](../introduction/forms-authentication-configuration-and-advanced-topics-vb.md)*  didacticiel, nous avons vu comment associer des objets principal et identity personnalisés à l’utilisateur authentifié. Nous avons accompli cela en créant un gestionnaire d’événements dans `Global.asax` pour l’application `PostAuthenticateRequest` événement qui se déclenche après la `FormsAuthenticationModule` a authentifié l’utilisateur. Dans ce gestionnaire d’événements, nous avons remplacé la `GenericPrincipal` et `FormsIdentity` objets ajoutés par le `FormsAuthenticationModule` avec la `CustomPrincipal` et `CustomIdentity` objets nous créé dans ce didacticiel.

Tandis que les objets principal et identity personnalisés sont utiles dans certains scénarios, dans la plupart des cas le `GenericPrincipal` et `FormsIdentity` objets sont suffisantes. Par conséquent, je pense qu’il serait utile de rétablir le comportement par défaut. Apporter cette modification en supprimant ou en commentant le `PostAuthenticateRequest` Gestionnaire d’événements ou en supprimant le `Global.asax` fichier entièrement.

> [!NOTE]
> Une fois que vous avez commenté ou supprimé le code dans `Global.asax`, vous devez mettre en commentaire le code dans `Default.aspx's` classe code-behind qui effectue un cast de le `User.Identity` propriété un `CustomIdentity` instance.


## <a name="step-5-programmatically-creating-a-new-user"></a>Étape 5 : Création d’un utilisateur par programme

Pour créer un nouveau compte d’utilisateur via l’utilisation de framework appartenance le `Membership` de classe [ `CreateUser` méthode](https://msdn.microsoft.com/library/system.web.security.membership.createuser.aspx). Cette méthode a paramètres d’entrée pour le nom d’utilisateur, mot de passe et d’autres champs relatifs à l’utilisateur. Lors de l’appel, elle délègue la création du nouveau compte d’utilisateur pour le fournisseur d’appartenances configuré, puis renvoie un [ `MembershipUser` objet](https://msdn.microsoft.com/library/system.web.security.membershipuser.aspx) représentant le compte d’utilisateur de nouvellement créé.

Le `CreateUser` méthode a quatre surcharges, chacune acceptant un nombre différent de paramètres d’entrée :

- [`CreateUser(username, password)`](https://msdn.microsoft.com/library/d8t4h2es.aspx)
- [`CreateUser(username, password, email)`](https://msdn.microsoft.com/library/t8yy6w3h.aspx)
- [`CreateUser(username, password, email, passwordQuestion, passwordAnswer, isApproved, MembershipCreateStatus)`](https://msdn.microsoft.com/library/82xx2e62.aspx)
- [`CreateUser(username, password, email, passwordQuestion, passwordAnswer, isApproved, providerUserKey, MembershipCreateStatus)`](https://msdn.microsoft.com/library/ms152012.aspx)

Ces quatre surcharges diffèrent en fonction de la quantité d’informations collectées. La première surcharge, par exemple, requiert uniquement le nom d’utilisateur et le mot de passe pour le nouveau compte d’utilisateur, tandis que la deuxième requiert également l’adresse de messagerie de l’utilisateur.

Ces surcharges existent, car les informations nécessaires pour créer un nouveau compte d’utilisateur dépendent des paramètres de configuration du fournisseur d’appartenances. Dans le  *<a id="_msoanchor_8"> </a> [création du schéma de l’appartenance dans SQL Server](creating-the-membership-schema-in-sql-server-vb.md)*  didacticiel, nous avons examiné les paramètres de configuration en spécifiant l’appartenance au fournisseur dans `Web.config`. Tableau 2 inclus une liste complète des paramètres de configuration.

Une telle configuration de fournisseur d’appartenance qui a une incidence sur les `CreateUser` surcharges peuvent être utilisées est le `requiresQuestionAndAnswer` paramètre. Si `requiresQuestionAndAnswer` a la valeur `true` (la valeur par défaut), puis lors de la création d’un nouveau compte d’utilisateur, nous devons spécifier une question de sécurité et une réponse. Ces informations sont ultérieurement utilisées si l’utilisateur doit réinitialiser ou modifier leur mot de passe. En particulier, à ce moment-là, elles sont affichées la question de sécurité, et ils doivent entrer la réponse correcte pour réinitialiser ou modifier leur mot de passe. Par conséquent, si le `requiresQuestionAndAnswer` a la valeur `true` puis appeler une des deux premières `CreateUser` surcharges entraîne une exception, car la question de sécurité et la réponse sont manquants. Étant donné que notre application est actuellement configurée pour exiger une question de sécurité et une réponse, vous devrez utiliser l’une des deux surcharges ce dernier lors de la création de l’utilisateur par programme.

Pour illustrer l’utilisation de la `CreateUser` (méthode), nous allons créer une interface utilisateur où nous inviter l’utilisateur à leur nom, un mot de passe, la messagerie et une réponse à une question de sécurité prédéfinis. Ouvrez le `CreatingUserAccounts.aspx` page dans le `Membership` dossier et ajouter les contrôles Web suivants pour le contrôle de contenu :

- Une zone de texte nommé`Username`
- Une zone de texte nommée `Password`, dont `TextMode` est définie sur`Password`
- Une zone de texte nommé`Email`
- Une étiquette nommée `SecurityQuestion` avec son `Text` propriété supprimées
- Une zone de texte nommé`SecurityAnswer`
- Un bouton nommé `CreateAccountButton` dont `Text` propriété est définie pour créer le compte d’utilisateur
- Un contrôle Label nommé `CreateAccountResults` avec son `Text` propriété supprimées

À ce stade votre écran doit ressembler à l’écran : WordGame indiqué dans la Figure 6.


[![Ajouter les différents contrôles Web à la Page CreatingUserAccounts.aspx](creating-user-accounts-vb/_static/image17.png)](creating-user-accounts-vb/_static/image16.png)

**Figure 6**: ajouter des contrôles Web différents pour le `CreatingUserAccounts.aspx Page` ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image18.png))


Le `SecurityQuestion` étiquette et `SecurityAnswer` zone de texte sont destinés à afficher une question de sécurité prédéfinis et de collecter les réponses de l’utilisateur. Notez que la question de sécurité et la réponse sont stockées sur une base par utilisateur, il est donc possible permettre à chaque utilisateur définir leur propre question de sécurité. Toutefois, pour cet exemple, j’ai décidé d’utiliser une question de sécurité universel, à savoir : quelle est votre couleur préférée ?

Pour implémenter cette question de sécurité prédéfinis, ajoutez une constante à la classe code-behind de la page nommée `passwordQuestion`, lui assignant la question de sécurité. Puis, dans le `Page_Load` Gestionnaire d’événements, affecter cette constante pour le `SecurityQuestion` l’étiquette `Text` propriété :

[!code-vb[Main](creating-user-accounts-vb/samples/sample5.vb)]

Ensuite, créez un gestionnaire d’événements pour le `CreateAccountButton'` s `Click` événement et ajoutez le code suivant :

[!code-vb[Main](creating-user-accounts-vb/samples/sample6.vb)]

Le `Click` Gestionnaire d’événements démarre en définissant une variable nommée `createStatus` de type [ `MembershipCreateStatus` ](https://msdn.microsoft.com/library/system.web.security.membershipcreatestatus.aspx). `MembershipCreateStatus`est une énumération qui indique l’état de la `CreateUser` opération. Par exemple, si le compte d’utilisateur est créé avec succès, résultant `MembershipCreateStatus` instance est fixée à la valeur `Success;` en revanche, si l’opération échoue, car il existe déjà un utilisateur avec le même nom d’utilisateur, elle sera définie sur la valeur `DuplicateUserName`. Dans le `CreateUser` , nous utilisons la surcharge, vous devez transmettre un `MembershipCreateStatus` instance dans la méthode. Ce paramètre est défini à la valeur appropriée dans le `CreateUser` (méthode) et nous pouvons examiner sa valeur après l’appel de méthode pour déterminer si le compte d’utilisateur a été correctement créé.

Après avoir appelé `CreateUser`, en passant `createStatus`, un `Select Case` instruction est utilisée pour générer un message approprié en fonction de la valeur affectée à `createStatus`. Figures 7 montre la sortie lorsqu’un nouvel utilisateur a été correctement créé. Les chiffres 8 et 9 illustrent la sortie lorsque le compte d’utilisateur n’est pas créé. Dans la Figure 8, le visiteur entré un mot de passe de cinq lettres qui ne respecte pas les exigences de force énoncés dans les paramètres de configuration du fournisseur d’appartenances. Dans la Figure 9, le visiteur tente de créer un compte d’utilisateur avec un nom d’utilisateur existant (celui créé dans la Figure 7).


[![Un nouveau compte d’utilisateur est créé avec succès](creating-user-accounts-vb/_static/image20.png)](creating-user-accounts-vb/_static/image19.png)

**Figure 7**: un nouveau compte d’utilisateur est créé avec succès ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image21.png))


[![Le compte d’utilisateur n’est pas créé, car le mot de passe fourni est trop faible](creating-user-accounts-vb/_static/image23.png)](creating-user-accounts-vb/_static/image22.png)

**Figure 8**: le compte d’utilisateur n’est pas créé, car le mot de passe fourni est trop faible ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image24.png))


[![Le compte d’utilisateur n’est que pas créé, car le nom d’utilisateur est déjà en cours d’utilisation](creating-user-accounts-vb/_static/image26.png)](creating-user-accounts-vb/_static/image25.png)

**Figure 9**: le compte d’utilisateur est pas créé, car le nom d’utilisateur est déjà en cours d’utilisation ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image27.png))


> [!NOTE]
> Vous vous demandez peut-être comment déterminer la réussite ou l’échec lors de l’utilisation d’une des deux premières `CreateUser` surcharges de méthode, ni de qui a un paramètre de type `MembershipCreateStatus`. Ces deux premières surcharges lèvent une [ `MembershipCreateUserException` exception](https://msdn.microsoft.com/library/system.web.security.membershipcreateuserexception.aspx) en cas de défaillance, ce qui inclut un [ `StatusCode` propriété](https://msdn.microsoft.com/library/system.web.security.membershipcreateuserexception.statuscode.aspx) de type `MembershipCreateStatus`.


Après avoir créé quelques comptes d’utilisateur, vérifiez que les comptes ont été créés en répertoriant le contenu de la `aspnet_Users` et `aspnet_Membership` les tables dans le `SecurityTutorials.mdf` base de données. Comme le montre la Figure 10, j’ai ajouté deux utilisateurs via la `CreatingUserAccounts.aspx` page : Tito et Bruce.


[![Il existe deux utilisateurs dans le magasin d’utilisateur d’appartenance : Tito et Bruce](creating-user-accounts-vb/_static/image29.png)](creating-user-accounts-vb/_static/image28.png)

**La figure 10**: il existe deux utilisateurs dans le magasin d’utilisateur d’appartenance : Tito et Bruce ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image30.png))


Tandis que le magasin d’utilisateur d’appartenance inclut désormais Bruce et de Tito informations de compte, nous avons encore implémenter la fonctionnalité qui permet de Bruce ou Tito pour se connecter au site. Actuellement, `Login.aspx` valide les informations d’identification de l’utilisateur par rapport à un jeu codé en dur de paires de nom d’utilisateur/mot de passe - il effectue *pas* valider les informations d’identification fournies par rapport à l’infrastructure d’appartenance. Pour ajouter les nouveaux comptes d’utilisateur dans le `aspnet_Users` et `aspnet_Membership` tables doivent suffire. Dans le didacticiel suivant,  *<a id="_msoanchor_9"> </a> [validation utilisateur informations d’identification par rapport à l’appartenance utilisateur stocker](validating-user-credentials-against-the-membership-user-store-vb.md)*, nous mettrons à jour la page de connexion à valider par rapport au magasin de l’appartenance.

> [!NOTE]
> Si vous ne voyez pas tous les utilisateurs dans votre `SecurityTutorials.mdf` base de données, il est possible que votre application web utilise le fournisseur d’appartenances par défaut, `AspNetSqlMembershipProvider`, qui utilise le `ASPNETDB.mdf` base de données comme magasin de l’utilisateur. Pour déterminer si c’est le problème, cliquez sur le bouton Actualiser dans l’Explorateur de solutions. Si une base de données nommée `ASPNETDB.mdf` a été ajouté à la `App_Data` dossier, il s’agit du problème. Retourner à l’étape 4 de la  *<a id="_msoanchor_10"> </a> [création du schéma de l’appartenance dans SQL Server](creating-the-membership-schema-in-sql-server-vb.md)*  didacticiel pour obtenir des instructions sur la façon de configurer correctement le fournisseur d’appartenances.


Dans la plupart des créer des scénarios de compte d’utilisateur, le visiteur est présenté avec une interface à entrer leur nom d’utilisateur, mot de passe, la messagerie et autres informations essentielles, à partir duquel un nouveau compte est créé. Dans cette étape, nous examinés à la création d’une telle interface manuellement et puis vu comment utiliser le `Membership.CreateUser` méthode pour ajouter par programmation le nouveau compte d’utilisateur basée sur les entrées de l’utilisateur. Notre code, venez de créer le nouveau compte d’utilisateur. Il n’a pas effectué les actions, telles que la journalisation de l’utilisateur pour le site sous le compte d’utilisateur de nouvellement créé, ou envoyer un e-mail de confirmation à l’utilisateur de suivi. Ces étapes supplémentaires nécessite un code supplémentaire dans du bouton `Click` Gestionnaire d’événements.

ASP.NET est fourni avec le contrôle CreateUserWizard, qui est conçu pour gérer le processus de création de compte utilisateur, à partir d’une interface utilisateur pour la création de nouveaux comptes d’utilisateur, à créer le compte dans le cadre de l’appartenance au compte après et de rendu tâches de création, telles que l’envoi d’un message électronique de confirmation et de la journalisation de l’utilisateur nouvellement créé dans le site. L’utilisation du contrôle CreateUserWizard est aussi simple que le contrôle CreateUserWizard à partir de la boîte à outils vers une page, le déplacement et puis en définissant certaines propriétés. Dans la plupart des cas, vous aurez pas à écrire une seule ligne de code. Nous allons examiner ce contrôle bien pratique en détail à l’étape 6.

Si de nouveaux comptes d’utilisateur sont uniquement créés via une page web de création d’un compte par défaut, il est peu probable que vous devez toujours écrire du code qui utilise le `CreateUser` (méthode), comme le contrôle CreateUserWizard sera probablement répondent à vos besoins. Toutefois, le `CreateUser` méthode est utile dans les scénarios où vous avez besoin d’une expérience utilisateur de créer un compte hautement personnalisée ou que vous deviez créer par programmation des nouveaux comptes d’utilisateur via une autre interface. Par exemple, vous pouvez avoir une page qui permet à un utilisateur de charger un fichier XML qui contient des informations utilisateur à partir d’une autre application. La page peut analyse le contenu du document XML chargé de fichiers et créer un nouveau compte pour chaque utilisateur représenté dans le document XML en appelant le `CreateUser` (méthode).

## <a name="step-6-creating-a-new-user-with-the-createuserwizard-control"></a>Étape 6 : Création d’un utilisateur avec le contrôle CreateUserWizard

ASP.NET est fourni avec un nombre de contrôles Web d’ouverture de session. Ces contrôles vous aider dans nombreux scénarios utilisateur courants liées au compte et un compte de connexion. Le [contrôle CreateUserWizard](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/ctrlref/login/createuserwizard.aspx) est un tel contrôle est conçu pour présenter une interface utilisateur pour l’ajout d’un nouveau compte d’utilisateur à l’infrastructure d’appartenance.

Comme de nombreux autres contrôles associées à la connexion de Web, CreateUserWizard peut être utilisé sans écrire une seule ligne de code. Il fournit une interface utilisateur basée sur le fournisseur d’appartenances paramètres de configuration et en interne appelle des intuitivement la `Membership` de classe `CreateUser` méthode une fois que l’utilisateur entre les informations nécessaires et qu’il clique sur le bouton Créer un utilisateur. Le contrôle CreateUserWizard est extrêmement personnalisable. Il existe un ensemble d’événements qui se déclenchent au cours des différentes étapes du processus de création de compte. Nous pouvons créer des gestionnaires d’événements, si nécessaire, pour injecter la logique personnalisée dans le flux de travail de création de compte. En outre, l’apparence de CreateUserWizard est très flexible. Il existe un nombre de propriétés qui définissent l’apparence de l’interface par défaut ; Si nécessaire, le contrôle peut être converti en un modèle ou les étapes de l’inscription utilisateur supplémentaires peuvent être ajoutés.

Commençons par examiner à l’aide d’interface par défaut et le comportement d' un contrôle CreateUserWizard. Nous allons ensuite observer comment personnaliser l’apparence via les propriétés et les événements du contrôle.

### <a name="examining-the-createuserwizards-default-interface-and-behavior"></a>Examiner l’Interface par défaut et le comportement de CreateUserWizard

Retour à la `CreatingUserAccounts.aspx` page dans le `Membership` dossier, basculez vers le mode Design ou fractionné et puis ajoutez un contrôle CreateUserWizard vers le haut de la page. Le contrôle CreateUserWizard est présentée sous la section contrôles de connexion de la boîte à outils. Après avoir ajouté le contrôle, définissez son `ID` propriété `RegisterUser`. Comme la capture d’écran dans la Figure 11 illustre, CreateUserWizard restitue une interface avec les zones de texte pour le nouvel utilisateur nom d’utilisateur, mot de passe, l’adresse de messagerie, question de sécurité et des réponses.


[![Les convertisseurs de contrôle CreateUserWizard générique créer l’Interface utilisateur](creating-user-accounts-vb/_static/image32.png)](creating-user-accounts-vb/_static/image31.png)

**Figure 11**: le contrôle CreateUserWizard restitue une Interface utilisateur de créer générique ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image33.png))


Prenons un moment pour comparer l’interface utilisateur par défaut généré par le contrôle CreateUserWizard avec l’interface que nous avons créé à l’étape 5. Pour commencer, le contrôle CreateUserWizard permet le visiteur spécifier la question de sécurité et de la réponse, alors que notre interface créés manuellement utilisé une question de sécurité prédéfinis. L’interface du contrôle CreateUserWizard inclut également des contrôles de validation, alors que nous avons dû encore implémenter la validation sur les champs de formulaire de notre interface. Et l’interface de contrôle CreateUserWizard comprend une zone de texte Confirmer le mot de passe (ainsi que d’un CompareValidator pour vous assurer que le texte entré le mot de passe et comparer le mot de passe de zones de texte sont égales).

Ce qui est intéressant, c’est que le contrôle CreateUserWizard consulte les paramètres de configuration du fournisseur d’appartenances lors du rendu de son interface utilisateur. Par exemple, les zones de texte questions et réponses de sécurité sont affichées uniquement si `requiresQuestionAndAnswer` est définie sur True. De même, CreateUserWizard ajoute automatiquement un RegularExpressionValidator (contrôle) pour vous assurer que les exigences de force du mot de passe sont respectées et définit son `ErrorMessage` et `ValidationExpression` propriétés basées sur les `minRequiredPasswordLength`, `minRequiredNonalphanumericCharacters`et `passwordStrengthRegularExpression` les paramètres de configuration.

Le contrôle CreateUserWizard, comme son nom l’indique, est dérivé de la [contrôle de l’Assistant](https://msdn.microsoft.com/library/s2etd1ek.aspx). Contrôles de l’Assistant sont conçus pour fournir une interface pour la réalisation des tâches de plusieurs étapes. Un contrôle de l’Assistant peut avoir un nombre arbitraire de `WizardSteps`, chacun d’eux est un modèle qui définit le code HTML et des contrôles Web pour cette étape. Le contrôle de l’Assistant affiche initialement la première `WizardStep`, ainsi que des contrôles de navigation qui permettent à l’utilisateur pour passer d’une étape à la suivante, ou de revenir aux étapes précédentes.

Comme le montre le balisage déclaratif dans la Figure 11, l’interface par défaut du contrôle CreateUserWizard inclut deux `WizardStep` %s :

- [`CreateUserWizardStep`](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizardstep.aspx) ? restitue l’interface permettant de collecter des informations pour la création du nouveau compte d’utilisateur. Il s’agit de l’étape de la Figure 11.
- [`CompleteWizardStep`](https://msdn.microsoft.com/library/system.web.ui.webcontrols.completewizardstep.aspx) ? affiche un message indiquant que le compte a été créé avec succès.

Apparence et le comportement de CreateUserWizard peuvent être modifiés en convertissant une de ces étapes pour les modèles, ou en ajoutant votre propre `WizardStep` s. Nous allons nous intéresser à l’ajout d’un `WizardStep` à l’interface d’inscription dans le *le stockage des informations utilisateur supplémentaires* didacticiel.

Vous allez voir le contrôle CreateUserWizard en action. Visitez le `CreatingUserAccounts.aspx` page via un navigateur. Commencez par entrer des valeurs non valides dans l’interface de CreateUserWizard. Essayez d’entrer un mot de passe n’est pas conforme aux exigences de force de mot de passe, ou si vous laissez la zone de texte Nom d’utilisateur vide. CreateUserWizard affichera un message d’erreur approprié. Figure 12 montre la sortie lorsque vous tentez de créer un utilisateur avec un mot de passe fort insuffisamment.


[![CreateUserWizard injecte automatiquement les contrôles de Validation](creating-user-accounts-vb/_static/image35.png)](creating-user-accounts-vb/_static/image34.png)

**Figure 12**: le contrôle CreateUserWizard automatiquement injecte des contrôles de Validation ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image36.png))


Ensuite, entrez les valeurs appropriées dans le contrôle CreateUserWizard et cliquez sur le bouton Créer un utilisateur. En supposant que les champs obligatoires ont été entrées et les force du mot de passe est suffisante, CreateUserWizard créer un nouveau compte d’utilisateur via l’infrastructure de l’appartenance, puis afficher le `CompleteWizardStep`de l’interface (voir la Figure 13). Les coulisses, CreateUserWizard appelle la `Membership.CreateUser` méthode, comme nous l’avons fait à l’étape 5.


[![Un nouveau compte d’utilisateur a été créé avec succès](creating-user-accounts-vb/_static/image38.png)](creating-user-accounts-vb/_static/image37.png)

**Figure 13**: un nouveau compte d’utilisateur a été créé avec succès ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image39.png))


> [!NOTE]
> Comme le montre la Figure 13, le `CompleteWizardStep`d’interface comprend un bouton Continuer. Toutefois, à ce stade en cliquant sur elle simplement effectue une publication (postback), en laissant le visiteur sur la même page. Dans la personnalisation de CreateUserWizard apparence et comportement via ses propriétés section nous allons examiner comment vous pouvez avoir ce bouton Envoyer le visiteur à `Default.aspx` (ou une autre page).


Après avoir créé un nouveau compte d’utilisateur, revenez à Visual Studio et examinez le `aspnet_Users` et `aspnet_Membership` tables comme nous l’avons fait dans la Figure 10 pour vérifier que le compte a été créé.

### <a name="customizing-the-createuserwizards-behavior-and-appearance-through-its-properties"></a>Personnalisation du comportement et l’apparence grâce à ses propriétés de CreateUserWizard

CreateUserWizard peut être personnalisée de différentes façons, via les propriétés, `WizardStep` s et les gestionnaires d’événements. Dans cette section, nous allons examiner comment personnaliser l’apparence du contrôle via ses propriétés ; la section suivante recherche à étendre le comportement du contrôle via les gestionnaires d’événements.

Pratiquement tout le texte affiché dans l’interface utilisateur du contrôle CreateUserWizard par défaut peut être personnalisé via son multitude de propriétés. Par exemple, les étiquettes de nom d’utilisateur, mot de passe, confirmez le mot de passe, par courrier électronique, Question de sécurité et réponse de sécurité s’affichés à gauche des zones de texte peuvent être personnalisés par le [ `UserNameLabelText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.usernamelabeltext.aspx), [ `PasswordLabelText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.passwordlabeltext.aspx), [ `ConfirmPasswordLabelText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.confirmpasswordlabeltext.aspx), [ `EmailLabelText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.emaillabeltext.aspx), [ `QuestionLabelText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.questionlabeltext.aspx), et [ `AnswerLabelText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.answerlabeltext.aspx) propriétés, respectivement. De même, il existe des propriétés permettant de spécifier le texte des boutons Create User et continuer dans les `CreateUserWizardStep` et `CompleteWizardStep`, ainsi que si ces boutons sont rendus sous forme de boutons, LinkButton ou ImageButtons.

Les couleurs, les bordures, les polices et les autres éléments visuels sont configurables via un hôte de propriétés de style. Le contrôle CreateUserWizard lui-même a les propriétés de style de contrôle Web common - `BackColor`, `BorderStyle`, `CssClass`, `Font`, et ainsi de suite - et un certain nombre de propriétés de style pour définir l’apparence des sections de la Interface du contrôle CreateUserWizard. Le [ `TextBoxStyle` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.textboxstyle.aspx), par exemple, définit les styles pour les zones de texte dans le `CreateUserWizardStep`, tandis que la [ `TitleTextStyle` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.titletextstyle.aspx) définit le style de titre (s’inscrire pour votre nouveau Compte).

Outre les propriétés relatives à l’apparence, il existe un nombre de propriétés qui affectent le comportement d' un contrôle CreateUserWizard. Le [ `DisplayCancelButton` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.wizard.displaycancelbutton.aspx), si défini sur True, affiche un bouton Annuler en regard du bouton Créer un utilisateur (la valeur par défaut est False). Si vous affichez le bouton Annuler, veillez à définir également la [ `CancelDestinationPageUrl` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.continuedestinationpageurl.aspx), qui spécifie la page de l’utilisateur est envoyé à après avoir cliqué sur Annuler. Comme indiqué dans la section précédente, le bouton Continuer dans les `CompleteWizardStep`d’interface entraîne une publication, mais laisse le visiteur sur la même page. Pour envoyer le visiteur vers une autre page après avoir cliqué sur le bouton Continuer, spécifiez simplement l’URL dans le [ `ContinueDestinationPageUrl` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.continuedestinationpageurl.aspx).

Nous allons mettre à jour le `RegisterUser` contrôle CreateUserWizard pour afficher un bouton Annuler et envoyer le visiteur à `Default.aspx` lorsque vous cliquez sur le bouton Annuler ou continuer. Pour ce faire, affectez le `DisplayCancelButton` propriété sur True et que les deux le `CancelDestinationPageUrl` et `ContinueDestinationPageUrl` propriétés ~ / Default.aspx. La figure 14 illustre CreateUserWizard mis à jour lors de l’affichage via un navigateur.


[![Le CreateUserWizardStep inclut un bouton Annuler](creating-user-accounts-vb/_static/image41.png)](creating-user-accounts-vb/_static/image40.png)

**La figure 14**: le `CreateUserWizardStep` inclut un bouton Annuler ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image42.png))


Lorsqu’un visiteur saisit un nom d’utilisateur, mot de passe, l’adresse de messagerie, question de sécurité et des réponses et clique sur Créer un utilisateur, un nouveau compte d’utilisateur est créé et le visiteur est connecté en tant qu’utilisateur nouvellement créé. En supposant que la personne visitant la page de création d’un compte pour eux-mêmes, cela est probablement le comportement souhaité. Toutefois, vous voudrez permettent aux administrateurs d’ajouter de nouveaux comptes d’utilisateur. Ce faisant, le compte d’utilisateur est créé, mais l’administrateur reste connecté en tant qu’administrateur (et non comme le compte qui vient d’être créé). Ce comportement peut être modifié via le booléen [ `LoginCreatedUser` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.logincreateduser.aspx).

Comptes d’utilisateur dans le cadre de l’appartenance contiennent un indicateur approuvé ; les utilisateurs qui ne sont pas approuvées ne peuvent pas se connecter au site. Par défaut, un compte nouvellement créé est marqué comme approuvé, permettant à l’utilisateur à se connecter au site immédiatement. Toutefois, il est possible, pour que les nouveaux comptes d’utilisateur marqués comme non approuvés. Vous pouvez choisir un administrateur d’approuver manuellement les nouveaux utilisateurs pouvoir se connecter ou vous souhaitez peut-être vérifier que l’adresse de messagerie entrée lors de l’inscription est valide avant d’autoriser un utilisateur de se connecter. Quel que soit le cas, vous pouvez avoir le compte d’utilisateur nouvellement créé marqué comme non approuvé par la définition d' un contrôle CreateUserWizard [ `DisableCreatedUser` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.disablecreateduser.aspx) True (la valeur par défaut est False).

Incluent d’autres propriétés relatives au comportement de note `AutoGeneratePassword` et `MailDefinition`. Si le [ `AutoGeneratePassword` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.autogeneratepassword.aspx) est définie sur True, le `CreateUserWizardStep` n’affiche pas les zones de texte mot de passe et confirmer le mot de passe ; au lieu de cela, le nouveau mot de passe est généré automatiquement à l’aide de la `Membership` la classe [ `GeneratePassword` méthode](https://msdn.microsoft.com/library/system.web.security.membership.generatepassword.aspx). Le `GeneratePassword` méthode construit un mot de passe d’une longueur spécifiée et avec un nombre suffisant de caractères non alphanumériques pour satisfaire les exigences de force du mot de passe configuré.

Le [ `MailDefinition` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.maildefinition.aspx) est utile si vous souhaitez envoyer un courrier électronique à l’adresse de messagerie spécifiée pendant le processus de création de compte. Le `MailDefinition` propriété comprend une série de sous-propriétés pour définir des informations sur le message électronique construit. Ces sous-propriétés incluent des options telles que `Subject`, `Priority`, `IsBodyHtml`, `From`, `CC`, et `BodyFileName`. Le [ `BodyFileName` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.maildefinition.bodyfilename.aspx) pointe vers un fichier texte ou HTML qui contient le corps du message électronique. Le corps de la prend en charge deux espaces réservés prédéfinis : `<%UserName%>` et `<%Password%>`. Ces espaces réservés, s’il est présent dans le `BodyFileName` de fichiers, est remplacé par le nom et mot de passe de l’utilisateur nouvellement créé.

> [!NOTE]
> Le `CreateUserWizard` du contrôle `MailDefinition` propriété spécifie uniquement les détails sur le courrier électronique est envoyé lorsqu’un nouveau compte est créé. Il n’inclut pas tous les détails sur la façon dont le message électronique est envoyé réellement (autrement dit, si un répertoire de serveur ou de la boîte aux lettres SMTP est utilisé, aucune information d’authentification et ainsi de suite). Ces détails de bas niveau doivent être définies dans le `<system.net>` section `Web.config`. Pour plus d’informations sur ces paramètres de configuration et lors de l’envoi de courrier électronique à partir d’ASP.NET 2.0 en général, reportez-vous à la [FAQ à SystemNetMail.com](http://www.systemnetmail.com/) et mon article, [envoi de courrier électronique dans ASP.NET 2.0](http://aspnet.4guysfromrolla.com/articles/072606-1.aspx).


### <a name="extending-the-createuserwizards-behavior-using-event-handlers"></a>Extension de comportement de CreateUserWizard à l’aide de gestionnaires d’événements

Le contrôle CreateUserWizard élève un nombre d’événements au cours de son flux de travail. Par exemple, une fois un visiteur entre son nom d’utilisateur, mot de passe et d’autres informations pertinentes et clique sur le bouton Créer un utilisateur, le contrôle CreateUserWizard déclenche son [ `CreatingUser` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.creatinguser.aspx). S’il existe un problème pendant le processus de création, la [ `CreateUserError` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.createusererror.aspx) est déclenchée ; Toutefois, si l’utilisateur est créé avec succès, puis le [ `CreatedUser` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.createduser.aspx) est déclenché. Autres événements de contrôle CreateUserWizard déclenché, mais ce sont les plus pertinentes trois.

Dans certains scénarios, nous pouvons adopter cliquer dans le flux de travail CreateUserWizard, nous pouvons faire en créant un gestionnaire d’événements pour l’événement approprié. Pour illustrer cela, nous allons améliorer la `RegisterUser` contrôle CreateUserWizard à inclure une validation personnalisée sur le nom d’utilisateur et un mot de passe. En particulier, nous allons améliorer notre CreateUserWizard afin que les noms d’utilisateur ne peut pas contenir d’espaces de début ou de fin et le nom d’utilisateur ne peut pas apparaître n’importe où dans le mot de passe. En bref, vous souhaitez empêcher un utilisateur à partir de la création d’un nom d’utilisateur comme « Scott », ou avoir une combinaison de nom d’utilisateur/mot de passe tels que Scott et Scott.1234.

Pour cela nous allons créer un gestionnaire d’événements pour le `CreatingUser` événement pour effectuer des vérifications de notre validation supplémentaire. Si les données fournies ne sont pas valides, nous devons annuler le processus de création. Nous devons également ajouter un contrôle Web Label à la page pour afficher un message pour expliquer que le nom d’utilisateur ou le mot de passe n’est pas valide. Commencez par ajouter un contrôle Label sous le contrôle CreateUserWizard, en définissant ses `ID` propriété `InvalidUserNameOrPasswordMessage` et son `ForeColor` propriété `Red`. Effacer son `Text` propriété et définissez son `EnableViewState` et `Visible` propriétés sur False.

[!code-aspx[Main](creating-user-accounts-vb/samples/sample7.aspx)]

Ensuite, créez un gestionnaire d’événements d' un contrôle CreateUserWizard `CreatingUser` événement. Pour créer un gestionnaire d’événements, sélectionnez le contrôle dans le concepteur et passez à la fenêtre Propriétés. À partir de là, cliquez sur l’icône d’éclair et puis double-cliquez sur l’événement approprié pour créer le Gestionnaire d’événements.

Ajoutez le code suivant au gestionnaire d'événements `CreatingUser` :

[!code-vb[Main](creating-user-accounts-vb/samples/sample8.vb)]

Notez que le nom d’utilisateur et un mot de passe entré dans le contrôle CreateUserWizard sont disponibles via son [ `UserName` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.username.aspx) et [ `Password` propriétés](https://msdn.microsoft.com/library/system.web.ui.webcontrols.createuserwizard.password.aspx), respectivement. Nous utilisons ces propriétés dans le Gestionnaire d’événements ci-dessus pour déterminer si le nom d’utilisateur fourni contient des espaces de début ou de fin, et si le nom d’utilisateur est trouvé dans le mot de passe. Si une des conditions suivantes est remplie, un message d’erreur s’affiche dans le `InvalidUserNameOrPasswordMessage` étiquette et le Gestionnaire d’événements `e.Cancel` est définie sur `True`. Si `e.Cancel` a la valeur `True`, CreateUserWizard court-circuite son flux de travail, en réalité l’annulation du processus de création de compte utilisateur.

La figure 15 montre une capture d’écran de `CreatingUserAccounts.aspx` lorsque l’utilisateur entre un nom d’utilisateur avec des espaces de début.


[![Noms d’utilisateur avec le début ou des espaces de fin ne sont pas autorisées.](creating-user-accounts-vb/_static/image44.png)](creating-user-accounts-vb/_static/image43.png)

**Figure 15**: noms d’utilisateur avec le début ou des espaces de fin ne sont pas autorisés ([cliquez pour afficher l’image en taille réelle](creating-user-accounts-vb/_static/image45.png))


> [!NOTE]
> Nous allons voir un exemple d’utilisation d' un contrôle CreateUserWizard `CreatedUser` événement dans le  *<a id="_msoanchor_11"> </a> [le stockage des informations utilisateur supplémentaires](storing-additional-user-information-vb.md)*  didacticiel.


## <a name="summary"></a>Récapitulatif

Le `Membership` de classe `CreateUser` méthode crée un nouveau compte d’utilisateur dans le cadre de l’appartenance. Il permet la délégation de l’appel du fournisseur d’appartenances configuré. Dans le cas de la `SqlMembershipProvider`, le `CreateUser` méthode ajoute un enregistrement à la `aspnet_Users` et `aspnet_Membership` tables de base de données.

Alors que les nouveaux comptes d’utilisateur peuvent être créés par programmation (comme nous l’avons vu à l’étape 5), une approche plus rapide et plus facile consiste à utiliser le contrôle CreateUserWizard. Ce contrôle affiche une interface utilisateur de plusieurs étapes pour la collecte des informations utilisateur et la création d’un nouvel utilisateur dans le cadre de l’appartenance. Dans les coulisses, ce contrôle utilise le même `Membership.CreateUser` examiné dans l’étape 5, mais le contrôle de la méthode crée l’interface utilisateur, des contrôles de validation et répond aux erreurs de création de compte utilisateur sans devoir écrire la moindre ligne de code.

À ce stade, nous avons la fonctionnalité en place pour créer de nouveaux comptes d’utilisateur. Toutefois, la page de connexion toujours est validée par rapport à ces informations d’identification codé en dur, que nous avons spécifié dans le deuxième didacticiel. Dans le <a id="_msoanchor_12"> </a> [didacticiel suivant](validating-user-credentials-against-the-membership-user-store-vb.md) nous mettrons à jour `Login.aspx` pour valider l’utilisateur de fourni des informations d’identification par rapport à l’infrastructure d’appartenance.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [`CreateUser`Documentation technique](https://msdn.microsoft.com/library/system.web.security.membershipprovider.createuser.aspx)
- [Vue d’ensemble du contrôle CreateUserWizard](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/ctrlref/login/createuserwizard.aspx)
- [Création d’un fournisseur de plan de Site basé sur le système de fichier](http://aspnet.4guysfromrolla.com/articles/020106-1.aspx)
- [Création d’une Interface utilisateur pas à pas avec le contrôle de l’Assistant 2.0 d’ASP.NET](http://aspnet.4guysfromrolla.com/articles/061406-1.aspx)
- [Examen de ASP.NET 2.0 de Navigation du Site](http://aspnet.4guysfromrolla.com/articles/111605-1.aspx)
- [Les Pages maîtres et Navigation du Site](https://asp.net/learn/data-access/tutorial-03-vb.aspx)
- [Le fournisseur de plan de Site SQL que vous attendiez pour](https://msdn.microsoft.com/msdnmag/issues/06/02/WickedCode/default.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4guysfromrolla.com).

>[!div class="step-by-step"]
[Précédent](creating-the-membership-schema-in-sql-server-vb.md)
[Suivant](validating-user-credentials-against-the-membership-user-store-vb.md)
