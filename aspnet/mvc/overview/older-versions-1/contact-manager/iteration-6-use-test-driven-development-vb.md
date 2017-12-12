---
uid: mvc/overview/older-versions-1/contact-manager/iteration-6-use-test-driven-development-vb
title: "Itération #6 – Utilisez le développement piloté par test (VB) | Documents Microsoft"
author: microsoft
description: "Dans cette itération sixième, nous ajouter de nouvelles fonctionnalités à notre application en écrivant des tests unitaires tout d’abord et d’écrire du code pour les tests unitaires. Dans cette itération,..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2009
ms.topic: article
ms.assetid: e1fd226f-3f8e-4575-a179-5c75b240333d
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/contact-manager/iteration-6-use-test-driven-development-vb
msc.type: authoredcontent
ms.openlocfilehash: 9b558df9c0b44f5f76115270d361b6022658f9f9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="iteration-6--use-test-driven-development-vb"></a>Itération #6 – Utilisez le développement piloté par test (VB)
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le Code](iteration-6-use-test-driven-development-vb/_static/contactmanager_6_vb1.zip)

> Dans cette itération sixième, nous ajouter de nouvelles fonctionnalités à notre application en écrivant des tests unitaires tout d’abord et d’écrire du code pour les tests unitaires. Dans cette itération, nous ajouter des groupes de contacts.


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

Dans l’itération précédente de l’application Gestionnaire de Contact, nous avons créé des tests unitaires pour fournir un filet de sécurité pour notre code. La motivation pour la création de tests unitaires a été pour rendre notre code plus fiable pour le modifier. Avec des tests unitaires en place, nous pouvons heureusement apporter toute modification apportée à notre code et savez immédiatement si nous avons altéré les fonctionnalités existantes.

Dans cette itération, nous utilisons des tests unitaires dans un but totalement différent. Dans cette itération, nous utilisons des tests unitaires dans le cadre d’une philosophie de conception d’application appelé *développement piloté par test*. Lorsque vous entraîner le développement piloté par test, vous écrivez tout d’abord des tests et puis écrivez du code pour les tests.

Plus précisément, lorsque qui emploient le développement piloté par test, il existe trois étapes que vous effectuez lors de la création de code (rouge / vert/refactoriser) :

1. Écrire un test unitaire Échec (rouge)
2. Écrire du code qui réussit le test unitaire (vert)
3. Refactoriser votre code (refactoriser)

Vous écrivez d’abord, le test unitaire. Le test unitaire doive exprimer votre intention pour que votre code se comportent. Lorsque vous créez le test unitaire, le test unitaire doit échouer. Le test doit échouer, car vous n’avez pas encore écrit tout code d’application qui satisfait au test.

Ensuite, vous écrivez juste assez de code afin que le test unitaire à passer. L’objectif est d’écrire le code de la manière laziest, sloppiest et plus rapide possible. Vous ne devez pas perdre de temps à réfléchir sur l’architecture de votre application. Au lieu de cela, vous devez vous concentrer sur l’écriture de la quantité minimale de code nécessaire pour satisfaire l’intention exprimée par le test unitaire.

Enfin, une fois que vous avez écrit suffisamment de code, vous pouvez revenir en arrière et prendre en compte l’architecture globale de votre application. Dans cette étape, vous réécrivez (refactoriser) votre code en tirant parti de la conception logicielle modèles--tels que le modèle de référentiel--afin que votre code est plus facile à gérer. Vous pouvez fearlessly réécrire votre code dans cette étape, car votre code est couverte par les tests unitaires.

Il existe de nombreux avantages résultant de la pratique du développement piloté par test. Première un développement piloté par test vous oblige à vous concentrer sur le code qui a réellement besoin à écrire. Étant donné que vous avez le focus en permanence sur la simple écriture de suffisamment de code pour passer d’un test particulier, vous ne pouvez pas FUGUES dans les plantes et l’écriture des quantités massives de code que vous n’utiliserez jamais.

En second lieu, une méthodologie de conception « test tout d’abord » vous oblige à écrire du code à partir de la perspective de l’utilisation de votre code. En d’autres termes, lorsque qui emploient le développement piloté par test, vous en permanence écrivez vos tests à partir d’une perspective de l’utilisateur. Par conséquent, développement piloté par test peut entraîner des API plus claire et plus compréhensibles.

