---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/intro-to-web-pages-programming
title: "Présentation des Pages Web ASP.NET - programmation | Documents Microsoft"
author: tfitzmac
description: "Ce didacticiel vous donne une vue d’ensemble de la façon de programme dans ASP.NET Web Pages avec syntaxe Razor. Vous apprendrez : la syntaxe « Razor » de base que vous utilisez pour la requête de tirage..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/17/2015
ms.topic: article
ms.assetid: 7526ed45-a97d-4e8a-8301-01324ef0eff9
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/intro-to-web-pages-programming
msc.type: authoredcontent
ms.openlocfilehash: eed07f4f8a13ea9082ab3aad3e3db24febff8ef6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introducing-aspnet-web-pages---programming-basics"></a>Présentation des Pages Web ASP.NET - notions de base de programmation
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Ce didacticiel vous donne une vue d’ensemble de la façon de programme dans ASP.NET Web Pages avec syntaxe Razor.
> 
> Ce que vous allez apprendre :
> 
> - La syntaxe « Razor » de base que vous utilisez pour la programmation dans ASP.NET Web Pages.
> - Certains c# de base, qui est le langage de programmation que vous allez utiliser.
> - Certains concepts de programmation fondamentales pour les Pages Web.
> - Comment installer des packages (composants qui contiennent du code prégénérée) à utiliser avec votre site.
> - L’utilisation de *programmes d’assistance* pour effectuer des tâches de programmation courantes.
>   
> 
> Fonctionnalités technologies abordées :
> 
> - NuGet et le Gestionnaire de package.
> - Le `Gravatar` helper.


Ce didacticiel est principalement un exercice de présentation de la syntaxe de programmation que vous utiliserez pour ASP.NET Web Pages. Vous allez en savoir plus sur *la syntaxe Razor* et langage de programmation du code qui est écrit en c#. Vous obtiendrez un aperçu de cette syntaxe dans le didacticiel précédent ; Dans ce didacticiel, nous expliquerons la syntaxe plus.

Nous vous assurons que ce didacticiel implique le meilleur parti de programmation que vous verrez dans un didacticiel unique et qu’il s’agit du didacticiel uniquement qui est *uniquement* sur la programmation. Dans les didacticiels restants dans ce jeu, vous créerez réellement des pages qui effectuent des opérations intéressantes.

Vous allez également découvrir *programmes d’assistance*. Un programme d’assistance est un composant, une partie des empaqueté de code, que vous pouvez ajouter à une page. L’application d’assistance effectue le travail qui sinon peut être fastidieuse ou complexes à réaliser manuellement.

## <a name="creating-a-page-to-play-with-razor"></a>Création d’une Page à jouer avec Razor

Dans cette section vous allez jouer un peu avec Razor afin d’obtenir une idée de la syntaxe de base.

Démarrez WebMatrix si elle n’est pas déjà en cours d’exécution. Vous allez utiliser le site Web que vous avez créé dans le didacticiel précédent ([mise en route de démarrage avec les Pages Web](https://go.microsoft.com/fwlink/?LinkId=251578)). Pour la rouvrir, cliquez sur **Mes Sites** et choisissez **WebPageMovies**:

![Écran d’accueil WebMatrix montrant les options d’ouvrir le Site et les Sites mes mis en surbrillance](intro-to-web-pages-programming/_static/image1.png)

Sélectionnez le **fichiers** espace de travail.

Dans le ruban, cliquez sur **nouveau** pour créer une page. Sélectionnez **CSHTML** et nommez la nouvelle page *TestRazor.cshtml*.

Cliquez sur **OK**.

Copiez le texte suivant dans le fichier, remplacer complètement ce qu’elle contient déjà.

> [!NOTE]
> Lorsque vous copiez balisage ou code dans les exemples dans une page, la mise en retrait et l’alignement ne peuvent pas être le même que dans le didacticiel. Mise en retrait et l’alignement n’affectent pas l’exécution du code, cependant.


[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample1.cshtml)]

## <a name="examining-the-example-page"></a>Examen de l’exemple de Page

La majeure partie de ce que vous voyez est HTML ordinaire. Toutefois, en haut, il est ce bloc de code :

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample2.cshtml)]

Notez les éléments suivants à propos de ce bloc de code :

