---
uid: mvc/overview/older-versions-1/security/preventing-javascript-injection-attacks-cs
title: "Prévention des attaques d’injection de code JavaScript (c#) | Documents Microsoft"
author: StephenWalther
description: "Empêcher les attaques par Injection de JavaScript et les attaques de script entre sites pour vous. Dans ce didacticiel, Stephen Walther explique comment vous pouvez facilement de..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/19/2008
ms.topic: article
ms.assetid: d0136da6-81a4-4815-b002-baa84744c09e
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/security/preventing-javascript-injection-attacks-cs
msc.type: authoredcontent
ms.openlocfilehash: 67f53162cb1bb0771d632ba7a3f5960db00e2744
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="preventing-javascript-injection-attacks-c"></a>Prévention des attaques d’injection de code JavaScript (c#)
====================
par [Stephen Walther](https://github.com/StephenWalther)

[Télécharger le PDF](http://download.microsoft.com/download/8/4/8/84843d8d-1575-426c-bcb5-9d0c42e51416/ASPNET_MVC_Tutorial_06_CS.pdf)

> Empêcher les attaques par Injection de JavaScript et les attaques de script entre sites pour vous. Dans ce didacticiel, Stephen Walther explique comment vous pouvez anéantir facilement ces types d’attaques par le codage de votre contenu HTML.


L’objectif de ce didacticiel est d’expliquer comment vous pouvez empêcher les attaques par injection de JavaScript dans vos applications ASP.NET MVC. Ce didacticiel décrit deux approches pour la protection de votre site Web contre une attaque par injection de JavaScript. Vous apprenez également à empêcher les attaques par injection de JavaScript en codant les données que vous affichez. Vous apprenez également à empêcher les attaques par injection de JavaScript en codant les données que vous acceptez.

## <a name="what-is-a-javascript-injection-attack"></a>Qu’est une attaque par Injection de JavaScript ?

Chaque fois que vous acceptez l’entrée d’utilisateur et réaffichez l’entrée d’utilisateur, vous ouvrez votre site Web à des attaques par injection de code JavaScript. Examinons une application concrète qui est ouverte aux attaques par injection de JavaScript.

Imaginez que vous avez créé un site Web des commentaires client (voir Figure 1). Les clients peuvent visiter le site Web et entrer des commentaires sur leur expérience à l’aide de vos produits. Lorsqu’un client soumet leurs commentaires, les commentaires s’affiche de nouveau sur la page de commentaires.


[![Site Web des commentaires client](preventing-javascript-injection-attacks-cs/_static/image2.png)](preventing-javascript-injection-attacks-cs/_static/image1.png)

**Figure 01**: site Web des commentaires client ([cliquez pour afficher l’image en taille réelle](preventing-javascript-injection-attacks-cs/_static/image3.png))


Le site Web des commentaires client utilise le `controller` dans la liste 1. Cela `controller` contient deux actions nommées `Index()` et `Create()`.

**La liste 1 :`HomeController.cs`**

[!code-csharp[Main](preventing-javascript-injection-attacks-cs/samples/sample1.cs)]

Le `Index()` méthode affiche le `Index` vue. Cette méthode passe tous les commentaires client précédente à la `Index` vue en récupérant des commentaires à partir de la base de données (à l’aide d’une requête LINQ to SQL).

Le `Create()` méthode crée un nouvel élément de commentaires et l’ajoute à la base de données. Le message que le client entre dans le formulaire est passé à la `Create()` méthode dans le paramètre de message. Un élément de commentaires est créé et le message est affecté à l’élément de commentaires `Message` propriété. L’élément de commentaires est soumis à la base de données avec le `DataContext.SubmitChanges()` appel de méthode. Enfin, l’utilisateur est redirigé vers la `Index` vue où tous les commentaires sont affichées.

Le `Index` affichage est contenu dans la liste 2.

**Liste 2 :`Index.aspx`**

[!code-aspx[Main](preventing-javascript-injection-attacks-cs/samples/sample2.aspx)]

Le `Index` affichage comporte deux sections. La section supérieure contient le formulaire de commentaires client réel. La section inférieure contient un For... Chaque boucle qui effectue une itération sur tous les éléments de commentaires client précédente et affiche les propriétés EntryDate et un Message pour chaque élément de commentaires.

Le site Web des commentaires client est un site Web simple. Malheureusement, le site Web est ouvert aux attaques par injection de JavaScript.

Imaginez que vous entrez le texte suivant dans le formulaire de commentaires client :

[!code-html[Main](preventing-javascript-injection-attacks-cs/samples/sample3.html)]

Ce texte représente un script JavaScript qui affiche une boîte de message d’alerte. Une fois que quelqu'un envoie ce script les commentaires écran, le message *Boo !* s’affiche chaque fois que tout le monde visite le site Web des commentaires client dans le futur (voir Figure 2).


[![Injection de code JavaScript](preventing-javascript-injection-attacks-cs/_static/image5.png)](preventing-javascript-injection-attacks-cs/_static/image4.png)

**Figure 02**: injection de code JavaScript ([cliquez pour afficher l’image en taille réelle](preventing-javascript-injection-attacks-cs/_static/image6.png))


À présent, votre réponse initiale à des attaques par injection de code JavaScript peut être apathie. Vous pouvez penser que les attaques par injection de JavaScript sont simplement un type de *dégradation* attaque. Vous pensez que personne ne peut rien réellement malveillant en validant une attaque par injection de JavaScript.

Malheureusement, un pirate informatique qui ont vraiment, vraiment malveillant choses par injection de JavaScript dans un site Web. Vous pouvez utiliser une attaque par injection de JavaScript pour effectuer une attaque de script entre sites (XSS). Dans une attaque de script entre sites, vous dérober des informations confidentielles et envoyez les informations à un autre site Web.

Par exemple, un pirate peut utiliser une attaque par injection de JavaScript pour dérober des données les valeurs des cookies de navigateur à partir d’autres utilisateurs. Si des informations sensibles, telles que les mots de passe, les numéros de carte de crédit ou les numéros de sécurité sociale – sont stockées dans les cookies de navigateur, un pirate peut utiliser une attaque par injection de JavaScript pour dérober des données ces informations. Ou, si un utilisateur entre des informations sensibles dans un champ de formulaire contenu dans une page qui a été compromise avec une attaque de JavaScript, puis le pirate peut utiliser JavaScript injecté pour extraire les données de formulaire et l’envoyer à un autre site Web.

*Soyez pris peur*. Prendre sérieusement des attaques par injection de JavaScript et protéger les informations confidentielles de l’utilisateur. Dans les deux sections suivantes, aborder deux techniques que vous pouvez utiliser pour protéger vos applications ASP.NET MVC contre les attaques d’injection de code JavaScript.

## <a name="approach-1-html-encode-in-the-view"></a>Méthode #1 : Encoder en HTML dans la vue

Une méthode simple pour empêcher les attaques par injection de code JavaScript est au format HTML encoder toutes les données entrées par les utilisateurs du site Web lorsque vous affichez de nouveau les données dans une vue. La mise à jour `Index` vue dans la liste 3 suit cette approche.

**La liste 3 – `Index.aspx` (codé en HTML)**

[!code-aspx[Main](preventing-javascript-injection-attacks-cs/samples/sample4.aspx)]

Notez que la valeur de `feedback.Message` est encodée en HTML avant la valeur est affichée avec le code suivant :

[!code-aspx[Main](preventing-javascript-injection-attacks-cs/samples/sample5.aspx)]

Quelles en moyenne au format HTML encoder une chaîne ? Lorsque vous HTML encodez une chaîne, dangereuses caractères tels que `<` et `>` sont remplacés par des références d’entité HTML comme `&lt;` et `&gt;`. Par conséquent, lorsque la chaîne `<script>alert("Boo!")</script>` est au format HTML encodé, il est converti en `&lt;script&gt;alert(&quot;Boo!&quot;)&lt;/script&gt;`. La chaîne encodée n’exécute plus comme un script JavaScript lorsqu’il est interprété par un navigateur. Au lieu de cela, vous obtenez la page sans incidence dans la Figure 3.


[![Attaque de JavaScript invalidée](preventing-javascript-injection-attacks-cs/_static/image8.png)](preventing-javascript-injection-attacks-cs/_static/image7.png)

**Figure 03**: invalidée JavaScript attaque ([cliquez pour afficher l’image en taille réelle](preventing-javascript-injection-attacks-cs/_static/image9.png))


Notez que dans le `Index` afficher dans la liste 3 uniquement la valeur de `feedback.Message` est encodé. La valeur de `feedback.EntryDate` n’est pas encodé. Vous devez uniquement encoder les données entrées par un utilisateur. Étant donné que la valeur de EntryDate a été générée dans le contrôleur, vous ne devez au format HTML coder cette valeur.

## <a name="approach-2-html-encode-in-the-controller"></a>Méthode #2 : Encoder en HTML dans le contrôleur

Au lieu de code HTML de l’encodage des données lorsque vous affichez les données dans une vue, vous pouvez HTML encoder les données juste avant d’envoyer les données à la base de données. Cette seconde approche est effectuée dans le cas de la `controller` sur la liste 4.

**La liste 4 – `HomeController.cs` (codé en HTML)**

[!code-csharp[Main](preventing-javascript-injection-attacks-cs/samples/sample6.cs)]

Notez que la valeur du Message est encodée en HTML avant la valeur est soumise à la base de données dans le `Create()` action. Lorsque le Message s’affiche de nouveau dans la vue, le Message est encodé au format HTML et n’importe quel JavaScript injecté dans le Message n’est pas exécutée.

En règle générale, vous devez pencher pour la première approche décrite dans ce didacticiel sur cette seconde approche. Le problème avec cette seconde approche est que vous vous retrouvez avec des données encodées en HTML dans votre base de données. En d’autres termes, votre base de données est modifications avec les caractères qui amusantes.

Pourquoi est-ce incorrect ? Si vous devez afficher les données de la base de données dans l’autre chose qu’une page web, vous devez les problèmes. Par exemple, vous pouvez afficher n’est plus facilement les données dans une application Windows Forms.

## <a name="summary"></a>Résumé

L’objectif de ce didacticiel a été à vous font peur sur la perspective d’une attaque par injection de JavaScript. Ce didacticiel décrit deux approches pour la protection de vos applications ASP.NET MVC contre les attaques par injection de JavaScript : vous pouvez soit HTML Encoder soumise par un utilisateur les données dans la vue, ou vous peuvent HTML Encoder soumise par un utilisateur les données dans le contrôleur.

>[!div class="step-by-step"]
[Précédent](authenticating-users-with-windows-authentication-cs.md)
[Suivant](authenticating-users-with-forms-authentication-vb.md)