Enfin, développement piloté par test vous oblige à écrire des tests unitaires dans le cadre du processus normal de l’écriture d’une application. Approche de l’échéance d’un projet, de test est généralement la première chose qui sort de la fenêtre. Lorsque qui emploient le développement piloté par test, en revanche, vous êtes plus susceptible d’être vertueux sur l’écriture de tests unitaires, car le développement piloté par test effectue des tests unitaires centrale pour le processus de création d’une application.

> [!NOTE] 
> 
> Pour en savoir plus sur le développement piloté par test, je vous conseillons de lire les livres de Michael plumes **travailler efficacement avec le Code hérité**.


Dans cette itération, nous ajouter une nouvelle fonctionnalité à notre application Gestionnaire de contacts. Nous ajoutons la prise en charge pour les groupes de Contact. Vous pouvez utiliser des groupes de Contact pour organiser vos contacts en catégories telles que de l’entreprise et des groupes de Friend.

Nous allons ajouter cette nouvelle fonctionnalité à notre application en suivant un processus de développement piloté par test. Nous allons écrire tout d’abord nos tests unitaires et nous allons écrire tout notre code par rapport à ces tests.

## <a name="what-gets-tested"></a>Ce qui est testé

Comme expliqué dans l’itération précédente, en général, ne pas écrire des tests unitaires pour la logique d’accès aux données ou afficher une logique. Vous n’avez pas t écriture des tests unitaires pour la logique d’accès aux données, car l’accès à une base de données est une opération relativement lente. Vous n’avez pas t écriture des tests unitaires pour afficher la logique, car l’accès à une vue nécessite la rotation d’un serveur web qui est une opération relativement lente. T ne doit pas dater de vous écrire un test unitaire, sauf si le test peut être exécuté à nouveau très rapide.

Étant donné que le développement piloté par test est piloté par les tests unitaires, nous concentrer initialement sur l’écriture de contrôleur et la logique métier. Nous Évitez de toucher la base de données ou des vues. Nous avons conclues t modifier la base de données ou créer des vues de notre jusqu'à la fin de ce didacticiel. Nous allons commencer avec ce qui peut être testé.

## <a name="creating-user-stories"></a>Création de récits utilisateur

Lorsque votre partenaire développement piloté par test, vous démarrez toujours en écrivant un test. Cela déclenche immédiatement la question : comment vous décidez quel test écrire tout d’abord ? Pour répondre à cette question, vous devez écrire un ensemble de [ *récits utilisateur*](http://en.wikipedia.org/wiki/User_stories).

Un récit utilisateur est une très brève description de (généralement une phrase) d’une configuration logicielle requise. Il doit être une description non technique d’une exigence écrite un point de vue utilisateur.

Voici l’ensemble des récits utilisateur qui décrivent les fonctionnalités requises par les nouvelles fonctionnalités de groupe de Contact de s :

1. Utilisateur peut afficher une liste de groupes de contacts.
2. Utilisateur peut créer un nouveau groupe de contact.
3. Utilisateur peut supprimer un groupe de contact existant.
4. Utilisateur peut sélectionner un groupe de contacts lors de la création d’un contact.
5. Utilisateur peut sélectionner un groupe de contacts lors de la modification d’un contact existant.
6. Une liste de groupes de contact s’affiche dans la vue Index.
7. Lorsqu’un utilisateur clique sur un groupe de contacts, une liste de contacts correspondants s’affiche.

Notez que cette liste de récits utilisateur est complètement compréhensible par un client. Il n’existe aucune mention de détails d’implémentation technique.

Lors du processus de génération de votre application, l’ensemble des récits utilisateur peut devenir plus précise. Vous risquez d’altérer un récit utilisateur dans plusieurs articles (configuration requise). Par exemple, vous pouvez décider que la création d’un nouveau groupe de contact doit impliquent la validation. Envoi d’un groupe sans nom de contact doit retourner une erreur de validation.

Après avoir créé une liste de récits utilisateur, vous êtes prêt à écrire votre premier test unitaire. Nous allons commencer par créer un test unitaire pour afficher la liste des groupes de contacts.

## <a name="listing-contact-groups"></a>Liste des groupes de contacts

Un récit utilisateur notre premier est qu’un utilisateur doit être en mesure d’afficher une liste de groupes de contacts. Nous devons express ce scénario avec un test.

Créer un nouveau test unitaire en double-cliquant sur le dossier contrôleurs dans le projet ContactManager.Tests, en sélectionnant **ajouter, le nouveau Test**et en sélectionnant le **de Test unitaire** modèle (voir Figure 1). Nom de la nouvelle unité GroupControllerTest.vb de test et cliquez sur le **OK** bouton.


[![Ajout du test unitaire GroupControllerTest](iteration-6-use-test-driven-development-vb/_static/image1.jpg)](iteration-6-use-test-driven-development-vb/_static/image1.png)

**Figure 01**: ajout de test unitaire GroupControllerTest ([cliquez pour afficher l’image en taille réelle](iteration-6-use-test-driven-development-vb/_static/image2.png))


Notre premier test unitaire est contenue dans la liste 1. Ce test vérifie que la méthode Index() du contrôleur de groupe renvoie un ensemble de groupes. Le test vérifie qu’une collection de groupes est retournée dans la vue données.

**La liste 1 - Controllers\GroupControllerTest.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample1.vb)]

