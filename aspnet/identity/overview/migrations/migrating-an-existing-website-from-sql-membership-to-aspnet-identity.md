---
uid: identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity
title: "Migration d’un site Web existant à partir de l’appartenance SQL pour ASP.NET Identity | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel illustre les étapes pour migrer une application web existante avec les données d’utilisateur et rôle créées à l’aide de l’appartenance de SQL vers la nouvelle identité ASP.NET s..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/19/2014
ms.topic: article
ms.assetid: 220d3d75-16b2-4240-beae-a5b534f06419
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: b88cd54040c02c977a83e20d7af7fda4fff969c1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="migrating-an-existing-website-from-sql-membership-to-aspnet-identity"></a>Migration d’un site Web existant à partir de l’appartenance SQL pour ASP.NET Identity
====================
par [Rick Anderson](https://github.com/Rick-Anderson), [Suhas Joshi](https://github.com/suhasj)

> Ce didacticiel illustre les étapes pour migrer une application web existante avec les données d’utilisateur et rôle créées à l’aide de l’appartenance de SQL vers le nouveau système d’identité ASP.NET. Cette approche implique la modification du schéma de base de données existante à celle requise par les ASP.NET Identity et le raccordement dans les classes ancien/nouveau. Une fois que vous adoptez cette approche, une fois que la migration de votre base de données, mises à jour ultérieures de l’identité seront gérées sans effort.


Pour ce didacticiel, nous allons prendre un modèle d’application web (Web Forms) créé à l’aide de Visual Studio 2010 pour créer des données utilisateur et le rôle. Nous allons ensuite utiliser des scripts SQL pour migrer la base de données aux tables requises par le système d’identité. Nous allons ensuite installer les packages NuGet et ajouter de nouvelles pages de gestion de compte qui utilisent le système d’identité pour la gestion de l’appartenance au. Comme un test de migration, les utilisateurs créés à l’aide de l’appartenance SQL doivent être en mesure de se connecter et nouveaux utilisateurs doivent être en mesure d’enregistrer. Vous pouvez trouver l’exemple complet [ici](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SQLMembership-Identity-OWIN/). Voir aussi [migration de l’appartenance ASP.NET vers ASP.NET Identity](http://travis.io/blog/2015/03/24/migrate-from-aspnet-membership-to-aspnet-identity.html).

## <a name="getting-started"></a>Bien démarrer

### <a name="creating-an-application-with-sql-membership"></a>Création d’une application avec un abonnement de SQL

1. Nous devons commencer par une application existante qui utilise l’appartenance SQL et qui possède des données utilisateur et le rôle. Pour les besoins de cet article, nous allons créer une application web dans Visual Studio 2010.

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image1.jpg)
2. À l’aide de l’outil de Configuration ASP.NET, créer 2 utilisateurs : **oldAdminUser** et **oldUser.**

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image1.png)

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image2.jpg)
3. Créer un rôle nommé Admin et ajoutez 'oldAdminUser' en tant qu’utilisateur appartenant à ce rôle.

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image2.png)
4. Créez une section de l’administrateur du site avec un Default.aspx. Définir la balise d’autorisation dans le fichier web.config pour activer l’accès uniquement aux utilisateurs dans les rôles d’administrateur. Trouverez ici plus d’informations [https://www.asp.net/web-forms/tutorials/security/roles/role-based-authorization-cs](../../../web-forms/overview/older-versions-security/roles/role-based-authorization-cs.md)

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image3.png)
5. Afficher la base de données dans l’Explorateur de serveurs pour comprendre les tables créées par le système d’appartenance SQL. Les données de connexion d’utilisateur sont stockées dans le compte aspnet\_utilisateurs et aspnet\_l’appartenance des tables, tandis que les données de rôle sont stockées dans le compte aspnet\_table Roles. Informations sur les utilisateurs dans les rôles sont stockées dans le compte aspnet\_UsersInRoles table. Pour la gestion de l’abonnement de base, il est suffisant porter les informations contenues dans les tables ci-dessus pour le système d’identité ASP.NET.

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image4.png)

