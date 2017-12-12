---
uid: web-pages/overview/data/7-displaying-data-in-a-chart
title: "Affichage des données dans un graphique avec les Pages Web ASP.NET (Razor) | Documents Microsoft"
author: microsoft
description: "Ce chapitre explique comment afficher des données dans un graphique. Dans les chapitres précédents, vous avez appris comment afficher des données dans une grille et manuellement. Ce chapitre explique..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/22/2012
ms.topic: article
ms.assetid: f889fd46-4dac-4ecb-83d8-60e64c22036e
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/data/7-displaying-data-in-a-chart
msc.type: authoredcontent
ms.openlocfilehash: 15daa5fa94094fb50971514a152312fd81a749a1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="displaying-data-in-a-chart-with-aspnet-web-pages-razor"></a>Affichage des données dans un graphique avec les Pages Web ASP.NET (Razor)
====================
par [Microsoft](https://github.com/microsoft)

> Cet article explique comment utiliser un graphique pour afficher des données dans un site Web ASP.NET Web Pages (Razor) à l’aide de la `Chart` helper.
> 
> **Vous apprendrez**:
> 
> - Comment afficher des données dans un graphique.
> - Comment les graphiques de style à l’aide de thèmes intégrés.
> - L’enregistrement des graphiques et comment les mettre en cache pour de meilleures performances.
> 
> Il s’agit de programmation des fonctionnalités introduites dans l’article ASP.NET :
> 
> - Le `Chart` helper.
> 
> > [!NOTE]
> > Les informations contenues dans cet article s’applique à la version 1.0 de Pages Web ASP.NET et Web Pages 2.


<a id="The_Chart_Helper"></a>
## <a name="the-chart-helper"></a>L’Assistant graphique

Lorsque vous souhaitez afficher vos données dans un graphique, vous pouvez utiliser `Chart` helper. Le `Chart` assistance peut rendre une image qui affiche les données dans une variété de types de graphiques. Il prend en charge de nombreuses options pour la mise en forme et l’étiquetage. Le `Chart` assistance peut rendre plus de 30 types de graphiques, y compris tous les types de graphiques que vous pouvez être familiarisé avec à partir de Microsoft Excel ou d’autres outils &#8212; graphiques en aires, à barres des graphiques, des histogrammes, de ligne graphiques et les graphiques à secteurs, ainsi que de plus graphiques spécialisés tels que les graphiques boursiers.

| **Graphique en aires** ![Description : image de graphique en aires](7-displaying-data-in-a-chart/_static/image1.jpg) | **Graphique à barres** ![Description : image du type de graphique à barres](7-displaying-data-in-a-chart/_static/image2.jpg) |
| --- | --- |
| **Histogramme** ![Description : image de la colonne du type de graphique](7-displaying-data-in-a-chart/_static/image3.jpg) | **Graphique en courbes** ![Description : image de la ligne du type de graphique](7-displaying-data-in-a-chart/_static/image4.jpg) |
| **Graphique à secteurs** ![Description : image du type de graphique à secteurs](7-displaying-data-in-a-chart/_static/image5.jpg) | **Graphique boursier** ![Description : image du type de graphique boursier](7-displaying-data-in-a-chart/_static/image6.jpg) |

### <a name="chart-elements"></a>Éléments de graphique

Graphiques affichent des données et des éléments supplémentaires tels que les légendes, axes, séries et ainsi de suite. L’image suivante affiche un grand nombre des éléments de graphique que vous pouvez personnaliser lorsque vous utilisez la `Chart` helper. Cet article vous explique comment définir certaines (pas tous) de ces éléments.

![Description : Illustration des éléments de graphique](7-displaying-data-in-a-chart/_static/image7.jpg)

<a id="Creating_a_Chart"></a>
## <a name="creating-a-chart-from-data"></a>Création d’un graphique à partir des données

Les données que vous affichez dans un graphique peuvent être un tableau, à partir des résultats retournés à partir d’une base de données, ou de données qui se trouve dans un fichier XML.

### <a name="using-an-array"></a>À l’aide d’un tableau

Comme expliqué dans [Introduction à ASP.NET Web Pages de programmation à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890), un tableau vous permet de stocker une collection d’éléments similaires dans une variable unique. Vous pouvez utiliser des tableaux pour contenir les données que vous souhaitez inclure dans votre graphique.

Cette procédure montre comment vous pouvez créer un graphique à partir des données dans des tableaux, à l’aide du type de graphique par défaut. Il montre également comment afficher le graphique dans la page.

1. Créer un nouveau fichier nommé *ChartArrayBasic.cshtml*.
2. Remplacez le contenu existant avec les éléments suivants : 

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample1.cshtml)]

    Tout d’abord, le code crée un graphique et définit sa largeur et sa hauteur. Vous spécifiez le titre du graphique à l’aide de la `AddTitle` (méthode). Pour ajouter des données, vous utilisez la `AddSeries` (méthode). Dans cet exemple, vous utilisez la `name`, `xValue`, et `yValues` les paramètres de la `AddSeries` (méthode). Le `name` paramètre est affiché dans la légende du graphique. Le `xValue` paramètre contient un tableau de données qui s’affiche sur l’axe horizontal du graphique. Le `yValues` paramètre contient un tableau de données qui sont utilisés pour tracer les points verticaux du graphique.

    Le `Write` méthode réellement effectue le rendu du graphique. Dans ce cas, car vous n’avez pas spécifié un type de graphique, la `Chart` helper restitue son graphique par défaut, ce qui constitue un histogramme.