Lorsque vous tapez le code dans la liste de 1 dans Visual Studio, vous obtenez un grand nombre de traits ondulés rouges. Nous n’avons pas créé les classes GroupController ou un groupe.

À ce stade, nous pouvons build même de t notre application afin que nous t exécuter notre premier test unitaire. S bon. Qui est considérée comme un test qui a échoué. Par conséquent, nous disposons désormais autorisé à commencer à écrire du code de l’application. Nous devons écrire suffisamment de code pour exécuter notre test.

La classe de contrôleur de groupe dans la liste 2 contient au minimum de code requis pour réussir le test unitaire. L’action Index() retourne une liste codée de manière statique de groupes (la classe du groupe est définie dans la liste 3).

**Listing 2 - Controllers\GroupController.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample2.vb)]

**La liste 3 - Models\Group.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample3.vb)]

Une fois que nous ajoutons les classes GroupController et groupe à notre projet, notre premier test unitaire se termine correctement (voir Figure 2). Nous avons terminé le travail minimal requis pour réussir le test. Il est temps de fête.


[![Opération réussie](iteration-6-use-test-driven-development-vb/_static/image2.jpg)](iteration-6-use-test-driven-development-vb/_static/image3.png)

**Figure 02**: opération réussie ! () [Cliquez pour afficher l’image en taille réelle](iteration-6-use-test-driven-development-vb/_static/image4.png))


## <a name="creating-contact-groups"></a>Création de groupes de Contact

Maintenant nous pouvons passer au deuxième récit utilisateur. Nous devons être en mesure de créer des groupes de contact. Nous devons express cette intention avec un test.

Le test dans la liste 4 vérifie que l’appel de la méthode avec un nouveau groupe ajoute le groupe à la liste des groupes retournés par la méthode Index() de Create(). En d’autres termes, si vous créez un nouveau groupe puis dois-je être en mesure de récupérer le nouveau groupe dans la liste des groupes retournés par la méthode Index().

**La liste 4 - Controllers\GroupControllerTest.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample4.vb)]

Le test dans la liste 4 appelle la méthode Create() avec un nouveau contact, groupe du contrôleur de groupe. Ensuite, le test vérifie qu’appelant le contrôleur de groupe Index() méthode retourne le nouveau groupe dans les données d’affichage.

Le contrôleur de groupe modifié dans la liste 5 contient au minimum de modifications requises pour transmettre le nouveau test.

**La liste 5 - Controllers\GroupController.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample5.vb)]

## <a name="the-group-controller-in-listing-5-has-a-new-create-action-this-action-adds-a-group-to-a-collection-of-groups-notice-that-the-index-action-has-been-modified-to-return-the-contents-of-the-collection-of-groups"></a>Le contrôleur de groupe dans la liste 5 a une nouvelle action Create(). Cette action ajoute un groupe à une collection de groupes. Notez que l’action Index() a été modifiée pour retourner le contenu de la collection de groupes.

Une fois encore, nous avons réalisé le minimum de travail requise pour réussir le test unitaire. Une fois que nous apporter ces modifications sur le contrôleur de groupe, notre unité tous les tests réussissent.

## <a name="adding-validation"></a>Ajout de la validation

Cette exigence n’a pas été explicitement précisée dans le récit utilisateur. Toutefois, il est raisonnable de nécessitent qu’un nom à un groupe. Dans le cas contraire, l’organisation des contacts en groupes serait pas très utile.

La liste 6 contienne un test qui exprime l’intention. Ce test vérifie que la tentative de création d’un groupe sans avoir à fournir un nom aboutit à un message d’erreur de validation dans l’état du modèle.

**La liste 6 - Controllers\GroupControllerTest.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample6.vb)]