- Le caractère « @ » indique à ASP.NET que ce qui suit est code Razor, pas HTML. ASP.NET traite tout ce qui suit le caractère en tant que code jusqu'à ce qu’il s’exécute à nouveau en HTML « @ ». (Dans ce cas, c’est le &lt;! DOCTYPE&gt; élément.
- Les accolades ({et}) placer un bloc de code Razor si le code a plusieurs lignes. Les accolades indiquent ASP.NET où le code de ce bloc commence et se termine.
- La / / marquent un commentaire, autrement dit, une partie du code qui ne s’exécutent.
- Chaque instruction doit se terminer par un point-virgule ( ;). (Pas de commentaires, cependant.)
- Vous pouvez stocker des valeurs dans *variables*, que vous créez (*déclarer*) avec le mot clé var. Lorsque vous créez une variable, vous lui donnez un nom, qui peut inclure des lettres, des chiffres et des traits de soulignement (\_). Les noms de variable ne peut pas commencer par un chiffre et ne peut pas utiliser le nom d’un mot clé de programmation (par exemple, var).
- Vous mettez entre guillemets les chaînes de caractères (comme « ASP.NET » et « Pages Web »). (Ils doivent être entre guillemets.) Nombres ne sont pas entre guillemets.
- Peu d’espace blanc en dehors des guillemets. Sauts de ligne principalement n’a pas d’importance ; l’exception est que vous ne peut pas fractionner une chaîne entre guillemets dans les lignes. Mise en retrait et l’alignement n’a pas d’importance.

Un élément qui n’est pas évident au vu de cet exemple est que tout le code respecte la casse. Cela signifie que la somme de variable est une variable différente que les variables qui peut être nommée somme ou somme. De même, var est un mot clé, mais Var n’est pas.

### <a name="objects-and-properties-and-methods"></a>Les objets et les propriétés et méthodes

Il y a l’expression DateTime.Now. En termes simples, DateTime est un *objet*. Un objet est une chose que vous pouvez programmer avec : une page, une zone de texte, un fichier, une image, une requête web, un message électronique, un enregistrement de client, etc. Objets possèdent un ou plusieurs *propriétés* qui décrivent leurs caractéristiques. Un objet de zone de texte a une propriété de texte (entre autres), un objet de requête a une propriété d’Url (et autres), un message électronique a une propriété From et une propriété à, et ainsi de suite. Les objets ont également *méthodes* qui sont « verbes » qu’ils peuvent effectuer. Vous allez travailler avec des objets beaucoup.

Comme vous pouvez le voir à partir de l’exemple, DateTime est un objet qui vous permet de programme dates et heures. Il a une propriété nommée maintenant qui retourne la date et heure actuelles.

### <a name="using-code-to-render-markup-in-the-page"></a>À l’aide de code pour restituer le balisage dans la page

Dans le corps de la page, notez les points suivants :

[!code-html[Main](intro-to-web-pages-programming/samples/sample3.html)]

Là encore, le caractère « @ » indique à ASP.NET que ce qui suit est le code, pas HTML. Dans le balisage vous pouvez ajouter suivi d’une expression de code, et ASP.NET restitue la valeur de ce droit de l’expression à ce stade. Dans l’exemple, @a rendra tout ce qui est par la valeur de la variable nommée, @product affiche tout ce qui se trouve dans le produit nommé variable et ainsi de suite.

Vous n’êtes pas limité à des variables, cependant. Dans certains cas, le caractère « @ » précède une expression :

- @(un\*b) restitue le produit de tout ce qui est dans les variables d’un et b. (Le \* opérateur : multiplication.)
- @(technologie + » « + produit) affiche les valeurs de la technologie de variables et les produits après les concaténer et ajout d’un espace entre les deux. L’opérateur (+) pour concaténer des chaînes est identique à l’opérateur pour l’ajout de nombres. ASP.NET peut indiquer généralement si vous travaillez avec des nombres ou des chaînes et qu’il effectue l’opération de droite avec l’opérateur « + ».
- @Request.Urlrend la propriété Url de l’objet de demande. L’objet de requête contient des informations sur la demande en cours à partir du navigateur, et bien entendu la propriété Url contient l’URL de la demande actuelle.