### <a name="migrating-to-visual-studio-2013"></a>Migration vers Visual Studio 2013

1. Installer Visual Studio Express 2013 pour le Web ou de Visual Studio 2013 avec le [dernières mises à jour](https://www.microsoft.com/en-us/download/details.aspx?id=44921).
2. Ouvrez le projet ci-dessus dans votre version installée de Visual Studio. Si SQL Server Express n’est pas installé sur l’ordinateur, une invite s’affiche lorsque vous ouvrez le projet, étant donné que la chaîne de connexion utilise SQL Express. Vous pouvez choisir d’installer SQL Express comme contourner modification de la chaîne de connexion à la base de données locale. Pour cet article, nous allons le modifier à la base de données locale.
3. Ouvrez le fichier web.config et modifier la chaîne de connexion. SQLExpess à v11.0 de (LocalDb). Supprimez ' User Instance = true' à partir de la chaîne de connexion.

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image3.jpg)
4. Ouvrez l’Explorateur de serveurs et vérifiez que le schéma de table et les données peuvent être observées.
5. Le système d’identité ASP.NET fonctionne avec la version 4.5 ou ultérieure du framework. Recibler l’application à 4.5 ou version ultérieure.

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image5.png)

    Générez le projet pour vérifier qu’il n’y a aucune erreur.

### <a name="installing-the-nuget-packages"></a>Installer les packages Nuget

1. Dans l’Explorateur de solutions, cliquez sur le projet &gt; **gérer les Packages NuGet**. Dans la zone de recherche, entrez « Asp.net Identity ». Sélectionnez le package dans la liste des résultats et cliquez sur Installer. Acceptez le contrat de licence en cliquant sur le bouton « J’accepte ». Notez que ce package installe les packages de dépendance : EntityFramework et Microsoft ASP.NET Identity Core. De même installer les packages suivants (ignorer les 4 derniers packages OWIN si vous ne souhaitez pas activer le journal dans OAuth) :

    - Microsoft.AspNet.Identity.Owin
    - Microsoft.Owin.Host.SystemWeb
    - Microsoft.Owin.Security.Facebook
    - Microsoft.Owin.Security.Google
    - Microsoft.Owin.Security.MicrosoftAccount
    - Microsoft.Owin.Security.Twitter

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image6.png)

### <a name="migrate-database-to-the-new-identity-system"></a>Migrer la base de données vers le nouveau système d’identité

L’étape suivante consiste à migrer la base de données existante à un schéma requis par le système d’identité ASP.NET. Pour faire cela nous exécuter SQL script qui a un ensemble de commandes pour créer des tables et de migrer les informations utilisateur existantes vers les nouvelles tables. Le fichier de script se trouve [ici](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SQLMembership-Identity-OWIN/Migrations.sql).

Ce fichier de script est spécifique à cet exemple. Si le schéma pour les tables créées à l’aide de l’appartenance SQL personnalisée ou modifié les scripts devoir être modifié en conséquence.

### <a name="how-to-generate-the-sql-script-for-schema-migration"></a>Comment générer le script SQL pour la migration de schéma

Pour les classes d’identité ASP.NET fonctionne directement avec les données des utilisateurs existants, nous devons faire migrer le schéma de base de données à celui requis par ASP.NET Identity. Pour cela, nous pouvons en ajoutant de nouvelles tables en copiant les informations existantes à ces tables. Par défaut, ASP.NET Identity utilise EntityFramework pour mapper les classes de modèle d’identité à la base de données à stocker/récupérer des informations. Ces classes de modèle implémentent les interfaces d’identité de base définissant les objets utilisateur et rôle. Les tables et les colonnes dans la base de données sont basés sur ces classes du modèle. Les classes du modèle EntityFramework dans v2.1.0 d’identité et leurs propriétés sont définis comme ci-dessous