Afin de répondre à ce test, que nous devons ajouter une propriété de nom à la classe groupe (voir la liste 7). En outre, nous devons ajouter une légère de logique de validation à notre contrôleur groupe s Create() action (voir liste 8).

**La liste 7 - Models\Group.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample7.vb)]

**Liste 8 - Controllers\GroupController.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample8.vb)]

Notez que le contrôleur de groupe action Create() maintenant contient la logique de validation et de base de données. Actuellement, la base de données utilisé par le contrôleur de groupe se compose de rien de plus qu’une collection en mémoire.

## <a name="time-to-refactor"></a>Temps de refactorisation

La troisième étape de rouge/vert/refactorisation est la partie de la refactorisation. À ce stade, nous devons étape à partir de notre code et de prendre en compte la façon dont nous pouvons refactoriser notre application pour améliorer sa conception. L’étape de refactorisation est l’étape à laquelle nous réfléchissez bien à la meilleure façon de l’implémentation des modèles et les principes de conception de logiciels.

Nous sommes libres de modifier le code d’une façon qui nous choisir pour améliorer la conception du code. Nous avons un filet de tests unitaires qui nous empêche de compromettre les fonctionnalités existantes.

Actuellement, notre contrôleur de groupe est en désordre du point de vue d’une bonne conception logicielle. Le contrôleur de groupe contient un danger vous devez y mettre de validation et le code d’accès aux données. Pour ne pas violer le principe de responsabilité unique, nous devons séparer ces problèmes en différentes classes.

Notre classe de contrôleur groupe refactorisé figure dans la liste 9. Le contrôleur a été modifié pour utiliser la couche de service ContactManager. Il s’agit de la même couche de service que nous utilisons avec le contrôleur de Contact.

La liste 10 contient les méthodes de nouveau ajoutés à la couche de service ContactManager pour prendre en charge la validation, la liste et la création de groupes. L’interface IContactManagerService a été mis à jour pour inclure les nouvelles méthodes.

La liste 11 contient une nouvelle classe FakeContactManagerRepository qui implémente l’interface IContactManagerRepository. Contrairement à la classe EntityContactManagerRepository qui implémente l’interface IContactManagerRepository, notre nouvelle classe FakeContactManagerRepository ne communique pas avec la base de données. La classe FakeContactManagerRepository utilise une collection en mémoire en tant que proxy pour la base de données. Nous allons utiliser cette classe dans nos tests unitaires comme une couche de référentiel fausse.

**Liste 9 - Controllers\GroupController.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample9.vb)]

**La liste 10 - Controllers\ContactManagerService.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample10.vb)]

**La liste 11 - Controllers\FakeContactManagerRepository.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample11.vb)]


Modifier la IContactManagerRepository interface requiert utiliser pour implémenter les méthodes CreateGroup() et ListGroups() dans la classe EntityContactManagerRepository. La laziest et la plus rapide pour ce faire consiste à ajouter les méthodes stub ressemblent à ceci :

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample12.vb)]


Enfin, ces modifications à la conception de notre application nécessitent apporter certaines modifications au cours de nos tests unitaires. Nous devons maintenant utiliser le FakeContactManagerRepository lors de l’exécution des tests unitaires. La classe GroupControllerTest mis à jour est contenue dans la liste 12.

**Liste des 12 - Controllers\GroupControllerTest.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample13.vb)]

Une fois que nous avons apporté tous ces éléments, une fois encore, toutes les modifications de nos passe des tests unitaires. Nous avons terminé l’intégralité du cycle de rouge/vert/refactoriser. Nous avons implémenté les récits utilisateur en deux première. Nous disposons désormais la prise en charge des tests unitaires pour les conditions exprimées dans les récits utilisateur. L’implémentation du reste des récits utilisateur implique la répétition de la même cycle de rouge/vert/refactoriser.

## <a name="modifying-our-database"></a>Modification de notre base de données

Malheureusement, bien que nous avons satisfait toutes les conditions requises exprimés par nos tests unitaires, notre travail n’est pas terminé. Nous devons encore modifier notre base de données.

Nous devons créer une table de base de données de groupe. Procédez comme suit :

1. Dans la fenêtre Explorateur de serveurs, cliquez sur le dossier Tables et sélectionnez l’option de menu **ajouter une nouvelle Table**.
2. Entrez les deux colonnes décrites ci-dessous dans le Concepteur de tables.
3. Marquer la colonne Id comme une clé primaire et une colonne d’identité.
4. Enregistrer la nouvelle table avec les groupes de nom en cliquant sur l’icône de disquette.