L’exemple est également conçu pour montrer que vous pouvez vous fonctionnent différemment. Vous pouvez effectuer des calculs dans le bloc de code en haut, placer les résultats dans une variable et rendre ensuite la variable dans le balisage. Ou bien, vous pouvez effectuer des calculs dans un droit de l’expression dans le balisage. L’approche que vous utilisez dépend de ce que vous faites et, dans une certaine mesure, de vos propres préférences.

### <a name="seeing-the-code-in-action"></a>Voir le code en action

Cliquez sur le nom du fichier, puis choisissez **lancer dans un navigateur**. Vous consultez la page dans le navigateur avec toutes les valeurs et les expressions résolues dans la page.

![Page 'TestRazor' en cours d’exécution dans le navigateur](intro-to-web-pages-programming/_static/image2.png)

Recherchez la source dans le navigateur.

![Source de la page « Test Razor » dans le navigateur](intro-to-web-pages-programming/_static/image3.png)

Comme vous le souhaitez à partir de votre expérience dans le didacticiel précédent, le code Razor est dans la page. Vous ne voyez que sont les valeurs réelles. Lorsque vous exécutez une page, vous effectuez en fait une demande au serveur web intégré à WebMatrix. Lorsque la demande est reçue, ASP.NET résout toutes les valeurs et les expressions et effectue le rendu leurs valeurs dans la page. Il envoie ensuite la page au navigateur.

> [!TIP] 
> 
> **Razor et c#**
> 
> Jusqu'à présent, nous avons dit que vous travaillez avec la syntaxe Razor. Qui a la valeur true, mais il n’est pas le récit terminé. Le langage de programmation réel que vous utilisez est appelé *c#*. C# a été créé par Microsoft dix ans plus tôt et est devenu l’un des langages de programmation principales pour la création d’applications Windows. Toutes les règles que vous avez vu comment nommer une variable et comment créer des instructions et ainsi de suite sont effectivement de toutes les règles du langage c#.
> 
> Razor fait référence plus spécifiquement à l’ensemble des conventions de la façon dont vous incorporez ce code dans une page. Par exemple, la convention de l’utilisation d’à marquer du code dans la page et @ {} pour incorporer un bloc de code est l’aspect Razor d’une page. Programmes d’assistance sont également considérés comme faisant partie de Razor. La syntaxe Razor est utilisée dans des emplacements plus élevée que simplement dans ASP.NET Web Pages. (Par exemple, il est utilisé dans les affichages d’ASP.NET MVC.)
> 
> Cela dit, car si vous recherchez des informations sur la programmation ASP.NET Web Pages, vous trouverez un grand nombre de références pour Razor. Toutefois, un grand nombre de ces références ne s’appliquent pas à ce que vous êtes cette opération et pouvez par conséquent être déroutant. Et en fait, la plupart de vos questions de programmation sont réellement sur l’utilisation de c# ou utilisation avec ASP.NET. Par conséquent, si vous recherchez spécifiquement pour plus d’informations sur Razor, vous ne pas trouver les réponses que vous avez besoin.


## <a name="adding-some-conditional-logic"></a>Ajout d’une logique conditionnelle

Une des fonctionnalités sur l’utilisation de code dans une page est que vous pouvez modifier ce qui se passe en fonction de différentes conditions. Dans cette partie du didacticiel, vous allez vous familiariser avec quelques méthodes permettant de modifier le contenu affiché dans la page.

L’exemple est simple et quelque peu inventé afin que nous pouvons nous concentrer sur la logique conditionnelle. La page que vous créerez est procédez comme suit :

- Afficher un texte différent sur la page selon qu’il s’agit de la première fois que la page s’affiche, ou si, après avoir cliqué sur un bouton pour envoyer la page. Qui sera le premier test conditionnel.
- Affiche le message uniquement si une certaine valeur est passée dans la chaîne de requête de l’URL (http://...?show=true). Ce sera le deuxième test conditionnel.

Dans WebMatrix, créez une page et nommez-le *TestRazorPart2.cshtml*. (Dans le ruban, cliquez sur **nouveau**, choisissez **CSHTML**, nommez le fichier, puis cliquez sur **OK**.)

Remplacez le contenu de cette page avec les éléments suivants :

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample4.cshtml)]

