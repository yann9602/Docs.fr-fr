---
uid: mvc/overview/older-versions-1/contact-manager/iteration-3-add-form-validation-vb
title: "Itération #3 : validation de formulaire Ajouter (VB) | Documents Microsoft"
author: microsoft
description: "Dans la troisième itération, nous ajouter la validation de formulaire de base. Nous empêcher des personnes d’envoi d’un formulaire sans terminer les champs obligatoires. Nous avons également valider emai..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2009
ms.topic: article
ms.assetid: 4805e75a-7911-46e3-b11b-229a6eed245e
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/contact-manager/iteration-3-add-form-validation-vb
msc.type: authoredcontent
ms.openlocfilehash: ffb502be3037e787d79bbd1e83b93cd0b34dca6a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="iteration-3--add-form-validation-vb"></a>Itération #3 : validation de formulaire Ajouter (VB)
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le Code](iteration-3-add-form-validation-vb/_static/contactmanager_3_vb1.zip)

> Dans la troisième itération, nous ajouter la validation de formulaire de base. Nous empêcher des personnes d’envoi d’un formulaire sans terminer les champs obligatoires. Nous avons également valider que les adresses de messagerie et les numéros de téléphone.


## <a name="building-a-contact-management-aspnet-mvc-application-vb"></a>Création d’une Application ASP.NET MVC de gestion des contacts (VB)
  

Dans cette série de didacticiels, nous générer une application de gestion des contacts entière à partir du début à la fin. L’application Gestionnaire de contacts permet de vous permet de stocker les informations de contact (des noms, des numéros de téléphone et adresses de messagerie) pour obtenir la liste des personnes.

Nous générer l’application sur plusieurs itérations. Avec chaque itération, nous améliorer progressivement l’application. L’objectif de cette approche itération plusieurs est pour vous permettre de comprendre la raison de chaque modification.

- Itération #1 - créer l’application. Dans la première itération, nous créons le Gestionnaire de Contact de la façon la plus simple possible. Prise en charge pour les opérations de base de données : création, lecture, mise à jour et supprimer (CRUD).

- Itération #2 : rendre des applications attrayantes. Dans cette itération, nous améliorer l’apparence de l’application en modifiant la valeur par défaut de page maître de vue ASP.NET MVC en cascade de feuille de style.

- Itération #3 - ajouter la validation de formulaire. Dans la troisième itération, nous ajouter la validation de formulaire de base. Nous empêcher des personnes d’envoi d’un formulaire sans terminer les champs obligatoires. Nous avons également valider que les adresses de messagerie et les numéros de téléphone.

- Itération #4 : rendre des applications faiblement couplées. Dans cette troisième itération, nous tirer parti de plusieurs modèles de conception de logiciel pour le rendre plus facile à gérer et modifier l’application Gestionnaire de contacts. Par exemple, nous refactoriser notre application pour utiliser le modèle de référentiel et le modèle d’Injection de dépendances.

- Itération #5 - créer des tests unitaires. Dans l’itération cinquième, nous faciliter notre application mettre à jour et modifier en ajoutant les tests unitaires. Nous simuler nos classes de modèle de données et générer des tests unitaires pour nos contrôleurs et la logique de validation.

- Itération #6 - utiliser le développement piloté par test. Dans cette itération sixième, nous ajouter de nouvelles fonctionnalités à notre application en écrivant des tests unitaires tout d’abord et d’écrire du code pour les tests unitaires. Dans cette itération, nous ajouter des groupes de contacts.

- Itération #7 - ajouter des fonctionnalités Ajax. Dans l’itération septième, nous améliorer la réactivité et les performances de votre application en ajoutant la prise en charge d’Ajax.


## <a name="this-iteration"></a>Cette itération

Dans cette deuxième itération de l’application Gestionnaire de Contact, nous ajouter la validation de formulaire de base. Nous empêcher des personnes d’envoi d’un contact sans saisie de valeurs pour les champs obligatoires. Nous avons également valider que les numéros de téléphone et adresses de messagerie (voir Figure 1).


[![La boîte de dialogue Nouveau projet](iteration-3-add-form-validation-vb/_static/image1.jpg)](iteration-3-add-form-validation-vb/_static/image1.png)

**Figure 01**: un formulaire avec la validation ([cliquez pour afficher l’image en taille réelle](iteration-3-add-form-validation-vb/_static/image2.png))


