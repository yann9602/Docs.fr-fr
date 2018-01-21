---
title: Globalisation et localisation dans ASP.NET Core
author: rick-anderson
description: "Découvrez comment ASP.NET Core fournit des services et intergiciel (middleware) pour la localisation de contenu dans différentes langues et cultures."
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/localization
ms.openlocfilehash: 1c93a53ea23ec13ca3d6fc138024ba38ec4883ee
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalisation et localisation dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien coupure](https://twitter.com/damien_bod), [Christian Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://twitter.com/NadeemAfana), et [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Création d’un site Web multilingue avec ASP.NET Core permettra à votre site atteindre un plus large public. ASP.NET Core offre des services et des intergiciels (middleware) de traduction dans différentes langues et cultures.

Internationalisation implique [globalisation](https://docs.microsoft.com/dotnet/api/system.globalization) et [localisation](https://docs.microsoft.com/dotnet/standard/globalization-localization/localization). La globalisation est le processus de conception d’applications qui prennent en charge des cultures différentes. Globalisation ajoute la prise en charge d’entrée, d’affichage et de sortie d’un ensemble défini de scripts de langue qui se rapportent à des zones géographiques spécifiques.

La localisation consiste à adapter une application globalisée, qui vous avez déjà été traités pour adaptabilité, à une culture/paramètres régionaux spécifiques. Pour plus d’informations, consultez **les termes du contrat de globalisation et localisation** vers la fin de ce document.

Localisation de l’application implique les étapes suivantes :

1. Rendre le contenu de l’application localisable

2. Fournir des ressources localisées pour les langues et les cultures que pris en charge

3. Implémenter une stratégie pour sélectionner la langue/culture pour chaque demande

## <a name="make-the-apps-content-localizable"></a>Rendre le contenu de l’application localisable

Introduit dans ASP.NET Core, `IStringLocalizer` et `IStringLocalizer<T>` ont été conçues pour améliorer la productivité de développement localisé des applications. `IStringLocalizer`utilise le [ResourceManager](https://docs.microsoft.com/dotnet/api/system.resources.resourcemanager) et [ResourceReader](https://docs.microsoft.com/dotnet/api/system.resources.resourcereader) pour fournir des ressources spécifiques à la culture au moment de l’exécution. L’interface simple possède un indexeur et un `IEnumerable` pour retourner des chaînes localisées. `IStringLocalizer`ne nécessite pas vous permet de stocker les chaînes de langue par défaut dans un fichier de ressources. Vous pouvez développer une application ciblée pour la localisation et n’avez pas besoin créer des fichiers de ressources au début de développement. Le code ci-dessous montre comment intégrer la chaîne « Sur titre » pour la localisation.

[!code-csharp[Main](localization/sample/Localization/Controllers/AboutController.cs)]

Dans le code ci-dessus, le `IStringLocalizer<T>` provient de l’implémentation [Injection de dépendance](dependency-injection.md). Si la valeur localisée de « Sur titre » n’est pas trouvée, la clé de l’indexeur est retournée, autrement dit, la chaîne « Sur titre ». Vous pouvez laisser des chaînes littérales de langage la valeur par défaut dans l’application et les encapsuler dans le localisateur, afin de pouvoir vous concentrer sur le développement d’applications. Vous développez votre application avec la langue par défaut et le préparez pour l’étape de localisation sans d’abord créer un fichier de ressources par défaut. Vous pouvez également utiliser l’approche traditionnelle et fournir une clé pour récupérer la chaîne de langue par défaut. Pour de nombreux développeurs le nouveau flux de travail de ne pas avoir une langue par défaut *.resx* encapsulant simplement les littéraux de chaîne et fichier peuvent réduire la charge de la localisation d’une application. Autres développeurs préfèreront le flux de travail classiques, comme il peut être plus facile de travailler avec des littéraux de chaîne plus longs et le rendre plus facile à mettre à jour les chaînes localisées.

Utilisez le `IHtmlLocalizer<T>` implémentation pour les ressources contenant du HTML. `IHtmlLocalizer`Encode les arguments qui sont mis en forme dans la chaîne de ressource HTML, mais ne pas HTML encode la chaîne de ressource elle-même. Dans l’exemple de mise en surbrillance ci-dessous, seule la valeur de `name` paramètre est encodé au format HTML.

[!code-csharp[Main](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Remarque :** vous souhaitez généralement localiser uniquement le texte et HTML pas.

Niveau le plus bas, vous pouvez obtenir `IStringLocalizerFactory` hors [Injection de dépendance](dependency-injection.md):

[!code-csharp[Main](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Le code ci-dessus illustre chacune de la fabrique de deux méthodes de création.

Vous pouvez partitionner vos chaînes localisées par contrôleur, zone, ou avoir qu’un seul conteneur. Dans l’exemple d’application, une classe fictive nommée `SharedResource` est utilisé pour les ressources partagées.

[!code-csharp[Main](localization/sample/Localization/Resources/SharedResource.cs)]

Certains développeurs utilisent la `Startup` classe pour contenir des chaînes globales ou partagés. Dans l’exemple ci-dessous, le `InfoController` et `SharedResource` localisateurs sont utilisés :

[!code-csharp[Main](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Localisation de la vue

Le `IViewLocalizer` service fournit des chaînes localisées pour une [vue](https://docs.microsoft.com/aspnet/core). La `ViewLocalizer` classe implémente cette interface et l’emplacement de la ressource du chemin du fichier de vue. Le code suivant montre comment utiliser l’implémentation par défaut de `IViewLocalizer`:

[!code-cshtml[Main](localization/sample/Localization/Views/Home/About.cshtml)]

L’implémentation par défaut de `IViewLocalizer` recherche le fichier de ressources basé sur le nom de fichier de la vue. Il n’existe aucune option pour utiliser un fichier de ressource partagée globale. `ViewLocalizer`implémente le localisateur à l’aide de `IHtmlLocalizer`, de sorte que Razor ne HTML encoder la chaîne localisée. Vous pouvez paramétrer des chaînes de ressources et `IViewLocalizer` HTML codera les paramètres, mais pas la chaîne de ressource. Prenez en compte le balisage Razor suivant :

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Un fichier de ressources Français peut contenir les éléments suivants :

| Touche | Value |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b> ` |

L’affichage contient le balisage HTML à partir du fichier de ressources.

**Remarque :** vous souhaitez généralement localiser uniquement le texte et HTML pas.

Pour utiliser un fichier de ressources partagées dans une vue, injecter `IHtmlLocalizer<T>`:

[!code-cshtml[Main](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Localisation de DataAnnotations

Messages d’erreur DataAnnotations localisés avec `IStringLocalizer<T>`. À l’aide de l’option `ResourcesPath = "Resources"`, les messages d’erreur dans `RegisterViewModel` peuvent être stockées dans des chemins suivants :

* Resources/ViewModels.Account.RegisterViewModel.fr.resx
* Resources/ViewModels/Account/RegisterViewModel.fr.resx

[!code-csharp[Main](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

Dans ASP.NET MVC de base 1.1.0 et les attributs de plus, la validation non localisés. ASP.NET Core MVC 1.0 est **pas** rechercher des chaînes localisées pour les attributs de validation non.

<a name="one-resource-string-multiple-classes"></a>
### <a name="using-one-resource-string-for-multiple-classes"></a>À l’aide d’une chaîne de ressource pour plusieurs classes

Le code suivant montre comment utiliser une chaîne de ressource pour les attributs de validation avec plusieurs classes :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Dans le code précédent, `SharedResource` est la classe correspondant à la resx où sont stockés vos messages de validation. Avec cette approche, DataAnnotations utilisera uniquement `SharedResource`, au lieu de la ressource pour chaque classe. 

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Fournir des ressources localisées pour les langues et les cultures que pris en charge  

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures et SupportedUICultures

ASP.NET Core vous permet de spécifier les deux valeurs de culture `SupportedCultures` et `SupportedUICultures`. Le [CultureInfo](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo) pour l’objet `SupportedCultures` détermine les résultats de fonctions spécifiques à une culture, telles que date, heure, nombre et la mise en forme de devise. `SupportedCultures`détermine également l’ordre de tri de texte, les conventions de casse et les comparaisons de chaînes. Consultez [CultureInfo.CurrentCulture](https://docs.microsoft.com/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) pour plus d’informations sur la façon dont le serveur obtient la Culture. Le `SupportedUICultures` détermine ce qui se traduit par des chaînes (à partir de *.resx* fichiers) sont recherchés par le [ResourceManager](https://docs.microsoft.com/dotnet/api/system.resources.resourcemanager). Le `ResourceManager` recherche simplement les chaînes spécifiques à la culture qui est déterminé par `CurrentUICulture`. Chaque thread dans .NET a `CurrentCulture` et `CurrentUICulture` objets. ASP.NET Core inspecte ces valeurs lors du rendu des fonctions spécifiques à la culture. Par exemple, si la culture du thread actuel a la valeur « en-US » (anglais, États-Unis), `DateTime.Now.ToLongDateString()` affiche « Jeudi 18 février 2016, » mais si `CurrentCulture` est définie à « es-ES » (espagnol, Espagne), le résultat sera « jueves, de 18 febrero de 2016".

## <a name="resource-files"></a>Fichiers de ressources

Un fichier de ressources est un mécanisme utile pour séparer les chaînes localisables à partir du code. Les chaînes traduites pour la langue par défaut sont isolées *.resx* fichiers de ressources. Par exemple, vous souhaiterez peut-être créer le fichier de ressources nommé *Welcome.es.resx* contenant traduit les chaînes. « es » sont le code de langue pour l’espagnol. Pour créer ce fichier de ressources dans Visual Studio :

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier qui contiendra le fichier de ressources > **ajouter** > **un nouvel élément**.

    ![Menu contextuel imbriqué : dans l’Explorateur de solutions, un menu contextuel est ouvert pour les ressources. Un second menu contextuel est ouvert pour ajouter, afficher la commande d’un nouvel élément mis en surbrillance.](localization/_static/newi.png)

2. Dans le **recherche des modèles installés** zone, entrez « ressource » et nommez le fichier.

    ![Boîte de dialogue Ajouter un nouvel élément](localization/_static/res.png)

3. Entrez la valeur de clé (chaîne natifs) dans le **nom** colonne et la chaîne traduite dans la **valeur** colonne.

    ![Fichier Welcome.es.resx (le fichier de ressources accueil pour l’espagnol) avec le mot Hello dans la colonne nom et le mot Hola (Hello en espagnol) dans la colonne valeur](localization/_static/hola.png)

    Visual Studio affiche la *Welcome.es.resx* fichier.

    ![Explorateur de solutions affichant le fichier de ressources Bienvenue Espagnol (es)](localization/_static/se.png)

<a name="error"></a>

Si vous utilisez la version de Visual Studio 2017 Preview 15.3, vous obtenez un indicateur d’erreur dans l’éditeur de ressources. Supprimer le *ResXFileCodeGenerator* valeur à partir de la *un outil personnalisé* grille des propriétés pour empêcher ce message d’erreur :

![Éditeur de resx](localization/_static/err.png)

Ou bien, vous pouvez ignorer cette erreur. Nous espérons résoudre ce problème dans la prochaine version.

## <a name="resource-file-naming"></a>Nom de fichier de ressources

Les ressources sont nommées pour le nom de type complet de leur classe moins le nom de l’assembly. Par exemple, une ressource Français dans un projet dont l’assembly principal est `LocalizationWebsite.Web.dll` pour la classe `LocalizationWebsite.Web.Startup` serait nommé *Startup.fr.resx*. Une ressource pour la classe `LocalizationWebsite.Web.Controllers.HomeController` serait nommé *Controllers.HomeController.fr.resx*. Si l’espace de noms de votre classe cible n’est pas le même que le nom de l’assembly, vous devez le nom de type complet. Par exemple, dans l’exemple de projet une ressource pour le type `ExtraNamespace.Tools` serait nommé *ExtraNamespace.Tools.fr.resx*.

Dans l’exemple de projet, le `ConfigureServices` méthode définit la `ResourcesPath` « Ressources », par conséquent, le chemin d’accès relatif de projet pour le fichier de ressources Français du contrôleur home est *Resources/Controllers.HomeController.fr.resx*. Ou bien, vous pouvez utiliser des dossiers pour organiser les fichiers de ressources. Pour le contrôleur home, le chemin d’accès serait *Resources/Controllers/HomeController.fr.resx*. Si vous n’utilisez pas le `ResourcesPath` option, le *.resx* fichier va passer dans le répertoire de base du projet. Le fichier de ressources pour `HomeController` serait nommé *Controllers.HomeController.fr.resx*. Le choix de l’utilisation de la convention d’affectation de noms point ou le chemin d’accès dépend de la façon dont vous souhaitez organiser vos fichiers de ressources.

| Nom de la ressource | Point ou le chemin d’accès d’affectation de noms |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Point  |
| Resources/Controllers/HomeController.fr.resx  | Chemin d’accès |
|    |     |

Fichiers de ressources à l’aide de `@inject IViewLocalizer` dans les vues Razor suivent un modèle similaire. Le fichier de ressources pour une vue peut être nommé à l’aide de point d’affectation de noms ou nom de chemin d’accès. Fichiers de ressources de vue Razor imiter le chemin d’accès de son fichier de vue associée. En supposant que nous avons défini le `ResourcesPath` pour « Ressources », le fichier de ressources Français associés à la *Views/Home/About.cshtml* vue peut être une des opérations suivantes :

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Si vous n’utilisez pas le `ResourcesPath` option, le *.resx* fichier pour une vue devrait se trouver dans le même dossier que la vue.

## <a name="culture-fallback-behavior"></a>Comportement de secours de culture

Par exemple, si vous supprimez l’indicateur de la culture « .fr » et que la culture définie pour le Français, le fichier de ressources par défaut est en lecture et les chaînes sont localisées. Le Gestionnaire de ressources permet de désigner une par défaut ou les ressources de secours pour lorsque rien répond à la culture demandée. Si vous souhaitez simplement retourner la clé quand une ressource manquante pour demandé de culture ne doit pas avoir un fichier de ressources par défaut.

### <a name="generate-resource-files-with-visual-studio"></a>Générer des fichiers de ressources avec Visual Studio

Si vous créez un fichier de ressources dans Visual Studio sans une culture dans le nom de fichier (par exemple, *Welcome.resx*), Visual Studio crée une classe c# avec une propriété pour chaque chaîne. Qui est généralement pas ce que vous souhaitez avec ASP.NET Core ; en général, vous n’avez pas de valeur par défaut *.resx* fichier de ressources (un *.resx* fichier sans le nom de culture). Nous vous suggérons de vous créez la *.resx* fichier avec un nom de culture (par exemple *Welcome.fr.resx*). Lorsque vous créez un *.resx* fichier avec un nom de culture, Visual Studio ne génère pas le fichier de classe. Nous estimons que de nombreux développeurs correspondra **pas** créer un fichier de ressources de langue par défaut.

### <a name="add-other-cultures"></a>Ajouter d’autres Cultures

Chaque combinaison de langue et de culture (autres que la langue par défaut) nécessite un fichier de ressources unique. Vous créez des fichiers de ressources pour les paramètres régionaux et cultures différents en créant de nouveaux fichiers de ressources dans lequel les codes de langue ISO font partie du nom de fichier (par exemple, **en-us**, **fr-ca**, et  **en Go**). Ces codes ISO sont placés entre le nom de fichier et le *.resx* fichier d’extension de nom, comme dans *Welcome.es-MX.resx* (espagnol/Mexique). Pour spécifier une langue culturellement neutre, supprimez le code de pays (`MX` dans l’exemple précédent). Le nom de fichier de ressources en espagnol culturellement neutre est *Welcome.es.resx*.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implémenter une stratégie pour sélectionner la langue/culture pour chaque demande  

### <a name="configure-localization"></a>Configurer la localisation

Localisation est configurée dans le `ConfigureServices` méthode :

[!code-csharp[Main](localization/sample/Localization/Program.cs?name=snippet1)]

* `AddLocalization`Ajoute les services de localisation pour le conteneur de services. Le code ci-dessus définit également le chemin d’accès de ressources pour « Ressources ».

* `AddViewLocalization`Ajoute la prise en charge pour les fichiers de vue localisée. Dans cette vue d’exemples de localisation est basée sur le suffixe de fichier de vue. Par exemple, « fr » dans le *Index.fr.cshtml* fichier.

* `AddDataAnnotationsLocalization`Ajoute la prise en charge pour les `DataAnnotations` validation des messages via `IStringLocalizer` abstractions.

### <a name="localization-middleware"></a>Localisation intergiciel (middleware)

La culture en cours sur une requête est définie dans la localisation [intergiciel (middleware)](middleware.md). L’intergiciel de localisation est activée dans le `Configure` méthode *Program.cs* fichier. Notez que l’intergiciel de localisation doit être configurée avant tout intergiciel (middleware) qui peut vérifier la culture de la demande (par exemple, `app.UseMvcWithDefaultRoute()`).

[!code-csharp[Main](localization/sample/Localization/Program.cs?name=snippet2)]

`UseRequestLocalization`Initialise un `RequestLocalizationOptions` objet. À chaque demande la liste de `RequestCultureProvider` dans le `RequestLocalizationOptions` est énumérée et le premier fournisseur capable de déterminer correctement la culture de la demande est utilisé. Les fournisseurs par défaut proviennent de la `RequestLocalizationOptions` classe :

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

La liste par défaut va du plus spécifique au moins spécifique. Plus loin dans l’article, nous verrons comment vous pouvez modifier l’ordre et même ajouter un fournisseur de la culture personnalisée. Si aucun des fournisseurs peut déterminer la culture de la demande, le `DefaultRequestCulture` est utilisé.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Certaines applications utilisent une chaîne de requête pour définir le [culture et la culture d’interface utilisateur](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx). Pour les applications qui utilisent le cookie ou l’approche d’en-tête Accept-Language, ajout d’une chaîne de requête à l’URL est utile pour déboguer et tester le code. Par défaut, le `QueryStringRequestCultureProvider` est inscrit en tant que le premier fournisseur de localisation dans le `RequestCultureProvider` liste. Vous passez des paramètres de chaîne de la requête `culture` et `ui-culture`. L’exemple suivant définit la culture spécifique (langue et région) pour l’espagnol/Mexique :

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Si vous passez uniquement dans un des deux (`culture` ou `ui-culture`), le fournisseur de chaîne de requête définit les deux valeurs à l’aide de celui que vous avez passé. Par exemple, définissant simplement la culture définit à la fois le `Culture` et `UICulture`:

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

Les applications de production souvent fournit un mécanisme permettant de définir la culture avec le cookie de culture ASP.NET Core. Utilisez la `MakeCookieValue` méthode pour créer un cookie.

Le `CookieRequestCultureProvider` `DefaultCookieName` retourne le nom de cookie par défaut utilisé pour effectuer le suivi de l’utilisateur par défaut les informations de culture. Le nom de cookie par défaut est ». AspNetCore.Culture ».

Le format de cookie est `c=%LANGCODE%|uic=%LANGCODE%`, où `c` est `Culture` et `uic` est `UICulture`, par exemple :

    c=en-UK|uic=en-US

Si vous spécifiez uniquement une des informations de culture et la culture d’interface utilisateur, la culture spécifiée sera utilisée pour les informations de culture et la culture d’interface utilisateur.

### <a name="the-accept-language-http-header"></a>L’en-tête HTTP Accept-Language

Le [en-tête Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) ne peut être définie dans la plupart des navigateurs et a été conçue à l’origine pour spécifier la langue de l’utilisateur. Ce paramètre indique que le navigateur a été configuré pour envoyer ou a hérité à partir du système d’exploitation sous-jacent. L’en-tête HTTP Accept-Language à partir d’une demande de navigateur n’est pas une méthode infaillible pour détecter la langue par défaut de l’utilisateur (voir [définition des préférences de langue dans un navigateur](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Une application de production doit inclure un moyen pour un utilisateur de personnaliser leur choix de la culture.

### <a name="set-the-accept-language-http-header-in-ie"></a>Définir l’en-tête HTTP Accept-Language dans Internet Explorer

1. À partir de l’icône d’engrenage, appuyez sur **Options Internet**.

2. Appuyez sur **langues**.

    ![Options Internet](localization/_static/lang.png)

3. Appuyez sur **définir les préférences de langue**.

4. Appuyez sur **ajouter une langue**.

5. Ajoutez la langue.

6. Cliquez sur la langue, puis appuyez sur **monter**.

### <a name="use-a-custom-provider"></a>Utilisez un fournisseur personnalisé

Supposons que vous voulez que vos clients de stocker leur langue et culture dans vos bases de données. Vous pouvez écrire un fournisseur pour rechercher ces valeurs pour l’utilisateur. Le code suivant montre comment ajouter un fournisseur personnalisé :

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Utilisez `RequestLocalizationOptions` pour ajouter ou supprimer des fournisseurs de localisation.

### <a name="set-the-culture-programmatically"></a>Définir la culture par programmation

Cet exemple **Localization.StarterWeb** de projet sur [GitHub](https://github.com/aspnet/entropy) contient l’interface utilisateur pour définir le `Culture`. Le *Views/Shared/_SelectLanguagePartial.cshtml* fichier vous permet de sélectionner la culture dans la liste des cultures prises en charge :

[!code-cshtml[Main](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Le *Views/Shared/_SelectLanguagePartial.cshtml* fichier est ajouté à la `footer` section du fichier de disposition afin qu’il sera disponible pour toutes les vues :

[!code-cshtml[Main](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Le `SetLanguage` méthode définit le cookie de la culture.

[!code-csharp[Main](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Vous ne pouvez pas incorporer le *_SelectLanguagePartial.cshtml* à l’exemple de code pour ce projet. Le **Localization.StarterWeb** de projet sur [GitHub](https://github.com/aspnet/entropy) a du code pour le flux de la `RequestLocalizationOptions` à un Razor partielle via la [Injection de dépendance](dependency-injection.md) conteneur.

## <a name="globalization-and-localization-terms"></a>Les termes du contrat de globalisation et localisation

Le processus de localisation de votre application nécessite également une compréhension de base appropriés de jeux de caractères couramment utilisés dans le développement de logiciels modernes et de comprendre les problèmes associés. Bien que tous les ordinateurs stockent du texte sous forme de nombres (codes), différents systèmes stockent le même texte en utilisant des nombres différents. Le processus de localisation fait référence à la traduction de l’interface utilisateur d’application (interface utilisateur) pour une culture/paramètres régionaux spécifiques.

[Adaptabilité](https://docs.microsoft.com/dotnet/standard/globalization-localization/localizability-review) est un processus intermédiaire pour vérifier qu’une application globalisée est prête pour la localisation.

Le [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) mettre en forme du nom de culture est `<languagecode2>-<country/regioncode2>`, où `<languagecode2>` est le code de langue et `<country/regioncode2>` est le code de sous-culture. Par exemple, `es-CL` pour l’espagnol (Chili), `en-US` pour l’anglais (États-Unis), et `en-AU` pour l’anglais (Australie). [La norme RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) est une combinaison d’un code de culture à deux lettres minuscules associé à une langue de norme ISO 639 et un code de sous-culture à deux lettres majuscules associé à un pays ou une région de norme ISO 3166. Consultez [nom de Culture de langue](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Internationalisation est souvent abrégée en « I18N ». L’abréviation prend les première et dernière lettres et le nombre de lettres entre eux, par conséquent, 18 représente le nombre de lettres entre le premier « I » et le dernier « N ». Il en va de même pour la globalisation (G11N) et la localisation (L10N).

Termes du contrat :

* Globalisation (G11N) : Le processus de fabrication d’une application prend en charge différentes langues et régions.
* Localisation (L10N) : Le processus de personnalisation d’une application pour une langue donnée et une région.
* Internationalisation (I18N) : Décrit la globalisation et localisation.
* Culture : Il est un langage et, éventuellement, une région.
* Culture neutre : une culture qui a une langue donnée, mais pas dans une région. (par exemple « en », « es »)
* Culture spécifique : une culture qui dispose d’un langage spécifié et la région. (par exemple « en-US », « en-GB », « es-CL »)
* Parent culture : culture neutre qui contient une culture spécifique. (par exemple, « fr » est la culture parente de « en-US » et « en-GB »)
* Paramètres régionaux : Un paramètre régional est identique à une culture.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Projet de Localization.StarterWeb](https://github.com/aspnet/entropy) utilisée dans l’article.
* [Fichiers de ressources dans Visual Studio](https://docs.microsoft.com/cpp/windows/resource-files-visual-studio)
* [Ressources dans les fichiers .resx](https://docs.microsoft.com/dotnet/framework/resources/working-with-resx-files-programmatically)
