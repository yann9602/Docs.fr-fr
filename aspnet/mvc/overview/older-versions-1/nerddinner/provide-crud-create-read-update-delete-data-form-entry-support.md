---
uid: mvc/overview/older-versions-1/nerddinner/provide-crud-create-read-update-delete-data-form-entry-support
title: "Fournir CRUD (créer, lire, mettre à jour, supprimer) prise en charge de l’entrée de données de formulaires | Documents Microsoft"
author: microsoft
description: "Étape 5 montre comment prendre notre classe DinnersController plus par prise en charge de l’activer pour la modification, la création et la suppression préparés avec lui aussi."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: bbb976e5-6150-4283-a374-c22fbafe29f5
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/provide-crud-create-read-update-delete-data-form-entry-support
msc.type: authoredcontent
ms.openlocfilehash: 5a314a1761527d8a2273166a743e3deac012a557
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="provide-crud-create-read-update-delete-data-form-entry-support"></a>Fournir CRUD (créer, lire, mettre à jour, supprimer) prise en charge de l’entrée de données de formulaires
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 5 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 5 montre comment prendre notre classe DinnersController plus par prise en charge de l’activer pour la modification, la création et la suppression préparés avec lui aussi.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-5-create-update-delete-form-scenarios"></a>NerdDinner étape 5 : Créer, mettre à jour, supprimer des scénarios de formulaire

Nous avons introduit les contrôleurs et les vues et expliqué comment les utiliser pour implémenter une expérience/détails de l’annonce pour préparés sur le site. L’étape suivante sera à notre classe DinnersController davantage et activer la prise en charge de la modification, la création et la suppression préparés avec lui aussi.

### <a name="urls-handled-by-dinnerscontroller"></a>URL gérées par DinnersController

Nous avons ajouté précédemment des méthodes d’action à DinnersController qui implémenté la prise en charge pour les deux URL : */Dinners* et */Dinners/détails / [id]*.

| **URL** | **VERBE** | **Fonction** |
| --- | --- | --- |
| */Dinners/* | GET | Afficher une liste HTML de préparés à venir. |
| */Dinners/détails / [id]* | GET | Afficher des détails sur un dîner spécifique. |

Nous allons maintenant ajouter des méthodes d’action pour implémenter les trois URL supplémentaires : */Dinners/modification / [id], préparés/Create,*et*/Dinners/Delete / [id]*. Ces URL activer la prise en charge pour l’éditions préparés existants, nouveau préparés de création et la suppression préparés.

Nous prendrons en charge les interactions de verbe HTTP GET et HTTP POST avec ces nouvelles URL. Demandes HTTP GET pour ces URL affiche la vue HTML initiale des données (un formulaire rempli avec les données Dinner « modifier » dans le cas d’un formulaire vide dans le cas de « créer » et un écran de confirmation de suppression dans le cas de « supprimer »). Les requêtes HTTP POST à ces URL seront enregistrement ou de suppression/mise à jour les données dîner dans notre DinnerRepository (et à partir de là, la base de données).

| **URL** | **VERBE** | **Fonction** |
| --- | --- | --- |
| */Dinners/modification / [id]* | GET | Afficher un formulaire HTML modifiable rempli avec des données de dîner. |
| PUBLIER | Enregistrer les modifications de formulaire pour un dîner particulier à la base de données. |
| */ Préparés/créer* | GET | Afficher un formulaire HTML vide qui permet aux utilisateurs de définir de nouveaux préparés. |
| PUBLIER | Créer un nouveau dîner et l’enregistrer dans la base de données. |
| */Dinners/delete / [id]* | GET | Afficher écran de confirmation de suppression. |
| PUBLIER | Supprime le dîner spécifié à partir de la base de données. |

### <a name="edit-support"></a>Modifier la prise en charge

Nous allons commencer en mettant en œuvre le scénario « modifier ».

#### <a name="the-http-get-edit-action-method"></a>La méthode d’Action HTTP-GET Edition

Nous allons commencer en implémentant le comportement de « GET » HTTP de notre méthode d’action de modification. Cette méthode sera appelée lorsque le */Dinners/modification / [id]* URL est demandée. Notre implémentation doit ressembler à :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample1.cs)]

Le code ci-dessus utilise la DinnerRepository pour récupérer un objet dîner. Il restitue ensuite un modèle d’affichage à l’aide de l’objet dîner. Étant donné que nous n’avons pas passé explicitement un nom de modèle pour le *View()* méthode d’assistance, il utilisera le chemin d’accès de la valeur par défaut basé sur une convention pour résoudre le modèle d’affichage : /Views/Dinners/Edit.aspx.

Nous allons maintenant créer ce modèle de vue. Pour cela, nous sera en double-cliquant sur dans la méthode de modification et en sélectionnant la commande de menu « Ajouter une vue » contexte :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image1.png)

Dans la boîte de dialogue « Ajouter une vue » nous allons indique que nous passent un objet dîner à notre modèle de vue en tant que son modèle, choisissez à auto-scaffold un modèle « Modifier » :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image2.png)