Dans cette itération, nous ajouter la logique de validation directement sur les actions de contrôleur. En général, cela n’est pas recommandée pour ajouter une validation à une application ASP.NET MVC. Une meilleure approche consiste à placer une logique de validation d’application s dans une fonction [couche de service](http://martinfowler.com/eaaCatalog/serviceLayer.html). Dans l’itération suivante, nous refactoriser l’application Gestionnaire de contacts pour rendre l’application plus facile à gérer.

Dans cette itération, pour simplifier les choses, nous écrire tout le code de validation à la main. Au lieu d’écrire du code de validation nous-mêmes, nous avons tirer parti d’une infrastructure de validation. Par exemple, vous pouvez utiliser Microsoft Enterprise Library Validation Application bloc (ces) pour implémenter la logique de validation de votre application ASP.NET MVC. Pour en savoir plus sur le bloc d’Application de Validation, consultez :

[*http://msdn.Microsoft.com/en-us/library/dd203099.aspx*](https://msdn.microsoft.com/en-us/library/dd203099.aspx)

## <a name="adding-validation-to-the-create-view"></a>Ajout d’une Validation à la vue de créer

Permettent de s commencez par ajouter la logique de validation à la vue à créer. Heureusement, car nous a généré la vue de créer avec Visual Studio, la vue Create contient déjà toute la logique d’interface utilisateur nécessaires pour afficher les messages de validation. La vue de créer est contenue dans la liste 1.

**La liste 1 - \Views\Contact\Create.aspx**

[!code-aspx[Main](iteration-3-add-form-validation-vb/samples/sample1.aspx)]

La classe d’erreur de validation de champ est utilisée pour définir le style de la sortie rendue par l’application d’assistance Html.ValidationMessage(). La classe d’erreur de validation d’entrée est utilisée pour définir le style de la zone de texte (entrée) rendue par l’application d’assistance Html.TextBox(). La classe Résumé-erreurs de validation est utilisée pour définir le style de la liste non triée rendue par l’application d’assistance Html.ValidationSummary().

> [!NOTE] 
> 
> Vous pouvez modifier les classes de feuille de style décrites dans cette section pour personnaliser l’apparence des messages d’erreur de validation.


## <a name="adding-validation-logic-to-the-create-action"></a>Ajout d’une logique de Validation à l’Action de création

Actuellement, la vue Create affiche jamais les messages d’erreur de validation, car nous avons écrit pas la logique pour générer des messages. Pour afficher les messages d’erreur de validation, vous devez ajouter les messages d’erreur pour le ModelState.

> [!NOTE] 
> 
> La méthode UpdateModel() ajoute automatiquement les messages d’erreur à ModelState lorsqu’il existe une erreur affectant la valeur d’un champ de formulaire à une propriété. Par exemple, si vous essayez d’assigner la chaîne « apple » à une propriété de date de naissance qui accepte des valeurs de date/heure, la méthode UpdateModel() ajoute une erreur à ModelState.


La méthode Create() modifiée dans la liste 2 contient une section qui valide les propriétés de la classe de Contact avant que le nouveau contact est inséré dans la base de données.

**Listing 2 - Controllers\ContactController.vb (créer avec une validation)**

[!code-vb[Main](iteration-3-add-form-validation-vb/samples/sample2.vb)]

La section de validation applique quatre règles de validation distinctes :

- La propriété FirstName doit avoir une longueur supérieure à zéro (et il ne peut pas être composé uniquement d’espaces)
- La propriété LastName doit avoir une longueur supérieure à zéro (et il ne peut pas être composé uniquement d’espaces)
- Si la propriété de téléphone possède une valeur (a une longueur supérieure à 0), puis la propriété téléphone doit correspondre à une expression régulière.
- Si la propriété de l’adresse de messagerie a une valeur (a une longueur supérieure à 0), puis la propriété adresse de messagerie doit correspondre à une expression régulière.

Lorsqu’il existe une violation de règle de validation, un message d’erreur est ajouté à ModelState à l’aide de la méthode AddModelError(). Lorsque vous ajoutez un message à ModelState, vous fournissez le nom d’une propriété et le texte d’un message d’erreur de validation. Ce message d’erreur s’affiche dans la vue par les méthodes d’assistance Html.ValidationSummary() et Html.ValidationMessage().

Une fois les règles de validation sont exécutées, la propriété IsValid de ModelState est activée. La propriété IsValid retourne la valeur false lorsque des messages d’erreur de validation ont été ajoutés à ModelState. Si la validation échoue, le formulaire de création s’affiche de nouveau avec les messages d’erreur.

> [!NOTE] 
> 
> J’ai reçu les expressions régulières pour la validation de l’adresse de messagerie et le numéro de téléphone à partir du référentiel d’expression régulière à [ *http://regexlib.com*](http://regexlib.com)


## <a name="adding-validation-logic-to-the-edit-action"></a>Ajout d’une logique de Validation à l’Action de modification

L’action Edit() met à jour un Contact. L’action Edit() doit effectuer exactement la même validation que l’action Create(). Au lieu de répéter le même code de validation, nous devons refactoriser le contrôleur de Contact, afin que les Create() Edit() actions et appellent la même méthode de validation.

La classe de contrôleur Contact modifiée est contenue dans la liste 3. Cette classe possède une nouvelle méthode ValidateContact() qui est appelée dans les Create() Edit() actions et.

**La liste 3 - Controllers\ContactController.vb**

[!code-vb[Main](iteration-3-add-form-validation-vb/samples/sample3.vb)]

## <a name="summary"></a>Résumé

Dans cette itération, nous avons ajouté la validation de formulaire de base à notre application Gestionnaire de contacts. Notre logique de validation empêche les utilisateurs de soumettre un nouveau contact ou de la modification d’un contact existant sans fournir des valeurs pour les propriétés FirstName et LastName. En outre, les utilisateurs doivent fournir des adresses de messagerie et les numéros de téléphone valide.

Dans cette itération, nous avons ajouté la logique de validation à notre application Gestionnaire de contacts de la manière la plus simple possible. Toutefois, combinaison de notre logique de validation dans notre logique du contrôleur créera des problèmes pour nous à long terme. Notre application sera plus difficile à gérer et modifier au fil du temps.

Dans l’itération suivante, nous devez refactoriser notre logique de validation et la logique d’accès de base de données hors de nos contrôleurs. Nous allons tirer parti de plusieurs principes de conception de logiciel pour pouvoir créer une application plus faiblement couplée et plus faciles à gérer.

>[!div class="step-by-step"]
[Précédent](iteration-2-make-the-application-look-nice-vb.md)
[Suivant](iteration-4-make-the-application-loosely-coupled-vb.md)
