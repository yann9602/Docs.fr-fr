---
uid: mvc/overview/older-versions-1/contact-manager/iteration-4-make-the-application-loosely-coupled-vb
title: "Itération #4 : rendre des applications faiblement couplées (VB) | Documents Microsoft"
author: microsoft
description: "Dans cette troisième itération, nous tirer parti de plusieurs modèles de conception de logiciel pour le rendre plus facile à gérer et modifier l’application Gestionnaire de contacts. Pré..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2009
ms.topic: article
ms.assetid: 92c70297-4430-4e4e-919a-9c2333a8d09a
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/contact-manager/iteration-4-make-the-application-loosely-coupled-vb
msc.type: authoredcontent
ms.openlocfilehash: 9c11c89710723c133a306aaf56cc8797cc036475
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="iteration-4--make-the-application-loosely-coupled-vb"></a>Itération #4 : rendre des applications faiblement couplées (VB)
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le Code](iteration-4-make-the-application-loosely-coupled-vb/_static/contactmanager_4_vb1.zip)

> Dans cette troisième itération, nous tirer parti de plusieurs modèles de conception de logiciel pour le rendre plus facile à gérer et modifier l’application Gestionnaire de contacts. Par exemple, nous refactoriser notre application pour utiliser le modèle de référentiel et le modèle d’Injection de dépendances.


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

Dans ce quatrième itération de l’application Gestionnaire de Contact, nous refactoriser l’application pour rendre l’application plus faiblement couplée. Lorsqu’une application est faiblement couplée, vous pouvez modifier le code dans une partie de l’application sans avoir à modifier le code dans d’autres parties de l’application. Applications faiblement couplées sont plus résilientes à modifier.

Actuellement, toute la logique d’accès et la validation de données utilisée par l’application Gestionnaire de contacts est contenue dans les classes de contrôleur. Il s’agit d’une bonne idée. Chaque fois que vous avez besoin modifier une partie de votre application, vous risquez d’introduire des bogues dans une autre partie de votre application. Par exemple, si vous modifiez votre logique de validation, vous risquez introduire de nouveaux bogues dans votre logique d’accès ou un contrôleur de données.

> [!NOTE] 
> 
> (SRP), une classe ne devriez jamais avoir plusieurs raisons à modifier. Mélange de contrôleur, validation et la logique de la base de données est une violation de gros volumes de principe de responsabilité unique.


Il existe plusieurs raisons, vous devrez peut-être modifier votre application. Vous devrez peut-être ajouter une nouvelle fonctionnalité à votre application, vous devrez peut-être corriger un bogue dans votre application, ou vous devrez peut-être modifier la façon dont une fonction de votre application est implémentée. Les applications sont rarement statiques. Ils ont tendance à croître et muter au fil du temps.

Par exemple, imaginez que vous décidez de modifier la façon dont vous implémentez votre couche d’accès aux données. À droite, l’application Gestionnaire de contacts utilise désormais Microsoft Entity Framework pour accéder à la base de données. Toutefois, vous pouvez décider de migrer vers une technologie d’accès aux données nouvelles ou autre telles que ADO.NET Data Services ou NHibernate. Toutefois, étant donné que le code d’accès aux données n’est pas isolé à partir du code de validation et le contrôleur, il n’existe aucun moyen pour modifier le code d’accès aux données dans votre application sans modifier tout autre code qui n’est pas directement liée à l’accès aux données.

Lorsqu’une application est faiblement couplée, quant à eux, vous pouvez apporter des modifications à une partie d’une application sans toucher aux autres parties d’une application. Par exemple, vous pouvez basculer des technologies d’accès aux données sans modifier votre logique de validation ou de contrôleur.

Dans cette itération, nous tirer parti de plusieurs modèles de conception de logiciels qui nous permettent de refactoriser notre application Gestionnaire de contacts dans une application plus faiblement couplée. Lorsque nous avons terminé, le Gestionnaire de Contact a gagné t faire quoi que ce soit il n’a pas à le faire avant. Toutefois, nous serons en mesure de modifier l’application plus facilement à l’avenir.