Lorsque vous cliquez sur le bouton « Ajouter », Visual Studio ajoute un nouveau fichier de modèle de vue « Edit.aspx » pour nous dans le répertoire « \Views\Dinners ». Il ouvre également le nouveau modèle de vue « Edit.aspx » dans l’éditeur de code – remplie avec une « Modifier » scaffold implémentation initiale, comme ci-dessous :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image3.png)

Nous allons apporter quelques modifications à la vue par défaut « modifier » généré et mettre à jour le modèle de vue de modification pour que le contenu suivant (ce qui supprime certaines des propriétés que nous ne souhaitez pas exposer) :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample2.aspx)]

Quand nous exécutons l’application et demande le *« / préparés/modifier/1 »* URL, nous allons voir la page suivante :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image4.png)

Le balisage HTML généré par notre vue se présente comme ci-dessous. C’est le code HTML standard – avec un &lt;formulaire&gt; élément qui effectue une requête HTTP POST à la */Dinners/Edit/1* URL lors de la « enregistrer » &lt;type d’entrée = « submit » /&gt; bouton est enfoncé. Un HTML &lt;input type = « text » /&gt; élément a été sortie pour chaque propriété modifiable :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image5.png)

#### <a name="htmlbeginform-and-htmltextbox-html-helper-methods"></a>Html.BeginForm() et méthodes de programme d’assistance Html Html.TextBox()

Notre modèle de vue « Edit.aspx » à l’aide de plusieurs méthodes de « Programme d’assistance Html » : Html.ValidationSummary(), Html.BeginForm(), Html.TextBox() et Html.ValidationMessage(). En plus de générer un balisage HTML pour nous, ces méthodes d’assistance fournissent la gestion des erreurs intégrée et la validation prend en charge.

##### <a name="htmlbeginform-helper-method"></a>Méthode d’assistance de Html.BeginForm()

La méthode d’assistance Html.BeginForm() est sortie HTML &lt;formulaire&gt; élément notre balisage. Dans notre modèle de vue Edit.aspx, vous remarquerez que nous appliquons une instruction « using » lors de l’utilisation de cette méthode c#. L’accolade ouvrante indique le début de la &lt;formulaire&gt; le contenu et l’accolade fermante est ce qui indique la fin de la &lt;/écran&gt; élément :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample3.cs)]

Vous pouvez également, si vous trouvez l’instruction « using » approche non naturelles pour un scénario à ceci, vous pouvez utiliser une combinaison de Html.BeginForm() et Html.EndForm() (qui produit la même chose) :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample4.aspx)]

Appel de Html.BeginForm() sans aucun paramètre entraînera vers la sortie d’un élément de formulaire qui exécute une requête HTTP-POST à l’URL de la demande actuelle. Autrement dit pourquoi notre modifier la vue génère une  *&lt;action de formulaire = méthode « / préparés/modifier/1 » = « post »&gt;*  élément. Nous avons également transmis paramètres explicites à Html.BeginForm() si nous voulons à une autre URL.

##### <a name="htmltextbox-helper-method"></a>Méthode d’assistance de Html.TextBox()

Notre vue Edit.aspx utilise la méthode d’assistance Html.TextBox() pour générer &lt;input type = « text » /&gt; éléments :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample5.aspx)]

La méthode Html.TextBox() ci-dessus prend un seul paramètre, qui est utilisé pour spécifier les attributs id/nom de la &lt;d’entrée de type = « text » /&gt; élément à la sortie, ainsi que la propriété du modèle pour renseigner la valeur de la zone de texte à partir de. Par exemple, l’objet de dîner nous passées à la vue d’édition avait une valeur de propriété « Title » de « .NET perspectives », et donc notre méthode Html.TextBox("Title") appeler sortie :  *&lt;id d’entrée = « Title » name = « Title » type = valeur de « texte » = « .NET perspectives » /&gt;* .

Ou bien, nous pouvons utiliser le premier paramètre Html.TextBox() pour spécifier l’id/nom de l’élément, puis passez explicitement la valeur à utiliser en tant que second paramètre :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample6.aspx)]

Souvent, nous vous souhaitez effectuer la mise en forme personnalisée de la valeur de sortie. La méthode statique String.Format () intégrée à .NET est utile pour ces scénarios. Notre modèle de vue Edit.aspx est à l’aide de ce à mettre en forme la valeur de date événement (qui est de type DateTime) afin qu’il n’affiche pas secondes de la durée :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample7.aspx)]

Un troisième paramètre à Html.TextBox() peut éventuellement servir pour générer des attributs HTML supplémentaires. L’extrait de code ci-dessous montre comment effectuer le rendu d’une taille supplémentaire = attribut « 30 » et une classe = attribut de « mycssclass » sur le &lt;d’entrée de type = « text » /&gt; élément. Notez la façon dont nous sommes échappement le nom de l’attribut de classe à l’aide un «@" character because "classe » est un mot clé réservé dans c# :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample8.aspx)]

#### <a name="implementing-the-http-post-edit-action-method"></a>Implémentation de la méthode d’Action HTTP-POST Edition

