---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/strategies-for-database-development-and-deployment-vb
title: "Stratégies de développement de base de données et de déploiement (VB) | Documents Microsoft"
author: rick-anderson
description: "Lorsque vous déployez une application piloté par les données pour la première fois que vous pouvez aveugle copier la base de données dans l’environnement de développement à l’environnement de production. B...."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/23/2009
ms.topic: article
ms.assetid: 07b8905d-78ac-4252-97fb-8675b3fb0bbf
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/strategies-for-database-development-and-deployment-vb
msc.type: authoredcontent
ms.openlocfilehash: 8632ed2fe5c1a296747a0206de1c6f5c5bb59dd1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="strategies-for-database-development-and-deployment-vb"></a>Stratégies de développement de base de données et de déploiement (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le PDF](http://download.microsoft.com/download/C/3/9/C391A649-B357-4A7B-BAA4-48C96871FEA6/aspnet_tutorial10_DBDevel_vb.pdf)

> Lorsque vous déployez une application piloté par les données pour la première fois que vous pouvez aveugle copier la base de données dans l’environnement de développement à l’environnement de production. Mais effectuant un blind copie dans les déploiements suivants remplace toutes les données entrées dans la base de données de production. Au lieu de cela, déploiement d’une base de données consiste à répercuter les modifications apportées à la base de données de développement depuis le dernier déploiement sur la base de données de production. Ce didacticiel examine ces défis et propose des stratégies différentes pour vous aider à chronicling et appliquer les modifications apportées à la base de données depuis le dernier déploiement.


## <a name="introduction"></a>Introduction

Comme expliqué dans les didacticiels précédents, déploiement d’une application ASP.NET implique la copie le contenu pertinent à partir de l’environnement de développement à l’environnement de production. Déploiement n’est pas un événement unique, mais plutôt quelque chose qui se produit chaque fois qu’une nouvelle version du logiciel est publiée ou quand des bogues ou problèmes de sécurité ont été identifiés et résolus. Lors de la copie des pages ASP.NET, les images, les fichiers JavaScript et les autres fichiers de ce type dans l’environnement de production que vous n’avez pas besoin de vous préoccuper de ces fichiers ont été modifiés depuis le dernier déploiement. Copiez aveugle le fichier en production, en remplaçant le contenu existant. Malheureusement, cette simplicité n’étend pas au déploiement de la base de données.

Lorsque vous déployez une application piloté par les données pour la première fois que vous pouvez aveugle copier la base de données dans l’environnement de développement à l’environnement de production. Mais effectuant un blind copie dans les déploiements suivants remplace toutes les données entrées dans la base de données de production. Au lieu de cela, déploiement d’une base de données implique l’application de la *modifications* apportées à la base de données de développement depuis le dernier déploiement sur la base de données de production. Ce didacticiel examine ces défis et propose des stratégies différentes pour vous aider à chronicling et appliquer les modifications apportées à la base de données depuis le dernier déploiement.

## <a name="the-challenges-of-deploying-a-database"></a>Les défis de déploiement d’une base de données

Avant d’une application orientée données a été déployée pour la première fois, il n'existe qu’une seule base de données, à savoir la base de données dans l’environnement de développement, c’est pourquoi lors du déploiement d’une application orientée données pour la première fois que vous pouvez aveugle copier la base de données dans le environnement de développement à l’environnement de production. Mais une fois que l’application a été déployée. il existe deux copies de la base de données : un dans le développement et l’autre en production.

Entre les déploiements, les bases de données de développement et de production peuvent devenir désynchronisés. Alors que le schéma de s de base de données de production reste inchangé, le schéma s développement peut changer lors de l’ajout de nouvelles fonctionnalités. Vous pouvez ajouter ou supprimer des colonnes, tables, vues ou procédures stockées. Il peut également être des données importantes qui sont ajoutées à la base de données de développement. De nombreuses applications pilotées par les données contiennent des tables de recherche remplis avec des données codées en dur, spécifiques à l’application qui ne sont pas modifiables par l’utilisateur. Par exemple, un site Web de vente aux enchères peut avoir une liste déroulante de choix qui décrivent la condition de l’élément en cours d’enchère : nouveau, comme nouvelle, efficace et équitable. Au lieu de coder en dur ces options directement dans la liste déroulante, il est généralement préférable de les placer dans une table de base de données. Si, pendant le développement, une nouvelle condition nommée médiocre est ajoutée à la table puis lors du déploiement de l’application de ce même enregistrement doit être ajouté à la table de recherche dans la base de données de production.

Dans l’idéal, le déploiement de la base de données impliquerait copie de la base de données de développement à la production. Mais n’oubliez pas qu’une fois que vous avez déployé l’application et la reprise de développement, la base de données de production est en cours rempli avec les données réelles provenant d’utilisateurs réels. Par conséquent, si vous deviez copier simplement la base de données de développement pour le prochain déploiement de production vous remplacer la base de données de production et perdre les données existantes. Le résultat net est que le déploiement de la base de données se résume à appliquer les modifications apportées à la base de données de développement depuis le dernier déploiement.

Étant donné que le déploiement d’une base de données implique d’appliquer les modifications dans le schéma et, éventuellement, les données depuis le dernier déploiement, un historique des modifications doit être conservé (ou déterminé au moment de déployer) afin que ces modifications peuvent être appliquées sur la production. Il existe de nombreuses techniques pour gérer et appliquer les modifications au modèle de données.

### <a name="defining-the-baseline"></a>Définition de la ligne de base

Pour conserver les modifications apportées à votre base de données s application, vous devez avoir un état de départ, une ligne de base à laquelle les modifications sont appliquées à. Une approche consiste à l’état de départ peut être une base de données vide sans tables, vues ou procédures stockées. Une ligne de base des résultats dans un journal de changement important, car il doit inclure la création de toutes les tables de base de données s, vues et des procédures stockées, ainsi que les modifications apportées après le déploiement initial. À l’autre extrémité du spectre, vous pouvez définir la ligne de base en tant que la version de la base de données est d’abord déployée dans l’environnement de production. Ce choix aboutit à un journal des modifications beaucoup plus petit, car elle inclut uniquement les modifications apportées à la base de données après le premier déploiement. Il s’agit de l’approche que je préfère. Et bien sûr vous pouvez choisir un milieu plus de l’approche de la feuille de route, définition de la ligne de base comme une point entre la création initiale de la base de données et lors du premier déploiement de la base de données.

Une fois que vous avez choisi une ligne de base envisagez de générer un script SQL qui peut être exécuté pour recréer la version de ligne de base. Un tel script permet de recréer rapidement la version de ligne de base de la base de données. Cette fonctionnalité est particulièrement utile dans les projets de grande taille, où il peut y avoir plusieurs développeurs travaillent sur le projet ou des environnements supplémentaires, tels que les tests ou intermédiaire, que chaque besoin de leur propre copie de la base de données.

Il existe une variété d’outils à votre disposition pour générer un script SQL de la version de ligne de base. À partir de SQL Server Management Studio (SSMS) vous pouvez avec le bouton droit sur la base de données, accédez au sous-menu tâches et choisissez l’option Générer des Scripts. Cette opération lance l’Assistant de Script, vous pouvez indiquer pour générer un fichier qui contient les commandes SQL pour créer des objets de s de votre base de données. Une autre option consiste à l’Assistant Publication de base de données, ce qui peut générer les commandes SQL non seulement créer le schéma de base de données, mais également les données dans les tables de base de données. L’Assistant Publication de base de données a été examiné en détail dans les *déploiement d’une base de données* didacticiel. Quel que soit l’outil que vous utilisez, à la fin, vous devez disposer d’un fichier de script que vous pouvez utiliser pour recréer la version de ligne de base de votre base de données, en cas de besoin surviennent.

## <a name="documenting-the-database-changes-in-prose"></a>Documenter les modifications de base de données dans le texte principal

Pour conserver un journal des modifications apportées au modèle de données pendant la phase de développement, le plus simple consiste à enregistrer les modifications dans prose. Par exemple, si pendant le développement d’une application déjà déployée, vous ajoutez une nouvelle colonne à la `Employees` table, de supprimer une colonne de la `Orders` de table et ajouter une nouvelle table (`ProductCategories`), vous devez mettre à jour un fichier texte ou un document Microsoft Word l’historique des suivantes :

<a id="0.8_table01"></a>


| **Date de modification** | **Modifier les détails** |
| --- | --- |
| 2009-02-03: | Colonne ajoutée `DepartmentID` (`int`, non NULL) pour le `Employees` table. Ajouter une contrainte de clé étrangère à partir de `Departments.DepartmentID` à `Employees.DepartmentID`. |
| 2009-02-05: | Colonne supprimé `TotalWeight` à partir de la `Orders` table. Données déjà capturées dans associés `OrderDetails` enregistrements. |
| 2009-02-12: | Créé le `ProductCategories` table. Il existe trois colonnes : `ProductCategoryID` (`int`, `IDENTITY`, `NOT NULL`), `CategoryName` (`nvarchar(50)`, `NOT NULL`), et `Active` (`bit`, `NOT NULL`). Ajouter une contrainte de clé primaire à `ProductCategoryID`et une valeur par défaut de 1 à `Active`. |


Il existe un nombre d’inconvénients à cette approche. Pour commencer, il n’est sans espoir d’automatisation. À tout moment ces modifications doivent être appliquées à une base de données - telles que quand l’application est déployée, un développeur doit implémenter manuellement chacune, à la fois. En outre, si vous avez besoin reconstruire une version particulière de la base de données à partir de la ligne de base à l’aide du journal des modifications, cette opération donc prendra plus de temps à mesure que la taille du journal augmente. Un autre inconvénient de cette méthode est que la clarté et le niveau de détail de chaque entrée de journal des modifications est de gauche à la personne que l’enregistrement de la modification. Dans une équipe avec plusieurs développeurs certaines peuvent rendre plus détaillées, plus lisibles et plus précisément les entrées que d’autres. En outre, les fautes de frappe et autres erreurs de saisie des données relatives à l’homme sont possibles.

Le principal avantage de documenter les modifications de base de données dans la grandeur est sa simplicité. Vous n’avez pas connaissance de la nécessité t avec la syntaxe SQL pour la création et modification d’objets de base de données. Au lieu de cela, vous pouvez enregistrer les modifications dans le texte principal et les implémenter via l’interface utilisateur graphique de s SQL Server Management Studio.

En conservant votre journal des modifications dans le texte principal n’est pas, il est vrai, très sophistiquée et gagné / fonctionne bien avec certains projets, telles que celles qui sont de grande taille dans la portée, sont souvent modifiés pour le modèle de données, ou impliquant plusieurs développeurs. Mais j’ai vu cette approche fonctionne très bien dans les projets de petite taille, adeptes qui ont uniquement des modifications au modèle de données occasionnelles et où le développeur individuelle ne dispose pas de solides dans la syntaxe SQL pour la création et modification d’objets de base de données.

> [!NOTE]
> Alors que les informations contenues dans le journal des modifications soient techniquement, nécessaire uniquement jusqu’au moment de déployer, je vous recommande de conserver un historique des modifications. Mais plutôt que de la gestion d’un seul, en constante augmentation fichier journal des modifications, envisagez de mettre en un fichier journal de modification différente pour chaque version de la base de données. En général, vous souhaiterez version de la base de données chaque fois qu’il est déployé. En conservant un journal des journaux de changement de n’importe quelle version de la base de données en exécutant les scripts du journal de modification à partir de la version 1 peut, en commençant à partir de la ligne de base, recréer et à se poursuivre jusqu'à ce que vous atteigniez la version que vous devrez recréer.


## <a name="recording-the-sql-change-statements"></a>Enregistrer les instructions de modification SQL

L’inconvénient principal de la maintenance du journal des modifications dans prose indique l’absence d’automation. Dans l’idéal, l’application des modifications de base de données à la base de données de production au moment de déployer serait aussi simple que de cliquer sur un bouton pour exécuter un script au lieu de devoir effectuer manuellement une liste d’instructions. Cette automatisation est possible en conservant un journal des modifications qui contient les commandes SQL utilisés pour modifier le modèle de données.

La syntaxe SQL comprend des instructions pour créer et modifier les divers objets de base de données. Par exemple, le [ *l’instruction CREATE TABLE*](https://msdn.microsoft.com/en-us/library/ms174979.aspx), lors de l’exécution, crée une table avec les colonnes spécifiées et les contraintes. Le [ *instruction ALTER TABLE* ](https://msdn.microsoft.com/en-us/library/ms190273.aspx) modifie une table existante, l’ajout, la suppression ou la modification des colonnes ou des contraintes. Il existe également des instructions pour créer, modifier et supprimer des index, vues, fonctions définies par l’utilisateur, procédures stockées, déclencheurs et autres objets de base de données.

Revenir à notre précédent exemple, l’image que pendant le développement d’une application déjà déployée, vous ajoutez une nouvelle colonne à la `Employees` table, de supprimer une colonne de la `Orders` de table et ajouter une nouvelle table (`ProductCategories`). Ces actions entraînerait un fichier journal des modifications avec les commandes SQL suivantes :

[!code-sql[Main](strategies-for-database-development-and-deployment-vb/samples/sample1.sql)]

En exécutant un push de ces modifications à la base de données de production au moment de déployer est une opération en un clic : ouvrez SQL Server Management Studio, vous connecter à votre base de données de production, ouvrez une fenêtre de nouvelle requête, collez le contenu du journal des modifications, cliquez sur Exécuter pour exécuter le script.

## <a name="using-a-comparison-tool-to-synchronize-the-data-models"></a>À l’aide d’un outil de comparaison pour synchroniser les modèles de données

Documenter les modifications de base de données dans la grandeur est facile, mais les modifications de mise en œuvre requiert un développeur effectuer chaque modification sur la base de données de production une à la fois. documenter les commandes SQL de modification rend l’implémentation de ces modifications sur la base de données de production en tant que simple et rapide qu’un clic sur un bouton, mais nécessite d’apprentissage et de maîtrise les instructions SQL et la syntaxe pour la création et modification d’objets de base de données. Outils de comparaison de base de données tirer le meilleur de ces deux approches et ignorer le pire.

Outil de comparaison de base de données compare le schéma ou les données de deux bases de données et affiche un rapport de synthèse affiche la différence entre les bases de données. Puis, en cliquant sur un bouton, vous pouvez générer les commandes SQL pour la synchronisation d’un ou plusieurs objets de base de données. En bref, vous pouvez utiliser un outil de comparaison de base de données à comparer le développement et les bases de données à déployer-temps, générant un fichier qui contient l’instruction SQL de commandes, quand elle est exécutée, s’applique les modifications au schéma de base de données s production donc qu’il reflète le schéma de base de données s de développement.

Il existe une variété d’outils de comparaison de base de données de tiers proposées par d’autres fournisseurs. Un exemple est [ *SQL Compare*](http://www.red-gate.com/products/SQL_Compare/), par [ *Red Gate Software*](http://www.red-gate.com/). Permettent de s décrivent le processus de l’utilisation de SQL Compare pour comparer et synchroniser les schémas de bases de données de développement et de production.

> [!NOTE]
> Au moment de la rédaction de cet la version actuelle de SQL Compare était version 7.1, avec l’Édition Standard d’évaluation des coûts 395 $. Vous pouvez suivre son déroulement en téléchargeant une version d’évaluation gratuite de 14 jours.


Démarrage de SQL Compare la boîte de dialogue des projets de comparaison s’ouvre et affiche les projets SQL Compare enregistrés. Créer un nouveau projet. Cette opération lance l’Assistant de Configuration de projet, qui vous invite à entrer pour plus d’informations sur les bases de données à comparer (voir Figure 1). Entrez les informations pour les bases de données d’environnement de développement et de production.


[![Comparer le développement et les bases de données de Production](strategies-for-database-development-and-deployment-vb/_static/image2.jpg)](strategies-for-database-development-and-deployment-vb/_static/image1.jpg)

**Figure 1**: comparer le développement et les bases de données de Production ([cliquez pour afficher l’image en taille réelle](strategies-for-database-development-and-deployment-vb/_static/image3.jpg))


> [!NOTE]
> Si votre base de données des environnement de développement est un fichier de base de données SQL Express Edition dans la `App_Data` dossier de votre site Web que vous devez inscrire la base de données sur le serveur de base de données SQL Server Express pour le sélectionner dans la boîte de dialogue indiquée dans la Figure 1. Le moyen le plus simple pour y parvenir consiste à ouvrir SQL Server Management Studio (SSMS), connectez-vous au serveur de base de données SQL Server Express et attacher la base de données. Si vous n’avez pas installé sur votre ordinateur SSMS vous pouvez télécharger et installer la version gratuite [ *base SQL Server 2008 Management Studio version*](https://www.microsoft.com/downloads/details.aspx?FamilyId=7522A683-4CB2-454E-B908-E805E9BD4E28&amp;displaylang=en).


En plus de sélectionner les bases de données à comparer, vous pouvez également spécifier divers paramètres de comparaison à partir de l’onglet Options. Une option, que vous pouvez souhaiter activer est « Ignorer contraintes et index noms. » Rappelez-vous que dans le didacticiel précédent nous avons ajouté que les objets de base de données pour les bases de données de développement et de production des services de l’application. Si vous avez utilisé le `aspnet_regsql.exe` outil pour créer ces objets sur la base de données de production, vous obtenez la clé primaire et les noms de contraintes uniques sont différents entre les bases de données de développement et de production. Par conséquent, SQL Compare marquera toutes les tables de services d’application comme différentes. Vous pouvez soit laisser les « ignorer contraintes et index names » est désactivée et synchroniser les noms de contrainte, ou demandez au SQL Compare pour ignorer ces différences.

Après avoir sélectionné les bases de données à comparer (et examen des options de comparaison), cliquez sur le bouton comparer pour commencer la comparaison. Sur les prochaines secondes, SQL Compare examine les schémas des deux bases de données et génère un rapport de la façon dont elles diffèrent. Je ve apporté délibérément des modifications à la base de données de développement pour afficher la façon dont ces différences sont indiquées dans l’interface SQL Compare. Comme le montre la Figure 2, je ve ajouté un `BirthDate` colonne à la `Authors` table, supprimé le `ISBN` colonne à partir de la `Books` de table et ajouter une nouvelle table, `Ratings`, qui est destinée à permettre aux utilisateurs visitant le taux de site les livres passés en revue.

> [!NOTE]
> Les modifications de modèle de données effectuées dans ce didacticiel a été effectuées pour illustrer l’utilisation d’un outil de comparaison de base de données. Vous trouverez pas ces modifications dans la base de données dans les didacticiels futures.


[![SQL Compare répertorie les différences entre le développement et les bases de données de Production](strategies-for-database-development-and-deployment-vb/_static/image5.jpg)](strategies-for-database-development-and-deployment-vb/_static/image4.jpg)

**Figure 2**: SQL Compare répertorie les différences entre le développement et les bases de données de Production ([cliquez pour afficher l’image en taille réelle](strategies-for-database-development-and-deployment-vb/_static/image6.jpg))


SQL Compare divise les objets de base de données en groupes, vous montrant rapidement les objets qui existent dans les deux bases de données mais sont différent, qui existent dans une base de données, mais pas l’autre et quels objets sont identiques. Comme vous pouvez le voir, il existe deux objets qui existent dans les deux bases de données, mais sont différents : le `Authors` table ayant une colonne ajoutée, et le `Books` table, qui contenait un supprimé. Il existe un objet qui existe uniquement dans le développement de base de données, à savoir nouvellement créé `Ratings` table. Et il existe des 117 objets sont identiques dans les deux bases de données.

Sélection d’un objet de base de données affiche la fenêtre différences de SQL, qui affiche la différence entre ces objets. Met en surbrillance de la fenêtre SQL différences, affichée en bas dans la Figure 2, qui les `Authors` table dans la base de données de développement a la `BirthDate` colonne, qui est introuvable dans le `Authors` table sur la base de données de production.

Après avoir examiné les différences et en sélectionnant les objets que vous souhaitez synchroniser, l’étape suivante consiste à générer les commandes SQL nécessaires pour mettre à jour le schéma de s de base de données de production pour correspondre à la base de données de développement. Pour cela, l’Assistant de synchronisation. L’Assistant synchronisation confirme que pour synchroniser des objets et résume l’action planifier (voir Figure 3). Vous pouvez synchroniser les bases de données immédiatement ou générer un script avec les commandes SQL qui peut être exécutée à tout moment.


[![Utilisez l’Assistant de synchronisation pour synchroniser vos schémas de bases de données](strategies-for-database-development-and-deployment-vb/_static/image8.jpg)](strategies-for-database-development-and-deployment-vb/_static/image7.jpg)

**Figure 3**: utiliser l’Assistant de synchronisation pour synchroniser vos schémas de bases de données ([cliquez pour afficher l’image en taille réelle](strategies-for-database-development-and-deployment-vb/_static/image9.jpg))


Outils de comparaison de base de données telles que Red Gate Software s SQL Compare rendre le schéma de base de données de développement à la base de données de production aussi simple que le point, cliquez sur Appliquer les modifications.

> [!NOTE]
> Compare et la synchronisation des deux bases de données SQL Compare *schémas*. Malheureusement, il ne comparer et synchroniser les données dans les tables de deux bases de données. Red Gate Software offre un produit nommé [ *de comparaison de données SQL* ](http://www.red-gate.com/products/SQL_Data_Compare/) qui compare et synchronise les données entre deux bases de données, mais il est un produit distinct à partir de SQL Compare et les coûts d’un autre 395 $.


## <a name="taking-the-application-offline-during-deployment"></a>Avec l’Application en mode hors connexion pendant le déploiement

Comme nous avons vu dans ces didacticiels, déploiement est un processus qui implique plusieurs étapes : copie les pages ASP.NET, les pages maîtres, CSS fichiers, JavaScript fichiers, images et autre contenu nécessaire à partir de l’environnement de développement à la production environnement ; copie les informations de configuration spécifiques à l’environnement de production, si nécessaire ; et appliquer les modifications au modèle de données depuis le dernier déploiement. Selon le nombre de fichiers et la complexité de votre base de données, ces étapes peuvent prendre de quelques secondes à quelques minutes. Pendant cette fenêtre de l’application web est en cours de conception et les utilisateurs visitant le site peuvent rencontrer des erreurs ou un comportement inattendu.

Lors du déploiement d’un site Web, il est préférable d’adopter l’application web « hors connexion » jusqu'à ce que le déploiement est terminé. Mise hors connexion de l’application (et sa mise en sauvegarder une fois terminé le processus de déploiement) est aussi simple que le téléchargement d’un fichier, puis leur suppression. À partir d’ASP.NET 2.0, la simple présence d’un fichier nommé `app_offline.htm` dans le s application répertoire racine prend tout le site Web « hors connexion ». Toute demande d’une page ASP.NET sur ce site est automatiquement répondu avec le contenu de la `app_offline.htm` fichier. Une fois que ce fichier est supprimé, l’application revient en ligne.

Prendre une application en mode hors connexion pendant le déploiement, puis, est aussi simple que de télécharger un `app_offline.htm` fichier à l’environnement de production s racine active avant de commencer le processus de déploiement et puis en le supprimant (ou en le renommant à quelque chose d’autre) une fois du déploiement est terminée. Pour plus d’informations sur cette technique, consultez l’article de s John Peterson, en prenant un [ *ASP.NET Application en mode hors connexion*](http://www.15seconds.com/issue/061207.htm).

## <a name="summary"></a>Résumé

Le principal défi de déploiement d’une application orientée données tourne autour de déploiement de la base de données. Car il existe deux versions de la base de données - un dans l’environnement de développement et l’autre dans l’environnement de production ces schémas de deux bases de données peuvent devenir désynchronisés que des nouvelles fonctionnalités sont ajoutées dans le développement. Nouveautés plus, étant donné que la base de données de production comme rempli avec les données réelles provenant d’utilisateurs réels, Impossible de remplacer la base de données de production avec la base de données de développement modifié comme vous pouvez le faire lorsque vous déployez les fichiers qui composent l’application (les pages ASP.NET, fichiers image et ainsi de suite). Au lieu de cela, déploiement d’une base de données implique la mise en œuvre de l’ensemble précis des modifications apportées à la base de données de développement sur la base de données de production depuis le dernier déploiement.

Ce didacticiel abordé trois techniques pour la gestion et appliquer un journal des modifications de la base de données. L’approche la plus simple consiste à enregistrer des modifications dans prose. Même si cela risquerait mise en œuvre de ces modifications sur la base de données de production un processus manuel, il ne nécessite pas de connaissances des commandes SQL pour la création et modification d’objets de base de données. Une approche plus sophistiquée et un qui est beaucoup plus aisée en plus des projets ou avec plusieurs développeurs, consiste à enregistrer les modifications comme une série de commandes SQL. Cela empresse considérablement le déploiement de ces modifications à la base de données cible. Le meilleur des deux approches peut être obtenu à l’aide d’un outil de comparaison de base de données, telles que des s Red Gate Software SQL Compare.

Ce didacticiel conclut notre le focus sur le déploiement d’une application pilotée par les données. L’ensemble suivant de didacticiels examine comment répondre aux erreurs dans l’environnement de production. Nous allons examiner comment afficher une page d’erreur convivial plutôt au lieu de l’écran de décès jaune. Et nous verrons comment consigner les détails de s erreur et vous informe lorsque ces erreurs se produisent.

Bonne programmation !

>[!div class="step-by-step"]
[Précédent](configuring-a-website-that-uses-application-services-vb.md)
[Suivant](displaying-a-custom-error-page-vb.md)
