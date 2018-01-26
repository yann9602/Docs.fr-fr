---
uid: web-forms/overview/moving-to-aspnet-20/configuration-and-instrumentation
title: Configuration et Instrumentation | Documents Microsoft
author: microsoft
description: "Voici les principales modifications de configuration et d’instrumentation dans ASP.NET 2.0. La nouvelle API de configuration ASP.NET permet des modifications de configuration doit être effectuée pr..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 21ebbaee-7ed8-45ae-b6c1-c27c88342e48
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/configuration-and-instrumentation
msc.type: authoredcontent
ms.openlocfilehash: 5780bfde928011f46c3f504aec927f2127f10d0d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="configuration-and-instrumentation"></a>Configuration et Instrumentation
====================
par [Microsoft](https://github.com/microsoft)

> Voici les principales modifications de configuration et d’instrumentation dans ASP.NET 2.0. La nouvelle API de configuration ASP.NET permet des modifications de configuration doit être effectuée par programme. En outre, existent de nombreux nouveaux paramètres de configuration permettant de nouvelles configurations et d’instrumentation.


Voici les principales modifications de configuration et d’instrumentation dans ASP.NET 2.0. La nouvelle API de configuration ASP.NET permet des modifications de configuration doit être effectuée par programme. En outre, existent de nombreux nouveaux paramètres de configuration permettant de nouvelles configurations et d’instrumentation.

Dans ce module, nous allons décrire l’API de configuration d’ASP.NET car il est lié à la lecture et écriture aux fichiers de configuration ASP.NET, et nous aborderons également instrumentation ASP.NET. Nous aborderons également les nouvelles fonctionnalités disponibles dans le suivi ASP.NET.

## <a name="aspnet-configuration-api"></a>API de Configuration ASP.NET

L’API de configuration ASP.NET vous permet de développer, déployer et gérer des données de configuration d’application à l’aide d’une seule interface de programmation. Vous pouvez utiliser l’API de configuration pour développer et modifier des configurations ASP.NET complètes par programme sans modifier directement le code XML dans les fichiers de configuration. En outre, vous pouvez utiliser l’API de configuration dans les applications console et les scripts que vous développez, dans les outils de gestion basée sur le Web et dans les composants logiciels enfichables Microsoft Management Console (MMC).

Les deux outils de gestion de la configuration suivants utilisent l’API de configuration et sont inclus avec le .NET Framework version 2.0 :

- Le composant logiciel enfichable MMC ASP.NET, qui utilise l’API de configuration pour simplifier les tâches d’administration, en fournissant une vue intégrée de données de configuration local de tous les niveaux de la hiérarchie de configuration.
- L’outil d’Administration de Site Web, ce qui vous permet de gérer les paramètres de configuration pour les applications locales et distantes, y compris les sites hébergés.

L’API de configuration ASP.NET comprend un ensemble d’objets de gestion ASP.NET que vous pouvez utiliser pour configurer par programme des sites et applications Web. Objets de gestion sont implémentées comme une bibliothèque de classes .NET Framework. Le modèle de programmation API de configuration permet de garantir la cohérence du code et la fiabilité en appliquant des types de données au moment de la compilation. Pour faciliter la gestion des configurations de l’application, l’API de configuration vous permet d’afficher les données fusionnées à partir de tous les points de la hiérarchie de configuration comme une collection unique, au lieu de l’affichage des données en tant que collections distinctes à partir de différents fichiers de configuration. En outre, l’API de configuration vous permet de manipuler des configurations de l’ensemble de l’application sans modifier directement le code XML dans les fichiers de configuration. Enfin, l’API simplifie les tâches de configuration en prenant en charge des outils d’administration, tels que l’outil Administration de Site Web. L’API de configuration simplifie le déploiement en prenant en charge la création de fichiers de configuration sur un ordinateur et l’exécution des scripts de configuration sur plusieurs ordinateurs.

> [!NOTE]
> L’API de configuration ne prend pas en charge la création d’applications IIS.


## <a name="working-with-local-and-remote-configuration-settings"></a>Utilisation des paramètres de Configuration locaux et distants

Un objet de Configuration représente l’affichage fusionné des paramètres de configuration qui s’appliquent à une entité physique spécifique, tel qu’un ordinateur, ou à une entité logique, comme une application ou un site Web. L’entité logique spécifiée peut exister sur l’ordinateur local ou sur un serveur distant. Lorsqu’aucun fichier de configuration n’existe pour une entité spécifiée, l’objet de Configuration représente les paramètres de configuration par défaut, comme défini par le fichier Machine.config.

Vous pouvez obtenir un objet de Configuration à l’aide d’une des méthodes de configuration ouvertes des classes suivantes :

1. La classe ConfigurationManager, si votre entité est une application cliente.
2. La classe WebConfigurationManager, si votre entité est une application Web.

Ces méthodes retournent un objet de Configuration, qui à son tour fournit les méthodes et propriétés requises pour gérer les fichiers de configuration sous-jacents. Vous pouvez accéder à ces fichiers pour lire ou écrire.

### <a name="reading"></a>Lecture

La méthode GetSection ou GetSectionGroup vous permet de lire les informations de configuration. L’utilisateur ou le processus qui lit doit avoir autorisations de lecture sur tous les fichiers de configuration dans la hiérarchie.

> [!NOTE]
> Si vous utilisez une méthode GetSection statique qui accepte un paramètre de chemin d’accès, le paramètre path doit faire référence à l’application dans laquelle le code est en cours d’exécution. Sinon, le paramètre est ignoré et les informations de configuration de l’application en cours d’exécution sont renvoyées.


### <a name="writing"></a>Écriture

Vous utilisez une des méthodes de sauvegarde pour écrire des informations de configuration. L’utilisateur ou le processus qui écrit doit avoir les autorisations sur le fichier de configuration et le répertoire au niveau de la hiérarchie de configuration en cours d’écriture, ainsi qu’autorisations de lecture sur tous les fichiers de configuration dans la hiérarchie.

Pour générer un fichier de configuration qui représente les paramètres de configuration hérités pour une entité spécifiée, utilisez une des méthodes de sauvegarde de la configuration suivante :

1. Pour créer un nouveau fichier de configuration de la méthode Save.
2. La méthode SaveAs pour générer un nouveau fichier de configuration à un autre emplacement.

## <a name="configuration-classes-and-namespaces"></a>Espaces de noms et Classes de configuration

De nombreuses méthodes et classes de configuration sont similaires entre eux. Le tableau suivant décrit les classes de configuration plus couramment utilisées et les espaces de noms.

| **Classe de configuration ou l’espace de noms** | **Description** |
| --- | --- |
| [System.Configuration](https://msdn.microsoft.com/library/system.configuration.aspx) espace de noms | Contient les classes de configuration principale pour toutes les applications .NET Framework. Les classes de gestionnaire de section sont utilisés pour obtenir des données de configuration d’une section à partir de méthodes, telles que GetSection et GetSectionGroup. Ces deux méthodes sont non statique. |
| Classe de System.Configuration.Configuration | Représente un jeu de données de configuration pour un ordinateur, application, répertoire Web ou autres ressources. Cette classe contient des méthodes utiles, telles que GetSection et GetSectionGroup, pour la mise à jour des paramètres de configuration et obtenir des références aux sections et groupes. Cette classe est utilisée comme type de retour pour les méthodes qui obtiennent des données de configuration de conception, telles que les méthodes des classes WebConfigurationManager et Configuration Manager. |
| Espace de noms System.Web.Configuration | Contient les classes de gestionnaire de section pour les sections de configuration ASP.NET définies par [les paramètres de Configuration ASP.NET](https://msdn.microsoft.com/library/b5ysx397.aspx). Les classes de gestionnaire de section sont utilisés pour obtenir des données de configuration d’une section à partir de méthodes, telles que GetSection et GetSectionGroup. |
| Classe de System.Web.Configuration.WebConfigurationManager | Fournit des méthodes utiles pour obtenir des références aux paramètres de configuration d’exécution et au moment du design. Ces méthodes utilisent la classe System.Configuration.Configuration comme type de retour. Vous pouvez utiliser la méthode GetSection statique de cette classe ou de la méthode GetSection non statique de la classe System.Configuration.ConfigurationManager indifféremment. Pour les configurations d’application Web, la classe System.Web.Configuration.WebConfigurationManager est préférable d’utiliser la classe System.Configuration.ConfigurationManager. |
| [System.Configuration.Provider](https://msdn.microsoft.com/library/system.configuration.provider.aspx) namespace | Fournit un moyen pour personnaliser et étendre le fournisseur de configuration. Ceci est la classe de base pour toutes les classes de fournisseur dans le système de configuration. |
| [System.Web.Management](https://msdn.microsoft.com/library/system.web.management.aspx) namespace | Contient des classes et interfaces pour gérer et surveiller l’intégrité des applications Web. En principe, cet espace de noms n'est pas considéré comme partie de l’API de configuration. Par exemple, le suivi et le déclenchement des événements est accomplie par les classes dans cet espace de noms. |
| [System.Management.Instrumentation](https://msdn.microsoft.com/library/system.management.instrumentation.aspx) namespace | Fournit les classes nécessaires à l’instrumentation d’applications d’exposer leurs informations de gestion et des événements via Windows Management Instrumentation (WMI) aux consommateurs potentiels. Contrôle d’état ASP.NET d’utilise WMI pour remettre des événements. En principe, cet espace de noms n'est pas considéré comme partie de l’API de configuration. |

## <a name="reading-from-aspnet-configuration-files"></a>La lecture à partir de fichiers de Configuration ASP.NET

La classe WebConfigurationManager est la classe de base pour la lecture à partir de fichiers de configuration ASP.NET. Il existe essentiellement trois étapes de la lecture des fichiers de configuration ASP.NET :

1. Obtenir un objet de Configuration à l’aide de la méthode OpenWebConfiguration.
2. Obtenir une référence à la section souhaitée dans le fichier de configuration.
3. Lire les informations souhaitées à partir du fichier de configuration.

Configuration de l’objet représente ne représente pas un fichier de configuration spécifique. Au lieu de cela, il représente une vue fusionnée de la configuration d’un ordinateur, une application ou un site Web. L’exemple de code suivant instancie un objet de Configuration représentant la configuration d’une application Web appelée *ProductInfo*.

[!code-csharp[Main](configuration-and-instrumentation/samples/sample1.cs)]

> [!NOTE]
> Notez que si le chemin d’accès /ProductInfo n’existe pas, le code ci-dessus retournera la configuration par défaut comme spécifié dans le fichier machine.config.


Une fois que vous avez l’objet de Configuration, vous pouvez ensuite utiliser la méthode GetSection ou GetSectionGroup pour affiner les paramètres de configuration. L’exemple suivant obtient une référence aux paramètres d’emprunt d’identité pour l’application ProductInfo ci-dessus :

[!code-csharp[Main](configuration-and-instrumentation/samples/sample2.cs)]

## <a name="writing-to-aspnet-configuration-files"></a>Écrire dans les fichiers de Configuration ASP.NET

Comme la lecture des fichiers de configuration, la classe WebConfigurationManager est l’essence pour écrire dans les fichiers de configuration Asp.NET. Il existe également trois étapes pour écrire dans les fichiers de configuration ASP.NET.

1. Obtenir un objet de Configuration à l’aide de la méthode OpenWebConfiguration.
2. Obtenir une référence à la section souhaitée dans le fichier de configuration.
3. Écrire les informations souhaitées à partir du fichier de configuration à l’aide de l’enregistrement ou SaveAs (méthode).

Le code suivant modifie le **déboguer** attribut de la &lt;compilation&gt; élément False :

[!code-csharp[Main](configuration-and-instrumentation/samples/sample3.cs)]

Lorsque ce code est exécuté, le **déboguer** attribut de la &lt;compilation&gt; élément sera être défini sur false pour le *webApp* fichier web.config de l’application.

## <a name="systemwebmanagement-namespace"></a>System.Web.Management Namespace

L’espace de noms System.Web.Management fournit les classes et interfaces pour gérer et surveiller l’intégrité des applications ASP.NET.

Journalisation s’effectue en définissant une règle qui associe les événements à un fournisseur. La règle définit le type d’événements qui sont envoyés au fournisseur. Les événements de base suivants sont disponibles pour vous connecter :

| **WebBaseEvent** | La classe d’événements de base pour tous les événements. Contient les informations requises de séquence de propriétés pour tous les événements tels que le code d’événement, le code d’événement en détail, date et heure de l’événement a été déclenché, nombre, le message d’événement et les détails de l’événement. |
| --- | --- |
| **WebManagementEvent** | La classe d’événements de base pour les événements de gestion, telles que la durée de vie des applications, demande, erreur et les événements d’audit. |
| **WebHeartbeatEvent** | L’événement généré par l’application dans des intervalles réguliers pour capturer des informations sur l’état exécution utile. |
| **WebAuditEvent** | La classe de base pour les événements d’audit de sécurité, qui sont utilisés pour marquer des conditions telles que de l’échec d’autorisation, échec du déchiffrement, *etc.* |
| **WebRequestEvent** | La classe de base pour tous les événements de demande d’information. |
| **WebBaseErrorEvent** | La classe de base pour tous les événements indiquant les conditions d’erreur. |

Les types de fournisseurs disponibles vous permettent de vous permet d’envoyer la sortie des événements Observateur d’événements, SQL Server, Windows Management Instrumentation (WMI), et par courrier électronique. Les fournisseurs préconfigurés et les mappages d’événements réduisent la quantité de travail nécessaire pour obtenir la sortie des événements enregistré.

ASP.NET 2.0 utilise le journal des événements fournisseur out-of-the-box pour enregistrer des événements basés sur les domaines d’application démarrage et arrêt, ainsi que pour la journalisation de toutes les exceptions non gérées. Cela permet de couvrir certains des scénarios de base. Par exemple, supposons que votre application lève une exception, mais l’utilisateur n’enregistre pas l’erreur et vous ne pouvez pas le reproduire. Avec la règle de journal des événements par défaut, vous ne pourrez pas collecter les informations d’exception et de la pile pour obtenir une meilleure idée de quel type d’erreur s’est produite. Un autre exemple s’applique si votre application perd l’état de session. Dans ce cas, vous pouvez rechercher dans le journal des événements pour déterminer si le domaine d’application est recyclé, et pourquoi le domaine d’application arrêté en premier lieu.

En outre, le contrôle d’intégrité système de surveillance est extensible. Par exemple, vous pouvez définir des événements Web personnalisés, déclenchement au sein de votre application et puis définir une règle pour envoyer les informations d’événement à un fournisseur tel que votre adresse de messagerie. Cela vous permet de facilement lier votre instrumentation pour les fournisseurs de contrôle d’état. Comme autre exemple, vous pouvez déclencher un événement chaque fois qu’une commande est traitée et définir une règle qui envoie chaque événement à la base de données SQL Server. Vous pouvez également déclencher un événement lorsqu’un utilisateur ne parvient pas à se connecter plusieurs fois dans une ligne et définir l’événement d’utiliser le fournisseur basée sur le courrier électronique.

La configuration pour les événements et les fournisseurs par défaut est stockée dans le fichier Web.config global. Le fichier Web.config global stocke tous les paramètres basée sur le Web qui ont été stockés dans le fichier Machine.config dans ASP.NET 1 x. Le fichier Web.config global se trouve dans le répertoire suivant :

`%windir%\Microsoft.Net\Framework\v2.0.*\config\Web.config`

Le &lt;healthMonitoring&gt; section du fichier Web.config global fournit des paramètres de configuration par défaut. Vous pouvez remplacer ces paramètres ou configurer vos propres paramètres en implémentant la &lt;healthMonitoring&gt; section dans le fichier Web.config pour votre application.

Le &lt;healthMonitoring&gt; section du fichier Web.config global contient les éléments suivants :

| **fournisseurs** | Contient des fournisseurs défini pour l’Observateur d’événements, WMI et SQL Server. |
| --- | --- |
| **eventMappings** | Contient des mappages pour les différentes classes WebBase. Vous pouvez étendre cette liste si vous générez votre propre classe d’événements. Générer votre propre classe d’événements vous donne une granularité plus fine sur les fournisseurs de que vous envoyez des informations. Par exemple, vous pouvez configurer des exceptions non gérées à envoyer à SQL Server, lors de l’envoi de vos propres événements personnalisés à la messagerie électronique. |
| **rules** | Lie l’eventMappings au fournisseur. |
| **buffering** | Utilisé avec les fournisseurs SQL Server et par courrier électronique pour déterminer la fréquence à laquelle les événements de vidage pour le fournisseur. |

Voici un exemple de code à partir du fichier Web.config global.

[!code-xml[Main](configuration-and-instrumentation/samples/sample4.xml)]

## <a name="how-to-store-events-to-event-viewer"></a>Comment stocker les événements à l’Observateur d’événements

Comme mentionné précédemment, le fournisseur pour la journalisation des événements de l’événement visionneuse est configurée dans le fichier Web.config global. Par défaut, tous les événements en fonction de **WebBaseErrorEvent** et **WebFailureAuditEvent** sont enregistrés. Vous pouvez ajouter des règles supplémentaires pour consigner des informations supplémentaires dans le journal des événements. Par exemple, si vous souhaitez consigner tous les événements (*c'est-à-dire*, tous les événements en fonction de **WebBaseEvent**), vous pouvez ajouter la règle suivante à votre fichier Web.config :

[!code-xml[Main](configuration-and-instrumentation/samples/sample5.xml)]

Cette règle se lient les **tous les événements** table d’événements pour le fournisseur du journal des événements. EventMapping et le fournisseur sont inclus dans le fichier Web.config global.

## <a name="how-to-store-events-to-sql-server"></a>Comment stocker les événements à SQL Server

Cette méthode utilise le **ASPNETDB** base de données, ce qui est généré par le compte Aspnet\_regsql.exe outil. Le fournisseur par défaut utilise la chaîne de connexion LocalSqlServer, qui utilise soit un fichier de base de données dans l’application\_dossier de données ou l’instance SQLExpress locale de SQL Server. La chaîne de connexion LocalSqlServer et le SqlProvider sont configurés dans le fichier Web.config global.

La chaîne de connexion dans le fichier Web.config global LocalSqlServer ressemble à ceci :

[!code-xml[Main](configuration-and-instrumentation/samples/sample6.xml)]

Si vous souhaitez utiliser une autre instance de SQL Server, vous devez utiliser le compte Aspnet\_outil regsql.exe, qui se trouve dans le répertoire % windir%\Microsoft.Net\Framework\v2.0.\* dossier. Utiliser le compte Aspnet\_outil regsql.exe pour générer une personnalisée **ASPNETDB** de base de données sur l’instance de SQL Server, puis ajouter la chaîne de connexion à votre fichier de configuration d’applications, et puis ajoutez un fournisseur à l’aide de la nouvelle chaîne de connexion. Une fois que vous avez le **ASPNETDB** base de données créée, vous devez définir une règle pour lier un eventMapping le sqlProvider.

Si vous utilisez la valeur par défaut SqlProvider ou que vous configurez votre propre fournisseur, vous devez ajouter une règle de liaison du fournisseur avec une table d’événements. La règle suivante lie le nouveau fournisseur que vous avez créé précédemment pour le **tous les événements** table d’événements. Cette règle enregistre tous les événements en fonction de **WebBaseEvent** et envoyez-le à le MySqlWebEventProvider qui utilise la chaîne de connexion MYASPNETDB. Le code suivant ajoute une règle pour lier le fournisseur avec une table d’événements :

[!code-xml[Main](configuration-and-instrumentation/samples/sample7.xml)]

Si vous souhaitez uniquement envoyer les erreurs de SQL Server, vous pouvez ajouter la règle suivante :

[!code-xml[Main](configuration-and-instrumentation/samples/sample8.xml)]

## <a name="how-to-forward-events-to-wmi"></a>Comment transférer les événements à WMI

Vous pouvez également transférer les événements à WMI. Le fournisseur WMI est configuré dans le fichier Web.config global par défaut.

L’exemple de code suivant ajoute une règle pour transférer les événements à WMI :

[!code-xml[Main](configuration-and-instrumentation/samples/sample9.xml)]

Vous devez ajouter une règle pour associer un eventMapping au fournisseur et également à une application d’écouteur pour écouter les événements WMI. L’exemple de code suivant ajoute une règle pour lier le fournisseur WMI pour les **tous les événements** table d’événements :

[!code-xml[Main](configuration-and-instrumentation/samples/sample10.xml)]

## <a name="how-to-forward-events-to-e-mail"></a>Comment transférer des événements à la messagerie électronique

Vous pouvez également transférer des événements à la messagerie électronique. Soyez prudent sur les règles d’événement vous mappez à votre fournisseur de messagerie, comme vous pouvez involontairement envoyer vous-même de nombreuses informations qui peuvent être mieux adapté pour SQL Server ou le journal des événements. Il existe deux fournisseurs de messagerie ; SimpleMailWebEventProvider et TemplatedMailWebEventProvider. Chacun a les mêmes attributs de configuration, à l’exception des attributs de « modèle » et « detailedTemplateErrors », qui sont disponibles uniquement sur le TemplatedMailWebEventProvider.

> [!NOTE]
> Aucune de ces fournisseurs de messagerie est configuré pour vous. Vous devez les ajouter à votre fichier Web.config.


La principale différence entre ces fournisseurs de deux messagerie est que SimpleMailWebEventProvider envoie des messages électroniques dans un modèle générique ne peut pas être modifié. L’exemple de fichier Web.config ajoute ce fournisseur de messagerie à la liste des fournisseurs configurés à l’aide de la règle suivante :

[!code-xml[Main](configuration-and-instrumentation/samples/sample11.xml)]

La règle suivante est également ajoutée pour lier le fournisseur de messagerie pour le **tous les événements** table d’événements :

[!code-xml[Main](configuration-and-instrumentation/samples/sample12.xml)]

## <a name="aspnet-20-tracing"></a>ASP.NET 2.0 de suivi

Il existe trois améliorations importantes pour le traçage dans ASP.NET 2.0.

1. Fonctionnalité intégrée de traçage
2. Accès par programme aux messages de trace
3. Amélioration de traçage au niveau de l’application

## <a name="integrated-tracing-functionality"></a>Intégré le suivi des fonctionnalités

Vous pouvez désormais router des messages émis par la classe System.Diagnostics.Trace pour la sortie de traçage ASP.NET et router des messages émis par le traçage ASP.NET à System.Diagnostics.Trace. Vous pouvez également transférer des événements d’instrumentation ASP.NET à System.Diagnostics.Trace. Cette fonctionnalité est fournie par la nouvelle **writeToDiagnosticsTrace** attribut de la &lt;trace&gt; élément. Lorsque cette valeur booléenne est true, les messages de Trace ASP.NET sont transférés à l’infrastructure de suivi System.Diagnostics pour une utilisation par tous les écouteurs qui sont inscrits pour afficher des messages de Trace.

## <a name="programmatic-access-to-trace-messages"></a>Accès par programme aux Messages de Trace

ASP.NET 2.0 autorise l’accès par programme à tous les messages de trace via le **TraceContextRecord** classe et le **TraceRecords** collection. Le moyen le plus efficace d’accéder à des messages de trace consiste à inscrire une **TraceContextEventHandler** délégué (nouveauté dans ASP.NET 2.0) pour gérer le nouveau **TraceFinished** événement. Vous pouvez ensuite parcourir les messages de trace que vous le souhaitez.

L’exemple de code suivant illustre ce comportement :

[!code-csharp[Main](configuration-and-instrumentation/samples/sample13.cs)]

Dans l’exemple ci-dessus, je parcourir la collection TraceRecords et puis écrire chaque message dans le flux de réponse.

## <a name="improved-application-level-tracing"></a>Amélioration de traçage au niveau de l’Application

Traçage au niveau de l’application est amélioré via l’introduction de la nouvelle **mostRecent** attribut de la &lt;trace&gt; élément. Cet attribut spécifie si la sortie de traçage au niveau de l’application plus récente est affichée et que les données de trace plus anciennes au-delà des limites indiquées par requestLimit sont ignorées. Si la valeur est false, les données de trace sont affichées pour les demandes jusqu'à ce que l’attribut requestLimit est atteint.

## <a name="aspnet-command-line-tools"></a>Outils de ligne de commande ASP.NET

Il existe plusieurs outils de ligne de commande à l’aide de la configuration d’ASP.NET. Les développeurs ASP.NET doivent être familiarisés avec le compte aspnet\_regiis.exe outil. ASP.NET 2.0 fournit les trois autres outils de ligne de commande à l’aide de la configuration.

Les outils de ligne de commande suivants sont disponibles :

| **Tool** | **Use** |
| --- | --- |
| **aspnet\_regiis.exe** | Permet l’inscription d’ASP.NET avec IIS. Il existe deux versions de cette outils fournis avec ASP.NET 2.0, un pour les systèmes 32 bits (dans le dossier Framework) et un pour les systèmes 64 bits (dans le dossier Framework64.) La version 64 bits n’est pas être installée sur un système d’exploitation 32 bits. |
| **aspnet\_regsql.exe** | L’outil d’inscription de ASP.NET SQL Server est utilisé pour créer une base de données Microsoft SQL Server pour une utilisation par les fournisseurs SQL Server dans ASP.NET, ou pour ajouter ou supprimer des options de base de données existante. Le compte Aspnet\_regsql.exe fichier se trouve dans le dossier [drive:]\WINDOWS\Microsoft.NET\Framework\versionNumber dossier sur votre serveur Web. |
| **aspnet\_regbrowsers.exe** | L’outil d’inscription de navigateur ASP.NET analyse et compile des définitions de navigateur à l’échelle du système dans un assembly et installe l’assembly dans le global assembly cache. L’outil utilise les fichiers de définition de navigateur (. Fichiers de navigateur) dans le sous-répertoire de navigateurs .NET Framework. L’outil peut être trouvé dans le répertoire %SystemRoot%\Microsoft.NET\Framework\version\. |
| **aspnet\_compiler.exe** | L’outil de Compilation ASP.NET vous permet de compiler une application Web ASP.NET, soit directement soit pour le déploiement vers un emplacement cible tel qu’un serveur de production. Compilation sur place permet de performances de l’application, car les utilisateurs finaux ne rencontrent pas de retard lors de la première demande à l’application pendant que l’application est compilée. |

Étant donné que le compte aspnet\_regiis.exe outil n’est pas nouveau pour ASP.NET 2.0, nous allons décrire pas une ici.

## <a name="aspnet-sql-server-registration-tool---aspnetregsqlexe"></a>Outil d’inscription de ASP.NET SQL Server - aspnet\_regsql.exe

Vous pouvez définir plusieurs types d’options à l’aide de l’outil d’inscription de ASP.NET SQL Server. Vous pouvez spécifier une connexion SQL, spécifier les services d’application ASP.NET utilisent SQL Server pour gérer les informations, d’indiquer quelle base de données ou la table est utilisée pour la dépendance de cache SQL et ajouter ou supprimer la prise en charge pour l’utilisation de SQL Server pour stocker les procédures et l’état de session.

Plusieurs services d’application ASP.NET reposent sur un fournisseur pour gérer le stockage et la récupération des données à partir d’une source de données. Chaque fournisseur est spécifique à la source de données. ASP.NET inclut un fournisseur SQL Server pour les fonctionnalités ASP.NET suivantes :

- L’appartenance (le [SqlMembershipProvider](https://msdn.microsoft.com/library/system.web.security.sqlmembershipprovider.aspx) classe).
- Gestion des rôles (la [SqlRoleProvider](https://msdn.microsoft.com/library/system.web.security.sqlroleprovider.aspx) classe).
- Profil (le [SqlProfileProvider](https://msdn.microsoft.com/library/system.web.profile.sqlprofileprovider.aspx) classe).
- WebParts (la [SqlPersonalizationProvider](https://msdn.microsoft.com/library/system.web.ui.webcontrols.webparts.sqlpersonalizationprovider.aspx) classe).
- Événements Web (le [SqlWebEventProvider](https://msdn.microsoft.com/library/system.web.management.sqlwebeventprovider.aspx) classe).

Lorsque vous installez ASP.NET, le fichier Machine.config pour votre serveur inclut des éléments de configuration qui spécifient les fournisseurs SQL Server pour chacune des fonctionnalités ASP.NET qui reposent sur un fournisseur. Ces fournisseurs sont configurés, par défaut, pour se connecter à une instance utilisateur locale de SQL Server Express 2005. Si vous modifiez la chaîne de connexion par défaut utilisée par les fournisseurs, puis avant de pouvoir utiliser les fonctionnalités ASP.NET configurées dans la configuration de l’ordinateur, vous devez installer la base de données SQL Server et les éléments de la base de données pour votre fonctionnalité choisie à l’aide de Aspnet\_regsql.exe. Si la base de données que vous spécifiez avec l’outil SQL registration n’existe pas déjà (aspnetdb sera la base de données par défaut si aucune n’est spécifiée sur la ligne de commande), puis l’utilisateur actuel doit disposer des droits nécessaires pour créer des bases de données dans SQL Server ainsi que pour créer des schémas éléments s’effectue dans une base de données.

### <a name="sql-cache-dependency"></a>Dépendance de cache SQL

Une fonctionnalité avancée de la mise en cache de sortie ASP.NET est la dépendance de cache SQL. Dépendance de cache SQL prend en charge deux modes de fonctionnement différents : un qui utilise une implémentation ASP.NET de l’interrogation de table et un second mode qui utilise les fonctionnalités de notification de requête de SQL Server 2005. L’outil d’inscription de SQL peut être utilisé pour configurer le mode d’interrogation de table de l’opération.

### <a name="session-state"></a>État de session

Par défaut, les informations et les valeurs d’état de session sont stockées dans la mémoire au sein du processus ASP.NET. Vous pouvez également stocker les données de session dans une base de données SQL Server, où il peut être partagé par plusieurs serveurs Web. Si la base de données que vous spécifiez pour l’état de session avec l’outil SQL registration n’existe pas déjà, l’utilisateur actuel doit avoir les droits nécessaires pour créer des bases de données dans SQL Server ainsi que pour créer des éléments de schéma dans une base de données. Si la base de données n’existe pas, l’utilisateur actuel doit avoir les droits nécessaires pour créer des éléments de schéma dans la base de données existante.

Pour installer la base de données des États de session sur SQL Server, exécutez le compte Aspnet\_regsql.exe outil et fournir les informations suivantes avec la commande :

- Le nom du serveur SQL de l’instance, à l’aide de la **-S** option.
- Les informations d’identification d’ouverture de session d’un compte qui a l’autorisation de créer une base de données sur un ordinateur exécutant SQL Server. Utilisez le **-E** possibilité d’utiliser l’utilisateur actuellement connecté, ou utiliser le **- U** permettant de spécifier un ID utilisateur avec le **-P** permettant de spécifier un mot de passe.
- Le **- ssadd** une option de ligne de commande pour ajouter la base de données des États de session.

Par défaut, vous ne pouvez pas utiliser le compte Aspnet\_outil regsql.exe pour installer la base de données des États de session sur un ordinateur exécutant SQL Server 2005 Express Edition.

### <a name="the-aspnet-browser-registration-tool---aspnetregbrowsersexe"></a>L’outil d’inscription de ASP.NET navigateur - aspnet\_regbrowsers.exe

Dans la version 1.1 d’ASP.NET, le fichier Machine.config contenait une section appelée &lt;browserCaps&gt;. Cette section contenue une série d’entrées XML qui définissait les configurations pour différents navigateurs selon une expression régulière. ASP.NET version 2.0, un nouveau. BROWSER définit les paramètres d’un navigateur particulier à l’aide d’entrées XML. Vous ajoutez des informations sur une nouvelle fenêtre de navigateur en ajoutant un nouveau. Fichier de navigateur dans le dossier situé dans %SystemRoot%\Microsoft.NET\Framework\version\CONFIG\Browsers sur votre système.

Une application ne lit pas un fichier .config chaque fois qu’il requiert des informations du navigateur, vous pouvez créer un nouveau. BROWSER et exécution Aspnet\_regbrowsers.exe pour ajouter les modifications requises à l’assembly. Cela permet d’accéder immédiatement aux nouvelles informations du navigateur pour ne pas avoir à fermer toutes vos applications pour recueillir les informations sur le serveur. Une application peut accéder aux fonctionnalités du navigateur via la propriété de navigateur de la requête HTTP actuelle.

Les options suivantes sont disponibles lors de l’exécution aspnet\_regbrowser.exe :

| **Option** | **Description** |
| --- | --- |
| **-?** | Affiche le compte Aspnet\_regbbrowsers.exe texte d’aide dans la fenêtre de commande. |
| **-i** | Crée l’assembly de fonctionnalités de navigateur runtime et l’installe dans le global assembly cache. |
| **-u** | Désinstalle l’assembly de fonctionnalités de navigateur runtime à partir du global assembly cache. |

## <a name="the-aspnet-compilation-tool---aspnetcompilerexe"></a>L’outil de Compilation ASP.NET - aspnet\_compiler.exe

L’outil de Compilation ASP.NET peut être utilisé de deux façons : pour la compilation sur place et pour le déploiement, où un répertoire de sortie cible est spécifié.

### <a name="compiling-an-application-in-placehttpsmsdnmicrosoftcomlibraryms229863aspx"></a>[La compilation d’une Application sur Place](https://msdn.microsoft.com/library/ms229863.aspx)

L’outil de Compilation ASP.NET peut compiler une application sur place, autrement dit, il reproduit le comportement de faire plusieurs demandes à l’application, ce qui entraîne la compilation normale. Les utilisateurs d’un site précompilé pas un retard peut être provoqué par la compilation de la page sur la première demande.

Lors de la précompilation d’un site en place, les éléments suivants s’appliquent :

- Le site conserve ses fichiers et la structure de répertoires.
- Vous devez posséder des compilateurs pour tous les langages de programmation utilisés par le site sur le serveur.
- Si n’importe quel fichier échoue, l’intégralité du site échoue.

Vous pouvez également recompiler une application sur place après l’ajout de nouveaux fichiers sources à celui-ci. L’outil compile uniquement les fichiers nouveaux ou modifiés sauf si vous incluez le **- c** option.

> [!NOTE]
> Compilation d’une application qui contient une application imbriquée ne compile pas l’application imbriquée. L’application imbriquée doit être compilée séparément.


### <a name="compiling-an-application-for-deploymenthttpsmsdnmicrosoftcomlibraryms229863aspx"></a>[Compilation d’une Application pour le déploiement](https://msdn.microsoft.com/library/ms229863.aspx)

Vous compilez une application pour le déploiement (compilation vers un emplacement cible) en spécifiant le paramètre targetDir. Le targetDir peut être l’emplacement final pour l’application Web, ou l’application compilée peut être encore déployée. À l’aide de la **-u** option compile l’application de sorte que vous pouvez apporter des modifications à certains fichiers dans l’application compilée sans la recompiler. ASPNET\_compiler.exe établit une distinction entre les types de fichiers statiques et dynamiques et les traite différemment lors de la création de l’application résultante.

- Types de fichiers statiques sont ceux qui ne pas avoir un compilateur associées ou générer fournisseur, telles que les fichiers dont nommé ont les extensions .css, .gif, .htm, .html, .jpg, .js et ainsi de suite. Ces fichiers sont simplement copiés vers l’emplacement cible, avec leur position relative dans la structure de répertoires conservés.
- Types de fichier dynamiques sont ceux qui ont un compilateur associé ou de génération de fournisseur, y compris les fichiers avec les extensions de nom de fichier spécifiques à ASP.NET telles que .asax, .ascx, .ashx, .aspx, Browser, .master et ainsi de suite. L’outil de Compilation ASP.NET génère des assemblys à partir de ces fichiers. Si le **-u** option est omise, l’outil crée également des fichiers avec l’extension de nom de fichier. COMPILED qui mappent les fichiers source d’origine à leur assembly. Pour vous assurer que la structure de répertoires de la source de l’application est conservée, l’outil génère des fichiers d’espace réservé dans les emplacements correspondants dans l’application cible.

Vous devez utiliser le **-u** option pour indiquer que le contenu de l’application compilée peut être modifié. Sinon, les modifications ultérieures sont ignorées ou provoquent des erreurs d’exécution.

Le tableau suivant décrit comment la Compilation ASP.NET outil handles différents types de fichier lorsque la **-u** option est incluse.

| **Type de fichier** | **Action de compilateur** |
| --- | --- |
| .ascx, .aspx, .master | Ces fichiers sont répartis entre le balisage et le code source, qui inclut les fichiers code-behind et tout code qui est placé entre &lt;script runat = « server »&gt; éléments. Code source est compilé dans des assemblys, avec des noms qui sont dérivés d’un algorithme de hachage, et les assemblys sont placés dans le répertoire Bin. Tout code incorporé, c'est-à-dire, le code placé entre le  **&lt; %**  et  **% &gt;**  des crochets, est inclus avec le balisage et non compilé. Nouveaux fichiers portant le même nom que les fichiers sources sont créés pour contenir le balisage et placés dans les répertoires de sortie correspondante. |
| .ashx, .asmx | Ces fichiers ne sont pas compilés et sont déplacés vers les répertoires de sortie et pas compilé. Si vous souhaitez avoir compilé le code du gestionnaire, placez le code dans des fichiers de code source dans l’application\_répertoire de Code. |
| .cs, .vb, .jsl, .cpp (sans les fichiers code-behind pour les types de fichiers répertoriés plus haut) | Ces fichiers sont compilés et inclus en tant que ressource dans les assemblys qui y fait référence. Fichiers sources ne sont pas copiés dans le répertoire de sortie. Si un fichier de code n’est pas référencé, il n’est pas compilé. |
| Types de fichiers personnalisés | Ces fichiers ne sont pas compilés. Ces fichiers sont copiés vers les répertoires de sortie correspondants. |
| Dans l’application, les fichiers de code source\_sous-répertoire de Code | Ces fichiers sont compilés dans des assemblys et placés dans le répertoire Bin. |
| fichiers .resx et .resource dans l’application\_GlobalResources sous-répertoire | Ces fichiers sont compilés dans des assemblys et placés dans le répertoire Bin. Aucune application\_GlobalResources sous-répertoire est créé sous le répertoire de sortie principal, et aucun fichier .resx ou .resources situé dans le répertoire source n’est copiés vers les répertoires de sortie. |
| fichiers .resx et .resource dans l’application\_LocalResources sous-répertoire | Ces fichiers ne sont pas compilés et sont copiés vers les répertoires de sortie correspondante. |
| fichiers .skin dans l’application\_sous-répertoire de thèmes | Les fichiers .skin et les fichiers de thème statiques ne sont pas compilés et sont copiés vers les répertoires de sortie correspondante. |
| Browser les fichiers Web.config statique types des assemblys déjà présents dans le répertoire Bin | Ces fichiers sont copiés en l’état pour les répertoires de sortie. |

Le tableau suivant décrit comment la Compilation ASP.NET outil handles différents types de fichier lorsque la **-u** option est omise.

| **Type de fichier** | **Action de compilateur** |
| --- | --- |
| .aspx, .asmx, .ashx, .master | Ces fichiers sont répartis entre le balisage et le code source, qui inclut les fichiers code-behind et tout code qui est placé entre &lt;script runat = « server »&gt; éléments. Code source est compilé dans des assemblys, avec des noms qui sont dérivés d’un algorithme de hachage. Les assemblys résultants sont placés dans le répertoire Bin. Tout code incorporé, c'est-à-dire, le code placé entre le  **&lt; %**  et  **% &gt;**  des crochets, est inclus avec le balisage et non compilé. Le compilateur crée de nouveaux fichiers pour contenir le balisage avec le même nom que les fichiers sources. Ces fichiers résultants sont placés dans le répertoire Bin. Le compilateur crée également des fichiers portant le même nom que les fichiers sources, mais avec l’extension. COMPILED qui contiennent des informations de mappage. La barre d’outils. Fichiers compilés sont placés dans les répertoires de sortie correspondant à l’emplacement d’origine des fichiers sources. |
| .ascx | Ces fichiers sont répartis entre le balisage et le code source. Code source est compilé dans des assemblys et placé dans le répertoire Bin, avec des noms qui sont dérivés d’un algorithme de hachage. Aucun fichier de balisage n’est générés. |
| .cs, .vb, .jsl, .cpp (sans les fichiers code-behind pour les types de fichiers répertoriés plus haut) | Le code source qui est référencé par les assemblys générés à partir des fichiers .aspx, .ashx ou .ascx est compilé dans des assemblys et placé dans le répertoire Bin. Aucun fichier source n’est copiées. |
| Types de fichiers personnalisés | Ces fichiers sont compilés comme des fichiers dynamiques. Selon le type de fichier que dont ils dépendent, le compilateur peut placer les fichiers de mappage dans les répertoires de sortie. |
| Fichiers dans l’application\_sous-répertoire de Code | Les fichiers de code source dans ce sous-répertoire sont compilés dans des assemblys et placés dans le répertoire Bin. |
| Fichiers dans l’application\_GlobalResources sous-répertoire | Ces fichiers sont compilés dans des assemblys et placés dans le répertoire Bin. Aucune application\_GlobalResources sous-répertoire est créé sous le répertoire de sortie principal. Si le fichier de configuration spécifie appliesTo = « All », les fichiers .resx et .resources sont copiés vers les répertoires de sortie. Ils ne sont pas copiés s’ils sont référencés par un [BuildProvider](https://msdn.microsoft.com/library/system.web.configuration.buildprovider.aspx). |
| fichiers .resx et .resource dans l’application\_LocalResources sous-répertoire | Ces fichiers sont compilés dans des assemblys avec des noms uniques et placés dans le répertoire Bin. Aucun fichier .resx ou .resource n’est copiés vers les répertoires de sortie. |
| fichiers .skin dans l’application\_sous-répertoire de thèmes | Thèmes sont compilés dans des assemblys et placés dans le répertoire Bin. Les fichiers stub sont créés pour les fichiers .skin et placés dans le répertoire de sortie correspondante. Fichiers statiques (tels que .css) sont copiés vers les répertoires de sortie. |
| Browser les fichiers Web.config statique types des assemblys déjà présents dans le répertoire Bin | Ces fichiers sont copiées dans le répertoire de sortie. |

### <a name="fixed-assembly-nameshttpsmsdnmicrosoftcomlibraryms229863aspx"></a>[Noms d’assemblys fixes](https://msdn.microsoft.com/library/ms229863.aspx##)

Certains scénarios, tels que le déploiement d’une application Web à l’aide de MSI Windows Installer, requièrent l’utilisation de noms de fichier cohérent et contenu, ainsi que les structures de répertoire cohérentes pour identifier des assemblys ou des paramètres de configuration des mises à jour. Dans ce cas, vous pouvez utiliser la **- fixednames** option pour spécifier que l’outil de Compilation ASP.NET doit compiler un assembly pour chaque fichier source au lieu d’utiliser la méthode où plusieurs pages sont compilées dans des assemblys. Cette opération peut générer un grand nombre d’assemblys, si vous êtes concerné par l’évolutivité, vous devez utiliser cette option avec précaution.

### <a name="strong-name-compilationhttpsmsdnmicrosoftcomlibraryms229863aspx"></a>[Compilation avec nom fort](https://msdn.microsoft.com/library/ms229863.aspx##)

Le **- aptca**, **- delaysign**, **- keycontainer** et **- keyfile** options sont fournies afin que vous puissiez utiliser Aspnet\_ Compiler.exe créer fortement nommés assemblys sans utiliser le [Strong Name Tool (Sn.exe)](https://msdn.microsoft.com/library/k5b5tt23.aspx) séparément. Ces options correspondent respectivement à **AllowPartiallyTrustedCallersAttribute**, **AssemblyDelaySignAttribute**, **AssemblyKeyNameAttribute**et  **AssemblyKeyFileAttribute**.

Ces attributs ne sont en dehors de l’étendue de ce cours.

## <a name="labs"></a>Ateliers pratiques

Chacune des pratiques suivantes s’appuie sur les travaux pratiques précédents. Vous devez les exécuter dans l’ordre.

## <a name="lab-1-using-the-configuration-api"></a>Atelier 1 : À l’aide de l’API de Configuration

1. Créer un nouveau site Web appelé *mod9lab*.
2. Ajouter un nouveau fichier de Configuration Web au site.
3. Ajoutez le code suivant au fichier web.config :


[!code-xml[Main](configuration-and-instrumentation/samples/sample14.xml)]

Cela garantit que vous êtes autorisé à enregistrer les modifications apportées au fichier web.config.

1. Ajouter un nouveau contrôle Label vers Default.aspx et modifiez le code pour **lblDebugStatus**.
2. Ajouter un nouveau contrôle de bouton vers Default.aspx.
3. Modifier l’ID du contrôle de bouton à **btnToggleDebug** et le texte à **état de débogage basculer**.
4. Afficher le Code pour le fichier code-behind de Default.aspx et ajoutez un **à l’aide de** instruction pour **System.Web.Configuration** comme suit :


[!code-csharp[Main](configuration-and-instrumentation/samples/sample15.cs)]

1. Ajoutez deux variables privées à la classe et une Page\_Init (méthode) comme indiqué ci-dessous :


[!code-csharp[Main](configuration-and-instrumentation/samples/sample16.cs)]

1. Ajoutez le code suivant à la Page\_charge :


[!code-csharp[Main](configuration-and-instrumentation/samples/sample17.cs)]

1. Enregistrez et parcourir default.aspx. Notez que le contrôle d’étiquette affiche l’état en cours de débogage.
2. Double-cliquez sur le contrôle de bouton dans le concepteur et ajoutez le code suivant à l’événement Click pour le contrôle bouton :


[!code-csharp[Main](configuration-and-instrumentation/samples/sample18.cs)]

1. Enregistrer et default.aspx et cliquez sur le bouton.
2. Ouvrez le fichier web.config après chaque bouton, cliquez sur et observez le **déboguer** d’attribut dans le &lt;compilation&gt; section.

## <a name="lab-2-logging-application-restarts"></a>Atelier 2 : Journalisation de le redémarrage de l’Application

Dans cet atelier, vous allez créer le code qui vous permet d’activer/désactiver la journalisation de recompilations dans l’Observateur d’événements, des Démarrages et arrêts de l’application.

1. Ajouter un contrôle DropDownList vers default.aspx et remplacez l’ID ddlLogAppEvents.
2. Définir le **AutoPostBack** propriété DropDownList pour **true**.
3. Ajoutez trois éléments à la collection d’éléments pour DropDownList. Rendre le **texte** pour le premier élément *Select Value* et la valeur -1. Rendre le **texte** et **valeur** du deuxième élément **True** et **texte** et **valeur** du troisième élément **False**.
4. Ajouter une nouvelle étiquette vers default.aspx. Modifier le code pour **lblLogAppEvents**.
5. Ouvrez l’affichage de code-behind pour default.aspx et ajouter une nouvelle déclaration d’une variable de type HealthMonitoringSection comme indiqué ci-dessous :


[!code-csharp[Main](configuration-and-instrumentation/samples/sample19.cs)]

1. Ajoutez le code suivant pour le code existant dans la Page\_Init :


[!code-csharp[Main](configuration-and-instrumentation/samples/sample20.cs)]

1. Double-cliquez sur DropDownList et ajoutez le code suivant à l’événement SelectedIndexChanged :


[!code-csharp[Main](configuration-and-instrumentation/samples/sample21.cs)]

1. Parcourir default.aspx.
2. La liste déroulante la valeur **False**.
3. Effacer le journal des applications dans l’Observateur d’événements.
4. Cliquez sur le bouton pour modifier l’attribut de débogage de l’application.
5. Actualiser le journal des applications dans l’Observateur d’événements. 

    1. Tous les événements ont été connectés ?
    2. Pourquoi ou pourquoi pas ?
6. La liste déroulante la valeur **True.**
7. Cliquez sur le bouton pour basculer de l’attribut de débogage de l’application.
8. Actualiser la connexion de l’Application l’Observateur d’événements. 

    1. Tous les événements ont été connectés ?
    2. Quel était le motif de l’arrêt de l’application ?
9. Faire des essais avec activer et désactiver la journalisation et examiner les modifications apportées au fichier web.config.

## <a name="more-information"></a>Plus d’informations :

ASP.NET du 2.0 modèle de fournisseur vous permet de créer vos propres fournisseurs pour l’instrumentation non seulement de l’application, mais de nombreux autres usages ainsi telles que l’appartenance, profils, etc. Pour plus d’informations sur l’écriture d’un fournisseur personnalisé pour le journal des événements d’application dans un fichier texte, visitez [ce lien](https://msdn.microsoft.com/library/default.asp?url=/library/dnaspp/html/ASPNETProvMod_Prt6.asp).