Nous disposons désormais la version HTTP-GET de notre méthode d’action de modification implémentée. Quand un utilisateur demande la */Dinners/Edit/1* URL qu’ils reçoivent une page HTML comme suit :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image6.png)

En appuyant sur le bouton « Enregistrer » provoque une publication de formulaire pour le */Dinners/Edit/1* URL, et envoie le code HTML &lt;d’entrée&gt; valeurs à l’aide du verbe HTTP POST de formulaire. Nous allons maintenant implémenter le comportement de HTTP POST de notre méthode d’action Modifier – qui gère l’enregistrement le dîner.

Nous allons commencer en ajoutant une méthode surchargée d’action « Modifier » à notre DinnersController que qu’elle comporte un attribut « AcceptVerbs » qui indique qu’il gère les scénarios HTTP POST :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample9.cs)]

Lorsque l’attribut [AcceptVerbs] est appliqué aux méthodes d’action surchargé, ASP.NET MVC gère automatiquement la distribution des demandes à la méthode d’action appropriée selon le verbe HTTP entrante. Des demandes HTTP POST à */Dinners/modification / [id]* URL seront transmise à la méthode Edit ci-dessus, tandis que les toutes les autres requêtes de verbe HTTP à */Dinners/modification / [id]*URL passera à la première méthode de modification, nous avons implémenté (qui a pas avoir un attribut [AcceptVerbs]).

| **Rubrique de côté : Différencier pourquoi via les verbes HTTP ?** |
| --- |
| Vous vous demandez peut-être – pourquoi nous sont à l’aide d’une URL unique et de différenciation son comportement via le verbe HTTP ? Pourquoi ne pas simplement ont des deux URL distinctes gère le chargement et l’enregistrement des modifications ? Par exemple : /Dinners/modification / [id] pour afficher le formulaire initial et /Dinners/Save / [id] pour gérer la publication de formulaire pour l’enregistrer ? L’inconvénient avec publication deux URL distincts est que dans les cas où nous valider /Dinners/Save/2 et que vous aurez besoin de réafficher le formulaire HTML en raison d’une erreur d’entrée, l’utilisateur final se retrouve ayant l’URL préparés/Save/2 dans la barre d’adresse de son navigateur (comme c’était l’URL de la publication du formulaire sur). Si l’utilisateur final insère un signet sur cette page redisplayed à leur liste de Favoris de navigateur, ou copie-colle l’URL et envoie par e-mail à une fonction friend, ils se retrouve l’enregistrement d’une URL qui ne fonctionne pas dans le futur (étant donné que cette URL dépend des valeurs de publication). En exposant une URL unique (telles que : /Dinners/Edit/[id]) et ce qui différencie le traitement de celui-ci par le verbe HTTP, il est sécurisé pour les utilisateurs finaux à la page de modification de signet et/ou envoyer l’URL à d’autres personnes. |

#### <a name="retrieving-form-post-values"></a>La récupération des valeurs de publication de formulaire

Il existe plusieurs façons nous pouvons accéder à validation des paramètres de formulaire dans notre méthode de « Modifier » HTTP POST. Une approche simple consiste à utiliser uniquement la propriété sur la classe de base du contrôleur pour accéder à la collection de formulaires et récupérer directement des valeurs publiées :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample10.cs)]

L’approche ci-dessus est un peu verbose, cependant, en particulier une fois que nous ajoutons une logique de gestion des erreurs.

Une meilleure approche de ce scénario consiste à tirer parti de la fonction intégrée *UpdateModel()* méthode d’assistance sur la classe de base du contrôleur. Il prend en charge la mise à jour les propriétés d’un objet que nous passons à l’aide des paramètres entrants de l’écran. Il utilise la réflexion pour déterminer les noms de propriété sur l’objet et puis convertit automatiquement et leur affecte des valeurs basées sur les valeurs d’entrée soumis par le client.

Nous pouvons utiliser la méthode UpdateModel() pour simplifier notre HTTP-POST modifier l’Action à l’aide de ce code :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample11.cs)]

Nous pouvons maintenant visiter le */Dinners/Edit/1* URL et modifier le titre de notre dîner :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image7.png)

Lorsque vous cliquez sur le bouton « Enregistrer », nous allons effectuer une publication de formulaire à notre action de modification et les valeurs mises à jour sont rendues persistantes dans la base de données. Nous sera redirigées vers l’URL de détails pour le dîner (qui affiche les valeurs qui vient d’être enregistrés) :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image8.png)

#### <a name="handling-edit-errors"></a>Gestion des erreurs de modification

Notre actuel fonctionne de mise en œuvre de HTTP-POST correctement – sauf quand il existe des erreurs.

Lorsqu’un utilisateur apporte une erreur de modification d’un formulaire, nous devons vous assurer que le formulaire s’affiche de nouveau avec un message d’erreur informatif qui guide les pour y remédier. Cela inclut les cas où un utilisateur final billets d’entrée incorrecte (par exemple : une chaîne de date incorrecte), ainsi que les cas où le format d’entrée est valide, mais il existe une violation de règle d’entreprise. Lorsque les erreurs se produisent que le formulaire doit conserver les données d’entrée à l’origine entrées afin qu’ils n’ont pas recharger leurs modifications manuellement par l’utilisateur. Ce processus doit se répéter autant de fois que nécessaire jusqu'à ce que le formulaire se termine avec succès.