| **IdentityUser** | **Type** | **IdentityRole** | **IdentityUserRole** | **IdentityUserLogin** | **IdentityUserClaim** |
| --- | --- | --- | --- | --- | --- |
| Id | chaîne | Id | RoleId | ProviderKey | Id |
| Utilisateur | chaîne | Nom | ID d’utilisateur | ID d’utilisateur | ClaimType |
| PasswordHash | chaîne |  |  | LoginProvider | ClaimValue |
| SecurityStamp | chaîne |  |  |  | Utilisateur\_Id |
| Messagerie | chaîne |  |  |  |  |
| EmailConfirmed | bool |  |  |  |  |
| Numéro de téléphone | chaîne |  |  |  |  |
| PhoneNumberConfirmed | bool |  |  |  |  |
| LockoutEnabled | bool |  |  |  |  |
| LockoutEndDate | DateTime |  |  |  |  |
| AccessFailedCount | int |  |  |  |  |

Nous devons disposer des tables pour chacun de ces modèles avec des colonnes correspondant aux propriétés. Le mappage entre les classes et les tables est défini dans le `OnModelCreating` méthode de la `IdentityDBContext`. Il s’agit de la configuration de la méthode d’API fluent et peut trouver plus d’informations [ici](https://msdn.microsoft.com/en-us/data/jj591617.aspx). La configuration pour les classes est comme indiqué ci-dessous

| **Classe** | **Table** | **Clé primaire** | **Clé étrangère** |
| --- | --- | --- | --- |
| IdentityUser | AspnetUsers | Id |  |
| IdentityRole | AspnetRoles | Id |  |
| IdentityUserRole | AspnetUserRole | UserId + RoleId | Utilisateur\_Id -&gt;AspnetUsers RoleId -&gt;AspnetRoles |
| IdentityUserLogin | AspnetUserLogins | ProviderKey + UserId + LoginProvider | UserId -&gt;AspnetUsers |
| IdentityUserClaim | AspnetUserClaims | Id | Utilisateur\_Id -&gt;AspnetUsers |

Avec ces informations nous pouvons créer des instructions SQL pour créer des tables. Nous pouvons écrire chaque instruction individuellement ou générer la totalité du script à l’aide des commandes EntityFramework PowerShell que nous pouvons ensuite modifier en fonction des besoins. Pour ce faire, dans Visual Studio open le **Package Manager Console** à partir de la **vue** ou **outils** menu

- Exécutez la commande « Enable-Migrations » pour activer les migrations de EntityFramework.
- Exécutez la commande « Add-migration initial », qui crée le code de la configuration initiale pour créer la base de données en C# / VB.
- L’étape finale consiste à exécuter « base de données de mise à jour – Script « commande qui génère le script SQL basé sur les classes du modèle.

Ce script de génération de base de données peut être utilisé comme un point de départ où nous allons apporter des modifications supplémentaires à ajouter de nouvelles colonnes et copier des données. L’avantage de cela est que nous générons le `_MigrationHistory` table qui est utilisé par EntityFramework pour modifier le schéma de base de données lorsque le modèle de modification pour les versions futures de versions de l’identité des classes. 

Les informations utilisateur d’appartenance SQL avaient d’autres propriétés en plus de celles de la classe de modèle identité utilisateur à savoir par courrier électronique, la tentatives de mot de passe, la dernière date de connexion, la dernière date de verrouillage etc. Il s’agit des informations utiles et nous qu’il doit être reportée sur le système d’identité. Cela est possible en ajoutant des propriétés supplémentaires pour le modèle utilisateur et les mapper vers les colonnes de table dans la base de données. Vous pouvez le faire en ajoutant une classe qui sous-classe la `IdentityUser` modèle. Nous pouvons ajouter les propriétés de cette classe personnalisée et modifier le script SQL pour ajouter les colonnes correspondantes lors de la création de la table. Le code de cette classe est décrit plus loin dans l’article. Le script SQL pour créer la `AspnetUsers` après l’ajout de nouvelles propriétés serait

[!code-sql[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample1.sql)]

Ensuite, nous avons besoin copier les informations existantes à partir de la base de données d’appartenance SQL aux tables nouvellement ajoutés pour l’identité. Cela est possible via SQL en copiant les données directement à partir d’une table vers une autre. Pour ajouter des données dans les lignes de table, nous utilisons le `INSERT INTO [Table]` construire. Pour copier à partir d’une autre table, nous pouvons utiliser le `INSERT INTO` instruction avec la `SELECT` instruction. Pour obtenir toutes les informations utilisateur que nous avons besoin pour interroger le *aspnet\_utilisateurs* et *aspnet\_appartenance* tables et copier les données à la *AspNetUsers*table. Nous utilisons le `INSERT INTO` et `SELECT` avec `JOIN` et `LEFT OUTER JOIN` instructions. Pour plus d’informations sur l’interrogation et la copie des données entre les tables, consultez [cela](https://technet.microsoft.com/en-us/library/ms190750%28v=sql.105%29.aspx) lien. En outre les tables AspnetUserLogins et AspnetUserClaims sont vides pour commencer car il n’existe aucune information de l’appartenance SQL qui correspond à ce par défaut. La seule information copiée est pour les utilisateurs et les rôles. Pour le projet créé dans les étapes précédentes, la requête SQL pour copier les informations dans la table users serait

[!code-sql[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample2.sql)]

Dans l’instruction SQL ci-dessus, les informations relatives à chaque utilisateur à partir de la *aspnet\_utilisateurs* et *aspnet\_appartenance* tables est copiée dans les colonnes de la  *AspnetUsers* table. La seule modification effectuée ici est lorsque nous copions le mot de passe. Étant donné que l’algorithme de chiffrement des mots de passe de l’appartenance SQL utilisé 'PasswordSalt' et « PasswordFormat », nous copions qui trop en même temps que le mot de passe haché afin qu’il peut être utilisé pour déchiffrer le mot de passe par identité. Cela est expliqué plus loin dans l’article lorsque raccorder un hacheur de mot de passe personnalisé. 

Ce fichier de script est spécifique à cet exemple. Pour les applications qui ont des tables supplémentaires, les développeurs peuvent suivre une approche similaire pour ajouter des propriétés supplémentaires sur la classe de modèle d’utilisateur et les mapper aux colonnes dans la table AspnetUsers. Pour exécuter le script,

1. Ouvrez l’Explorateur de serveurs. Développez la connexion « ApplicationServices » pour afficher les tables. Cliquez avec le bouton droit sur le nœud Tables et sélectionnez l’option « Nouvelle requête »

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image7.png)
2. Dans la fenêtre de requête, copiez et collez la totalité du script SQL à partir du fichier Migrations.sql. Exécutez le fichier de script en cliquant sur le bouton de flèche « Exécuter ».

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image4.jpg)

    Actualisez la fenêtre de l’Explorateur de serveurs. Cinq de nouvelles tables est créées dans la base de données.

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image8.png)

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image9.png)

    Voici comment les informations contenues dans les tables d’appartenances SQL sont mappés vers le nouveau système d’identité.

    ASPNET\_rôles--&gt; AspNetRoles

    ASP\_netUsers et asp\_netMembership--&gt; AspNetUsers

    ASPNET\_UserInRoles--&gt; AspNetUserRoles

    Comme expliqué dans la section ci-dessus, les tables AspNetUserClaims et AspNetUserLogins sont vides. Le champ 'Discriminateur' dans la table AspNetUser doit correspondre au nom de classe de modèle qui est défini comme une étape suivante. La colonne PasswordHash est également sous la forme ' mot de passe chiffré | salt du mot de passe | format de mot de passe '. Cela vous permet d’utiliser une logique de chiffrement d’appartenance SQL spéciale afin que vous puissiez réutiliser des anciens mots de passe. Dans, qui est expliquée plus loin dans l’article.