3. Exécutez la page dans le navigateur. Le navigateur affiche le graphique. 

    ![](7-displaying-data-in-a-chart/_static/image8.jpg)

### <a name="using-a-database-query-for-chart-data"></a>À l’aide d’une requête de base de données pour les données de graphique

Si les informations souhaitées dans le graphique se trouve dans une base de données, vous pouvez exécuter une requête de base de données et ensuite utiliser les données à partir des résultats pour créer le graphique. Cette procédure vous montre comment lire et afficher les données à partir de la base de données créée dans l’article [Introduction à l’utilisation d’une base de données dans les Sites ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202893).

1. Ajouter un *application\_données* dossier à la racine du site Web si le dossier n’existe pas déjà.
2. Dans le *application\_données* dossier, ajoutez le fichier de base de données nommé *SmallBakery.sdf* qui est décrit dans [Introduction à l’utilisation d’une base de données dans les Sites ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202893).
3. Créer un nouveau fichier nommé *ChartDataQuery.cshtml*.
4. Remplacez le contenu existant avec les éléments suivants :   

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample2.cshtml)]

    Tout d’abord, le code s’ouvre à la base de données SmallBakery et lui assigne à une variable nommée `db`. Cette variable représente un `Database` objet qui peut être utilisé pour lire et écrire dans la base de données. Ensuite, le code exécute une requête SQL pour obtenir le nom et le prix de chaque produit. Le code crée un graphique et lui transmet la requête de base de données en appelant du graphique `DataBindTable` (méthode). Cette méthode accepte deux paramètres : le `dataSource` paramètre concerne les données à partir de la requête et le `xField` paramètre vous permet de définir la colonne de données utilisée pour l’axe des abscisses du graphique.

    En guise d’alternative à l’utilisation de la `DataBindTable` (méthode), vous pouvez utiliser la `AddSeries` méthode de la `Chart` helper. Le `AddSeries` méthode vous permet de définir la `xValue` et `yValues` paramètres. Par exemple, au lieu d’utiliser le `DataBindTable` méthode comme suit :

    [!code-css[Main](7-displaying-data-in-a-chart/samples/sample3.css)]

    Vous pouvez utiliser la `AddSeries` méthode comme suit :

    [!code-html[Main](7-displaying-data-in-a-chart/samples/sample4.html)]

    Les deux restituent les mêmes résultats. Le `AddSeries` méthode est plus souple car vous pouvez spécifier le type de graphique et les données d’une manière plus explicite, mais le `DataBindTable` méthode est plus facile à utiliser si vous n’avez pas besoin la flexibilité supplémentaire.