ASP.NET MVC inclut des fonctionnalités intéressantes intégrées qui facilitent la gestion des erreurs et actualiser de formulaire. Pour afficher ces fonctionnalités en action nous allons mettre à jour notre méthode d’action de modification avec le code suivant :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample12.cs)]

Le code ci-dessus est similaire à notre implémentation précédente, sauf que nous sommes maintenant encapsulant un bloc de gestion des erreurs try/catch autour de notre travail. Si une exception se produit lors de l’appel UpdateModel() ou essayez et d’enregistrer le DinnerRepository (ce qui déclenchera une exception si l’objet Dinner que nous examinons la tentative d’enregistrement n’est pas valide en raison d’une violation de règle au sein de notre modèle), notre bloc de gestion d’erreur catch sera Exécutez. Qu’il contient, nous une boucle sur toutes les violations de règles qui existent dans l’objet dîner et les ajouter à un objet ModelState (dont nous parlerons peu de temps). Nous réaffichez la vue.

Pour voir cette utilisation nous allons exécuter à nouveau l’application, modifiez un dîner et pour donner un titre, une date d’événement de « BOGUS » et d’utiliser un numéro de téléphone du Royaume-Uni avec la valeur pays États-Unis. Lorsque vous appuyez sur le bouton « Enregistrer » notre méthode de modification de HTTP POST ne sera pas en mesure d’enregistrer le dîner (car il existe des erreurs) et s’affichera de nouveau le formulaire :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image9.png)

Notre application possède une expérience de l’erreur correcte. Les éléments de texte avec les entrées non valides sont mises en surbrillance en rouge, et les messages d’erreur de validation sont affichés à l’utilisateur final à leur sujet. Le formulaire conserve également les données d’entrée que l’utilisateur a entré à l’origine, afin qu’ils n’ont pas de rechargement quoi que ce soit.

Comment procéder, vous vous demandez peut-être, cela se produisait ? Comment les zones de texte Titre et Date événement ContactPhone se mettre en surbrillance en rouge et connaître les valeurs d’utilisateur entré à l’origine de la sortie ? Et comment des messages d’erreur s’affichent dans la liste en haut ? La bonne nouvelle est que cela n’a pas à se produire par magie - il a été plutôt que nous avons utilisé certaines des fonctionnalités ASP.NET MVC intégrées qui facilitent erreur et validation d’entrée des scénarios de gestion.

#### <a name="understanding-modelstate-and-the-validation-html-helper-methods"></a>Présentation des ModelState et les méthodes de programme d’assistance HTML de Validation

Classes de contrôleur ont une collection de propriétés « ModelState » qui offre un moyen d’indiquer que des erreurs existent avec un objet de modèle passé à une vue. Entrées d’erreur dans la collection ModelState identifient le nom de la propriété de modèle avec le problème (par exemple : « Title », « Date événement » ou « ContactPhone ») et autoriser un message d’erreur de convivial être spécifié (par exemple : « Le titre est requis »).

Le *UpdateModel()* méthode d’assistance remplit automatiquement la collection ModelState lorsqu’il rencontre des erreurs lors de la tentative affecter des valeurs de formulaire à des propriétés sur l’objet de modèle. Par exemple, la propriété de date événement l’objet de notre dîner est de type DateTime. Lorsque la méthode UpdateModel() n’a pas pu lui affecter la valeur de chaîne « BOGUS » dans le scénario ci-dessus, la méthode UpdateModel() ajouté une entrée à la collection ModelState indiquant une erreur d’assignation s’est produite avec cette propriété.

Les développeurs peuvent également écrire du code pour ajouter explicitement les entrées d’erreur dans la collection ModelState que nous faisons ci-dessous dans notre « catch » erreur gestion bloc, le remplissage de la collection ModelState avec des entrées selon les Violations de règle active dans le Objet Dinner :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample13.cs)]

#### <a name="html-helper-integration-with-modelstate"></a>Intégration d’application d’assistance HTML avec ModelState

Des méthodes d’assistance HTML - comme Html.TextBox() - vérifient dans la collection ModelState lors du rendu de sortie. Si une erreur pour l’élément existe, leur rendu la valeur entrée par l’utilisateur et une classe d’erreurs CSS.

Par exemple, dans notre « Modifier » de la vue, nous utilisons la méthode d’assistance Html.TextBox() pour afficher la date d’événement de notre objet Dinner :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample14.aspx)]

Lorsque la vue a été rendue dans le scénario de l’erreur, la méthode Html.TextBox() vérifié la collection ModelState pour voir si les erreurs associées à la propriété de date « événement » de notre objet dîner. Lorsqu’il détermine qu’une erreur s’est produite il rendu de l’entrée d’utilisateur envoyé (« BOGUS ») comme valeur et ajouter une classe d’erreurs css à le &lt;d’entrée de type = « textbox » /&gt; balisage il généré :

[!code-html[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample15.html)]