Le bloc de code en haut Initialise une variable nommée message contenant du texte. Dans le corps de la page, le contenu de la variable de message s’affiche à l’intérieur d’un &lt;p&gt; élément. La balise contient également un &lt;d’entrée&gt; élément pour créer un **Submit** bouton.

Exécutez la page pour voir comment il fonctionne maintenant. Pour l’instant, il s’agit essentiellement une page statique, même si vous cliquez sur le **Submit** bouton.

Revenez à WebMatrix. Dans le bloc de code, ajoutez le code en surbrillance suivant *après* la ligne qui initialise le message :

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample5.cshtml?highlight=4-6)]

### <a name="the-if---block"></a>If {} bloc

Ce que vous venez d’ajouter a été if condition. Dans le code, le si la condition a une structure similaire à ceci :

[!code-csharp[Main](intro-to-web-pages-programming/samples/sample6.cs)]

La condition à tester est entre parenthèses. Il doit être une valeur ou une expression qui retourne true ou false. Si la condition est true, ASP.NET exécute l’instruction ou les instructions à l’intérieur des accolades. (Ce sont les *puis* dans le cadre de la *if-then* logique.) Si la condition est false, le bloc de code est ignoré.

Voici quelques exemples de conditions que vous pouvez tester dans une instruction if instruction :

[!code-csharp[Main](intro-to-web-pages-programming/samples/sample7.cs)]

Vous pouvez tester les variables par rapport aux valeurs ou par rapport aux expressions en utilisant un *opérateur logique* ou *opérateur de comparaison*: égal à (==), supérieur à (&gt;), inférieur à (&lt;), supérieur ou égal à (&gt;=) et inférieur ou égal à (&lt;=). Le ! = signifie d’opérateur non égal à, par exemple, si (un ! = 0) signifie *si* *un**n’est pas égal à 0*.

> [!NOTE]
> Assurez-vous que vous remarquez que l’opérateur de comparaison égal à (==) n’est pas le même que =. Le = opérateur est utilisé uniquement pour affecter des valeurs (var un = 2). Si vous combinez ces opérateurs de, vous obtenez une erreur ou que vous obtiendrez des résultats inattendus.


Pour tester si un élément a la valeur true, la syntaxe complète est if(IsDone == true). Mais vous pouvez également utiliser le raccourci if(IsDone). S’il n’existe aucun opérateur de comparaison, ASP.NET suppose que vous testez pour la valeur true.

Le ! opérateur par lui-même : un NOT logique. Par exemple, la condition if ( ! IsPost) signifie *si IsPost n’est pas vrai*.

Vous pouvez combiner les conditions à l’aide d’une opération AND logique (&amp; &amp; opérateur) ou l’opérateur logique OR (|| (opérateur)). Par exemple, le dernier if conditions dans les moyens d’exemples précédents *si FileProcessingIsDone n’est pas définie sur true et displayMessage est défini sur false*.

### <a name="the-else-block"></a>Le bloc else

Une dernière chose sur if blocs : une si le bloc peut être suivi par un autre bloc. Un bloc else est utile est d’avoir à exécuter un code différent lorsque la condition est false. Voici un exemple simple :

[!code-csharp[Main](intro-to-web-pages-programming/samples/sample8.cs)]

Vous verrez des exemples dans les didacticiels plus loin dans cette série où il est utile d’à l’aide d’un autre bloc.

### <a name="testing-whether-the-request-is-a-submit-post"></a>Tester si la demande est un envoi (post)

Il existe plus mais revenons à l’exemple, qui a le if(IsPost) condition {...}. IsPost est réellement une propriété de la page actuelle. La première fois que la page est demandée, IsPost retourne la valeur false. Toutefois, si vous cliquez sur un bouton ou que vous envoyez la page, autrement dit, vous le validez — IsPost retourne la valeur true. Par conséquent, IsPost vous permet de déterminer si vous êtes confronté à un envoi de formulaire. (En termes de verbes HTTP, si la demande est une opération GET, IsPost retourne false. Si la demande est une opération POST, IsPost retourne la valeur true.) Dans un prochain didacticiel, vous allez travailler avec des formes d’entrée, où ce test s’avère particulièrement utile.