5. Exécutez la page dans un navigateur. 

    ![](7-displaying-data-in-a-chart/_static/image9.jpg)

### <a name="using-xml-data"></a>Utilisation de données XML

La troisième option de création de graphiques est d’utiliser un fichier XML en tant que les données du graphique. Cela implique que le fichier XML ont également un fichier de schéma (*.xsd* fichier) qui décrit la structure XML. Cette procédure montre comment lire des données à partir d’un fichier XML.

1. Dans le *application\_données* dossier, créez un nouveau fichier XML nommé *data.xml*.
2. Remplacez le code XML existant par le code suivant, c'est-à-dire des données XML relatives aux employés d’une société fictive. 

    [!code-xml[Main](7-displaying-data-in-a-chart/samples/sample5.xml)]
3. Dans le *application\_données* dossier, créez un nouveau fichier XML nommé *data.xsd*. (Notez que l’extension de ce temps est *.xsd*.)
4. Remplacez le code XML existant avec les éléments suivants : 

    [!code-xml[Main](7-displaying-data-in-a-chart/samples/sample6.xml)]
5. Dans la racine du site Web, créez un nouveau fichier nommé *ChartDataXML.cshtml*.
6. Remplacez le contenu existant avec les éléments suivants : 

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample7.cshtml)]

    Le code crée d’abord un `DataSet` objet. Cet objet est utilisé pour gérer les données qui sont lu à partir du fichier XML et organiser selon les informations contenues dans le fichier de schéma. (Notez que la partie supérieure du code inclut l’instruction `using SystemData`. Cela est indispensable pour pouvoir travailler avec le `DataSet` objet. Pour plus d’informations, consultez [ &quot;Using&quot; instructions et des noms qualifiés complets](#SB_UsingStatements) plus loin dans cet article.)

    Ensuite, le code crée un `DataView` objet basé sur le jeu de données. La vue de données fournit un objet qui peut lier le graphique &#8212; Autrement dit, en lecture et tracer. Le graphique est lié aux données à l’aide du `AddSeries` (méthode), en tant que vous avez vu précédemment lorsque vous traitez les données de tableau, à ceci près que cette fois le `xValue` et `yValues` paramètres sont définis sur le `DataView` objet.

    Cet exemple montre également comment spécifier un type de graphique particulier. Lorsque les données sont ajoutées dans le `AddSeries` (méthode), la `chartType` paramètre est également configuré pour afficher un graphique à secteurs.
7. Exécutez la page dans un navigateur. 

    ![](7-displaying-data-in-a-chart/_static/image10.jpg)

> [!TIP] 
> 
> <a id="SB_UsingStatements"></a>
> ### <a name="using-statements-and-fully-qualified-names"></a>Instructions « Using » et les noms qualifiés complets
> 
> Le .NET Framework basée sur ASP.NET Web Pages avec syntaxe Razor se compose de plusieurs milliers de composants (classes). Pour qu’il soit facile à gérer pour travailler avec toutes ces classes, elles sont organisées en *espaces de noms*, qui sont un peu comme les bibliothèques. Par exemple, le `System.Web` espace de noms contient des classes qui prennent en charge la communication de navigateur et le serveur, le `System.Xml` espace de noms contient des classes qui sont utilisées pour créer et lire des fichiers XML, et le `System.Data` espace de noms contient des classes qui vous permettent de travailler avec des données.
> 
> Pour accéder à une classe donnée dans le .NET Framework, code doit connaître non seulement le nom de classe, mais également l’espace de noms figurant dans la classe. Par exemple, pour pouvoir utiliser le `Chart` assistance, le code doit trouver la `System.Web.Helpers.Chart` (classe), qui associe l’espace de noms (`System.Web.Helpers`) avec le nom de classe (`Chart`). Il s’agit de la classe *complet* nom &#8212; son emplacement complète, et non équivoque dans le vastness du .NET Framework. Dans le code, cela ressemble à ce qui suit :
> 
> `var myChart = new System.Web.Helpers.Chart(width: 600, height: 400) // etc.`
> 
> Toutefois, il est fastidieux (et sujet aux erreurs) pour utiliser ces noms complet, long, chaque fois que vous voulez faire référence à une classe ou une application d’assistance. Par conséquent, pour le rendre plus facile d’utiliser des noms de classe, vous pouvez *importer* les espaces de noms que vous êtes intéressé, et qui est généralement est quelques-unes parmi les nombreux espaces de noms dans le .NET Framework. Si vous avez importé un espace de noms, vous pouvez utiliser simplement un nom de classe (`Chart`) au lieu du nom qualifié complet (`System.Web.Helpers.Chart`). Lorsque votre code s’exécute et rencontre un nom de classe, il peut rechercher uniquement les espaces de noms que vous avez importé afin de déterminer cette classe.
> 
> Lorsque vous utilisez ASP.NET Web Pages avec syntaxe Razor pour créer des pages web, vous utilisez généralement le même ensemble de classes chaque fois, y compris la `WebPage` classe, les programmes d’assistance différents et ainsi de suite. Pour vous permettre d’économiser le travail de l’importation d’espaces de noms appropriés chaque fois que vous créez un site Web, ASP.NET est configuré de sorte qu’il importe automatiquement un ensemble d’espaces de noms de base pour tous les sites Web. C’est pourquoi vous n’avez pas eu à traiter des espaces de noms ou l’importation jusqu'à maintenant ; toutes les classes que vous avez travaillé avec sont dans les espaces de noms qui sont déjà importés pour vous.
> 
> Toutefois, vous avez parfois besoin de travailler avec une classe qui n’est pas dans un espace de noms est importé automatiquement pour vous. Dans ce cas, vous pouvez soit utiliser le nom qualifié complet de la classe, ou vous pouvez importer manuellement de l’espace de noms qui contient la classe. Pour importer un espace de noms, vous utilisez la `using` instruction (`import` en Visual Basic), vous savez dans un exemple de l’article.
> 
> Par exemple, le `DataSet` classe se trouve dans le `System.Data` espace de noms. Le `System.Data` espace de noms n’est pas automatiquement disponible pour les pages ASP.NET Razor. Par conséquent, pour travailler avec les `DataSet` à l’aide de son nom complet de la classe, vous pouvez utiliser le code comme suit :
> 
> `var dataSet = new System.Data.DataSet();`
> 
> Si vous devez utiliser le `DataSet` classe à plusieurs reprises vous pouvez importer un espace de noms comme suit et ensuite utiliser simplement le nom de classe dans le code :
> 
> [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample8.cshtml)]
> 
> Vous pouvez ajouter `using` instructions pour toutes les autres espaces de noms de .NET Framework vous souhaitez référencer. Toutefois, comme indiqué, vous ne devez effectuer cette opération souvent, étant donné que la plupart des classes que vous allez travailler avec les espaces de noms qui sont importés automatiquement par ASP.NET pour une utilisation dans *.cshtml* et *.vbhtml* pages.


<a id="Displaying_Charts"></a>
## <a name="displaying-charts-inside-a-web-page"></a>Afficher des graphiques à l’intérieur d’une Page Web

Dans les exemples, vous avez vu jusqu'à présent, vous créez un graphique, et ensuite le rendu du graphique directement au navigateur sous forme graphique. Dans de nombreux cas, toutefois, vous souhaitez afficher un graphique en tant que partie d’une page, non seulement par lui-même dans le navigateur. Pour cela nécessite un processus en deux étapes. La première étape consiste à créer une page qui génère le graphique, comme vous l’avez déjà vu.

La deuxième étape consiste à afficher l’image obtenue dans une autre page. Pour afficher l’image, vous utilisez HTML `<img>` élément, de la même façon que vous le feriez pour afficher une image. Toutefois, au lieu de référencer un *.jpg* ou *.png* fichier, le `<img>` références d’élément le *.cshtml* fichier qui contient le `Chart` application d’assistance qui crée le graphique. Lorsque la page d’affichage s’exécute, le `<img>` élément reçoit la sortie de la `Chart` helper et effectue le rendu du graphique.

![](7-displaying-data-in-a-chart/_static/image11.jpg)

1. Créez un fichier nommé *ShowChart.cshtml*.
2. Remplacez le contenu existant avec les éléments suivants : 

    [!code-html[Main](7-displaying-data-in-a-chart/samples/sample9.html)]

    Le code utilise le `<img>` élément pour afficher le graphique que vous avez créé précédemment dans le *ChartArrayBasic.cshtml* fichier.
3. Exécutez la page web dans un navigateur. Le *ShowChart.cshtml* fichier affiche l’image de graphique en fonction du code contenu dans le *ChartArrayBasic.cshtml* fichier.

<a id="Styling_a_Chart"></a>
## <a name="styling-a-chart"></a>Un graphique de conception de styles

Le `Chart` helper prend en charge un grand nombre d’options qui vous permettent de personnaliser l’apparence du graphique. Vous pouvez définir des couleurs, les polices, les bordures et ainsi de suite. Un moyen simple de personnaliser l’apparence d’un graphique consiste à utiliser un *thème*. Les thèmes sont des ensembles d’informations qui spécifient comment effectuer le rendu d’un graphique à l’aide de polices, couleurs, étiquettes, palettes, les bordures et les effets. (Notez que le style d’un graphique n’indique pas le type de graphique).

Le tableau suivant répertorie les thèmes intégrés.

| Thème | Description |
| --- | --- |
| `Vanilla` | Affiche les colonnes rouges sur un arrière-plan blanc. |
| `Blue` | Affiche bleu colonnes sur un arrière-plan dégradé bleu. |
| `Green` | Affiche bleu colonnes sur un arrière-plan dégradé vert. |
| `Yellow` | Affiche les colonnes oranges sur un arrière-plan dégradé jaune. |
| `Vanilla3D` | Affiche les colonnes rouges 3D sur un arrière-plan blanc. |

Vous pouvez spécifier le thème à utiliser lorsque vous créez un nouveau graphique.

1. Créer un nouveau fichier nommé *ChartStyleGreen.cshtml*.
2. Remplacez le contenu existant dans la page avec les éléments suivants :

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample10.cshtml)]

    Ce code est identique à l’exemple précédent qui utilise la base de données pour les données, mais il ajoute le `theme` paramètre lorsqu’il crée le `Chart` objet. Voici le code modifié :

    [!code-csharp[Main](7-displaying-data-in-a-chart/samples/sample11.cs)]