Vous pouvez personnaliser l’apparence de la classe d’erreur css toutefois vous souhaitez une apparence. La classe d’erreur CSS – « entrée-erreur de validation » – par défaut est définie dans le *\content\site.css* feuille de style et de recherche comme ci-dessous :

[!code-css[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample16.css)]

Cette règle CSS est la cause de nos éléments d’entrée non valides être mis en surbrillance, comme ci-dessous :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image10.png)

##### <a name="htmlvalidationmessage-helper-method"></a>Méthode d’assistance de Html.ValidationMessage()

La méthode d’assistance Html.ValidationMessage() peut être utilisée pour générer le message d’erreur ModelState associé à une propriété de modèle particulier :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample17.aspx)]

Le code ci-dessus génère :  *&lt;span classe = « erreur de validation de champ »&gt; la valeur « BOGUS » n’est pas valide&lt;/span&gt;*

La méthode d’assistance Html.ValidationMessage() prend également en charge un deuxième paramètre qui permet aux développeurs de substituer le message texte d’erreur qui s’affiche :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample18.aspx)]

Le code ci-dessus génère :  *&lt;span classe = « erreur de validation de champ »&gt;\*&lt;/span&gt;*au lieu du texte d’erreur par défaut lorsqu’une erreur est présente pour le Propriété de date événement.

##### <a name="htmlvalidationsummary-helper-method"></a>Méthode d’assistance de Html.ValidationSummary()

La méthode d’assistance Html.ValidationSummary() peut être utilisée pour afficher un message d’erreur récapitulatif, accompagné d’un &lt;ul&gt;&lt;li /&gt;&lt;/ul&gt; la liste des erreurs détaillées de tous les messages dans le Collection de ModelState :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image11.png)

La méthode d’assistance Html.ValidationSummary() prend un paramètre de chaîne facultatif – qui définit un message d’erreur récapitulatif à afficher au-dessus de la liste d’erreurs détaillées :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample19.aspx)]

Vous pouvez éventuellement utiliser CSS pour remplacer l’aspect de la liste d’erreurs.

#### <a name="using-a-addruleviolations-helper-method"></a>À l’aide d’une méthode d’assistance de AddRuleViolations

Notre implémentation HTTP-POST modifier initiale utilisé une instruction foreach dans son bloc catch pour une boucle sur les Violations de règles de l’objet dîner et ajoutez-les à la collection de ModelState du contrôleur :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample20.cs)]

Vous pouvez rendre ce code de classe au projet de NerdDinner un nettoyeur peu en ajoutant un « ControllerHelpers » et implémenter une méthode d’extension « AddRuleViolations » qu’il contient qui ajoute une méthode d’assistance à la classe d’ASP.NET MVC ModelStateDictionary. Cette méthode d’extension peut encapsuler la logique nécessaire pour remplir le ModelStateDictionary avec une liste d’erreurs de RuleViolation :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample21.cs)]

Nous pouvons ensuite mettre à jour notre méthode d’action HTTP-POST modifier pour utiliser cette méthode d’extension pour remplir la collection ModelState avec notre dîner des Violations de règle.

#### <a name="complete-edit-action-method-implementations"></a>Terminer les implémentations de méthode d’Action Edition

Le code suivant implémente l’ensemble de la logique nécessaire pour notre scénario de modification du contrôleur :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample22.cs)]

Agréable dans notre implémentation de la modification est que notre modèle de vue ni de notre classe de contrôleur dispose d’aucune information sur la validation spécifique ou les règles d’entreprise appliquées par notre modèle de Dinner. Nous peut ajouter des règles supplémentaires à notre modèle dans le futur et n’ont pas à apporter des modifications de code à notre contrôleur ou une vue dans l’ordre pour pouvoir être pris en charge. Ainsi, nous permet d’évoluer facilement nos exigences des applications à l’avenir avec un minimum de modifications du code.

### <a name="create-support"></a>Prise en charge

Nous avons fini d’implémenter le comportement « Modifier » de notre classe DinnersController. Nous allons maintenant passer à implémenter la prise en charge « Créer » sur elle, ce qui permettra aux utilisateurs d’ajouter de nouveau préparés.

#### <a name="the-http-get-create-action-method"></a>HTTP-GET créer la méthode d’Action

Nous allons commencer en implémentant le comportement de « GET » HTTP de notre créer la méthode d’action. Cette méthode sera appelée lorsqu’un utilisateur visite le *préparés/créer* URL. Notre implémentation ressemble à :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample23.cs)]

Le code ci-dessus crée un nouvel objet dîner et affecte sa propriété Date événement soit une semaine à venir. Il restitue ensuite une vue qui est basée sur le nouvel objet dîner. Étant donné que nous n’avons pas passé explicitement un nom pour le *View()* méthode d’assistance, il utilisera le chemin d’accès de la valeur par défaut basé sur une convention pour résoudre le modèle d’affichage : /Views/Dinners/Create.aspx.

