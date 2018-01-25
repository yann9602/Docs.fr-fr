---
uid: web-pages/overview/ui-layouts-and-themes/4-working-with-forms
title: Utilisation des formulaires HTML dans des Sites ASP.NET Web Pages (Razor) | Documents Microsoft
author: tfitzmac
description: "Un formulaire est une section d’un document HTML dans lequel vous enregistrez des contrôles d’entrée d’utilisateur, telles que les zones de texte, les cases à cocher, les cases d’option et les listes déroulantes. Vous utilisez des formulaires Much..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/10/2014
ms.topic: article
ms.assetid: f3f4b8c8-e8f6-4474-ad94-69228a6c01ee
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/4-working-with-forms
msc.type: authoredcontent
ms.openlocfilehash: 8579c444fd19d1a366349cc09f9f768de23055f8
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="working-with-html-forms-in-aspnet-web-pages-razor-sites"></a>Utilisation des formulaires HTML dans les Sites ASP.NET Web Pages (Razor)
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article décrit comment traiter un formulaire HTML (avec des zones de texte et les boutons) lorsque vous travaillez dans un site Web ASP.NET Web Pages (Razor).
> 
> **Ce que vous allez apprendre :** 
> 
> - Comment créer un formulaire HTML.
> - Comment lire l’entrée d’utilisateur à partir du formulaire.
> - Comment valider l’entrée d’utilisateur.
> - Comment rétablir les valeurs de formulaire après l’envoi de la page.
> 
> Il s’agit de programmation les concepts présentés dans l’article ASP.NET :
> 
> - Objet `Request`.
> - Validation d’entrée.
> - Le codage HTML.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.


## <a name="creating-a-simple-html-form"></a>Création d’un formulaire HTML Simple

1. Créer un nouveau site Web.
2. Dans le dossier racine, créez une page web nommée *Form.cshtml* et entrez le balisage suivant :

    [!code-html[Main](4-working-with-forms/samples/sample1.html)]
3. Lancez la page dans votre navigateur. (Dans WebMatrix, dans le **fichiers** espace de travail, cliquez sur le fichier, puis sélectionnez **lancer dans un navigateur**.) Un simple formulaire avec trois champs d’entrée et un **Submit** bouton s’affiche.

    ![Capture d’écran d’un formulaire avec trois zones de texte.](4-working-with-forms/_static/image1.jpg)

    À ce stade, si vous cliquez sur le **Submit** bouton, rien ne se produit. Pour rendre le formulaire utile, vous devez ajouter du code qui s’exécute sur le serveur.

## <a name="reading-user-input-from-the-form"></a>La lecture de l’entrée d’utilisateur à partir de l’écran

Pour traiter le formulaire, vous ajoutez le code qui lit les valeurs de champ soumis et fait quelque chose avec eux. Cette procédure montre comment lire les champs et afficher l’entrée d’utilisateur sur la page. (Dans une application de production, vous le faites généralement des opérations plus intéressantes avec l’entrée d’utilisateur. Vous devez le faire dans l’article sur l’utilisation des bases de données.)

1. En haut de la *Form.cshtml* de fichier, entrez le code suivant :

    [!code-cshtml[Main](4-working-with-forms/samples/sample2.cshtml)]

    Lorsque l’utilisateur demande tout d’abord la page, seul le formulaire vide s’affiche. L’utilisateur (qui sera vous) remplit le formulaire et clique ensuite sur **Submit**. Cela envoie (publie) l’entrée d’utilisateur sur le serveur. Par défaut, la demande est transmise à la même page (à savoir, *Form.cshtml*).

    Quand vous envoyez la page de ce temps, les valeurs que vous avez entré sont affichent juste au-dessus de la forme :

    ![Capture d’écran qui affiche les valeurs que vous avez entrées affichées sur la page.](4-working-with-forms/_static/image2.jpg)

    Examinez le code de la page. Vous utilisez d’abord la `IsPost` méthode pour déterminer si la page est en cours de publication &#8212; autrement dit, si un utilisateur a cliqué sur le **Submit** bouton. S’il s’agit d’une publication, `IsPost` retourne la valeur true. Il s’agit de la méthode standard dans les Pages Web ASP.NET pour déterminer si vous travaillez avec une demande initiale (une demande GET) ou une publication (une demande POST). (Pour plus d’informations sur GET et POST, consultez la barre latérale « HTTP GET et POST et le IsPost Property » dans [Introduction à ASP.NET Web Pages de programmation à l’aide de la syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890#SB_HttpGetPost).)

    Ensuite, vous obtenez les valeurs renseigné par l’utilisateur à partir de la `Request.Form` objet et que vous les placez dans les variables pour une date ultérieure. Le `Request.Form` objet contient toutes les valeurs qui ont été envoyés avec la page, chacune étant identifiée par une clé. La clé est équivalente à la `name` attribut du champ de formulaire que vous souhaitez lire. Par exemple, pour lire le `companyname` champ (zone de texte), vous utilisez `Request.Form["companyname"]`.

    Les valeurs de formulaire sont stockées dans le `Request.Form` objet sous forme de chaînes. Par conséquent, lorsque vous devez utiliser une valeur comme un nombre ou une date ou un autre type, vous devez convertir une chaîne de ce type. Dans l’exemple, le `AsInt` méthode de la `Request.Form` est utilisée pour convertir la valeur du champ employés (qui contient un nombre d’employés) en un entier.