3. Exécutez la page dans un navigateur. Vous voyez les mêmes données qu’avant, mais le graphique ressemble plus sophistiquée : 

    ![](7-displaying-data-in-a-chart/_static/image12.jpg)

<a id="Saving_a_Chart"></a>
## <a name="saving-a-chart"></a>L’enregistrement d’un graphique

Lorsque vous utilisez le `Chart` d’assistance que vous avez déjà vu jusqu'à présent dans cet article, l’application d’assistance recrée le graphique à partir de zéro chaque fois qu’elle est appelée. Si nécessaire, ou le code pour le graphique également interroge à nouveau la base de données relit le fichier XML pour obtenir les données. Dans certains cas, cela peut être une opération complexe, comme si la base de données que vous effectuez une requête est volumineuse, ou si le fichier XML contient une grande quantité de données. Même si le graphique n’implique pas une grande quantité de données, le processus de création dynamique d’une image prend les ressources du serveur, et si de nombreuses personnes demande l’ou les pages qui affichent le graphique, il peut y avoir un impact sur les performances de votre site Web.

Pour vous aider à réduire l’impact potentiel de création d’un graphique, vous pouvez créer une graphique la première heure vous en avez besoin, puis l’enregistrer. Lorsque le graphique est à nouveau nécessaire, au lieu de la régénération, vous pouvez simplement récupérer la version enregistrée et restituer.