Exécutez la page. Étant donné que c’est la première fois vous êtes demandé la page, vous voyez « Il s’agit de la première fois que vous avez demandé la page ». Cette chaîne est la valeur que vous avez initialisé à la variable de message. Il existe un test if(IsPost), mais qui retourne la valeur false pour le moment, pour que le code à l’intérieur d’if bloque ne s’exécute pas.

Cliquez sur le **Submit** bouton. La page est demandée de nouveau. Comme avant, la variable de message est définie à « Il s’agit de la première fois... ». Mais cette fois, le test if(IsPost) retourne la valeur true, ce qui s’exécute de bloquer le code dans le cas. Le code modifie la valeur de la variable de message sur une autre valeur, qui est ce qui est rendu dans le balisage.

Ajoutez ensuite une instruction if condition dans le balisage. Sous le &lt;p&gt; élément qui contient le **Submit** bouton, ajoutez le balisage suivant :

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample9.cshtml)]

Vous ajoutez le code au sein du balisage, afin que vous ayez pour commencer @. Il y a une instruction if test similaire à celui que vous avez ajouté précédemment dans le bloc de code. Les accolades, cependant, vous ajoutez HTML ordinaire, au moins, il est ordinaire jusqu'à ce qu’elle arrive au @DateTime.Now. Il s’agit d’un autre peu de code Razor, et vous devez ajouter placés devant lui.

L’important est que vous pouvez ajouter si les conditions à la fois dans le code de bloc en haut et dans le balisage. Si vous utilisez une instruction if condition dans le corps de la page, les lignes à l’intérieur du bloc peut être de balisage ou code. Dans ce cas, et que cette propriété a la valeur true lorsque vous combinez le balisage et le code, vous devez utiliser afin de pouvoir effacer pour ASP.NET où le code est.

Exécutez la page et cliquez sur **Submit**. Ce temps vous permet de voir un message différent lorsque vous envoyez (« maintenant vous avez soumis... »), mais que vous voyez un message qui indique la date et l’heure.

![Page 'Test Razor 2' en cours d’exécution dans le navigateur avec un horodateur afficher après l’envoi](intro-to-web-pages-programming/_static/image4.png)

### <a name="testing-the-value-of-a-query-string"></a>Test de la valeur de chaîne de requête

Un test plus. Cette fois-ci, vous allez ajouter une instruction if bloc qui teste une valeur nommé show qui peut-être être transmis dans la chaîne de requête. (À ceci : '' http://localhost:43097/TestRazorPart2.cshtml`?show=true`) vous allez modifier la page afin que le message vous avez été affichage (« Il s’agit de la première fois... », etc.) s’affiche uniquement si la valeur d’émission est true.

Dans le bas (mais à l’intérieur), le bloc de code en haut de la page, ajoutez le code suivant :

[!code-csharp[Main](intro-to-web-pages-programming/samples/sample10.cs)]

L’apparence de maintenant de bloc de code complet à l’exemple suivant. (N’oubliez pas que lorsque vous copiez le code dans votre page, la mise en retrait peut différer. Mais qui n’affecte pas l’exécution du code.)

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample11.cshtml)]

Le nouveau code dans le bloc Initialise une variable nommée showMessage sur false. Il effectue ensuite une instruction if test pour rechercher une valeur dans la chaîne de requête. Lorsque vous demandez tout d’abord la page, il possède une URL similaire à celle-ci :

`http://localhost:43097/TestRazorPart2.cshtml`

Le code détermine si l’URL contient une variable nommée afficher dans la chaîne de requête, telles que cette version de l’URL :

`http://localhost:43097/TestRazorPart2.cshtml`? Afficher = true

Le test lui-même examine la propriété de chaîne de requête de l’objet de demande. Si la chaîne de requête contient un élément nommé afficher, et si cet élément est défini sur true, le cas bloc s’exécute et définit la variable showMessage sur true.

Il existe une astuce ici, comme vous pouvez le voir. Comme son nom l’indique, la chaîne de requête est une chaîne. Toutefois, vous pouvez tester uniquement pour la valeur true et false si la valeur que vous testez est la valeur booléenne (true/false). Avant de pouvoir tester la valeur de la variable de l’afficher dans la chaîne de requête, vous devez convertir en valeur booléenne. C’est ce que fait la méthode AsBool, elle prend une chaîne comme entrée et la convertit en une valeur booléenne. Clairement, si la chaîne est « true », la méthode AsBool convertit cette valeur sur true. Si la valeur de la chaîne est autre chose, AsBool retourne la valeur false.

