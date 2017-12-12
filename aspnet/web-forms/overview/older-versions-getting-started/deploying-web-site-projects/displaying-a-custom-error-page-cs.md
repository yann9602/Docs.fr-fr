---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/displaying-a-custom-error-page-cs
title: "Affichage d’une Page d’erreur personnalisés (c#) | Documents Microsoft"
author: rick-anderson
description: "Ce qui voit l’utilisateur lorsqu’une erreur d’exécution se produit dans une application web ASP.NET ? La réponse dépend du site Web &lt;customErrors&gt; configuration..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/09/2009
ms.topic: article
ms.assetid: cb061642-faf3-41b2-9372-69e13444d458
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/displaying-a-custom-error-page-cs
msc.type: authoredcontent
ms.openlocfilehash: 92a7e945a6f82e78b848bae8f4f362e16a567b1f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="displaying-a-custom-error-page-c"></a>Affichage d’une Page d’erreur personnalisés (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/1/0/C/10CC829F-A808-4302-97D3-59989B8F9C01/ASPNET_Hosting_Tutorial_11_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/5/C/5/5C57DB8C-5DEA-4B3A-92CA-4405544D313B/aspnet_tutorial11_CustomErrors_cs.pdf)

> Ce qui voit l’utilisateur lorsqu’une erreur d’exécution se produit dans une application web ASP.NET ? La réponse dépend du site Web &lt;customErrors&gt; configuration. Par défaut, les utilisateurs reçoivent un écran jaune disgracieux proclaiming qu’une erreur d’exécution s’est produite. Ce didacticiel montre comment personnaliser ces paramètres à la page d’erreur personnalisée affichage une esthétiques qui correspond aux apparence de votre site.


## <a name="introduction"></a>Introduction

Dans un monde idéal ne serait aucune erreur d’exécution. Les programmeurs seraient écrire du code avec n-aires un bogue et la validation d’entrée utilisateur robuste et externes ressources telles que les serveurs de messagerie et les serveurs de base de données va jamais passer en mode hors connexion. Bien sûr, en réalité, les erreurs sont inévitables. Les classes dans le .NET Framework indiquent une erreur en levant une exception. Par exemple, la méthode d’ouverture de l’objet appelant un SqlConnection établit une connexion à la base de données spécifiée par une chaîne de connexion. Toutefois, si la base de données est arrêté, ou si les informations d’identification dans la chaîne de connexion ne sont pas valides la méthode Open lève une `SqlException`. Exceptions peuvent être gérées à l’aide de `try/catch/finally` blocs. Si le code situé dans un `try` bloc lève une exception, le contrôle est transféré au bloc catch approprié sur lequel le développeur peut tenter une récupération à partir de l’erreur. S’il n’existe aucun bloc catch correspondant, ou si le code qui a levé l’exception n’est pas dans un bloc try, l’exception percolates la pile des appels search de `try/catch/finally` blocs.

Si l’exception se propage jusqu'à l’exécution d’ASP.NET sans être géré, le [ `HttpApplication` classe](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.aspx)de [ `Error` événement](https://msdn.microsoft.com/en-us/library/system.web.httpapplication.error.aspx) est déclenché et configuré *page d’erreur*  s’affiche. Par défaut, ASP.NET affiche une page d’erreur affectueusement porte la [jaune écran de décès](http://en.wikipedia.org/wiki/Yellow_Screen_of_Death#Yellow) (YSOD). Il existe deux versions de la YSOD : un élément indique les détails de l’exception, une trace de pile et d’autres informations utiles aux développeurs de débogage de l’application (consultez **Figure 1**) ; l’autre déclare simplement une erreur d’exécution (voir  **Figure 2**).