Vous pouvez enregistrer un graphique des façons suivantes :

- Mettre en cache le graphique de mémoire de l’ordinateur (sur le serveur).
- Enregistrez le graphique en tant qu’image.
- Enregistrez le graphique dans un fichier XML. Cette option vous permet de modifier le graphique avant de l’enregistrer.

### <a name="caching-a-chart"></a>Mise en cache d’un graphique

Une fois que vous avez créé un graphique, vous pouvez mettre en cache. Mise en cache d’un graphique signifie que ne doit pas nécessairement être recréé s’il doit être affiché à nouveau. Lorsque vous enregistrez un graphique dans le cache, vous lui donnez une clé qui doit être unique dans ce graphique.

Graphiques enregistrés dans le cache peuvent être supprimées si le serveur manque de mémoire. En outre, le cache est désactivé si le redémarrage de votre application pour une raison quelconque. Par conséquent, le moyen standard de travailler avec un graphique mis en cache consiste à toujours vérifier d’abord si elle est disponible dans le cache, dans le cas contraire, puis à créer ou recréer.

1. À la racine de votre site Web, créez un fichier nommé *ShowCachedChart.cshtml*.
2. Remplacez le contenu existant avec les éléments suivants : 

    [!code-html[Main](7-displaying-data-in-a-chart/samples/sample12.html)]

    Le `<img>` balise inclut un `src` attribut qui pointe vers le *ChartSaveToCache.cshtml* de fichiers et transmet une clé à la page comme une chaîne de requête. La clé contient la valeur &quot;myChartKey&quot;. Le *ChartSaveToCache.cshtml* fichier contient le `Chart` assistance qui crée le graphique. Vous allez créer cette page dans quelques instants.

    À la fin de la page, il existe un lien vers une page nommée *ClearCache.cshtml*. Qui est une page que vous créerez également dans quelques instants. Vous devez le *ClearCache.cshtml* uniquement pour tester la mise en cache pour cet exemple, il n’est pas un lien ou une page qui vous incluriez normalement lorsque vous travaillez avec des graphiques mis en cache.
