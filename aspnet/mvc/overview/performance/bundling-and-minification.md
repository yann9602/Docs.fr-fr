---
uid: mvc/overview/performance/bundling-and-minification
title: Groupement et la minimisation | Documents Microsoft
author: Rick-Anderson
description: "Groupement et la minimisation sont deux techniques que vous pouvez utiliser dans ASP.NET 4.5 pour améliorer les temps de chargement de demande. Groupement et la minimisation améliore les temps de chargement en reducin..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/23/2012
ms.topic: article
ms.assetid: 5894dc13-5d45-4dad-8096-136499120f1d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/performance/bundling-and-minification
msc.type: authoredcontent
ms.openlocfilehash: 7192481de46c36f7de71164766e68afdbba74f6d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="bundling-and-minification"></a>Groupement et minimisation
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Groupement et la minimisation sont deux techniques que vous pouvez utiliser dans ASP.NET 4.5 pour améliorer les temps de chargement de demande. Groupement et la minimisation améliore les temps de chargement en réduisant le nombre de demandes au serveur et de réduire la taille des actifs demandés (par exemple, CSS et JavaScript)


La plupart des navigateurs principaux actuels limite le nombre de [connexions simultanées](http://www.browserscope.org/?category=network) par chaque nom d’hôte à six. Cela signifie que pendant le traitement des demandes de six, les requêtes supplémentaires pour les ressources sur un ordinateur hôte seront mises en attente par le navigateur. Dans l’image ci-dessous, les onglets de réseau Internet Explorer F12 developer tools présente la temporisation pour les ressources requises par la vue à propos d’un exemple d’application.

![B/M](bundling-and-minification/_static/image1.png)

Les barres grises affichent le temps que la demande est en file d’attente par le navigateur en attente sur la limite de six connexion. La barre jaune est la durée de la demande au premier octet, autrement dit, le temps nécessaire pour envoyer la demande et la réception de la première réponse à partir du serveur. Les barres bleues indiquent le temps nécessaire pour recevoir les données de réponse du serveur. Vous pouvez double-cliquer sur un élément multimédia pour obtenir des informations de minutage détaillées. Par exemple, l’illustration suivante montre les détails de minuterie pour le chargement du */Scripts/MyScripts/JavaScript6.js* fichier.

![](bundling-and-minification/_static/image2.png)

L’image précédente montre la **Démarrer** événement, afin d’obtenir l’heure de la demande a été mise en attente en raison de l’Explorateur limite le nombre de connexions simultanées. Dans ce cas, la demande a été en file d’attente pour 46 millisecondes en attente d’une autre demande terminer.

## <a name="bundling"></a>Regroupement

Regroupement d’est une nouvelle fonctionnalité dans ASP.NET 4.5 qui vous permettent de combiner ou de regrouper plusieurs fichiers dans un seul fichier. Vous pouvez créer le code CSS, JavaScript et autres groupes. Moins de fichiers signifie moins de demandes HTTP et qui peut améliorer les performances de chargement première page.

L’illustration suivante montre la même vue de minutage de la vue à propos d’indiqué précédemment, mais cette fois avec le groupement et la minimisation activée.

![](bundling-and-minification/_static/image3.png)

## <a name="minification"></a>Minification

Minimisation effectue diverses optimisations de code différentes css, telles que la suppression d’un espace blanc inutile et les commentaires et de raccourcir les noms de variables pour un caractère ou de scripts. Considérez la fonction JavaScript suivante.

[!code-javascript[Main](bundling-and-minification/samples/sample1.js)]

Après réduction, la fonction est réduite à ce qui suit :

[!code-javascript[Main](bundling-and-minification/samples/sample2.js)]

Outre la suppression des commentaires et espaces inutiles, les paramètres suivants et les noms de variables ont été renommés (abrégé) comme suit :

| **Original** | **Renommé** |
| --- | --- |
| imageTagAndImageID | n |
| imageContext | t |
| imageElement | g |

## <a name="impact-of-bundling-and-minification"></a>Impact de groupement et minimisation

Le tableau suivant montre plusieurs différences importantes entre la liste de tous les composants individuellement et à l’aide de groupement et la minimisation (B/M) dans l’exemple de programme.

|  | **À l’aide de B/M** | **Sans B/M** | **Change** |
| --- | --- | --- | --- |
| **Demandes de fichiers** | 9 | 34 | 256% |
| **Ko envoyé** | 3.26 | 11.92 | 266% |
| **Ko reçus** | 388.51 | 530 | 36% |
| **Temps de chargement** | 510 MS | 780 MS | 53% |

Les octets envoyés avaient une réduction significative avec regroupement comme les navigateurs sont assez détaillés avec les en-têtes HTTP qu’ils s’appliquent sur les demandes. La réduction du nombre d’octets reçus n’est pas aussi volumineux, car les fichiers plus volumineux (*Scripts\jquery-ui-1.8.11.min.js* et *Scripts\jquery-1.7.1.min.js*) sont déjà réduites. Remarque : Le minutage de l’exemple de programme utilisé le [Fiddler](http://www.fiddler2.com/fiddler2/) outil pour simuler un réseau lent. (À partir de la Fiddler **règles** menu, sélectionnez **performances** puis **simuler la vitesse de Modem**.)

## <a name="debugging-bundled-and-minified-javascript"></a>Débogage groupée et réduites de JavaScript

Il est facile de déboguer votre JavaScript dans un environnement de développement (où le [compilation élément](https://msdn.microsoft.com/library/s10awwz0.aspx) dans les *Web.config* fichier est défini sur `debug="true"` ), car les fichiers JavaScript ne sont pas fournis ou réduites. Vous pouvez également déboguer une version Release où vos fichiers JavaScript sont regroupés et réduites. Utilisez les outils de développement Internet Explorer F12, déboguer une fonction JavaScript dans un regroupement réduite à l’aide de l’approche suivante :

1. Sélectionnez le **Script** onglet, puis sélectionnez le **démarrer le débogage** bouton.
2. Sélectionnez le groupe qui contient la fonction JavaScript à déboguer à l’aide du bouton de ressources.  
    ![](bundling-and-minification/_static/image4.png)
3. Mettre en forme le code JavaScript réduite en sélectionnant le **bouton Configuration** ![](bundling-and-minification/_static/image5.png), puis en sélectionnant **Format JavaScript**.
4. Dans le **recherche titre** zone d’entrée de t, sélectionnez le nom de la fonction que vous souhaitez déboguer. Dans l’image suivante, **AddAltToImg** a été entré dans le **recherche titre** zone d’entrée de t.  
    ![](bundling-and-minification/_static/image6.png)

Pour plus d’informations sur le débogage avec les outils de développement F12, consultez l’article MSDN [utilisant les outils de développement F12 pour déboguer les erreurs JavaScript](https://msdn.microsoft.com/library/ie/gg699336(v=vs.85).aspx).

## <a name="controlling-bundling-and-minification"></a>Contrôle de regroupement et minimisation

Groupement et la minimisation est activé ou désactivé en définissant la valeur de l’attribut de débogage dans le [compilation élément](https://msdn.microsoft.com/library/s10awwz0.aspx) dans les *Web.config* fichier. Dans le code XML suivant, `debug` est un groupement de possède la valeur true, et la réduction est désactivée.

[!code-xml[Main](bundling-and-minification/samples/sample3.xml?highlight=2)]

Pour activer le groupement et la minimisation, définissez la `debug` la valeur « False ». Vous pouvez remplacer le *Web.config* avec la `EnableOptimizations` propriété sur la `BundleTable` classe. Le code suivant permet de groupement et la minimisation et remplace tout paramètre dans le *Web.config* fichier.

[!code-csharp[Main](bundling-and-minification/samples/sample4.cs?highlight=7)]

> [!NOTE]
> À moins que `EnableOptimizations` est `true` ou l’attribut de débogage dans le [compilation, élément](https://msdn.microsoft.com/library/s10awwz0.aspx) dans les *Web.config* fichier est défini sur `false`, fichiers ne seront pas fournis ou réduites. En outre, la version .min de fichiers ne sera pas utilisée, les versions de débogage complet seront sélectionnées. `EnableOptimizations`remplace l’attribut de débogage dans le [compilation élément](https://msdn.microsoft.com/library/s10awwz0.aspx) dans les *Web.config* fichier


## <a name="using-bundling-and-minification-with-aspnet-web-forms-and-web-pages"></a>À l’aide de regroupement et la minimisation avec Web Forms ASP.NET et des Pages Web

- Pour les Pages Web, consultez le billet de blog [Ajout de l’optimisation de Web pour un Site Web Pages](https://blogs.msdn.com/b/rickandy/archive/2012/08/15/adding-web-optimization-to-a-web-pages-site.aspx).
- Pour Web Forms, consultez le billet de blog [Ajout de groupement et la minimisation pour Web Forms](https://blogs.msdn.com/b/rickandy/archive/2012/08/14/adding-bundling-and-minification-to-web-forms.aspx).

## <a name="using-bundling-and-minification-with-aspnet-mvc"></a>À l’aide de regroupement et la minimisation avec ASP.NET MVC

Dans cette section, nous allons créer un ASP.NET MVC de projet pour examiner le groupement et minimisation. Commencez par créer un nouveau projet ASP.NET MVC internet nommé **MvcBM** sans modifier les valeurs par défaut.

Ouvrir le *application\_Start\BundleConfig.cs* de fichiers et d’examiner le `RegisterBundles` méthode qui permet de créer, enregistrer et configurer des groupes. Le code suivant montre une partie de la `RegisterBundles` (méthode).

[!code-csharp[Main](bundling-and-minification/samples/sample5.cs)]

Le code précédent crée un nouveau lot JavaScript nommé *~/bundles/jquery* qui inclut tous les contrôles (qui est debug ou réduites mais pas. *VSDoc*) des fichiers dans le *Scripts* dossier qui correspond à la chaîne de caractère générique « .js ~/Scripts/jquery-{version} ». Dans ASP.NET MVC 4, cela signifie que dans une configuration de débogage, le fichier *jquery-1.7.1.js* seront ajoutés au regroupement. Dans une configuration release, *jquery-1.7.1.min.js* sera ajouté. L’infrastructure de regroupement suit plusieurs conventions courantes telles que :

- Sélection d’un fichier « .min » pour cette version lorsque « FileX.min.js » et « FileX.js » existent.
- Sélectionner la version de .min « non » pour le débogage.
- En ignorant «-vsdoc » (par exemple, jquery-1.7.1-vsdoc.js), les fichiers qui sont utilisés uniquement par IntelliSense.

Le `{version}` correspondance générique ci-dessus est utilisé pour créer automatiquement un ensemble de jQuery avec la version appropriée de jQuery dans votre *Scripts* dossier. Dans cet exemple, à l’aide d’un caractère générique offre les avantages suivants :

- Vous permet d’utiliser NuGet pour mettre à jour vers une version plus récente de jQuery sans modifier le code de regroupement précédente ou les références de jQuery dans vos pages de vue.
- Sélectionne la version complète pour les configurations debug et la version « .min » pour la mise en production crée automatiquement.

## <a name="using-a-cdn"></a>À l’aide d’un CDN

 Le code suivant remplace le groupe local jQuery avec un groupe de jQuery CDN.

[!code-csharp[Main](bundling-and-minification/samples/sample6.cs)]

Dans le code ci-dessus, vous sera demandé jQuery du CDN tandis que dans la version en mode et la version debug de jQuery seront extraites localement en mode débogage. Lorsque vous utilisez un CDN, vous devez disposer un mécanisme de secours en cas d’échec de la demande CDN. Le balisage suivant, extrait de la fin de la mise en page fichier montre ajouté à la requête jQuery si la panne CDN.

[!code-cshtml[Main](bundling-and-minification/samples/sample7.cshtml?highlight=5-13)]

## <a name="creating-a-bundle"></a>Création d’un regroupement

Le [groupe](https://msdn.microsoft.com/library/system.web.optimization.bundle(v=VS.110).aspx) classe `Include` méthode prend un tableau de chaînes, où chaque chaîne est un chemin d’accès virtuel à la ressource. Le code suivant à partir de la méthode RegisterBundles dans le *application\_Start\BundleConfig.cs* fichier montre comment plusieurs fichiers sont ajoutés à un regroupement :

[!code-csharp[Main](bundling-and-minification/samples/sample8.cs)]

Le [groupe](https://msdn.microsoft.com/library/system.web.optimization.bundle(v=VS.110).aspx) classe `IncludeDirectory` méthode est fournie pour ajouter tous les fichiers dans un répertoire (et éventuellement tous ses sous-répertoires) qui correspondent à un modèle de recherche. Le [groupe](https://msdn.microsoft.com/library/system.web.optimization.bundle(v=VS.110).aspx) classe `IncludeDirectory` API est indiqué ci-dessous :

[!code-csharp[Main](bundling-and-minification/samples/sample9.cs)]

Offres groupées sont référencées dans les vues à l’aide de la méthode de rendu ( `Styles.Render` pour CSS et `Scripts.Render` pour JavaScript). Le balisage suivant à partir de la *Views\Shared\\_Layout.cshtml* fichier montre comment les vues de projet par défaut ASP.NET internet référencent offres CSS et JavaScript.

[!code-cshtml[Main](bundling-and-minification/samples/sample10.cshtml?highlight=5-6,11)]

Notez que les méthodes de rendu prend un tableau de chaînes, vous pouvez ajouter plusieurs lots sur une seule ligne de code. En règle générale, vous devez utiliser les méthodes de rendu qui créent le HTML nécessaire pour faire référence à l’élément multimédia. Vous pouvez utiliser la `Url` méthode permettant de générer l’URL à l’élément multimédia sans les balises nécessaires pour faire référence à l’élément multimédia. Supposons que vous souhaitez utiliser la nouvelle HTML5 [async](http://www.whatwg.org/specs/web-apps/current-work/#attr-script-async) attribut. Le code suivant montre comment faire référence à l’aide de modernizr à la `Url` (méthode).

[!code-cshtml[Main](bundling-and-minification/samples/sample11.cshtml?highlight=11)]

## <a name="using-the--wildcard-character-to-select-files"></a>À l’aide de la «\*« caractère générique pour sélectionner les fichiers

Le chemin d’accès virtuel spécifié dans le `Include` (méthode) et la recherche de modèle dans le `IncludeDirectory` méthode peut accepter un «\*« caractère générique en tant que préfixe ou suffixe à dans le dernier segment de chemin d’accès. La chaîne de recherche respecte la casse. Le `IncludeDirectory` méthode a la possibilité de rechercher des sous-répertoires.

Prenez un projet avec les fichiers JavaScript suivants :

- *Scripts\Common\AddAltToImg.js*
- *Scripts\Common\ToggleDiv.js*
- *Scripts\Common\ToggleImg.js*
- *Scripts\Common\Sub1\ToggleLinks.js*

![dir imag](bundling-and-minification/_static/image7.png)

Le tableau suivant présente les fichiers ajoutés à un groupe en utilisant le caractère générique, comme indiqué :

| **Call** | **Fichiers ajoutés ou Exception levée** |
| --- | --- |
| Include("~/Scripts/Common/\*.js") | *AddAltToImg.js, ToggleDiv.js, ToggleImg.js* |
| Include("~/Scripts/Common/T\*.js") | Exception de modèle non valide. Le caractère générique est uniquement autorisé sur le préfixe ou le suffixe. |
| Include("~/Scripts/Common/\*og.\*") | Exception de modèle non valide. Qu’un seul caractère générique est autorisé. |
| "Include("~/Scripts/Common/T\*") | *ToggleDiv.js, ToggleImg.js* |
| "Include("~/Scripts/Common/\*") | Exception de modèle non valide. Un segment de caractère générique pure n’est pas valide. |
| IncludeDirectory("~/Scripts/Common", "T\*") | *ToggleDiv.js, ToggleImg.js* |
| IncludeDirectory("~/Scripts/Common", "T\*",true) | *ToggleDiv.js, ToggleImg.js, ToggleLinks.js* |

Ajouter explicitement chaque fichier à une offre groupée est généralement préférable sur le chargement de caractère générique des fichiers pour les raisons suivantes :

- Ajout de scripts par défaut de caractère générique pour les charger dans l’ordre alphabétique, qui est généralement pas ce que vous souhaitez. Fichiers CSS et JavaScript fréquemment doivent être ajoutés dans l’ordre (non alphabétiques). Vous pouvez réduire ce risque en ajoutant une personnalisée [IBundleOrderer](https://msdn.microsoft.com/library/system.web.optimization.ibundleorderer(VS.110).aspx) implémentation, mais ajouter explicitement chaque fichier est moins sujet aux erreurs. Par exemple, vous pouvez ajouter de nouvelles ressources dans un dossier dans le futur, qui peuvent vous amener à modifier votre [IBundleOrderer](https://msdn.microsoft.com/library/system.web.optimization.ibundleorderer(VS.110).aspx) mise en œuvre.
- Afficher les fichiers spécifiques ajoutées à un répertoire à l’aide du caractère générique du chargement peuvent être inclus dans toutes les vues faisant référence à cette offre groupée. Si le script d’affichage spécifique est ajouté à un groupe, vous pouvez obtenir une erreur de JavaScript sur les autres vues qui font référence à l’offre groupée.
- Fichiers CSS importer d’autres fichiers entraîner des fichiers importés chargés à deux reprises. Par exemple, le code suivant crée un regroupement avec la plupart des fichiers CSS thème de l’interface utilisateur de jQuery chargés à deux reprises. 

    [!code-csharp[Main](bundling-and-minification/samples/sample12.cs)]

 Le sélecteur de caractère générique «\*.css » permet de bénéficier de chaque fichier CSS dans le dossier, y compris le *Content\themes\base\jquery.ui.all.css* fichier. Le *jquery.ui.all.css* fichier importe d’autres fichiers CSS.

## <a name="bundle-caching"></a>Regrouper la mise en cache

Regroupements définir l’en-tête d’expiration HTTP un an à partir de la création de l’application. Si vous accédez à une page précédemment affichée, montre Fiddler IE ne rend pas une demande conditionnelle pour le regroupement, autrement dit, il existe aucune demande HTTP GET à partir d’Internet Explorer pour les regroupements et aucune réponse HTTP 304 à partir du serveur. Vous pouvez forcer Internet Explorer pour effectuer une demande conditionnelle pour chaque regroupement avec la touche F5 (entraînant une réponse HTTP 304 pour chaque regroupement). Vous pouvez forcer une actualisation complète à l’aide de ^ F5 (entraînant une réponse HTTP 200 pour chaque regroupement.)

L’illustration suivante montre le **mise en cache** onglet du volet Fiddler réponse :

![image de mise en cache de Fiddler](bundling-and-minification/_static/image8.png)

La demande   
`http://localhost/MvcBM_time/bundles/AllMyScripts?v=r0sLDicvP58AIXN_mc3QdyVvVj5euZNzdsa2N1PKvb81`  
 est de l’offre groupée **AllMyScripts** et contient une paire de chaîne de requête **v = r0sLDicvP58AIXN\_mc3QdyVvVj5euZNzdsa2N1PKvb81**. La chaîne de requête **v** a la valeur de jeton qui est un identificateur unique utilisé pour la mise en cache. Tant que le groupe ne change pas, l’application ASP.NET demande le **AllMyScripts** regrouper à l’aide de ce jeton. Si n’importe quel fichier dans le groupe de modifications, l’infrastructure d’optimisation ASP.NET génère un nouveau jeton, garantissant que les demandes du navigateur pour le regroupement obtiennent le dernier groupe.

Si vous exécutez les outils de développement F12 d’Internet Explorer 9 et que vous accédez à une page chargée, IE incorrectement montre les requêtes GET conditionnelles apportées à chaque lot et le serveur HTTP 304. Vous pouvez en savoir pourquoi Internet Explorer 9 a des problèmes de déterminer si une demande conditionnelle a été effectuée dans le billet de blog [à l’aide de CDN et expire pour améliorer les performances de Site Web](https://blogs.msdn.com/b/rickandy/archive/2011/05/21/using-cdns-to-improve-web-site-performance.aspx).

## <a name="less-coffeescript-scss-sass-bundling"></a>MOINS, CoffeeScript, SCSS, Sass de regroupement.

Le groupement et la minimisation framework fournit un mécanisme permettant de traiter les langages intermédiaires telles que [SCSS](http://sass-lang.com/), [Sass](http://sass-lang.com/), [moins](http://www.dotlesscss.org/) ou [Coffeescript ](http://coffeescript.org/)et appliquer des transformations telles que de réduction à l’offre groupée résultante. Par exemple, pour ajouter [.less](http://www.dotlesscss.org/) fichiers à votre projet MVC 4 :

1. Créez un dossier pour moins de votre contenu. L’exemple suivant utilise le *Content\MyLess* dossier.
2. Ajouter le [.less](http://www.dotlesscss.org/) package NuGet **sans point** à votre projet.  
    ![Installation sans point de NuGet](bundling-and-minification/_static/image9.png)
3. Ajoutez une classe qui implémente le [IBundleTransform](https://msdn.microsoft.com/library/system.web.optimization.ibundletransform(VS.110).aspx) interface. Pour la transformation .less, ajoutez le code suivant à votre projet.

    [!code-csharp[Main](bundling-and-minification/samples/sample13.cs)]
4. Créer un groupe de fichiers LESS avec la `LessTransform` et [CssMinify](https://msdn.microsoft.com/library/system.web.optimization.cssminify(VS.110).aspx) transformer. Ajoutez le code suivant à la `RegisterBundles` méthode dans le *application\_Start\BundleConfig.cs* fichier.

    [!code-csharp[Main](bundling-and-minification/samples/sample14.cs)]
5. Ajoutez le code suivant à toutes les vues qui fait référence à l’application de moins.

    [!code-cshtml[Main](bundling-and-minification/samples/sample15.cshtml)]

## <a name="bundle-considerations"></a>Considérations de groupe

Une bonne convention à suivre lors de la création de groupes est à inclure « groupe » en tant que préfixe du nom de groupe. Cela empêchera une éventuelle [conflit de routage](https://forums.asp.net/post/5012037.aspx).

Une fois que vous mettez à jour un fichier dans un regroupement, un nouveau jeton est généré pour le paramètre de chaîne de requête de regroupement et la prochaine fois qu’un client demande une page qui contient le groupe doit être téléchargé à l’offre complète. Dans le balisage traditionnel où chaque élément multimédia est répertorié individuellement, vous est téléchargé uniquement le fichier modifié. Ressources qui changent fréquemment ne peuvent pas être de bons candidats pour le regroupement.

Groupement et la minimisation principalement améliorent le premier temps de chargement de demande de page. Une fois qu’une page Web a été demandée, le navigateur met en cache les ressources (JavaScript, CSS et images) afin de groupement et la minimisation ne fournissent n’importe quel légèrement les performances lors de la demande de la même page ou de pages sur le même site demande les mêmes actifs. Si vous ne définissez pas l’expiration en-tête correctement sur vos éléments multimédias, vous n’utilisez pas groupement et la minimisation l’heuristique d’actualisation navigateurs marque les éléments multimédias obsolètes après quelques jours et, le navigateur nécessite une demande de validation pour chaque élément multimédia. Dans ce cas, groupement et la minimisation fournissent une augmentation des performances après la première demande de page. Pour plus d’informations, consultez le blog de [à l’aide de CDN et expire pour améliorer les performances de Site Web](https://blogs.msdn.com/b/rickandy/archive/2011/05/21/using-cdns-to-improve-web-site-performance.aspx).

La limitation du navigateur de six connexions simultanées par chaque nom d’hôte peut être atténuée en utilisant un [CDN](https://blogs.msdn.com/b/rickandy/archive/2011/05/21/using-cdns-to-improve-web-site-performance.aspx). Étant donné que le CDN doit avoir un nom d’hôte différents à votre site d’hébergement, des demandes de ressources du CDN comptera pas la limite de six connexions simultanées à votre environnement d’hébergement. Un CDN peut également fournir bord avantages de la mise en cache et mise en cache du package commun.

Regroupements doivent être partitionnées par les pages qui en ont besoin. Par exemple, la valeur par défaut du modèle ASP.NET MVC d’une application internet crée un groupe de Validation jQuery distinct de jQuery. Étant donné que les affichages par défaut créés n’ont aucune entrée et ne publiez pas de valeurs, ils n’indiquent pas le groupe de validation.

Le `System.Web.Optimization` espace de noms est implémenté dans System.Web.Optimization.DLL. Il tire parti de la bibliothèque WebGrease (WebGrease.dll) pour les fonctions de réduction, qui à son tour utilise Antlr3.Runtime.dll.

*Utiliser Twitter pour effectuer des publications rapides et de partager des liens. Le handle de mon Twitter est*:[@RickAndMSFT](http://twitter.com/RickAndMSFT)

## <a name="additional-resources"></a>Ressources supplémentaires

- Vidéo :[groupement et optimisation](https://channel9.msdn.com/Events/aspConf/aspConf/Bundling-and-Optimizing) par [Howard Dierking](https://twitter.com/#!/howard_dierking)
- [Ajout de l’optimisation du Web à un Site Web Pages](https://blogs.msdn.com/b/rickandy/archive/2012/08/15/adding-web-optimization-to-a-web-pages-site.aspx).
- [Groupement de l’ajout et la minimisation pour Web Forms](https://blogs.msdn.com/b/rickandy/archive/2012/08/14/adding-bundling-and-minification-to-web-forms.aspx).
- [Impact sur les performances de regroupement et la minimisation sur la navigation Web](https://blogs.msdn.com/b/henrikn/archive/2012/06/17/performance-implications-of-bundling-and-minification-on-http.aspx) par [Henrik F Nielsen](http://en.wikipedia.org/wiki/Henrik_Frystyk_Nielsen)[@frystyk](https://twitter.com/frystyk)
- [À l’aide du CDN et expire pour améliorer les performances de Site Web](https://blogs.msdn.com/b/rickandy/archive/2011/05/21/using-cdns-to-improve-web-site-performance.aspx) par Rick Anderson[@RickAndMSFT](https://twitter.com/#!/RickAndMSFT)
- [Réduire RTT (durée des boucles)](https://developers.google.com/speed/docs/best-practices/rtt)

## <a name="contributors"></a>Contributors

- Arts martiaux de hao
- [Howard Dierking](https://twitter.com/#!/howard_dierking)
- Diana LaRose