> [!TIP] 
> 
> **Types de données et les méthodes As()**
> 
> Nous avons uniquement dit jusqu'à présent que lorsque vous créez une variable, vous utilisez le mot clé var. Qui n’est pas bien que la totalité de l’article. Afin de manipuler des valeurs, ajouter des numéros, ou concaténer des chaînes, ou comparer les dates de test pour la valeur true/false, C# doit fonctionner avec une représentation interne appropriée de la valeur. C# peuvent *généralement* déterminer ce que cette représentation doit être (autrement dit, ce qui *type* les données sont) en fonction de ce que vous faites avec les valeurs. Maintenant, puis, cependant, il ne peut pas le faire. Si ce n’est pas le cas, vous devez aider en explicitement indiquant comment c# doivent représenter des données. La méthode AsBool effectue cette opération, il indique c# qu’une valeur de chaîne « true » ou « false » doit être traitée comme une valeur booléenne. Il existe des méthodes similaires pour représenter des chaînes en tant que d’autres types, telles que AsInt (considérer comme un entier), AsDateTime (considérer comme une date/heure), AsFloat (considérer comme un nombre à virgule flottante) et ainsi de suite. Lorsque vous utilisez en tant que méthodes de (), si c# ne peut pas représenter la valeur de chaîne comme demandé, vous obtiendrez une erreur.


Dans le balisage de la page, supprimez ou commentez cet élément (ici qu’il est indiqué comme commenté) :

[!code-html[Main](intro-to-web-pages-programming/samples/sample12.html)]

Right où vous supprimé ou commenté ce texte, ajoutez le code suivant :

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample13.cshtml)]

Le si test indique que si la variable showMessage est true, rendre un &lt;p&gt; élément avec la valeur de la variable de message.

### <a name="summary-of-your-conditional-logic"></a>Résumé de la logique conditionnelle

Dans le cas où vous ne savez pas entièrement de ce que vous venez de faire, Voici un résumé.

- La variable de message est initialisée à une chaîne par défaut (« Ceci est la première fois... »).
- Si la demande de page est le résultat d’un envoi (post), la valeur du message est modifiée pour « maintenant vous avez soumis... »
- La variable showMessage est initialisée sur false.
- Si la chaîne de requête contient ? afficher = true, la variable showMessage est définie sur true.
- Dans le balisage, si showMessage est true, un &lt;p&gt; élément est rendu qui affiche la valeur du message. (Si showMessage est false, rien n’est restitué à ce stade dans le balisage.)
- Dans le balisage, si la demande est une publication, un &lt;p&gt; élément est rendu qui affiche la date et l’heure.

Exécutez la page. Aucun message, ne tient showMessage est false, dans le balisage, le test if(showMessage) retourne false.

Cliquez sur **envoyer**. Affiche la date et une heure, mais toujours pas de message.

Dans votre navigateur, accédez à la zone URL et ajoutez le code suivant à la fin de l’URL : ? afficher = true et appuyez sur ENTRÉE.

![Page de « Test Razor 2 » dans le navigateur avec la chaîne de requête](intro-to-web-pages-programming/_static/image5.png)

La page s’affiche de nouveau. (Étant donné que vous avez modifié l’URL, c’est une nouvelle requête, pas un envoi.) Cliquez sur **Submit** à nouveau. Le message s’affiche à nouveau, en l’état de la date et l’heure.

![La page « Test Razor 2 » après soumettre lorsqu’il existe une chaîne de requête](intro-to-web-pages-programming/_static/image6.png)

Dans l’URL, remplacez ? afficher = true ? afficher = false et appuyez sur ENTRÉE. Soumettre à nouveau à la page. La page est en mode de démarrage, aucun message.

Comme indiqué précédemment, la logique de cet exemple est quelque peu inventé. Toutefois, si va s’afficher dans la plupart de vos pages, et va prendre un ou plusieurs des formes que vous avez vu ici.

## <a name="installing-a-helper-displaying-a-gravatar-image"></a>L’installation d’un programme d’assistance (affichage d’une Image de Gravatar)