### <a name="creating-models-and-membership-pages"></a>Création de modèles et les pages d’appartenance

Comme mentionné précédemment, la fonctionnalité d’identité utilise Entity Framework pour communiquer avec la base de données pour stocker les informations de compte par défaut. Pour travailler avec des données existantes dans la table, nous devons créer des classes de modèle qui mappent vers les tables et les associer dans le système d’identité. Dans le cadre du contrat d’identité, les classes du modèle doivent implémenter les interfaces définies dans la dll Identity.Core ou étendent l’implémentation existante de ces interfaces disponibles dans Microsoft.AspNet.Identity.EntityFramework.

Dans notre exemple, les tables AspNetRoles, AspNetUserClaims, AspNetLogins et AspNetUserRole ont des colonnes qui sont similaires à l’implémentation existante du système d’identité. Par conséquent, nous pouvons réutiliser classes existantes pour mapper à ces tables. La table AspNetUser a quelques colonnes supplémentaires qui sont utilisés pour stocker des informations supplémentaires à partir des tables d’appartenances SQL. Cela peut être mappée en créant une classe de modèle qui étendent l’implémentation existante de 'IdentityUser' et ajoutez les propriétés supplémentaires.

1. Dossier de modèles de cette méthode crée dans le projet et ajouter une classe d’utilisateur. Le nom de la classe doit correspondre les données ajoutées dans la colonne « Discriminateur » de 'AspnetUsers' table.

    ![](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/_static/image10.png)

    La classe d’utilisateur doit étendre la classe IdentityUser trouvée dans le *Microsoft.AspNet.Identity.EntityFramework* dll. Déclarer des propriétés de la classe qui mappent vers les colonnes AspNetUser. Les propriétés ID, nom d’utilisateur, PasswordHash et SecurityStamp sont définies dans le IdentityUser et ne sont pas incluses. Voici le code de la classe d’utilisateur qui possède toutes les propriétés

    [!code-csharp[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample3.cs)]
