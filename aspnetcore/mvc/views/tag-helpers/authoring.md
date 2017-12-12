---
title: "Création de programmes d’assistance de balise dans ASP.NET Core"
author: rick-anderson
description: "Découvrez comment créer des programmes d’assistance de balise dans ASP.NET Core."
keywords: "ASP.NET Core, les programmes d’assistance de balise"
ms.author: riande
manager: wpickett
ms.date: 06/14/2017
ms.topic: article
ms.assetid: 4f16d978-5695-4abf-a785-fdaabf3bbcb9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/authoring
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6858b6b8ec89a5e5ffa9e5f8dddb905f38e16603
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="authoring-tag-helpers-in-aspnet-core-a-walkthrough-with-samples"></a>Programmes d’assistance de balise dans ASP.NET Core, une procédure pas à pas avec les exemples de création

De [Rick Anderson](https://twitter.com/RickAndMSFT)

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

## <a name="getting-started-with-tag-helpers"></a>Prise en main de programmes d’assistance de balise

Ce didacticiel fournit une introduction à la programmation des programmes d’assistance de balise. [Introduction aux applications d’assistance de balise](intro.md) décrit les avantages qui fournissent des programmes d’assistance de balise.

Une application d’assistance de balise est toute classe qui implémente le `ITagHelper` interface. Toutefois, lorsque vous créez une application d’assistance de balise, vous dérivez généralement `TagHelper`, en procédant ainsi, l’accès à la `Process` (méthode).

1. Créer un nouveau projet ASP.NET Core nommé **AuthoringTagHelpers**. Vous n’aurez pas d’authentification pour ce projet.

2. Créez un dossier pour stocker les programmes d’assistance de balise appelée *TagHelpers*. Le *TagHelpers* dossier est *pas* requis, mais il s’agit d’une convention raisonnable. Maintenant nous pouvons commencer l’écriture de certains programmes d’assistance de balise simple.

## <a name="a-minimal-tag-helper"></a>Un programme d’assistance de balise minimale

Dans cette section, vous écrivez une application d’assistance de balise qui met à jour une balise par courrier électronique. Exemple :

```html
<email>Support</email>
   ```

Le serveur utilisera notre programme d’assistance de balise par courrier électronique à convertir ce balisage dans les éléments suivants :

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
   ```

Autrement dit, une balise d’ancrage qui rend cette un lien par courrier électronique. Vous pouvez souhaiter procéder ainsi si vous écrivez un moteur de blog et que vous avez besoin pour envoyer un courrier électronique pour le marketing, prise en charge et autres contacts, tous au même domaine.

1.  Ajoutez le code suivant `EmailTagHelper` classe le *TagHelpers* dossier.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]
    
    **Remarques :**
    
    * Programmes d’assistance de la balise utilisent une convention d’affectation de noms qui cible des éléments de la classe racine (moins le *TagHelper* partie du nom de classe). Dans cet exemple, le nom de racine **messagerie**TagHelper est *messagerie*, donc le `<email>` balise est ciblé. Cette convention d’affectation de noms doit fonctionner pour la plupart des programmes d’assistance de balise, plus tard, je vais montrer comment substituer.
    
    * La classe `EmailTagHelper` dérive de la classe `TagHelper`. La `TagHelper` classe fournit les méthodes et propriétés pour l’écriture de programmes d’assistance de balise.
    
    * Le substituée `Process` méthode contrôle ce que fait l’application d’assistance de balise lors de l’exécution. Le `TagHelper` classe fournit également une version asynchrone (`ProcessAsync`) avec les mêmes paramètres.
    
    * Le paramètre de contexte à `Process` (et `ProcessAsync`) contient des informations associées à l’exécution de la balise HTML actuelle.
    
    * Le paramètre de sortie à `Process` (et `ProcessAsync`) contient un élément HTML avec état représentatif de la source d’origine utilisée pour générer une balise HTML et le contenu.
    
    * Le nom de notre classe comporte un suffixe de **TagHelper**, qui est *pas* requis, mais elle est considérée comme une meilleure convention pratique. Vous pouvez déclarer la classe en tant que :
    
    ```csharp
    public class Email : TagHelper
    ```

2.  Pour rendre le `EmailTagHelper` classe disponible pour toutes les vues de notre Razor, ajoutez le `addTagHelper` directive pour le *Views/_ViewImports.cshtml* fichier :[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]
    
    Le code ci-dessus utilise la syntaxe des caractères génériques pour spécifier que tous les programmes d’assistance de balise dans notre assembly seront disponibles. La première chaîne après `@addTagHelper` Spécifie l’application d’assistance de balise à charger (utilisez « * » pour tous les programmes d’assistance de balise), et la deuxième chaîne de « AuthoringTagHelpers » Spécifie l’assembly de l’application d’assistance de balise. En outre, notez que la deuxième ligne met dans les programmes d’assistance de balise de noyaux de ASP.NET MVC à l’aide de la syntaxe des caractères génériques (ces programmes d’assistance sont décrites dans [Introduction aux programmes d’assistance de balise](intro.md).) Il s’agit du `@addTagHelper` directive qui rend l’application d’assistance de balise disponibles à la vue Razor. Ou bien, vous pouvez fournir le nom qualifié complet (FQN) d’une application d’assistance de balise comme indiqué ci-dessous :
    
    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
    
    Pour ajouter un programme d’assistance de balise à une vue à l’aide d’un FQN, vous ajoutez d’abord le FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) et le nom d’assembly (*AuthoringTagHelpers*). La plupart des développeurs préfèrent utiliser la syntaxe des caractères génériques. [Introduction aux applications d’assistance de balise](intro.md) détaille syntaxe Ajout, suppression, hiérarchie et de caractères génériques des balises d’assistance.
    
3.  Mettre à jour le balisage dans le *Views/Home/Contact.cshtml* fichier avec ces modifications :

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

4.  Exécutez l’application et utiliser votre navigateur favori pour afficher la source HTML afin de vérifier que les balises de courrier électronique sont remplacés par un balisage d’ancrage (par exemple, `<a>Support</a>`). *Prise en charge* et *Marketing* sont rendus sous la forme d’un liens, mais ils n’ont pas un `href` attribut pour les rendre fonctionnel. Qui sera résolu dans la section suivante.

Remarque : Comme des balises HTML et des attributs, des balises, des noms de classe et des attributs dans Razor et c# ne respectent pas la casse.

## <a name="setattribute-and-setcontent"></a>SetAttribute et SetContent

Dans cette section, nous allons mettre à jour le `EmailTagHelper` afin qu’il crée une balise d’ancrage valide pour le courrier électronique. Nous allons mettre à jour pour tirer des informations à partir d’une vue Razor (sous la forme d’un `mail-to` attribut) et l’utiliser lors de la génération de l’ancre.

Mise à jour la `EmailTagHelper` classe par le code suivant :

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

**Remarques :**

* Noms de classe et la propriété casse Pascal pour les programmes d’assistance de balise sont traduites en leur [réduire les cas rapide](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Par conséquent, pour utiliser le `MailTo` attribut, vous allez utiliser `<email mail-to="value"/>` équivalent.

* La dernière ligne définit le contenu terminé pour notre programme d’assistance de balise fonctionnel au minimum.

* La ligne en surbrillance montre la syntaxe pour l’ajout d’attributs :

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Cette approche fonctionne pour l’attribut « href » tant qu’il n’existe pas actuellement dans la collection d’attributs. Vous pouvez également utiliser le `output.Attributes.Add` méthode pour ajouter un attribut d’assistance de balise à la fin de la collection d’attributs de balise.

1.  Mettre à jour le balisage dans le *Views/Home/Contact.cshtml* fichier avec ces modifications :[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

2.  Exécutez l’application et vérifiez qu’il génère les liens corrects.
    
    > [!NOTE]
    >Si vous deviez écrire la balise de messagerie fermeture automatique (`<email mail-to="Rick" />`), la sortie finale serait également la fermeture automatique. Pour activer la capacité d’écrire la balise avec uniquement une balise de début (`<email mail-to="Rick">`) vous devez décorer la classe par le code suivant :
    >
    > [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]
    
    Avec une assistance de balise fermeture automatique par courrier électronique, le résultat serait `<a href="mailto:Rick@contoso.com" />`. Les balises d’ancrage fermeture automatique ne sont pas valide en HTML, afin que vous ne souhaitez pas créer un, mais vous souhaiterez peut-être créer un programme d’assistance de balise se ferme automatiquement. Programmes d’assistance de balise définir le type de la `TagMode` propriété après la lecture d’une balise.
    
### <a name="processasync"></a>ProcessAsync

Dans cette section, nous allons écrire une application d’assistance de messagerie asynchrone.

1.  Remplacez la `EmailTagHelper` classe par le code suivant :

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

    **Remarques :**

    * Cette version utilise asynchrone `ProcessAsync` (méthode). Asynchrone `GetChildContentAsync` retourne un `Task` contenant le `TagHelperContent`.

    * Utilisez le `output` pour obtenir le contenu de l’élément HTML.

2.  Apportez la modification suivante à la *Views/Home/Contact.cshtml* de fichiers pour permettre l’application d’assistance de balise d’obtenir l’adresse de messagerie cible.

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

3.  Exécutez l’application et vérifiez qu’il génère des liens de messagerie valide.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent.SetHtmlContent et PostContent.SetHtmlContent

1.  Ajoutez le code suivant `BoldTagHelper` classe le *TagHelpers* dossier.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

    **Remarques :**
    
    * Le `[HtmlTargetElement]` passe un paramètre d’attribut qui spécifie qu’un élément HTML qui contient un attribut HTML nommé « bold » correspondra, d’attribut et la `Process` méthode de substitution dans la classe s’exécute. Dans notre exemple, le `Process` méthode supprime l’attribut « bold » et entoure le balisage contenant avec `<strong></strong>`.
    
    * Car vous ne souhaitez pas remplacer la balise contenue, vous devez écrire l’ouverture `<strong>` balise avec le `PreContent.SetHtmlContent` (méthode) et la clôture `</strong>` la balise avec le `PostContent.SetHtmlContent` (méthode).
    
2.  Modifier la *About.cshtml* vue contienne un `bold` valeur d’attribut. Le code complet est indiqué ci-dessous.

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

3.  Exécutez l’application. Vous pouvez utiliser votre navigateur préféré pour inspecter la source et de vérifier le balisage.

    Le `[HtmlTargetElement]` attribut ci-dessus cible uniquement le balisage HTML qui fournit un nom d’attribut de « bold ». Le `<bold>` élément n’a pas été modifié par l’application d’assistance de balise.

4. Commentez la `[HtmlTargetElement]` ligne d’attribut et il utilisera par défaut ciblant `<bold>` balises, autrement dit, un balisage HTML sous la forme `<bold>`. N’oubliez pas, la convention d’affectation de noms par défaut correspond au nom de classe **gras**TagHelper à `<bold>` balises.

5. Exécutez l’application et vérifiez que le `<bold>` la balise est traitée par l’application d’assistance de balise.

Le fait de décorer une classe avec plusieurs `[HtmlTargetElement]` les résultats dans une opération OR logique des cibles d’attributs. Par exemple, le code ci-dessous, une balise en gras ou un attribut gras est mettre en correspondance.

[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Lorsque plusieurs attributs sont ajoutés à la même instruction, le runtime les traite comme un AND logique. Par exemple, dans le code ci-dessous, un élément HTML doit être nommé « bold » avec un attribut nommé « bold » (`<bold bold />`) pour faire correspondre.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Vous pouvez également utiliser le `[HtmlTargetElement]` pour modifier le nom de l’élément ciblé. Par exemple, si vous souhaitiez le `BoldTagHelper` à cibler `<MyBold>` balises, vous utiliseriez l’attribut suivant :

```csharp
[HtmlTargetElement("MyBold")]
   ```

## <a name="passing-a-model-to-a-tag-helper"></a>Passage d’un modèle dans une application d’assistance de balise

1.  Ajouter un *modèles* dossier.

2.  Ajoutez la classe `WebsiteContext` suivante au dossier *Models* :

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

3.  Ajoutez le code suivant `WebsiteInformationTagHelper` classe le *TagHelpers* dossier.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]
    
    **Remarques :**
    
    * Comme mentionné précédemment, les programmes d’assistance de balise traduit les noms de classe C# casse Pascal et des propriétés pour les programmes d’assistance de balise dans [réduire les cas rapide](http://wiki.c2.com/?KebabCase). Par conséquent, pour utiliser le `WebsiteInformationTagHelper` dans Razor, vous allez écrire `<website-information />`.
    
    * Vous ne sont pas identifiant explicitement l’élément cible avec le `[HtmlTargetElement]` attribut, la valeur par défaut de `website-information` sont ciblés. Si vous avez appliqué l’attribut suivant (Notez qu’il n’est pas rapide cas mais il correspond au nom de la classe) :
    
    ```csharp
    [HtmlTargetElement("WebsiteInformation")]
    ```
    
    La balise de cas rapide inférieure `<website-information />` ne correspondent pas. Si vous souhaitez utiliser le `[HtmlTargetElement]` attribut, vous utiliseriez cas rapide comme indiqué ci-dessous :
    
    ```csharp
    [HtmlTargetElement("Website-Information")]
    ```
    
    * Les éléments qui sont de fermeture automatique ont aucun contenu. Pour cet exemple, le balisage Razor utilise une balise de fermeture automatique, mais l’application d’assistance de balise créeront un [section](http://www.w3.org/TR/html5/sections.html#the-section-element) élément (qui n’est pas une fermeture automatique et que vous écrivez le contenu à l’intérieur du `section` élément). Par conséquent, vous devez définir `TagMode` à `StartTagAndEndTag` pour écrire la sortie. Ou bien, vous pouvez placer en commentaire le paramètre de ligne `TagMode` et écrire les balises avec une balise de fermeture. (Le balisage de l’exemple est fourni plus loin dans ce didacticiel.)
    
    * Le `$` (signe dollar) dans la ligne suivante utilise une [interpolées chaîne](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/interpolated-strings):
    
    ```cshtml
    $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
    ```

4.  Ajoutez le balisage suivant à la *About.cshtml* vue. La balise en surbrillance affiche les informations de site web.
    
    [!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,12-)]
    
    >[!NOTE]
    > Dans le balisage de Razor indiqué ci-dessous :
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=13-17)]
    > 
    >Razor connaît le `info` attribut est une classe, et non une chaîne, et que vous souhaitez écrire du code c#. N’importe quel attribut d’assistance de balise de chaîne non doit être écrite sans la `@` caractère.
    
5.  Exécuter l’application et accédez à la vue à propos de pour afficher les informations de site web.

    >[!NOTE]
    >Vous pouvez utiliser le balisage suivant avec une balise de fermeture et supprimez la ligne avec `TagMode.StartTagAndEndTag` dans l’application d’assistance de balise :
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=13-18)]

## <a name="condition-tag-helper"></a>Condition d’assistance de balise

L’application d’assistance de balise condition restitue la sortie lorsqu’il est passé de la valeur true.

1.  Ajoutez le code suivant `ConditionTagHelper` classe le *TagHelpers* dossier.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

2.  Remplacez le contenu de la *Views/Home/Index.cshtml* fichier par le balisage suivant :

    ```cshtml
    @using AuthoringTagHelpers.Models
    @model WebsiteContext
    
    @{
        ViewData["Title"] = "Home Page";
    }
    
    <div>
        <h3>Information about our website (outdated):</h3>
        <website-information info=@Model />
        <div condition="@Model.Approved">
            <p>
                This website has <strong surround="em"> @Model.Approved </strong> been approved yet.
                Visit www.contoso.com for more information.
            </p>
        </div>
    </div>
    ```
    
3.  Remplacez le `Index` méthode dans le `Home` contrôleur avec le code suivant :

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

4.  Exécuter l’application et accédez à la page d’accueil. Le balisage de l’attribut conditional `div` ne sont pas rendus. Ajouter la chaîne de requête `?approved=true` à l’URL (par exemple, `http://localhost:1235/Home/Index?approved=true`). `approved`a la valeur true et que l’attribut conditional balisage s’affichera.

>[!NOTE]
>Utilisez le [nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) opérateur pour spécifier l’attribut cible, plutôt que de spécifier une chaîne comme vous le faisiez avec l’application d’assistance de balise en gras :
>
>[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
>Le [nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof) opérateur protège le code doit il jamais être refactorisé (nous voulons modifier le nom à `RedCondition`).

### <a name="avoiding-tag-helper-conflicts"></a>Prévention des conflits d’application d’assistance de balise

Dans cette section, vous écrivez une paire de liaison automatique les programmes d’assistance de balise. La première remplacera le balisage contenant une URL commençant par HTTP à un HTML d’ancrage balise qui contient la même URL (et produisant ainsi un lien vers l’URL). Le second sera faites de même pour une URL commençant par WWW.

Étant donné que ces deux programmes d’assistance sont étroitement liés et peuvent les refactoriser à l’avenir, nous allons les conserver dans le même fichier.

1.  Ajoutez le code suivant `AutoLinkerHttpTagHelper` classe le *TagHelpers* dossier.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

    >[!NOTE]
    >Le `AutoLinkerHttpTagHelper` classe cibles `p` éléments et utilise [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) pour créer le point d’ancrage.

2.  Ajoutez le balisage suivant à la fin de la *Views/Home/Contact.cshtml* fichier :

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

3.  Exécutez l’application et vérifiez que l’application d’assistance de balise restitue correctement le point d’ancrage.

4.  Mise à jour la `AutoLinker` classe pour inclure le `AutoLinkerWwwTagHelper` qui convertira www texte à une balise d’ancrage qui contient également le texte www d’origine. Le code mis à jour est mis en surbrillance ci-dessous :

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

5.  Exécutez l’application. Notez que le texte www est affiché sous forme de lien, mais le texte HTTP n’est pas. Si vous placez un point d’arrêt dans les deux classes, vous pouvez voir que la classe d’assistance de balise HTTP s’exécute en premier. Le problème est que la sortie d’assistance de balise est mis en cache, et lorsque l’application d’assistance de balise WWW est exécutée, il remplace la sortie mise en cache à partir de l’application d’assistance de balise HTTP. Plus loin dans ce didacticiel, nous verrons comment contrôler l’ordre dans lequel exécutent des programmes d’assistance de balise dans. Nous allons corriger le code avec les éléments suivants :

    [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

    >[!NOTE]
    >Dans la première édition des programmes d’assistance des balises de la liaison automatique, que vous avez obtenu le contenu de la cible avec le code suivant :
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
    >
    >Autrement dit, vous appelez `GetChildContentAsync` à l’aide de la `TagHelperOutput` passé dans le `ProcessAsync` (méthode). Comme mentionné précédemment, étant donné que la sortie est mise en cache, la dernière balise d’assistance pour exécuter wins. Vous avez résolu ce problème avec le code suivant :
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
    >
    >Le code ci-dessus vérifie si le contenu a été modifié et s’il a, il obtient le contenu à partir de la mémoire tampon de sortie.

6.  Exécutez l’application et vérifiez que les deux liaisons fonctionnent comme prévu. Pendant qu’il peut apparaître de que notre programme d’assistance de balise de l’éditeur de liens automatique est correcte et complète, il a un problème subtil. Si l’application d’assistance de balise WWW s’exécute en premier, il se peut que les liens Web ne seront pas correctes. Mettre à jour le code en ajoutant le `Order` pour contrôler l’ordre de la balise s’exécute dans la surcharge. Le `Order` propriété détermine l’ordre d’exécution par rapport à d’autres programmes d’assistance de balise ciblant le même élément. La valeur d’ordre par défaut est égale à zéro et les instances ayant des valeurs inférieures sont exécutées en premier.

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]
    
    Le code ci-dessus garantit que l’application d’assistance de balise HTTP s’exécute avant l’application d’assistance de balise WWW. Modification `Order` à `MaxValue` et vérifiez que le balisage généré pour la balise WWW est incorrect.

## <a name="inspecting-and-retrieving-child-content"></a>Inspection et la récupération du contenu enfant

Les programmes d’assistance de balise fournissent plusieurs propriétés permettant de récupérer le contenu.

-  Le résultat de `GetChildContentAsync` peuvent être ajoutés à `output.Content`.
-  Vous pouvez examiner le résultat de `GetChildContentAsync` avec `GetContent`.
-  Si vous modifiez `output.Content`, le corps TagHelper ne sera pas exécuté ou restitué, sauf si vous appelez `GetChildContentAsync` comme dans notre exemple auto-éditeur de liens :

[!code-csharp[Main](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

-  Appels multiples à `GetChildContentAsync` renvoie la même valeur et ne sera pas exécuter de nouveau la `TagHelper` de corps, sauf si vous passez dans un paramètre false indiquant n’utilise pas le résultat mis en cache.