3. À la racine de votre site Web, créez un nouveau fichier nommé *ChartSaveToCache.cshtml*.
4. Remplacez le contenu existant avec les éléments suivants :

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample13.cshtml)]

    Le code vérifie d’abord si tout a été passé en tant que la valeur de clé dans la chaîne de requête. Si, par conséquent, le code essaie de lire un graphique hors du cache en appelant le `GetFromCache` (méthode) et en lui passant la clé. S’il s’avère que rien dans le cache sous cette clé (ce qui se passe la première fois que le graphique est demandé), le code crée le graphique comme d’habitude. Lorsque le graphique est terminé, le code enregistre dans le cache en appelant `SaveToCache`. Cette méthode requiert une clé (de sorte que le graphique peut être demandé ultérieurement) et la durée pendant laquelle le graphique doit être enregistré dans le cache. (L’heure exacte, que vous devez mettre en cache un graphique dépendant de la fréquence à laquelle vous pensiez que les données qu’il représente peuvent changer.) Le `SaveToCache` méthode requiert également un `slidingExpiration` paramètre &#8212; si la valeur est true, le délai d’attente compteur est réinitialisé chaque fois que le graphique est accessible. Dans ce cas, cela signifie en effet qu’entrée de cache du graphique expire deux minutes après la dernière fois que quelqu'un accédé le graphique. (L’alternative à l’expiration décalée est expiration absolue, c'est-à-dire que l’entrée de cache expire exactement 2 minutes après que qu’il a été placé dans le cache, quel que soit la fréquence à laquelle il avait été sollicitée.)

    Enfin, le code utilise la `WriteFromCache` méthode pour extraire et afficher le graphique à partir du cache. Notez que cette méthode est en dehors de la `if` bloc qui vérifie le cache, car il recevra le graphique à partir du cache si le graphique s’y trouvait pour commencer ou a dû être généré et enregistré dans le cache.

    Notez que dans l’exemple, le `AddTitle` méthode inclut un horodatage. (Il ajoute la date et heure &#8212; `DateTime.Now` &#8212; pour le titre.)
5. Créer une nouvelle page nommée *ClearCache.cshtml* et remplacer son contenu par le code suivant :

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample14.cshtml)]

    Cette page utilise le `WebCache` application d’assistance pour supprimer le graphique est mis en cache *ChartSaveToCache.cshtml*. Comme indiqué précédemment, vous n’avez normalement d’avoir une page comme suit. Vous créez ici uniquement à rendre plus faciles à tester la mise en cache.