Certaines tâches que les utilisateurs souhaitent souvent sur les pages web nécessitent une grande quantité de code ou nécessitent des connaissances supplémentaires. Exemples : affichage d’un graphique pour les données ; placer un Facebook « Comme « bouton sur une page ; envoi de courrier électronique à partir de votre site Web ; rognage ou redimensionner des images ; à l’aide de PayPal pour votre site. Pour faciliter la faire de ces types d’éléments différents, les Pages Web ASP.NET vous permet d’utiliser *programmes d’assistance*. Il s’agit de composants que vous installez un site et qui vous permettent d’effectuer des tâches courantes à l’aide de quelques lignes de code Razor.

Les Pages Web ASP.NET a plusieurs programmes d’assistance intégrées. Toutefois, les nombreuses applications auxiliaires sont disponibles dans des packages (compléments) qui sont fournies à l’aide du Gestionnaire de package NuGet. NuGet vous permet de sélectionner un package à installer et puis elle s’occupe de tous les détails de l’installation.

Dans cette partie du didacticiel, vous allez installer un programme d’assistance qui vous permet d’afficher une image Gravatar (« avatar globalement reconnue »). Vous allez apprendre deux choses. L’une est à rechercher et installer un programme d’assistance. Vous apprendrez également comment une application auxiliaire permet de facilement faire quelque chose que vous devez faire en utilisant beaucoup de code, que vous devez écrire vous-même dans le cas contraire.