Détails de l’exception YSOD est très utile pour les développeurs de débogage de l’application, mais affichant un YSOD aux utilisateurs finaux est collant et un. Au lieu de cela, les utilisateurs finaux devra être prises pour une page d’erreur qui maintient l’apparence du site avec prose plus conviviale qui décrit la situation. La bonne nouvelle est que la création de ce une page d’erreur personnalisée est très simple. Ce didacticiel commence par examiner ASP. Pages d’erreur différentes du réseau. Il montre ensuite comment configurer l’application web pour afficher les utilisateurs à une page d’erreur personnalisée en dépit d’une erreur.

### <a name="examining-the-three-types-of-error-pages"></a>Examen des trois Types de Pages d’erreurs

Lorsqu’une exception non gérée se produit dans une application ASP.NET, un des trois types de pages d’erreur s’affiche :

- La page d’erreur Exception détails de l’écran jaune de décès,
- La page d’erreur Runtime erreur écran jaune de décès, ou
- Une page d’erreur personnalisée

Les développeurs de page d’erreur sont plus familier avec sont le YSOD détails de l’Exception. Par défaut, cette page s’affiche pour les utilisateurs visitent localement et est par conséquent de la page que vous voyez quand une erreur se produit lorsque vous testez le site dans l’environnement de développement. Comme son nom l’indique, le YSOD détails de l’Exception fournit des détails sur l’exception : le type, le message et la trace de pile. De plus, si l’exception a été levée par le code dans la classe de code-behind de votre page ASP.NET, et si l’application est configurée pour le débogage du YSOD détails de l’Exception s’affichent également cette ligne de code (et quelques lignes de code ci-dessus et en dessous).

**Figure 1** montre la page YSOD des détails de l’Exception. Notez l’URL dans la fenêtre du navigateur adresse : `http://localhost:62275/Genre.aspx?ID=foo`. N’oubliez pas que le `Genre.aspx` page répertorie les révisions du livre d’un genre particulier. Il requiert que `GenreId` valeur (une `uniqueidentifier`) être passé à la chaîne de requête ; par exemple, l’URL appropriée pour afficher les révisions de science-fiction est `Genre.aspx?ID=7683ab5d-4589-4f03-a139-1c26044d0146`. Si non -`uniqueidentifier` la valeur est passée dans la chaîne de requête (par exemple, « foo ») par le biais une exception est levée.

> [!NOTE]
> Pour reproduire cette erreur dans l’application web de démonstration disponible pour téléchargement vous pouvez soit visite `Genre.aspx?ID=foo` directement ou cliquez sur le lien « Générer une erreur d’exécution » dans `Default.aspx`.


Notez les informations d’exception présentées dans **Figure 1**. Le message d’exception, « Échec de la conversion d’une chaîne de caractères en uniqueidentifier » se trouve en haut de la page. Le type de l’exception, `System.Data.SqlClient.SqlException`, est également répertorié. Il existe également une trace de la pile.

[![](displaying-a-custom-error-page-cs/_static/image2.png)](displaying-a-custom-error-page-cs/_static/image1.png)

**Figure 1**: détails de l’Exception YSOD inclut des informations sur l’Exception  
 ([Cliquez pour afficher l’image en taille réelle](displaying-a-custom-error-page-cs/_static/image3.png))

L’autre type de YSOD est la YSOD d’erreur de Runtime et qu’il est indiqué dans **Figure 2**. Le YSOD erreur Runtime informe le visiteur une erreur d’exécution s’est produite, mais elle n’inclut pas toutes les informations concernant l’exception qui a été levée. (Il ne, toutefois, fournit des instructions sur la façon d’afficher les détails de l’erreur en modifiant le `Web.config` fichier, qui fait partie de ce qui constitue une telle YSOD un aspect.)