2. Une classe d’Entity Framework DbContext est requis afin de rendre persistantes des données dans les modèles de tables et de récupérer des données à partir des tables pour remplir les modèles. *Microsoft.AspNet.Identity.EntityFramework* dll définit la classe IdentityDbContext qui interagit avec les tables d’identité pour récupérer et stocker des informations. L’IdentityDbContext&lt;tuser&gt; prend une classe 'TUser' qui peut être n’importe quelle classe qui étend la classe IdentityUser.

    Créez une classe ApplicationDBContext qui étend IdentityDbContext sous le dossier « Modèles », en passant dans la classe 'User' créée à l’étape 1

    [!code-csharp[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample4.cs)]
3. Gestion des utilisateurs dans le système d’identité s’effectue à l’aide de la UserManager&lt;tuser&gt; classe définie dans le *Microsoft.AspNet.Identity.EntityFramework* dll. Nous devons créer une classe personnalisée qui étend UserManager, en passant dans la classe 'User' créée à l’étape 1.

    Dans le dossier de modèles, créez une classe UserManager qui étend UserManager&lt;utilisateur&gt;

    [!code-csharp[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample5.cs)]
4. Les mots de passe des utilisateurs de l’application sont chiffrées et stockées dans la base de données. L’algorithme de chiffrement utilisé dans l’appartenance SQL est différent de celui dans le nouveau système d’identité. Pour réutiliser des anciens mots de passe, nous devons sélectivement déchiffrer les mots de passe quand anciens utilisateurs de se connectent à l’aide de l’algorithme d’appartenances SQL lors de l’utilisation de l’algorithme de chiffrement d’identité pour les nouveaux utilisateurs.

    La classe UserManager possède une propriété 'PasswordHasher', qui stocke une instance d’une classe qui implémente l’interface 'Ipasswordhasher.'. Cela est utilisé pour chiffrer/déchiffrer les mots de passe pendant les transactions de l’authentification utilisateur. Dans la classe UserManager est définie à l’étape 3, créez une classe SQLPasswordHasher et copiez le code ci-dessous.

    [!code-csharp[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample6.cs)]

    Résoudre les erreurs de compilation en important les espaces de noms System.Text et System.Security.Cryptography.

    La méthode EncodePassword chiffre le mot de passe en fonction de l’implémentation de chiffrement d’appartenance SQL par défaut. Cela provient de la dll de System.Web. Si l’ancienne application utilisé une implémentation personnalisée puis il doit être indiqué ici. Nous devons définir deux autres méthodes *HashPassword* et *VerifyHashedPassword* qui utilisent le *EncodePassword* pour le hachage d’un mot de passe donné ou vérifier un texte brut (méthode) mot de passe avec un existant dans la base de données.

    Le système d’appartenance SQL utilisé PasswordHash, PasswordSalt et PasswordFormat pour hacher le mot de passe entré par les utilisateurs lorsqu’ils inscriront ou de modifier leur mot de passe. Lors de la migration, les trois champs sont stockées dans la colonne de PasswordHash dans la table AspNetUser séparée par le ' |' caractères. Lorsqu’un utilisateur se connecte et le mot de passe a ces champs, nous utilisons le chiffrement appartenance SQL pour vérifier le mot de passe ; dans le cas contraire, nous utilisons de chiffrement par défaut du système d’identité pour vérifier le mot de passe. De cette manière anciens utilisateurs n’ont pas à modifier leurs mots de passe une fois que la migration de l’application.