<a id="0.12_table01"></a>


| **Nom de la colonne** | **Type de données** | **Autoriser les valeurs null** |
| --- | --- | --- |
| Id | int | False |
| Nom | nvarchar (50) | False |


Ensuite, nous devons supprimer toutes les données à partir de la table Contacts (dans le cas contraire, nous a gagné t être en mesure de créer une relation entre les tables de Contacts et groupes). Procédez comme suit :

1. Avec le bouton droit de la table Contacts et sélectionnez l’option de menu **afficher les données de Table**.
2. Supprimer toutes les lignes.

Ensuite, nous devons définir une relation entre la table de base de données de groupes et la table de base de données de Contacts existante. Procédez comme suit :

1. Double-cliquez sur la table Contacts dans la fenêtre de l’Explorateur de serveurs pour ouvrir le Concepteur de tables.
2. Ajouter une nouvelle colonne d’entiers à la table Contacts nommée GroupId.
3. Cliquez sur le bouton pour ouvrir la boîte de dialogue relations de clé étrangère de la relation (voir Figure 3).
4. Cliquez sur le bouton Ajouter.
5. Cliquez sur le bouton de sélection qui s’affiche en regard du bouton de la Table et une spécification de colonnes.
6. Dans la boîte de dialogue Tables et colonnes, sélectionnez les groupes en tant que la table de clé primaire et l’Id en tant que colonne de clé primaire. Sélectionnez des Contacts en tant que la table de clé étrangère et GroupId comme colonne de clé étrangère (voir Figure 4). Cliquez sur le bouton OK.
7. Sous **Spécification INSERT et UPDATE**, sélectionnez la valeur **Cascade** pour **supprimer la règle**.
8. Cliquez sur le bouton Fermer pour fermer la boîte de dialogue relations de clé étrangère.
9. Cliquez sur le bouton Enregistrer pour enregistrer les modifications apportées à la table Contacts.


[![Création d’une relation de table de base de données](iteration-6-use-test-driven-development-vb/_static/image3.jpg)](iteration-6-use-test-driven-development-vb/_static/image5.png)

**Figure 03**: création d’une relation de table de base de données ([cliquez pour afficher l’image en taille réelle](iteration-6-use-test-driven-development-vb/_static/image6.png))


[![Spécification de relations entre les tables](iteration-6-use-test-driven-development-vb/_static/image4.jpg)](iteration-6-use-test-driven-development-vb/_static/image7.png)

**Figure 04**: spécification de relations entre les tables ([cliquez pour afficher l’image en taille réelle](iteration-6-use-test-driven-development-vb/_static/image8.png))


### <a name="updating-our-data-model"></a>Mise à jour de notre modèle de données

Ensuite, nous devons mettre à jour notre modèle de données pour représenter la nouvelle table de base de données. Procédez comme suit :

1. Double-cliquez sur le fichier ContactManagerModel.edmx dans le dossier de modèles pour ouvrir le Concepteur d’entités.
2. Cliquez sur l’aire du concepteur et sélectionnez l’option de menu **modèle de mise à jour à partir de la base de données**.
3. Dans l’Assistant Mise à jour, sélectionnez les groupes de table et cliquez sur Terminer bouton (voir Figure 5).
4. Cliquez sur l’entité de groupes et sélectionnez l’option de menu **renommer**. Modifier le nom de la *groupes* entité *groupe* (singulier).
5. Cliquez sur la propriété de navigation de groupes qui s’affiche au bas de l’entité Contact. Modifier le nom de la *groupes* propriété de navigation *groupe* (singulier).


[![Mise à jour d’un modèle Entity Framework à partir de la base de données](iteration-6-use-test-driven-development-vb/_static/image5.jpg)](iteration-6-use-test-driven-development-vb/_static/image9.png)

**Figure 05**: mise à jour d’un modèle Entity Framework à partir de la base de données ([cliquez pour afficher l’image en taille réelle](iteration-6-use-test-driven-development-vb/_static/image10.png))


Après avoir effectué ces étapes, votre modèle de données représente les Contacts et les groupes de tables. Le Concepteur d’entités doit afficher les deux entités (voir Figure 6).


[![Entity Designer, affichage de groupe et Contact](iteration-6-use-test-driven-development-vb/_static/image6.jpg)](iteration-6-use-test-driven-development-vb/_static/image11.png)