Par défaut, le YSOD d’erreur de Runtime est présenté aux utilisateurs visitant à distance (via http://www.yoursite.com), comme l’atteste de l’URL dans la barre d’adresses du navigateur dans **Figure 2**: `http://httpruntime.web703.discountasp.net/Genre.aspx?ID=foo`. Les deux écrans YSOD différents existent car les développeurs sont intéressés de connaître les détails de l’erreur, mais ces informations ne doivent pas être affichées sur un site en direct comme il peut révéler des éventuelles failles de sécurité ou d’autres informations sensibles à toute personne visitant votre site.

> [!NOTE]
> Si vous sont les suivantes : le long et que vous utilisez DiscountASP.NET comme votre hôte web, vous pouvez remarquer que le YSOD d’erreur de Runtime n’affiche pas lors de la visite le site actif. Il s’agit, car DiscountASP.NET a leurs serveurs par défaut configurés pour afficher le YSOD détails de l’Exception. La bonne nouvelle est que vous pouvez remplacer ce comportement par défaut en ajoutant un `<customErrors>` section à votre `Web.config` fichier. La section « Configuration qui erreur Page s’affiche » examine le `<customErrors>` section détail.


[![](displaying-a-custom-error-page-cs/_static/image5.png)](displaying-a-custom-error-page-cs/_static/image4.png)

**Figure 2**: erreur d’exécution YSOD n’inclut pas les détails de l’erreur  
 ([Cliquez pour afficher l’image en taille réelle](displaying-a-custom-error-page-cs/_static/image6.png))

Le troisième type de page d’erreur est la page d’erreur personnalisée, qui est une page web que vous créez. L’avantage d’une page d’erreur personnalisée est que vous disposez d’un contrôle complet sur les informations qui s’affiche à l’utilisateur, ainsi qu’apparence de la page ; la page d’erreur personnalisée peut utiliser la même page maître et des styles que les autres pages de votre. La section « À l’aide d’une Page d’erreur personnalisée » Guide de création d’une page d’erreur personnalisée et de configuration à afficher dans le cas d’une exception non gérée. **Figure 3** offre en avant-première de cette page d’erreur personnalisée. Comme vous pouvez le voir, l’apparence de la page d’erreur est professionnels beaucoup plus que les écrans jaune de décès indiqué dans les Figures 1 et 2.

[![](displaying-a-custom-error-page-cs/_static/image8.png)](displaying-a-custom-error-page-cs/_static/image7.png)

**Figure 3**: une Page d’erreur personnalisée offre une apparence personnalisée  
 ([Cliquez pour afficher l’image en taille réelle](displaying-a-custom-error-page-cs/_static/image9.png))

Prenez un moment pour inspecter la barre d’adresses du navigateur dans **Figure 3**. Notez que la barre d’adresse affiche l’URL de la page d’erreur personnalisée (`/ErrorPages/Oops.aspx`). Dans les Figures 1 et 2, les écrans de décès jaune sont affiché dans la même page que l’erreur provenance de (`Genre.aspx`). La page d’erreur personnalisée est passée à l’URL de la page où l’erreur s’est produite via le `aspxerrorpath` paramètre querystring.

## <a name="configuring-which-error-page-is-displayed"></a>Configuration de Page d’erreur qui s’affiche.

Parmi les trois pages d’erreur possibles s’affiche est basée sur deux variables :

- Les informations de configuration dans la `<customErrors>` section, et
- Indique si l’utilisateur visite le site local ou à distance.

Le [ `<customErrors>` section](https://msdn.microsoft.com/en-us/library/h0hfz6fc.aspx) dans `Web.config` a deux attributs qui affectent la page d’erreur est affiché : `defaultRedirect` et `mode`. L'attribut `defaultRedirect` est facultatif. Si fourni, il spécifie l’URL de la page d’erreur personnalisée et indique que la page d’erreurs personnalisées doit être affichée au lieu du YSOD d’erreur de Runtime. Le `mode` attribut est requis et qu’il accepte l’une des trois valeurs : `On`, `Off`, ou `RemoteOnly`. Ces valeurs ont le comportement suivant :

- `On`-Indique que la page d’erreur personnalisée ou la YSOD d’erreur de Runtime est affichée pour tous les visiteurs, qu’ils soient locaux ou distants.
- `Off`-Spécifie que le YSOD détails de l’Exception est affichée pour tous les visiteurs, qu’ils soient locaux ou distants.
- `RemoteOnly`-Indique que la page d’erreur personnalisée ou la YSOD d’erreur de Runtime est affichée à distance visiteurs, alors que le YSOD détails de l’Exception est affiché pour les visiteurs locales.

Sauf indication contraire, ASP.NET se comporte comme si vous aviez défini l’attribut mode `RemoteOnly` et n’avait pas spécifié un `defaultRedirect` valeur. En d’autres termes, le comportement par défaut est que le YSOD détails de l’Exception est affiché pour les visiteurs locales alors que le YSOD d’erreur de Runtime est affiché pour les visiteurs à distance. Vous pouvez substituer ce comportement par défaut en ajoutant un `<customErrors>` section à votre application web`Web.config file.`

## <a name="using-a-custom-error-page"></a>À l’aide d’une Page d’erreur personnalisée

Chaque application web doit avoir une page d’erreurs personnalisée. Il fournit une alternative plus professionnels à l’YSOD d’erreur de Runtime, il est facile de créer et configuration de l’application pour utiliser la page d’erreur personnalisé prend un peu de temps. La première étape crée la page d’erreurs personnalisée. J’ai ajouté un nouveau dossier à l’application critiques nommée `ErrorPages` et ajouté à qu’une nouvelle page ASP.NET nommé `Oops.aspx`. Utilisent la page de la même page maître à l’instar des pages sur votre site afin qu’elle hérite automatiquement de la même apparence.

[![](displaying-a-custom-error-page-cs/_static/image11.png)](displaying-a-custom-error-page-cs/_static/image10.png)

**Figure 4**: créer une Page d’erreur personnalisée

Ensuite, passez quelques minutes créer le contenu de la page d’erreurs. J’ai créé une page d’erreurs personnalisée plutôt simple avec un message indiquant qu’une erreur inattendue s’est produite et le renvoi à la page d’accueil du site.

[![](displaying-a-custom-error-page-cs/_static/image13.png)](displaying-a-custom-error-page-cs/_static/image12.png)

**Figure 5**: concevoir votre Page d’erreur personnalisée  
 ([Cliquez pour afficher l’image en taille réelle](displaying-a-custom-error-page-cs/_static/image14.png))

La page d’erreur s’est terminée, configurez l’application web pour utiliser la page d’erreur personnalisée à la place la YSOD d’erreur de Runtime. Cela est accompli en spécifiant l’URL de la page d’erreur dans le `<customErrors>` section `defaultRedirect` attribut. Ajoutez le balisage suivant à votre application `Web.config` fichier :

[!code-xml[Main](displaying-a-custom-error-page-cs/samples/sample1.xml)]

Le balisage ci-dessus configure l’application pour afficher le YSOD détails de l’Exception pour les utilisateurs visitant localement, lors de l’utilisation de la page d’erreur personnalisée Oops.aspx pour ces utilisateurs visitant à distance. Pour le voir en action, déployez votre site Web à l’environnement de production, puis visitez la page Genre.aspx sur le site actif avec une valeur de chaîne de requête non valide. Vous devez voir la page d’erreur personnalisée (font référence aux **Figure 3**).

Pour vérifier que la page d’erreur personnalisée apparaît uniquement aux utilisateurs distants, visitez le `Genre.aspx` page avec une chaîne de requête non valide à partir de l’environnement de développement. Vous devez voir toujours le YSOD détails de l’Exception (font référence aux **Figure 1**). Le `RemoteOnly` paramètre s’assure que les utilisateurs visitant le site sur l’environnement de production voient la page d’erreurs personnalisée pendant que les développeurs qui travaillent localement continuent afficher les détails de l’exception.

## <a name="notifying-developers-and-logging-error-details"></a>Pour informer les développeurs et journalisation des détails de l’erreur

Les erreurs qui se produisent dans l’environnement de développement ont été causées par le développeur assis à son ordinateur. Elle s’affiche les informations de l’exception dans le YSOD détails de l’Exception, et elle sait quelle procédure qu’elle était occupée à effectuer lorsque l’erreur s’est produite. Mais, en cas d’erreur sur la production, le développeur n’a aucune connaissance qu’une erreur s’est produite, sauf si l’utilisateur final sur le site prend du temps pour signaler l’erreur. Et même si l’utilisateur est hors de sa façon pour avertir l’équipe de développement, une erreur s’est produite, sans connaître le type d’exception, le message et la trace de la pile il peut être difficile à diagnostiquer la cause de l’erreur, sans parler de corriger.

C’est pourquoi qu'il est essentiel que les erreurs dans l’environnement de production sont connecté à un magasin persistant (par exemple, une base de données) et que les développeurs sont informés de cette erreur. La page d’erreur personnalisée peut sembler un bon point de faire de cet enregistrement et la notification. Malheureusement, la page d’erreur personnalisée n’a pas accès aux détails de l’erreur et ne peut donc pas être utilisée pour se connecter à ces informations. La bonne nouvelle est qu’il existe plusieurs méthodes pour intercepter les détails de l’erreur et pour les connecter, et les trois didacticiels ce sujet, consultez plus en détail.

## <a name="using-different-custom-error-pages-for-different-http-error-statuses"></a>À l’aide des Pages d’erreurs personnalisées différentes pour les États d’erreur HTTP différent

Lorsqu’une exception est levée par une page ASP.NET et n’est pas gérée, l’exception percolates jusqu'à l’exécution ASP.NET, qui affiche la page d’erreurs configuré. Si une demande est fourni dans le moteur ASP.NET mais ne peut pas être traitée pour une raison quelconque, peut-être que le fichier demandé est introuvable ou lire des autorisations ont été désactivées pour le fichier, puis le moteur ASP.NET déclenche une `HttpException`. Cette exception, telles que les exceptions levées à partir de pages ASP.NET se propage jusqu'à l’exécution, à l’origine de la page d’erreur approprié à afficher.

Cela signifie pour l’application web en production est que si un utilisateur demande une page qui n’est pas trouvée, puis ils verront la page d’erreurs personnalisée. **Figure 6** montre un exemple de ce type. Étant donné que la demande concerne une page inexistante (`NoSuchPage.aspx`), un `HttpException` est levée et la page d’erreur personnalisé s’affiche (Notez la référence à `NoSuchPage.aspx` dans le `aspxerrorpath` paramètre querystring).

[![](displaying-a-custom-error-page-cs/_static/image16.png)](displaying-a-custom-error-page-cs/_static/image15.png)

**Figure 6**: le Runtime ASP.NET affiche la configuré erreur Page en réponse à une demande non valide ([cliquez pour afficher l’image en taille réelle](displaying-a-custom-error-page-cs/_static/image17.png))

Par défaut, tous les types d’erreurs provoquent la même page d’erreur personnalisé à afficher. Toutefois, vous pouvez spécifier une page autre erreur personnalisée pour une utilisation spécifique de HTTP état code `<error>` les éléments enfants dans la `<customErrors>` section. Par exemple, une page d’erreurs différentes affichée dans le cas d’une page d’erreur, ayant un code d’état HTTP de 404 introuvable pour mettre à jour le `<customErrors>` section à inclure le balisage suivant :

[!code-xml[Main](displaying-a-custom-error-page-cs/samples/sample2.xml)]

Cette modification en place, chaque fois qu’un utilisateur visite à distance demande une ressource ASP.NET qui n’existe pas, il est redirigé vers la `404.aspx` page d’erreur personnalisée au lieu de `Oops.aspx`. En tant que **Figure 7** illustre, le `404.aspx` page peut inclure un message plus spécifique à la page d’erreur personnalisée général.

> [!NOTE]
> Extraire [des Pages d’erreur 404, une fois plus](http://www.smashingmagazine.com/2009/01/29/404-error-pages-one-more-time/) pour obtenir des conseils sur la création de pages d’erreur 404 efficace.


[![](displaying-a-custom-error-page-cs/_static/image19.png)](displaying-a-custom-error-page-cs/_static/image18.png)**Figure 7**: la Page d’erreur 404 personnalisée affiche un Message plus ciblé que`Oops.aspx`  
 ([Cliquez pour afficher l’image en taille réelle](displaying-a-custom-error-page-cs/_static/image20.png)) 

Étant donné que vous savez que le `404.aspx` page est atteint uniquement quand l’utilisateur effectue une demande pour une page qui n’a été trouvée, vous pouvez améliorer cette page d’erreur personnalisée pour inclure les fonctionnalités pour aider l’utilisateur à résoudre ce type d’erreur spécifique. Par exemple, vous pouvez créer une table de base de données qui mappe connue une URL incorrecte pour la bonne URL, puis le `404.aspx` page d’erreur personnalisée exécuter une requête de table et de suggérer des pages de l’utilisateur essaie d’accéder.

> [!NOTE]
> La page d’erreur personnalisée est uniquement affichée lorsqu’une demande est faite à une ressource gérée par le moteur ASP.NET. Comme expliqué dans la [principales différences entre IIS et le serveur de développement ASP.NET](core-differences-between-iis-and-the-asp-net-development-server-cs.md) didacticiel, le serveur web peut-être gérer certaines demandes lui-même. Par défaut, IIS web server traite les requêtes de contenu statique, comme des images et des fichiers HTML sans appeler le moteur ASP.NET. Par conséquent, si l’utilisateur demande un fichier inexistant image qu’ils obtiendront précédent message d’erreur 404 d’IIS par défaut plutôt que ASP. Page d’erreurs configuré du réseau.


## <a name="summary"></a>Résumé

Lorsqu’une exception non gérée se produit dans une application ASP.NET, l’utilisateur est affiché à un des trois pages d’erreur : Exception détails jaune écran de décès ; Erreur d’exécution jaune écran décès. ou une page d’erreurs personnalisée. Page erreur affichée dépend de l’application `<customErrors>` configuration et indique si l’utilisateur visite localement ou à distance. Le comportement par défaut est d’illustrer le YSOD détails de l’Exception pour les visiteurs locales et le YSOD d’erreur de Runtime pour les visiteurs à distance.

Alors que le YSOD erreur Runtime masque les informations d’erreur potentiellement sensibles à partir de l’utilisateur sur le site, il s’arrête à partir de l’apparence de votre site et rende comportant des bogues de votre application. Une meilleure approche consiste à utiliser une page d’erreur personnalisée, ce qui implique la création et la conception de la page d’erreur personnalisée et en spécifiant son URL dans le `<customErrors>` section `defaultRedirect` attribut. Vous pouvez même avoir plusieurs pages d’erreurs personnalisées pour les différents États d’erreur HTTP.

La page d’erreur personnalisée est la première étape dans une stratégie pour un site Web en production de gestion d’erreur complète. Le développeur de l’erreur de génération d’alertes et de ses détails de journalisation sont également des étapes importantes. Les trois didacticiels Explorer techniques pour la notification d’erreur et la journalisation.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Pages d’erreur, une nouvelle fois](http://www.smashingmagazine.com/2009/01/29/404-error-pages-one-more-time/)
- [Règles de conception pour les Exceptions](https://msdn.microsoft.com/en-us/library/ms229014.aspx)
- [Pages d’erreur convivial](http://aspnet.4guysfromrolla.com/articles/090606-1.aspx)
- [Gestion et levée des Exceptions](https://msdn.microsoft.com/en-us/library/5b2yeyab.aspx)
- [Correctement à l’aide des Pages d’erreurs personnalisées dans ASP.NET](http://professionalaspnet.com/archive/2007/09/30/Properly-Using-Custom-Error-Pages-in-ASP.NET.aspx)

>[!div class="step-by-step"]
[Précédent](strategies-for-database-development-and-deployment-cs.md)
[Suivant](processing-unhandled-exceptions-cs.md)