Nous allons maintenant créer ce modèle de vue. Pour cela, nous pouvons en double-cliquant sur dans la méthode d’action de création et en sélectionnant la commande de menu contextuel « Ajouter une vue ». Dans la boîte de dialogue « Ajouter une vue » nous allons indique que nous passent un objet dîner au modèle d’affichage, choisissez à auto-scaffold un modèle « Créer » :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image12.png)

Lorsque vous cliquez sur le bouton « Ajouter », Visual Studio enregistrer une nouvelle vue basée sur la structure « Create.aspx » dans le répertoire « \Views\Dinners » et ouvrez-le dans l’IDE :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image13.png)

Nous allons apporter quelques modifications pour le fichier par défaut « créer » une vue de structure a été généré pour nous et modifiez-le pour se présenter comme ci-dessous :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample24.aspx)]

Et maintenant quand nous exécutons notre accès aux applications et le *« Préparés/créer »* URL dans le navigateur, il doit rendre l’interface utilisateur, comme ci-dessous à partir de notre implémentation d’action de création :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image14.png)

#### <a name="implementing-the-http-post-create-action-method"></a>Implémentation de la requête HTTP-POST créer de méthode d’Action

Nous avons la version HTTP-GET de notre méthode d’action de création implémentée. Lorsqu’un utilisateur clique sur le bouton « Enregistrer » il effectue une publication de formulaire pour le *préparés/créer* URL, et envoie le code HTML &lt;d’entrée&gt; valeurs à l’aide du verbe HTTP POST de formulaire.

Nous allons à présent implémenter le comportement de la requête HTTP POST de notre créer la méthode d’action. Nous allons commencer en ajoutant une méthode d’action « Créer » surchargée pour notre DinnersController que qu’elle comporte un attribut « AcceptVerbs » qui indique qu’il gère les scénarios HTTP POST :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample25.cs)]

Il existe plusieurs façons, nous pouvons accéder à des paramètres de l’écran publié dans notre méthode de « Créer » HTTP-POST est activé.

Une approche consiste à créer un nouvel objet dîner, puis utiliser le *UpdateModel()* méthode d’assistance (comme nous l’avons fait avec l’action de modification) à remplir avec les valeurs de formulaire publiées. Nous pouvons ensuite ajouter à notre DinnerRepository, rendre persistante dans la base de données et rediriger l’utilisateur vers notre action détails pour afficher le dîner nouvellement créé en utilisant le code ci-dessous :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample26.cs)]

Nous pouvons également utiliser une approche où nous avons notre méthode d’action Create() prennent un objet dîner comme paramètre de méthode. ASP.NET MVC sera automatiquement instancier un nouvel objet dîner pour nous, remplir ses propriétés à l’aide des entrées de formulaire, puis passer à la méthode d’action :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample27.cs)]

La méthode d’action ci-dessus vérifie que l’objet dîner a été correctement remplie avec les valeurs de publication de formulaire en vérifiant la propriété ModelState.IsValid. Cette opération retourne la valeur false s’il n’y a d’entrée des problèmes de conversion (par exemple : une chaîne de « BOGUS » pour la propriété de date événement), et si les éventuels problèmes de méthode d’action réaffiche le formulaire.

Si les valeurs d’entrée sont valides, la méthode d’action tentatives ajouter et enregistrer le nouveau dîner à la DinnerRepository. Elle encapsule ce travail dans un bloc try/catch et réaffiche le formulaire s’il existe des violations de règle métier (ce qui provoquent la méthode dinnerRepository.Save() lever une exception).

Pour voir ce comportement gestion des erreurs en action, nous pouvons demander le *préparés/créer* URL et le remplissage des détails sur un nouveau dîner. Entrée incorrecte ou valeurs entraîne le formulaire de création être actualisé avec les erreurs de mise en surbrillance, comme ci-dessous :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image15.png)

Notez la façon dont notre formulaire de création honore exacte même entreprise règles de validation et en tant que notre formulaire de modification. Il s’agit, car ses règles d’entreprise et de validation ont été définies dans le modèle et n’ont pas été incorporés dans l’interface utilisateur ou d’un contrôleur de l’application. Cela signifie que nous pouvons ultérieurement modifier/évoluer notre validation ou des règles d’entreprise dans un même placent et demandez-lui de s’appliquant à notre application. Il nous faudra pas modifier tout code dans une notre modifier ou créer des méthodes d’action pour honorer automatiquement de toutes les nouvelles règles ou les modifications existants.

Lorsque nous corriger les valeurs d’entrée et cliquez sur le bouton « Enregistrer », plus de nos le DinnerRepository réussira et un nouveau dîner sera ajouté à la base de données. Nous sera redirigées vers le */Dinners/détails / [id]* URL où nous s’affiche avec des détails sur le dîner nouvellement créé :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image16.png)

### <a name="delete-support"></a>Supprimer la prise en charge

Nous allons maintenant ajouter la prise en charge de « Supprimer » notre DinnersController.

#### <a name="the-http-get-delete-action-method"></a>La méthode d’Action Delete HTTP-GET

Nous allons commencer en implémentant le comportement HTTP GET de notre méthode d’action delete. Cette méthode est appelée lorsqu’un utilisateur visite le */Dinners/Delete / [id]* URL. Voici l’implémentation :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample28.cs)]