5. Déclarez le constructeur pour la classe UserManager et passer ce que le SQLPasswordHasher à la propriété dans le constructeur.

    [!code-csharp[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample7.cs)]

### <a name="create-new-account-management-pages"></a>Créer des pages de gestion de compte

L’étape suivante de la migration consiste à ajouter des pages de gestion de compte qui permettent un utilisateur d’inscrire et se connecter. Les pages de compte ancien à partir de l’appartenance SQL utilisent des contrôles qui ne fonctionnent pas avec le nouveau système d’identité. Pour ajouter le nouvel utilisateur de pages de gestion de suivent le didacticiel sur ce lien [https://www.asp.net/identity/overview/getting-started/adding-aspnet-identity-to-an-empty-or-existing-web-forms-project](../getting-started/adding-aspnet-identity-to-an-empty-or-existing-web-forms-project.md) à partir de l’étape ' Ajout de Web Forms pour l’inscription aux utilisateurs de votre application », car nous avons déjà créé le projet et ajouter les packages NuGet.

Nous devons apporter quelques modifications pour l’exemple fonctionne avec le projet que nous avons ici.

- Le code Register.aspx.cs et Login.aspx.cs derrière l’utilisation de classes le `UserManager` des packages d’identité pour créer un utilisateur. Pour cet exemple montre comment utiliser le UserManager ajouté dans le dossier de modèles en suivant les étapes mentionnées plus haut.
- Utilisez la classe d’utilisateur créée à la place la IdentityUser dans le code Register.aspx.cs et Login.aspx.cs derrière les classes. Cela connecte notre classe d’utilisateur personnalisée dans le système d’identité.
- Le composant à créer la base de données peut être ignoré.
- Le développeur doit être définie pour le nouvel utilisateur ApplicationId pour correspondre à l’ID d’application actuel. Cela est possible en interrogeant les ApplicationId pour cette application avant la création d’un objet utilisateur dans la classe Register.aspx.cs et en lui affectant avant de créer l’utilisateur. 

    Exemple :

    Définir une méthode dans la page Register.aspx.cs pour interroger le compte aspnet\_Applications de table et obtenir l’Id de l’application en fonction du nom de l’application

    [!code-csharp[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample8.cs)]

    Maintenant obtenir définir sur l’objet utilisateur

    [!code-csharp[Main](migrating-an-existing-website-from-sql-membership-to-aspnet-identity/samples/sample9.cs)]

Utilisez le nom d’utilisateur ancien et le mot de passe pour se connecter à un utilisateur existant. Pour créer un nouvel utilisateur, utilisez la page d’inscription. Vérifiez également que les utilisateurs sont dans des rôles, comme prévu.

Porter vers le système d’identité permet à l’utilisateur d’ajouter l’authentification ouverte (OAuth) à l’application. Reportez-vous à l’exemple [ici](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SQLMembership-Identity-OWIN/) qui a l’authentification OAuth est activée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous vous avons montré comment les utilisateurs d’appartenance SQL pour ASP.NET Identity de port, mais nous n’a pas les données de profil de port. Dans l’étape suivante du didacticiel, nous allons en déplacement des données de profil de l’appartenance SQL vers le nouveau système d’identité.

Vous pouvez laisser des commentaires au bas de cet article.

*Merci d’avoir à Tom Dykstra et Rick Anderson d’examen de l’article.*