> [!NOTE] 
> 
> La refactorisation est le processus de réécriture d’une application de sorte qu’il ne perd pas toutes les fonctionnalités existantes.


## <a name="using-the-repository-software-design-pattern"></a>À l’aide du modèle de conception de logiciels de référentiel

Notre première modification est pour tirer parti d’un modèle de conception de logiciel appelé le motif de référentiel. Nous allons utiliser le modèle de référentiel pour isoler votre code d’accès aux données du reste de notre application.

Implémentation du modèle de référentiel nous oblige à effectuer les deux étapes suivantes :

1. Créez une interface
2. Créer une classe concrète qui implémente l’interface

Tout d’abord, nous devons créer une interface qui décrit toutes les méthodes d’accès aux données que nous devons effectuer. L’interface IContactManagerRepository est contenue dans la liste 1. Décrit les cinq méthodes de cette interface : CreateContact(), DeleteContact(), EditContact(), GetContact et ListContacts().

**La liste 1 - Models\IContactManagerRepository.vb**

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample1.vb)]

Ensuite, nous avons besoin créer une classe concrète qui implémente l’interface IContactManagerRepository. Étant donné que nous utilisons Microsoft Entity Framework pour accéder à la base de données, nous allons créer une nouvelle classe nommée EntityContactManagerRepository. Cette classe est contenue dans le Listing 2.

**Listing 2 - Models\EntityContactManagerRepository.vb**

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample2.vb)]

Notez que la classe EntityContactManagerRepository implémente l’interface IContactManagerRepository. La classe implémente les cinq méthodes décrites par cette interface.

Vous vous demandez peut-être pourquoi nous devons préoccuper d’une interface. Pourquoi nous devons créer une interface et une classe qui l’implémente ?

Une exception, le reste de notre application interagit avec l’interface et non de la classe concrète. Au lieu d’appeler les méthodes exposées par la classe EntityContactManagerRepository, nous allons appeler les méthodes exposées par l’interface IContactManagerRepository.

De cette façon, nous pouvons implémenter l’interface avec une nouvelle classe sans avoir à modifier le reste de notre application. Par exemple, à une date future, nous souhaitions implémenter une classe DataServicesContactManagerRepository qui implémente l’interface IContactManagerRepository. La classe DataServicesContactManagerRepository peut utiliser ADO.NET Data Services pour accéder à une base de données au lieu de Microsoft Entity Framework.

Si notre code de l’application est programmé à l’interface IContactManagerRepository au lieu de la classe EntityContactManagerRepository concrète nous pouvons basculer les classes concrètes sans modifier la partie de notre code. Par exemple, nous pouvons basculer à partir de la classe EntityContactManagerRepository à la classe DataServicesContactManagerRepository sans modifier notre logique de validation ou d’accès aux données.

Programmation par rapport aux interfaces (abstractions) au lieu de classes concrètes rend notre application plus fiable à modifier.

> [!NOTE] 
> 
> Vous pouvez rapidement créer une interface à partir d’une classe concrète dans Visual Studio en sélectionnant l’option de menu refactoriser, extraire l’Interface. Vous pouvez par exemple, créez d’abord la classe EntityContactManagerRepository et utiliser extraire l’Interface pour générer l’interface IContactManagerRepository automatiquement.


## <a name="using-the-dependency-injection-software-design-pattern"></a>À l’aide du modèle de conception de logiciels d’Injection dépendance

Maintenant que nous avons migré de notre code d’accès aux données à une classe distincte de référentiel, nous devons modifier notre contrôleur Contact pour utiliser cette classe. Nous tirera parti d’un modèle de conception de logiciels appelé d’Injection de dépendances pour utiliser la classe de référentiel dans notre contrôleur.

Le contrôleur de Contact modifié est contenu dans la liste 3.

**La liste 3 - Controllers\ContactController.vb**

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample3.vb)]