La méthode d’action tente de récupérer le dîner à supprimer. Si le dîner existe, il restitue une vue basée sur l’objet dîner. Si l’objet n’existe pas (ou a déjà été supprimé) il retourne une vue qui restitue le « NotFound » permet d’afficher modèle que nous avons créé précédemment pour notre méthode d’action « Détails ».

Nous pouvons créer le modèle d’affichage « Supprimer » en cliquant au sein de la méthode d’action Delete et en sélectionnant la commande de menu contextuel « Ajouter une vue ». Dans la boîte de dialogue « Ajouter une vue » nous allons indique que nous passent un objet dîner à notre modèle de vue en tant que son modèle, choisissez de créer un modèle vide :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image17.png)

Lorsque vous cliquez sur le bouton « Ajouter », Visual Studio ajoute un nouveau fichier de modèle de vue « Delete.aspx » pour nous dans notre répertoire « \Views\Dinners ». Nous allons ajouter du code HTML et le code pour le modèle pour implémenter un écran de confirmation de suppression comme ci-dessous :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample29.aspx)]

Le code ci-dessus affiche le titre des dîner à supprimer et des sorties un &lt;formulaire&gt; élément qui effectue une publication à l’URL de /Dinners/Delete / [id] si l’utilisateur final clique sur le bouton « Supprimer » qu’il contient.

Quand nous exécutons notre accès aux applications et le *» / préparés/Delete / [id] »* URL pour un dîner valid l’objet restitue l’interface utilisateur comme ci-dessous :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image18.png)

| **Rubrique de côté : Pourquoi nous effectuons une publication ?** |
| --- |
| Vous vous demandez peut-être – pourquoi nous allons examiner l’effort de création d’un &lt;formulaire&gt; dans notre écran de confirmation de suppression ? Pourquoi ne pas utiliser simplement un lien hypertexte standard à lier à une méthode d’action qui effectue l’opération de suppression ? La raison est car nous souhaitons veillez à vous prémunir contre robots et découverte nos URL et à l’origine par inadvertance des données doit être supprimé lorsqu’elles respectent les liens de moteurs de recherche. En fonction de HTTP-GET URL sont considérées comme « sécurisés » pour pouvoir / l’analyse de l’accès, et ils sont censés ne suit ne pas ceux de HTTP-POST. Une bonne règle est de s’assurer que vous placez toujours le destructeur ou des opérations de modification de données derrière les requêtes HTTP-POST. |

#### <a name="implementing-the-http-post-delete-action-method"></a>Implémentation de la méthode d’Action Delete HTTP-POST

Nous disposons de la version de HTTP-GET de notre méthode d’action Delete implémenté qui affiche un écran de confirmation de suppression. Lorsqu’un utilisateur final clique sur le bouton « Supprimer », elle effectue une publication de formulaire pour le */Dinners dîner / [id]* URL.

Nous allons maintenant implémenter le comportement de « POST » HTTP de la méthode d’action delete en utilisant le code ci-dessous :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample30.cs)]

La version HTTP-POST de la méthode d’action Delete tente de récupérer l’objet dîner à supprimer. S’il ne peut pas trouver (car il a déjà été supprimé) il rend notre modèle de « NotFound ». S’il trouve le dîner, il le supprime le DinnerRepository. Il restitue ensuite un modèle de « Deleted ».

Pour implémenter le modèle de « Deleted », nous allons avec le bouton droit dans la méthode d’action et choisissez le menu contextuel « Ajouter une vue ». Nous nommez notre vue « Deleted » et qu’il est un modèle vide (et pas prendre un objet de modèle fortement typé). Nous allons ensuite ajouter du contenu HTML lui :

[!code-aspx[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample31.aspx)]

Et maintenant quand nous exécutons notre accès aux applications et le *» / préparés/Delete / [id] »* URL pour un objet, il doit rendre notre dîner confirmation de la suppression de Dinner valide de l’écran, comme ci-dessous :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image19.png)

Lorsque vous cliquez sur le bouton « Supprimer », elle effectue une requête HTTP POST à la */Dinners/Delete / [id]* URL, ce qui supprime le dîner à partir de notre base de données et afficher de notre modèle d’affichage de « Deleted » :

![](provide-crud-create-read-update-delete-data-form-entry-support/_static/image20.png)

### <a name="model-binding-security"></a>Sécurité de liaison de modèle

Nous avons décrit deux façons différentes d’utiliser les fonctionnalités de liaison de modèle intégrées d’ASP.NET MVC. Le premier à l’aide de la méthode UpdateModel() pour mettre à jour les propriétés sur un objet de modèle existant et le deuxième à l’aide prise en charge de ASP.NET MVC pour passer des objets de modèle dans en tant que paramètres de méthode d’action. Ces deux techniques sont très puissants et extrêmement utiles.