**Figure 06**: Entity Designer, affichage de groupe et Contact ([cliquez pour afficher l’image en taille réelle](iteration-6-use-test-driven-development-vb/_static/image12.png))


### <a name="creating-our-repository-classes"></a>Création de Classes de notre référentiel

Ensuite, nous devons implémenter notre classe référentiel. Au cours de cette itération, nous avons ajouté plusieurs nouvelles méthodes à l’interface IContactManagerRepository lors de l’écriture de code pour satisfaire nos tests unitaires. La version finale de l’interface IContactManagerRepository est contenue dans la liste de 14.

**Liste 14 - Models\IContactManagerRepository.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample14.vb)]

Nous vous n avez encore t réellement implémentées une des méthodes liées à l’utilisation des groupes de contacts dans notre classe EntityContactManagerRepository réel. Actuellement, la classe EntityContactManagerRepository a les méthodes stub pour chacune des méthodes de contact de groupe répertoriés dans l’interface IContactManagerRepository. Par exemple, la méthode ListGroups() est actuellement ressemble à ceci :

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample15.vb)]

Les méthodes stub nous permis de compiler notre application et les tests unitaires. Toutefois, il est maintenant temps réellement implémenter ces méthodes. La version finale de la classe EntityContactManagerRepository est contenue dans la liste 13.

**Liste de 13 - Models\EntityContactManagerRepository.vb**

[!code-vb[Main](iteration-6-use-test-driven-development-vb/samples/sample16.vb)]

### <a name="creating-the-views"></a>Création de vues

Application ASP.NET MVC lorsque vous utilisez le moteur d’affichage par défaut ASP.NET. Par conséquent, ne pas créer des vues en réponse à un test unitaire particulier. Toutefois, car une application serait inutile sans vue, nous pouvons t terminer cette itération sans créer et modifier les vues contenues dans l’application Gestionnaire de contacts.

Nous devons créer les nouveaux affichages suivants pour la gestion des groupes de contacts (voir la Figure 7) :

- Views\Group\Index.aspx - affiche la liste des groupes de contacts
- Views\Group\Delete.aspx - écran de confirmation affiche pour la suppression d’un groupe de contacts


[![La vue de l’Index de groupe](iteration-6-use-test-driven-development-vb/_static/image7.jpg)](iteration-6-use-test-driven-development-vb/_static/image13.png)

**Figure 07**: affichage de l’Index de groupe ([cliquez pour afficher l’image en taille réelle](iteration-6-use-test-driven-development-vb/_static/image14.png))


Nous devons modifier les vues existantes suivantes afin qu’ils contiennent des groupes de contacts :

- Views\Home\Create.aspx
- Views\Home\Edit.aspx
- Views\Home\Index.aspx

Vous pouvez voir les vues modifiés en examinant l’application Visual Studio qui accompagne ce didacticiel. Par exemple, la Figure 8 illustre l’affichage de l’Index de contacts.


[![La vue Index des contacts](iteration-6-use-test-driven-development-vb/_static/image8.jpg)](iteration-6-use-test-driven-development-vb/_static/image15.png)

**Figure 08**: affichage de l’Index de Contact ([cliquez pour afficher l’image en taille réelle](iteration-6-use-test-driven-development-vb/_static/image16.png))


## <a name="summary"></a>Résumé

Dans cette itération, nous avons ajouté les nouvelles fonctionnalités à notre application Gestionnaire de contacts en suivant une méthodologie de conception d’application développement piloté par test. Nous avons commencé en créant un ensemble de récits utilisateur. Nous avons créé un ensemble de tests unitaires qui correspond aux exigences exprimées par les récits utilisateur. Enfin, nous avons rédigé juste assez de code pour répondre aux exigences exprimées par les tests unitaires.

Une fois que nous avons terminé l’écriture de suffisamment de code pour répondre aux exigences exprimées par les tests unitaires, nous avons mis à jour notre base de données et les vues. Nous avons ajouté une nouvelle table de groupes à notre base de données et mise à jour de notre modèle de données Entity Framework. Nous avons également créé et modifié un ensemble de vues.

Dans la prochaine itération--la dernière itération--nous réécrire notre application pour tirer parti d’Ajax. En tirant parti d’Ajax, nous allons améliorer la réactivité et les performances de l’application Gestionnaire de contacts.

>[!div class="step-by-step"]
[Précédent](iteration-5-create-unit-tests-vb.md)
[Suivant](iteration-7-add-ajax-functionality-vb.md)