2. Lancement de la page dans votre navigateur, renseignez les champs de formulaire, puis cliquez sur **Submit**. La page affiche les valeurs que vous avez entré.

> [!TIP] 
> 
> <a id="SB_HTMLEncoding"></a>
> ### <a name="html-encoding-for-appearance-and-security"></a>Encodage pour l’apparence et la sécurité HTML
> 
> Le code HTML contient des utilisations particulières de caractères tels que `<`, `>`, et `&`. Si ces caractères spéciaux s’affiche, où elles ne sont pas attendues, ils peuvent détruire l’apparence et les fonctionnalités de votre page web. Par exemple, le navigateur interprète le `<` de caractères (sauf si elle est suivie par un espace) comme le début d’un élément HTML, tel que `<b>` ou `<input ...>`. Si le navigateur ne reconnaît pas l’élément, il ignore simplement la chaîne commence par `<` jusqu'à atteindre un élément qui il reconnaît à nouveau. Évidemment, cela peut entraîner certaines bizarre rendu dans la page.
> 
> L’encodage HTML remplace ces caractères réservés un code navigateurs interprètent en tant que le symbole correct. Par exemple, le `<` est remplacé par `&lt;` et `>` est remplacé par `&gt;`. Le navigateur rend ces chaînes de remplacement comme les caractères que vous souhaitez voir.
> 
> Il est judicieux d’utiliser toutes les fois que vous affichez les chaînes du codage HTML (entrée) que vous avez obtenu à partir d’un utilisateur. Si vous ne le faites pas, un utilisateur peut essayer d’obtenir votre page web pour exécuter un script malveillant ou faire autre chose qui peut compromettre la sécurité de votre site ou qui n’est pas ce que vous avez l’intention. (Cela est particulièrement important si vous prenez stocker un endroit où l’entrée d’utilisateur et afficher ultérieurement &#8212; par exemple, un commentaire de blog, utilisateur de consulter, ou quelque chose comme qui).
> 
> Pour aider à éviter ces problèmes, les Pages Web ASP.NET automatiquement encode au format HTML tout texte de contenu que vous avez la sortie à partir de votre code. Par exemple, lorsque vous affichez le contenu d’une variable ou une expression à l’aide de code tel que `@MyVar`, les Pages Web ASP.NET encode automatiquement la sortie.


## <a name="validating-user-input"></a>Validation des entrées utilisateur

Les utilisateurs font des erreurs. Demandez-lui pour renseigner un champ et ils oublient, ou vous lui demandez d’entrer le nombre d’employés et ils tapent un nom à la place. Pour vous assurer qu’un formulaire a été renseigné correctement avant le traitement terminé, vous validez les entrées de l’utilisateur.

Cette procédure montre comment valider tous les champs de formulaire trois pour vous assurer que l’utilisateur n’a pas été laissez-les vides. Vous vérifiez également que la valeur du nombre employé est un nombre. S’il existe des erreurs, vous allez afficher une erreur de message qui indique à l’utilisateur que les valeurs n’a pas passé la validation.

1. Dans le *Form.cshtml* fichier, remplacez le premier bloc de code avec le code suivant : 

    [!code-cshtml[Main](4-working-with-forms/samples/sample3.cshtml)]

    Pour valider l’entrée d’utilisateur, vous utilisez la `Validation` helper. Vous enregistrez les champs obligatoires en appelant `Validation.RequireField`. Inscrire d’autres types de validation en appelant `Validation.Add` et en spécifiant le champ à valider et le type de validation à effectuer.

    Lorsque la page s’exécute, ASP.NET effectue toutes la validation pour vous. Vous pouvez vérifier les résultats en appelant `Validation.IsValid`, qui retourne true si tout a réussi et false si n’importe quel champ Échec de la validation. En général, vous appelez `Validation.IsValid` avant d’effectuer tout traitement de l’entrée utilisateur.
2. Mise à jour la `<body>` élément en ajoutant les trois appels à la `Html.ValidationMessage` méthode, comme suit :

    [!code-cshtml[Main](4-working-with-forms/samples/sample4.cshtml?highlight=8,13,18)]

    Pour afficher les messages d’erreur de validation, vous pouvez appeler Html.`ValidationMessage` et lui transmettre le nom du champ que vous souhaitez que le message pour.
3. Exécutez la page. Laissez les champs vides et cliquez sur **Submit**. Vous consultez les messages d’erreur.

    ![Capture d’écran qui affiche les messages d’erreur affichées si l’entrée d’utilisateur ne sont pas validées.](4-working-with-forms/_static/image3.jpg)
4. Ajouter une chaîne (par exemple, « ABC ») à la **Employee Count** champ et cliquez sur **Submit** à nouveau. Cette fois que vous voyez une erreur qui indique que la chaîne n’est pas dans le bon format, à savoir, un entier.

    ![Capture d’écran qui affiche des messages d’erreur affichées si les utilisateurs entrent une chaîne pour le champ d’employés.](4-working-with-forms/_static/image4.jpg)

Les Pages Web ASP.NET fournit des options pour la validation des entrées d’utilisateur, y compris la possibilité d’effectuer automatiquement la validation à l’aide du script client, afin que les utilisateurs obtiennent un retour immédiat dans le navigateur. Consultez le [des ressources supplémentaires](#Additional_Resources) section ultérieurement pour plus d’informations.

## <a name="restoring-form-values-after-postbacks"></a>Restauration des valeurs de formulaire après les publications (postback)

Lorsque vous avez testé la page dans la section précédente, vous avez peut-être remarqué que si une erreur de validation, tout ce dont vous avez entré (pas seulement les données non valides) a disparu, et que vous deviez entrer à nouveau les valeurs pour tous les champs. Ceci illustre un point important : lorsque vous soumettez une page, le traiter, puis à nouveau pour restituer la page, la page est recréée à partir de zéro. Comme vous l’avez vu, cela signifie que toutes les valeurs qui étaient dans la page lorsqu’il a été soumis sont perdues.

Vous pouvez résoudre ce problème, toutefois. Vous avez accès aux valeurs qui ont été envoyés (dans le `Request.Form` de l’objet, donc vous pouvez remplir ces valeurs dans les champs de formulaire lorsque la page est rendue.

1. Dans le *Form.cshtml* du fichier, remplacez le `value` attributs de la `<input>` éléments à l’aide de la `value` attribut. : 

    [!code-cshtml[Main](4-working-with-forms/samples/sample5.cshtml?highlight=13,19,25)]

    Le `value` attribut de la `<input>` éléments a été défini dynamiquement lire la valeur de champ de la `Request.Form` objet. La première fois que la page est demandée, les valeurs dans le `Request.Form` objet sont toutes vides. Cela convient, car cette façon le formulaire est vide.
2. Lancement de la page dans votre navigateur, renseignez les champs de formulaire ou les laisser vierge, puis cliquez sur **Submit**. Une page qui affiche les valeurs saisies s’affiche.

    ![forms-5](4-working-with-forms/_static/image5.jpg)

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

- [1 001 façons pour obtenir une entrée à partir des utilisateurs Web](https://msdn.microsoft.com/library/ms971057.aspx)
- [L’utilisation de formulaires et le traitement de l’entrée d’utilisateur](https://msdn.microsoft.com/library/ms525182(VS.90).aspx)
- [Validation des entrées utilisateur dans les sites ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=253002)
- [À l’aide de la saisie semi-automatique dans les formulaires HTML](https://msdn.microsoft.com/library/ms533032(VS.85).aspx)