6. Exécutez le *ShowCachedChart.cshtml* page web dans un navigateur. La page affiche l’image de graphique en fonction du code contenu dans le *ChartSaveToCache.cshtml* fichier. Prenez note de la signification de l’horodateur dans le titre du graphique. 

    ![Description : Les images de graphique de base avec un horodateur dans le titre du graphique](7-displaying-data-in-a-chart/_static/image13.jpg)
7. Fermez le navigateur.
8. Exécutez le *ShowCachedChart.cshtml* à nouveau. Notez que l’horodatage est le même qu’avant, ce qui signifie que le graphique n’a pas été régénéré, mais qu’il a été lu à la place à partir du cache.
9. Dans *ShowCachedChart.cshtml*, cliquez sur le **effacer le cache** lien. Cela permet d’accéder à *ClearCache.cshtml*, ce qui indique que le cache a été effacé.
10. Cliquez sur le **revenir à ShowCachedChart.cshtml** lier ou réexécutez *ShowCachedChart.cshtml* à partir de WebMatrix. Notez que cette fois l’horodatage a été modifié, car le cache a été effacé. Par conséquent, le code devait régénérer le graphique et placez-le dans le cache.

### <a name="saving-a-chart-as-an-image-file"></a>L’enregistrement d’un graphique en tant qu’Image

Vous pouvez également enregistrer un graphique en tant qu’un fichier image (par exemple, comme un *.jpg* fichier) sur le serveur. Vous pouvez ensuite utiliser le fichier d’image comme vous le feriez aucune image. L’avantage est le fichier est stocké plutôt que l’enregistrement dans un cache temporaire. Vous pouvez enregistrer une nouvelle image de graphique à des moments différents (par exemple, toutes les heures) et puis conserver un suivi permanent des modifications qui se produisent au fil du temps. Notez que vous devez vous assurer que votre application web a l’autorisation d’enregistrer un fichier dans le dossier sur le serveur où vous souhaitez placer le fichier image.

1. À la racine de votre site Web, créez un dossier nommé  *\_ChartFiles* si elle n’existe pas déjà.
2. À la racine de votre site Web, créez un nouveau fichier nommé *ChartSave.cshtml*.
3. Remplacez le contenu existant avec les éléments suivants :

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample15.cshtml)]

    Le code vérifie si le *.jpg* fichier existe en appelant le `File.Exists` (méthode). Si le fichier n’existe pas, le code crée un nouveau `Chart` à partir d’un tableau. Cette fois, le code appelle la `Save` méthode et passe le `path` pour spécifier le chemin d’accès et le nom de fichier où enregistrer le graphique. Dans le corps de la page, un `<img>` élément utilise le chemin d’accès pour pointer vers le *.jpg* fichier à afficher.