Notez que le contrôleur de Contact dans la liste 3 a deux constructeurs. Le premier constructeur passe une instance concrète de l’interface IContactManagerRepository pour le deuxième constructeur. La classe de contrôleur Contact utilise *Injection de dépendances constructeur*.

Celle et uniquement sur place que la classe EntityContactManagerRepository est utilisée est le premier constructeur. Le reste de la classe utilise l’interface IContactManagerRepository au lieu de la classe EntityContactManagerRepository concrète.

Cela facilite le basculer les implémentations de la classe IContactManagerRepository dans le futur. Si vous souhaitez utiliser la classe DataServicesContactRepository au lieu de la classe EntityContactManagerRepository, il suffit de modifier le premier constructeur.

Injection de dépendances constructeur permet également de la classe de contrôleur de Contact très testable. Dans vos tests unitaires, vous pouvez instancier le contrôleur de Contact en passant une implémentation fictive de la classe IContactManagerRepository. Cette fonctionnalité d’Injection de dépendance sera très importante pour nous dans l’itération suivante lorsque nous générer des tests unitaires pour l’application Gestionnaire de contacts.

> [!NOTE] 
> 
> Si vous souhaitez complètement découpler la classe de contrôleur de Contact à partir d’une implémentation particulière de l’interface IContactManagerRepository puis vous pouvez tirer parti d’une infrastructure qui prend en charge l’Injection de dépendances telles que StructureMap ou Microsoft Entity Framework (MEF). En tirant parti d’une infrastructure d’Injection de dépendances, vous devez jamais faire référence à une classe concrète dans votre code.


## <a name="creating-a-service-layer"></a>Création d’une couche de Service

Vous avez peut-être remarqué que notre logique de validation est toujours confondue notre logique du contrôleur dans la classe de contrôleur modifié dans la liste 3. Pour la même raison qu’il est judicieux d’isoler notre logique d’accès aux données, il est judicieux d’isoler notre logique de validation.

Pour résoudre ce problème, nous pouvons créer un distinct [couche de service](http://martinfowler.com/eaaCatalog/serviceLayer.html). La couche de service est une couche distincte qui nous pouvons insérer entre notre contrôleur et les classes du référentiel. La couche de service contient notre logique d’entreprise, y compris toute notre logique de validation.

Le ContactManagerService est contenue dans la liste 4. Il contient la logique de validation à partir de la classe de contrôleur du Contact.

**La liste 4 - Models\ContactManagerService.vb**

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample4.vb)]

Notez que le constructeur de la ContactManagerService nécessite un ValidationDictionary. La couche de service communique avec la couche de contrôleur via ce ValidationDictionary. Nous aborderons la ValidationDictionary en détail dans la section suivante lorsque nous aborderons le modèle Decorator.

En outre, notez que le ContactManagerService implémente l’interface IContactManagerService. Vous devez toujours vous efforcer de programmer des interfaces, et non des classes concrètes. Autres classes dans l’application Gestionnaire de Contact n’interagissent pas directement avec la classe ContactManagerService. Au lieu de cela, une exception, le reste de l’application Gestionnaire de contacts est programmé à l’interface IContactManagerService.

L’interface IContactManagerService est contenue dans la liste 5.

**La liste 5 - Models\IContactManagerService.vb**

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample5.vb)]

La classe de contrôleur Contact modifiée est contenue dans la liste 6. Notez que le contrôleur de Contact est n’est plus interagit avec le référentiel de ContactManager. Au lieu de cela, le contrôleur de Contact interagit avec le service ContactManager. Chaque couche est isolée autant que possible à partir d’autres couches.

**La liste 6 - Controllers\ContactController.vb**

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample6.vb)]

N’est plus notre application s’exécute afoul du numéro le principe de responsabilité unique (SRP). Le contrôleur de Contact de la liste 6 a été supprimé de chaque responsabilité autre que le contrôle du flux d’exécution de l’application. La logique de validation a été supprimée du contrôleur de Contact et placée dans la couche de service. Toute la logique de la base de données a été placé dans la couche de référentiels.