Vous pouvez inscrire votre propre Gravatar sur le site Web de Gravatar à [http://www.gravatar.com/](http://www.gravatar.com/), mais il n’est pas essentiel pour créer un compte Gravatar pour effectuer cette partie du didacticiel.

Dans WebMatrix, cliquez sur le **NuGet** bouton.

![Boîte de dialogue de la galerie NuGet dans WebMatrix](intro-to-web-pages-programming/_static/image7.png)

Cela lance le Gestionnaire de package NuGet et affiche les packages disponibles. (Tous les packages sont des programmes d’assistance ; certaines ajoutent des fonctionnalités à WebMatrix, certaines sont des modèles supplémentaires et ainsi de suite.) Vous pouvez obtenir un message d’erreur sur l’incompatibilité de version. Vous pouvez ignorer ce message d’erreur en cliquant sur **OK** et poursuivre ce didacticiel.

![Boîte de dialogue de la galerie NuGet dans WebMatrix](intro-to-web-pages-programming/_static/image8.png)

Dans la zone de recherche, entrez « asp.net applications auxiliaires ». NuGet affiche les packages qui correspondent aux termes recherchés.

![Galerie NuGet dans WebMatrix montrant des packages](intro-to-web-pages-programming/_static/image9.png)

La bibliothèque de programmes d’assistance ASP.NET Web contient le code pour simplifier de nombreuses tâches courantes, notamment l’utilisation d’images de Gravatar. Sélectionnez le **ASP.NET Web Helpers Library** du package, puis cliquez sur **installer** pour lancer le programme d’installation. Sélectionnez **Oui** lorsque vous êtes invité à installer le package et acceptez les termes du contrat pour terminer l’installation.

C'est tout ! NuGet télécharge et installe tout, y compris tous les composants supplémentaires qui peuvent être requises (*dépendances*).

Si vous devez désinstaller un programme d’assistance pour une raison quelconque, le processus est très similaire. Cliquez sur le **NuGet** , sur le **installé** onglet et sélectionnez le package que vous souhaitez désinstaller.

## <a name="using-a-helper-in-a-page"></a>À l’aide d’un programme d’assistance dans une Page

Vous allez maintenant utiliser l’application d’assistance que vous venez d’installer. Le processus d’ajout d’un programme d’assistance à une page est similaire pour la plupart des applications auxiliaires.

Dans WebMatrix, créez une page et nommez-le *GravatarTest.cshml*. (Que vous créez une page spéciale pour tester l’application d’assistance, mais vous pouvez utiliser des programmes d’assistance dans n’importe quelle page de votre site).

À l’intérieur de la &lt;corps&gt; élément, ajouter un &lt;div&gt; élément. À l’intérieur de la &lt;div&gt; élément, tapez ce qui suit :

@Gravatar.

Le caractère « @ » est le même caractère que vous avez utilisé pour marquer le code Razor. **Gravatar** est l’objet d’assistance qui avec lequel vous travaillez.

Dès que vous tapez le point (.), WebMatrix affiche une liste de *méthodes* (fonctions) que l’application d’assistance Gravatar rend disponible :

![Liste déroulante IntelliSense d’assistance de Gravatar](intro-to-web-pages-programming/_static/image10.png)

Cette fonctionnalité est appelée *IntelliSense*. Il vous aide à votre code en fournissant des choix de contexte approprié. IntelliSense fonctionne avec HTML, CSS, ASP.NET code, JavaScript et autres langues sont prises en charge dans WebMatrix. Il s’agit d’une autre fonctionnalité qui facilite le développement de pages web dans WebMatrix.

Appuyez sur G sur le clavier et que vous consultez que IntelliSense recherche la méthode GetHtml. Appuyez sur Tab. IntelliSense insère la méthode sélectionnée (GetHtml). Tapez une parenthèse ouvrante et notez que la parenthèse fermante est automatiquement ajoutée. Tapez votre adresse de messagerie entre guillemets entre les deux parenthèses. Si vous avez un compte Gravatar, votre photo de profil s’affichera. Si vous n’avez pas un compte Gravatar, une image par défaut est retournée. Lorsque vous avez terminé, la ligne ressemble à ceci :

[!code-css[Main](intro-to-web-pages-programming/samples/sample14.css)]

Désormais afficher la page dans un navigateur. L’image ou l’image par défaut s’affiche, selon que vous disposez d’un compte Gravatar.

![Gravatar](intro-to-web-pages-programming/_static/image11.png) ![image par défaut](intro-to-web-pages-programming/_static/image12.png)

Pour avoir une idée de ce que fait l’application d’assistance pour vous, affichez la source de la page dans le navigateur. En même temps que le code HTML que vous aviez dans votre page, vous voyez un élément d’image qui inclut un identificateur. C’est le code de l’application d’assistance rendue dans la page à l’endroit où vous aviez @Gravatar.GetHtml. Le programme d’assistance a nécessité les informations fournies et généré le code qui communique directement avec Gravatar afin de récupérer l’image appropriée pour le compte fourni.

La méthode GetHtml vous permet également de personnaliser l’image en fournissant des autres paramètres. Le code suivant montre comment demander une image a une largeur et une hauteur de 40 pixels et utilise une image spécifiée par défaut nommée **wavatar** si le compte spécifié n’existe pas.

[!code-javascript[Main](intro-to-web-pages-programming/samples/sample15.js)]

Ce code génère un nom tel que le résultat suivant (l’image par défaut varient au hasard).

![](intro-to-web-pages-programming/_static/image13.png)

## <a name="coming-up-next"></a>Prochaine

Pour conserver ce court didacticiel, nous avons dû se concentrer sur quelques notions de base uniquement. Naturellement, il existe un *lot* plus Razor et c#. Vous allez découvrir plus en tant que vous parcourez ces didacticiels. Si vous souhaitez en savoir plus sur les aspects de programmation de Razor et c# dès maintenant, vous pouvez lire une présentation plus détaillée ici : [Introduction à ASP.NET Web Programming à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890).

Le didacticiel suivant vous présente à l’utilisation d’une base de données. Dans ce didacticiel, vous allez commencer à créer l’exemple d’application qui vous permet de répertorier vos films favoris.

## <a name="complete-listing-for-testrazor-page"></a>Liste complète de la Page de TestRazor

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample16.cshtml)]

## <a name="complete-listing-for-testrazorpart2-page"></a>Liste complète de la Page de TestRazorPart2

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample17.cshtml)]

## <a name="complete-listing-for-gravatartest-page"></a>Liste complète de la Page de GravatarTest

[!code-cshtml[Main](intro-to-web-pages-programming/samples/sample18.cshtml)]

## <a name="additional-resources"></a>Ressources supplémentaires

- [Introduction à la programmation Web ASP.NET à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)
- [Application auxiliaire Twitter](../../ui-layouts-and-themes/twitter-helper.md)

>[!div class="step-by-step"]
[Précédent](getting-started.md)
[Suivant](displaying-data.md)