4. Exécutez le *ChartSave.cshtml* fichier.
5. Revenir à WebMatrix. Notez qu’un fichier image nommé *chart01.jpg* a été enregistré dans le  *\_ChartFiles* dossier.

### <a name="saving-a-chart-as-an-xml-file"></a>L’enregistrement d’un graphique dans un fichier XML

Enfin, vous pouvez enregistrer un graphique dans un fichier XML sur le serveur. Le des avantages de l’utilisation de cette méthode sur le graphique de la mise en cache ou enregistrer le graphique dans un fichier sont que vous pouvez modifier le code XML avant d’afficher le graphique, si vous le souhaitez. Votre application doit disposer des autorisations en lecture/écriture pour le dossier sur le serveur où vous souhaitez placer le fichier image.

1. À la racine de votre site Web, créez un nouveau fichier nommé *ChartSaveXml.cshtml*.
2. Remplacez le contenu existant avec les éléments suivants :

    [!code-cshtml[Main](7-displaying-data-in-a-chart/samples/sample16.cshtml)]

    Ce code est semblable au code que vous avez vu précédemment pour le stockage d’un graphique dans le cache, sauf qu’elle utilise un fichier XML. Le code vérifie d’abord si le fichier XML existe en appelant le `File.Exists` (méthode). Si le fichier n’existe pas, le code crée un nouveau `Chart` de l’objet et passe le nom de fichier en tant que le `themePath` paramètre. Cette opération crée le graphique en fonction de tout ce qui est dans le fichier XML. Si le fichier XML n’existe pas déjà, le code crée un graphique normalement, puis appelle `SaveXml` pour l’enregistrer. Le graphique est restitué à l’aide de la `Write` (méthode), que vous avez vu avant.

    Comme avec la page qui a donné lieu à la mise en cache, ce code inclut un horodateur dans le titre du graphique.
3. Créer une nouvelle page nommée *ChartDisplayXMLChart.cshtml* et lui ajouter le balisage suivant : 

    [!code-html[Main](7-displaying-data-in-a-chart/samples/sample17.html)]
4. Exécutez le *ChartDisplayXMLChart.cshtml* page. Le graphique est affiché. Prenez note de l’horodatage dans les titre du graphique.
5. Fermez le navigateur.
6. Dans WebMatrix, cliquez sur le  *\_ChartFiles* dossier, cliquez sur **Actualiser**, puis ouvrez le dossier. Le *XMLChart.xml* fichier dans ce dossier a été créé par le `Chart` helper. 

    ![Description : Le _ChartFiles dossier affiche le fichier XMLChart.xml créé par l’application d’assistance du graphique.](7-displaying-data-in-a-chart/_static/image14.jpg)
7. Exécutez le *ChartDisplayXMLChart.cshtml* page à nouveau. Le graphique montre le même horodatage que la première fois que vous avez exécuté la page. C’est parce que le graphique est généré à partir du XML que vous avez enregistré précédemment.
8. Dans WebMatrix, ouvrez le  *\_ChartFiles* dossier et supprimer les *XMLChart.xml* fichier.
9. Exécutez le *ChartDisplayXMLChart.cshtml* page une fois de plus. Cette fois-ci, l’horodatage est mis à jour, car le `Chart` helper avait recréer le fichier XML. Si vous le voulez, activez la  *\_ChartFiles* dossier et notez que le fichier XML est de retour.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

- [Introduction à l’utilisation d’une base de données dans ASP.NET Web Pages de Sites](https://go.microsoft.com/fwlink/?LinkId=202893)
- [À l’aide de la mise en cache dans ASP.NET Web Pages de Sites pour améliorer les performances](https://go.microsoft.com/fwlink/?LinkId=202903)
- [Classe graphique](https://msdn.microsoft.com/en-us/library/system.web.helpers.chart(v=vs.99)) (référence des API de Pages Web ASP.NET sur MSDN)