## <a name="using-the-decorator-pattern"></a>Utilisation du modèle Decorator

Vous souhaitez être en mesure de découpler complètement de notre couche de service à partir de la couche de notre contrôleur. En principe, nous devrions pouvoir compiler notre couche de service dans un assembly distinct à partir de la couche de notre contrôleur sans avoir à ajouter une référence à notre application MVC.

Toutefois, la couche de service doit peut transmettre des messages d’erreur de validation à la couche de contrôleur. Comment nous pouvons pour activer la couche de service communiquer des messages d’erreur de validation sans association étroite entre le contrôleur et la couche de service ? Nous pouvons tirer parti d’un modèle de conception de logiciel nommé le [modèle d’élément décoratif](http://en.wikipedia.org/wiki/Decorator_pattern).

Un contrôleur utilise un ModelStateDictionary nommé ModelState pour représenter les erreurs de validation. Par conséquent, il se peut que vous pouvez être tenté de passer ModelState à partir de la couche de contrôleur pour la couche de service. Toutefois, dans la couche de service à l’aide de ModelState rend votre couche de service dépend d’une fonctionnalité de l’infrastructure ASP.NET MVC. Cela peut être incorrect car, un jour, vous pouvez utiliser la couche de service avec une application WPF au lieu d’une application ASP.NET MVC. Dans ce cas, les t commode vous souhaitez faire référence à l’infrastructure ASP.NET MVC pour utiliser la classe ModelStateDictionary.

Le modèle d’élément décoratif vous permet d’encapsuler une classe existante dans une nouvelle classe pour implémenter une interface. Notre projet de gestionnaire de contacts inclut la classe ModelStateWrapper contenue dans la liste 7. La classe ModelStateWrapper implémente l’interface dans la liste 8.

**La liste 7 - Models\Validation\ModelStateWrapper.vb**

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample7.vb)]

**Liste 8 - Models\Validation\IValidationDictionary.vb**

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample8.vb)]

Si vous examinez une liste 5 vous verrez alors que la couche de service ContactManager utilise l’interface IValidationDictionary exclusivement. Le service ContactManager n’est pas dépendant de la classe ModelStateDictionary. Lorsque le contrôleur de Contact crée le service ContactManager, le contrôleur encapsule son ModelState comme suit :

[!code-vb[Main](iteration-4-make-the-application-loosely-coupled-vb/samples/sample9.vb)]

## <a name="summary"></a>Résumé

Dans cette itération, nous n’avez pas ajouté de nouvelles fonctionnalités à l’application Gestionnaire de contacts. L’objectif de cette itération a été de refactoriser l’application Gestionnaire de contacts pour qu’elle soit plus facile à gérer et modifier.

Tout d’abord, nous avons implémenté le modèle de conception de logiciels de référentiel. Nous avons migré tout le code d’accès aux données à une classe de référentiel ContactManager distincte.

Nous avons également isolé notre logique de validation à partir de notre logique du contrôleur. Nous avons créé une couche de service distinct qui contient l’ensemble de notre code de validation. La couche de contrôleur interagit avec la couche de service, et la couche de service interagit avec la couche de référentiels.

Lorsque nous avons créé la couche de service, nous avons tiré parti du modèle Decorator pour isoler le ModelState à partir de la couche de service. Dans la couche de service, nous programmées à l’interface IValidationDictionary au lieu de ModelState.

Enfin, nous avons pris parti d’un modèle de conception de logiciel nommé le modèle d’Injection de dépendances. Ce modèle permet de programmer des interfaces (abstractions) au lieu de classes concrètes. Implémentation du modèle de conception d’Injection de dépendances permet également de notre code des tests. Dans l’itération suivante, nous ajoutons des tests unitaires à notre projet.

>[!div class="step-by-step"]
[Précédent](iteration-3-add-form-validation-vb.md)
[Suivant](iteration-5-create-unit-tests-vb.md)