Cette puissance offre également responsabilité. Il est important de toujours être obsessif sur la sécurité lors de l’acceptation d’entrée d’utilisateur, et cela s’applique également lors de la liaison d’objets à l’entrée du formulaire. Veillez à toujours HTML coder toutes les valeurs entrées par l’utilisateur pour éviter les attaques par injection de code HTML et JavaScript et veillez à des attaques par injection SQL (Remarque : nous utilisons LINQ to SQL pour notre application, qui encode automatiquement les paramètres pour empêcher ces types d’attaques). Vous devez jamais compter sur la validation côté client autonome et toujours utiliser la validation côté serveur pour vous prémunir contre les pirates qui tentent d’envoyer des valeurs factices.

Un élément de sécurité supplémentaires pour vous assurer que vous pensez lors de l’utilisation d’ASP.NET MVC, les fonctionnalités de liaison est l’étendue des objets que vous établissez une liaison. Vous souhaitez plus précisément, assurez-vous que vous comprenez les implications de sécurité des propriétés que vous autorisez à être liés et assurez-vous que vous autorisez uniquement les propriétés qui vraiment doivent être mis à jour par un utilisateur final à être mis à jour.

Par défaut, la méthode UpdateModel() va tenter de mettre à jour toutes les propriétés sur l’objet de modèle qui correspondent aux valeurs de paramètre de formulaire entrantes. De même, objets passés comme paramètres de méthode d’action également par défaut peuvent avoir toutes leurs propriétés définies via les paramètres de l’écran.

#### <a name="locking-down-binding-on-a-per-usage-basis"></a>Verrouillage de liaison sur une base par utilisation

Vous pouvez verrouiller la stratégie de liaison dans une base de l’utilisation en fournissant un explicite « include liste » des propriétés qui peuvent être mis à jour. Cela est possible en passant un paramètre de tableau de chaînes supplémentaire à la méthode UpdateModel() comme ci-dessous :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample32.cs)]

Objets passés comme paramètres de méthode d’action également prennent en charge un attribut [lier] qui permet une « include liste » d’autorisé des propriétés de la définir comme ci-dessous :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample33.cs)]

#### <a name="locking-down-binding-on-a-type-basis"></a>Verrouillage de liaison par type

Vous pouvez également verrouiller les règles de liaison dans une base par type. Cela vous permet de spécifier les règles de liaison d’une seule fois et puis les appliquer dans tous les scénarios (y compris les scénarios de paramètre de méthode UpdateModel et action) sur tous les contrôleurs et les méthodes d’action.

Vous pouvez personnaliser les règles de liaison par type en ajoutant un attribut [lier] sur un type, ou en l’enregistrant dans le fichier Global.asax de l’application (utile pour les scénarios où vous ne possédez pas le type). Vous pouvez ensuite utiliser inclure de l’attribut de liaison et exclure les propriétés pour contrôler les propriétés qui peuvent être liées pour l’interface ou une classe particulière.

Nous allons utiliser cette technique pour la classe dîner dans notre application NerdDinner et ajoutez un attribut [lier] qui restreint la liste des propriétés pouvant être liées à ce qui suit :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample34.cs)]

Notez que nous n’autorisons pas être manipulées via la liaison à la collection RSVP, ni nous autorisons les DinnerID ou HostedBy des propriétés via la liaison. Pour des raisons de sécurité nous allons manipuler à la place uniquement ces propriétés particulières à l’aide de code explicite dans les méthodes d’action.

### <a name="crud-wrap-up"></a>Synthèse CRUD

ASP.NET MVC inclut un nombre de fonctionnalités intégrées qui permettent à l’implémentation de scénarios de validation de formulaire. Nous avons utilisé un large éventail de ces fonctionnalités pour prendre en charge l’interface utilisateur CRUD sur notre DinnerRepository.

Nous utilisons une approche axée sur le modèle à mettre en œuvre de notre application. Cela signifie que tous les nos validation et la règle d’entreprise logique est définie au sein de la couche de notre modèle – et non dans des contrôleurs ou des vues. Nos modèles de vue ni de notre classe de contrôleur de connaître les règles d’entreprise spécifiques appliquées par notre classe de modèle dîner.

Cela sera nettoyer l’architecture de notre application et le rendre plus faciles à tester. Nous pouvons ajouter des règles d’entreprise supplémentaires à la couche de modèle dans le futur et *n’a pas d’apporter des modifications de code* à notre contrôleur ou une vue dans l’ordre de leur être pris en charge. Cela va nous fournir une grande flexibilité pour notre application dans le futur et évoluent.

Notre DinnersController maintenant permet d’annonces dîner/détails, ainsi que créer, modifier et supprimer la prise en charge. Vous trouverez le code complet pour la classe ci-dessous :

[!code-csharp[Main](provide-crud-create-read-update-delete-data-form-entry-support/samples/sample35.cs)]

### <a name="next-step"></a>Étape suivante

Nous disposons désormais implémenter au sein de notre classe DinnersController prise en charge de base CRUD (création, lecture, mise à jour et suppression).

Nous allons maintenant voir comment nous pouvons utiliser des classes ViewData et ViewModel pour activer l’interface utilisateur enrichie sur nos formulaires.

>[!div class="step-by-step"]
[Précédent](use-controllers-and-views-to-implement-a-listingdetails-ui.md)
[Suivant](use-viewdata-and-implement-viewmodel-classes.md)
