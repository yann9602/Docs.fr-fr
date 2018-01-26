---
uid: web-forms/overview/moving-to-aspnet-20/caching
title: La mise en cache | Documents Microsoft
author: microsoft
description: "Compréhension de la mise en cache est importante pour une application ASP.NET et performant. ASP.NET 1.x proposé trois options différentes pour la mise en cache ; sortie mise en cache..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 2bb109d2-e299-46ea-9054-fa0263b59165
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/caching
msc.type: authoredcontent
ms.openlocfilehash: 9b229de60e09b94189f62a6bb6fa61a9973d637b
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="caching"></a>Mise en cache
====================
par [Microsoft](https://github.com/microsoft)

> Compréhension de la mise en cache est importante pour une application ASP.NET et performant. ASP.NET 1.x proposé trois options différentes pour la mise en cache ; la mise en cache de sortie, la mise en cache de fragment et l’API du cache.


Compréhension de la mise en cache est importante pour une application ASP.NET et performant. ASP.NET 1.x proposé trois options différentes pour la mise en cache ; la mise en cache de sortie, la mise en cache de fragment et l’API du cache. ASP.NET 2.0 offre les trois de ces méthodes, mais il ajoute des fonctionnalités supplémentaires significatives. Il existe plusieurs nouvelles dépendances de cache et les développeurs disposent désormais de créer également les dépendances de cache personnalisé. La configuration de la mise en cache a également été considérablement améliorée dans ASP.NET 2.0.

## <a name="new-features"></a>Nouvelles fonctionnalités

## <a name="cache-profiles"></a>Profils de cache

Les profils de cache permettent aux développeurs de définir les paramètres de cache spécifiques qui peuvent ensuite être appliqués à des pages individuelles. Par exemple, si vous avez certaines pages du cache doivent expirer après 12 heures, vous pouvez créer facilement un profil de cache qui peut être appliqué à ces pages. Pour ajouter un nouveau profil de cache, utilisez la &lt;outputCacheSettings&gt; section dans le fichier de configuration. Par exemple, voici la configuration d’un profil de cache appelé *twoday* qui configure une durée de 12 heures.

[!code-xml[Main](caching/samples/sample1.xml)]

Pour appliquer ce profil de cache à une page spécifique, utilisez l’attribut CacheProfile de la directive @ OutputCache comme indiqué ci-dessous :

[!code-aspx[Main](caching/samples/sample2.aspx)]

## <a name="custom-cache-dependencies"></a>Dépendances de Cache personnalisé

Les développeurs ASP.NET 1.x crié dépendances de cache personnalisé. Dans ASP.NET 1.x, la classe CacheDependency est sealed qui permet aux développeurs a empêché de dériver leurs propres classes à partir de celui-ci. Dans ASP.NET 2.0, cette restriction est supprimée et les développeurs sont libres de développer leurs propres dépendances de cache personnalisé. La classe CacheDependency permet la création d’une dépendance de cache personnalisé basée sur les fichiers, les répertoires ou les clés de cache.

Par exemple, le code ci-dessous crée une nouvelle dépendance de cache personnalisé basée sur un fichier appelé stuff.xml situé à la racine de l’application Web :

[!code-csharp[Main](caching/samples/sample3.cs)]

Dans ce scénario, lorsque le fichier stuff.xml change, l’élément mis en cache est invalidée.

Il est également possible de créer une dépendance de cache personnalisé à l’aide de clés de cache. À l’aide de cette méthode, la suppression de la clé de cache invalide les données en cache. L'exemple suivant illustre ce comportement :

[!code-csharp[Main](caching/samples/sample4.cs)]

Pour invalider l’élément qui a été insérée au-dessus, supprimez simplement l’élément a été inséré dans le cache en tant que la clé de cache.

[!code-csharp[Main](caching/samples/sample5.cs)]

Notez que la clé de l’élément qui agit en tant que la clé de cache doit être identique à la valeur ajoutée dans le tableau de clés de cache.

## <a name="polling-based-sql-cache-dependenciesalso-called-table-based-dependencies"></a>Reposant sur l’interrogation des dépendances de Cache SQL*(également appelé dépendances basées sur une Table)*

SQL Server 7 et 2000 utilisent le modèle basé sur l’interrogation pour les dépendances de cache SQL. Le modèle basé sur l’interrogation utilise un déclencheur sur une table de base de données qui est déclenchée lorsque des modifications des données dans la table. Qui déclenchent des mises à jour un **changeId** de la table de notification ASP.NET vérifie régulièrement. Si le **changeId** champ a été mis à jour, ASP.NET sait que les données ont changé et n’invalide les données en cache.

> [!NOTE]
> SQL Server 2005 peuvent également utiliser le modèle basé sur l’interrogation, mais étant donné que le modèle basé sur l’interrogation n’est pas le modèle plus efficace, il est recommandé d’utiliser un modèle de requête (décrit plus loin) avec SQL Server 2005.


Dans l’ordre pour une dépendance de cache SQL à l’aide du modèle basé sur l’interrogation fonctionne correctement, les tables doivent avoir des notifications sont activées. Cela peut être accompli par programme à l’aide de la classe SqlCacheDependencyAdmin ou en utilisant le compte aspnet\_regsql.exe utilitaire.

La ligne de commande suivante enregistre la table Products dans la base de données Northwind situé sur une instance de SQL Server nommée *dbase* SQL de la dépendance de cache.

[!code-console[Main](caching/samples/sample6.cmd)]

Voici une explication des commutateurs de ligne de commande utilisés dans la commande ci-dessus :

| **Commutateur de ligne de commande** | **Fonction** |
| --- | --- |
| -S *server* | Spécifie le nom du serveur. |
| -ed | Spécifie que la base de données doit être activé pour la dépendance de cache SQL. |
| -d *base de données\_nom* | Spécifie le nom de la base de données qui doit être activé pour la dépendance de cache SQL. |
| -E | Spécifie qu’aspnet\_regsql doit utiliser l’authentification Windows lors de la connexion à la base de données. |
| -et | Spécifie que nous sommes activation d’une table de base de données pour la dépendance de cache SQL. |
| -t *table\_nom* | Spécifie le nom de la table de base de données à activer pour la dépendance de cache SQL. |

> [!NOTE]
> Autres commutateurs sont disponibles pour aspnet\_regsql.exe. Pour obtenir la liste complète, exécutez aspnet\_regsql.exe- ? à partir d’une ligne de commande.


Lorsque cette commande exécute les modifications suivantes sont apportées à la base de données SQL Server :

- Un **AspNet\_SqlCacheTablesForChangeNotification** table est ajoutée. Cette table contient une ligne pour chaque table dans la base de données pour lesquelles une dépendance de cache SQL a été activée.
- Les procédures stockées suivantes sont créées à l’intérieur de la base de données :


| AspNet\_SqlCachePollingStoredProcedure | Interroge le compte AspNet\_SqlCacheTablesForChangeNotification table et retourne toutes les tables qui sont activées pour la dépendance de cache SQL et la valeur de changeId pour chaque table. Cette procédure stockée est utilisée pour l’interrogation pour déterminer si les données ont changé. |
| --- | --- |
| AspNet\_SqlCacheQueryRegisteredTablesStoredProcedure | Retourne toutes les tables activées pour la dépendance de cache SQL en interrogeant le compte AspNet\_SqlCacheTablesForChangeNotification table et retourne toutes les tables activées pour SQL la dépendance de cache. |
| AspNet\_SqlCacheRegisterTableStoredProcedure | Inscrit une table pour la dépendance de cache SQL en ajoutant l’entrée nécessaire dans la table de notifications et ajoute le déclencheur. |
| AspNet\_SqlCacheUnRegisterTableStoredProcedure | Annule l’inscription d’une table pour la dépendance de cache SQL en supprimant l’entrée dans la table de notifications et supprime le déclencheur. |
| AspNet\_SqlCacheUpdateChangeIdStoredProcedure | Met à jour la table de notifications en incrémentant le changeId sur la table modifiée. ASP.NET utilise cette valeur pour déterminer si les données ont changé. Comme indiqué ci-dessous, cette procédure stockée est exécutée par le déclencheur créé lors de la table est activée. |


- Un déclencheur SQL Server appelée ***table\_nom *\_AspNet\_SqlCacheNotification\_déclencheur** est créé pour la table. Ce déclencheur s’exécute le compte AspNet\_SqlCacheUpdateChangeIdStoredProcedure lorsqu’un INSERT, UPDATE ou DELETE est exécutée sur la table.
- Un rôle SQL Server appelé **aspnet\_ChangeNotification\_ReceiveNotificationsOnlyAccess** est ajouté à la base de données.

Le **aspnet\_ChangeNotification\_ReceiveNotificationsOnlyAccess** rôle SQL Server dispose des autorisations d’exécution pour le compte AspNet\_SqlCachePollingStoredProcedure. Dans l’ordre pour le modèle d’interrogation fonctionne correctement, vous devez ajouter votre compte de processus pour le compte aspnet\_ChangeNotification\_ReceiveNotificationsOnlyAccess rôle. Le compte aspnet\_regsql.exe outil n’effectue cela pour vous.

### <a name="configuring-polling-based-sql-cache-dependencies"></a>Configuration des dépendances de Cache basée sur l’interrogation de SQL

Il existe plusieurs étapes qui sont requises pour la configuration basée sur l’interrogation des dépendances de cache SQL. La première étape consiste à activer la base de données et la table, comme expliqué ci-dessus. Une fois cette étape terminée, le reste de la configuration est la suivante :

- Configuration du fichier de configuration ASP.NET.
- Configuration SqlCacheDependency

### <a name="configuring-the-aspnet-configuration-file"></a>Configuration du fichier de Configuration ASP.NET

En plus d’ajouter une chaîne de connexion comme indiqué dans un module précédent, vous devez également configurer un &lt;cache&gt; élément avec un &lt;sqlCacheDependency&gt; comme indiqué ci-dessous :

[!code-xml[Main](caching/samples/sample7.xml)]

Cette configuration permet à une dépendance de cache SQL sur le *pubs* base de données. Notez que le pollTime d’attribut dans le &lt;sqlCacheDependency&gt; élément par défaut est 60 000 millisecondes ou égale à 1 minute. (Cette valeur ne peut pas être inférieure à 500 millisecondes.) Dans cet exemple, le &lt;ajouter&gt; élément ajoute une nouvelle base de données et remplace le pollTime, la valeur 9000000 millisecondes.

#### <a name="configuring-the-sqlcachedependency"></a>Configuration SqlCacheDependency

L’étape suivante consiste à configurer SqlCacheDependency. Pour ce faire, le plus simple consiste à spécifier la valeur de l’attribut SqlDependency dans la directive @ Outcache comme suit :

[!code-aspx[Main](caching/samples/sample8.aspx)]

Dans la directive @ OutputCache ci-dessus, une dépendance de cache SQL est configurée pour le *auteurs* de table dans le *pubs* base de données. Plusieurs dépendances peuvent être configurés en les séparant par un point-virgule comme suit :

[!code-aspx[Main](caching/samples/sample9.aspx)]

Une autre méthode de configuration SqlCacheDependency consiste à le faire par programme. Le code suivant crée une nouvelle dépendance de cache SQL sur le *auteurs* de table dans le *pubs* base de données.

[!code-csharp[Main](caching/samples/sample10.cs)]

Un des avantages de la définition par programme de la dépendance de cache SQL est que vous pouvez gérer toutes les exceptions qui peuvent se produire. Par exemple, si vous essayez de définir une dépendance de cache SQL pour une base de données n’a pas été activée pour la notification d’un **DatabaseNotEnabledForNotificationException** exception sera levée. Dans ce cas, vous pouvez essayer d’activer la base de données pour les notifications en appelant le **SqlCacheDependencyAdmin.EnableNotifications** (méthode) et en lui passant le nom de la base de données.

De même, si vous essayez de définir une dépendance de cache SQL pour une table qui n’a pas été activée pour la notification d’un **TableNotEnabledForNotificationException** sera levée. Vous pouvez ensuite appeler la **SqlCacheDependencyAdmin.EnableTableForNotifications** méthode en lui passant le nom de la base de données et le nom de la table.

L’exemple de code suivant illustre comment configurer la gestion des exceptions lors de la configuration d’une dépendance de cache SQL.

[!code-csharp[Main](caching/samples/sample11.cs)]

Plus d’informations : [https://msdn.microsoft.com/library/t9x04ed2.aspx](https://msdn.microsoft.com/library/t9x04ed2.aspx)

## <a name="query-based-sql-cache-dependencies-sql-server-2005-only"></a>Dépendances de Cache basée sur une requête SQL (SQL Server 2005 uniquement)

Lorsque vous utilisez SQL Server 2005 pour la dépendance de cache SQL, le modèle basé sur l’interrogation n’est pas nécessaire. Lorsqu’il est utilisé avec SQL Server 2005, les dépendances de cache SQL communiquent directement par le biais de connexions SQL à l’instance de SQL Server (aucune configuration supplémentaire n’est nécessaire) à l’aide de notifications de requête SQL Server 2005.

Pour activer la notification de requête le plus simple consiste à faire de façon déclarative en définissant le **SqlCacheDependency** attribut de l’objet de source de données à **CommandNotification** et en définissant le **EnableCaching** attribut **true**. À l’aide de cette méthode, aucun code n’est requis. Si le résultat d’une commande exécutée sur les données source est modifié, il invalide les données du cache.

L’exemple suivant configure un contrôle de source de données pour la dépendance de cache SQL :

[!code-aspx[Main](caching/samples/sample12.aspx)]

Dans ce cas, si la requête spécifiée dans le **SelectCommand** retourne un résultat différent qu’elle ne l’avez fait, les résultats sont mis en cache sont invalidés.

Vous pouvez également spécifier que toutes vos sources de données soit activée pour les dépendances de cache SQL en définissant le **SqlDependency** attribut de la **@ OutputCache** directive **CommandNotification** . L’exemple ci-dessous illustre ce comportement.

[!code-aspx[Main](caching/samples/sample13.aspx)]

> [!NOTE]
> Pour plus d’informations sur les notifications de requête dans SQL Server 2005, consultez la documentation en ligne de SQL Server.


Une autre méthode de configuration d’une dépendance de cache basée sur une requête SQL est à le faire par programmation à l’aide de la classe SqlCacheDependency. L’exemple de code suivant illustre comment cela fonctionne.

[!code-csharp[Main](caching/samples/sample14.cs)]

Plus d’informations : [https://msdn.microsoft.com/library/default.asp?url=/library/enus/dnvs05/html/querynotification.asp](https://msdn.microsoft.com/library/default.asp?url=/library/enus/dnvs05/html/querynotification.asp)

## <a name="post-cache-substitution"></a>Substitution post-cache

Une page mise en cache peut améliorer considérablement les performances d’une application Web. Toutefois, dans certains cas, vous devez la plupart de la page doit être mis en cache et que certains fragments au sein de la page dynamique. Par exemple, si vous créez une page d’actualités qui est entièrement statique pour des périodes définies, vous pouvez définir la page entière doit être mis en cache. Si vous souhaitez inclure une bannière publicitaire tournante changé lors de chaque demande de page, la partie de la page contenant la publicité doit être dynamique. Pour vous permettre de mettre en cache une page, mais remplacer un contenu dynamique, vous pouvez utiliser la substitution post-cache ASP.NET. Avec la substitution post-cache, la page entière est mis en cache avec des parties spécifiques sont marquées comme exemptés de la mise en cache de sortie. Dans l’exemple des bannières publicitaires, le contrôle AdRotator vous permet de tirer parti de substitution post-cache afin que les publicités créées dynamiquement pour chaque utilisateur et pour chaque actualisation de la page.

Il existe trois façons d’implémenter la substitution post-cache :

- À l’aide de façon déclarative, le contrôle de Substitution.
- Par programme, à l’aide de l’API de contrôle de Substitution.
- Implicitement, utilisant le contrôle AdRotator.

### <a name="substitution-control"></a>Contrôle de substitution

Le contrôle de Substitution ASP.NET spécifie une section d’une page mise en cache qui est créée dynamiquement plutôt que mises en cache. Vous placez un contrôle de Substitution à l’emplacement sur la page où vous souhaitez que le contenu dynamique s’affichent. Au moment de l’exécution, le contrôle de Substitution appelle une méthode que vous spécifiez avec la propriété MethodName. La méthode doit retourner une chaîne qui remplace ensuite le contenu du contrôle de Substitution. La méthode doit être une méthode statique sur le contrôle de Page ou UserControl qui le contient. L’utilisation du contrôle de substitution entraîne la mise en cache côté client à modifier dans le cache du serveur, afin que la page n’est pas mis en cache sur le client. Cela garantit que les requêtes ultérieures à la page d’appellent la méthode pour générer le contenu dynamique.

### <a name="substitution-api"></a>API de substitution

Pour créer un contenu dynamique pour une page mise en cache par programme, vous pouvez appeler la [WriteSubstitution](https://msdn.microsoft.com/library/system.web.httpresponse.writesubstitution.aspx) méthode dans votre code de page, en lui passant le nom d’une méthode en tant que paramètre. La méthode qui gère la création du contenu dynamique prend un seul [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.aspx) paramètre et retourne une chaîne. La chaîne retournée est le contenu qui est remplacé à l’emplacement donné. Un avantage de l’appel de la méthode WriteSubstitution au lieu d’utiliser de façon déclarative le contrôle de Substitution est que vous pouvez appeler une méthode de n’importe quel objet arbitraire, plutôt que d’appeler une méthode statique de la Page ou l’objet UserControl.

Appel de la méthode WriteSubstitution provoque la mise en cache côté client à modifier dans le cache du serveur, afin que la page n’est pas mis en cache sur le client. Cela garantit que les requêtes ultérieures à la page d’appellent la méthode pour générer le contenu dynamique.

### <a name="adrotator-control"></a>Contrôle AdRotator

AdRotator implémente du contrôle serveur prend en charge en interne pour la substitution post-cache. Si vous placez un contrôle AdRotator sur votre page, il restituera des publicités uniques à chaque demande, indépendamment de si la mise en cache de la page parente. Par conséquent, une page qui inclut un contrôle AdRotator est mis en cache uniquement du côté serveur.

## <a name="controlcachepolicy-class"></a>Classe de ControlCachePolicy

La classe ControlCachePolicy permet le contrôle par programmation du fragment de la mise en cache à l’aide de contrôles utilisateur. ASP.NET incorpore des contrôles utilisateur dans un [BasePartialCachingControl effectue](https://msdn.microsoft.com/library/system.web.ui.basepartialcachingcontrol.aspx) instance. La classe BasePartialCachingControl effectue représente un contrôle utilisateur qui possède une mise en cache de sortie.

Lorsque vous accédez à la [BasePartialCachingControl.CachePolicy](https://msdn.microsoft.com/library/system.web.ui.basepartialcachingcontrol.cachepolicy.aspx) propriété d’un [contrôle PartialCachingControl](https://msdn.microsoft.com/library/system.web.ui.partialcachingcontrol.aspx) (contrôle), vous recevez toujours un objet ControlCachePolicy valide. Toutefois, si vous accédez à la [UserControl.CachePolicy](https://msdn.microsoft.com/library/system.web.ui.usercontrol.cachepolicy.aspx) propriété d’un [UserControl](https://msdn.microsoft.com/library/system.web.ui.usercontrol.aspx) (contrôle), vous recevez un objet ControlCachePolicy valide uniquement si le contrôle utilisateur est déjà encapsulé par un BasePartialCachingControl effectue le contrôle. S’il n’est pas encapsulé, l’objet ControlCachePolicy retournée par la propriété lèvera des exceptions lorsque vous tentez de manipuler parce qu’il n’a pas un BasePartialCachingControl effectue associé. Pour déterminer si une instance UserControl prend en charge la mise en cache sans générer d’exception, vérifiez le [SupportsCaching](https://msdn.microsoft.com/library/system.web.ui.controlcachepolicy.supportscaching.aspx) propriété.

À l’aide de la classe ControlCachePolicy est de plusieurs manières, que vous pouvez activer la mise en cache de sortie. La liste suivante décrit les méthodes que vous pouvez utiliser pour activer la mise en cache de sortie :

- Utilisez le [@ OutputCache](https://msdn.microsoft.com/library/hdxfb6cy.aspx) directive pour activer la sortie mise en cache dans les scénarios déclaratifs.
- Utilisez le [PartialCachingAttribute](https://msdn.microsoft.com/library/system.web.ui.partialcachingattribute.aspx) attribut pour activer la mise en cache pour un contrôle utilisateur dans un fichier code-behind.
- Utilisez la classe ControlCachePolicy pour spécifier les paramètres de cache dans les scénarios de programmation dans lequel vous travaillez avec des instances BasePartialCachingControl effectue qui ont été activées par cache à l’aide d’une des méthodes précédentes et chargées dynamiquement à l’aide de la [System.Web.UI.TemplateControl.LoadControl](https://msdn.microsoft.com/library/system.web.ui.templatecontrol.loadcontrol.aspx) (méthode).

Une instance de ControlCachePolicy peut être manipulée avec succès uniquement entre les étapes du cycle de vie contrôle Init et PreRender. Si vous modifiez un objet ControlCachePolicy après la phase PreRender, ASP.NET lève une exception, car les modifications apportées après le rendu du contrôle n’affectent pas réellement des paramètres de cache (un contrôle est mis en cache pendant l’étape de rendu). Enfin, une instance de contrôle utilisateur (et par conséquent son objet ControlCachePolicy) est uniquement disponibles pour la manipulation de programmation lorsqu’il est réellement restitué.

## <a name="changes-to-caching-configuration---the-ltcachinggt-element"></a>Modifications apportées à la mise en cache de Configuration - le &lt;mise en cache&gt; élément

Il existe plusieurs modifications apportées à la configuration de mise en cache dans ASP.NET 2.0. Le &lt;mise en cache&gt; élément est nouveau dans ASP.NET 2.0 et vous permet d’apporter des modifications de configuration mise en cache dans le fichier de configuration. Les attributs suivants sont disponibles.

| **Élément** | **Description** |
| --- | --- |
| **cache** | Élément facultatif. Définit les paramètres du cache d’application globale. |
| **outputCache** | Élément facultatif. Spécifie les paramètres de cache de sortie de l’application. |
| **outputCacheSettings** | Élément facultatif. Spécifie les paramètres de cache de sortie qui peuvent être appliqués aux pages dans l’application. |
| **sqlCacheDependency** | Élément facultatif. Configure les dépendances de cache SQL pour une application ASP.NET. |

### <a name="the-ltcachegt-element"></a>Le &lt;cache&gt; élément

Les attributs suivants sont disponibles dans le &lt;cache&gt; élément :

| **Attribut** | **Description** |
| --- | --- |
| **disableMemoryCollection** | Facultatif **Boolean** attribut. Obtient ou définit une valeur indiquant si la collection de la mémoire cache qui se produit lorsque l’ordinateur est soumis à une sollicitation de la mémoire est désactivée. |
| **disableExpiration** | Facultatif **Boolean** attribut. Obtient ou définit une valeur indiquant si l’expiration du cache est désactivé. Lorsque désactivé, les éléments mis en cache n’expirent pas et le nettoyage d’arrière-plan des éléments du cache expirés ne se produit pas. |
| **privateBytesLimit** | Facultatif **Int64** attribut. Obtient ou définit une valeur qui indique la taille maximale d’octets privés de l’application avant que le cache commence à vider les éléments expirés et tente de récupérer de la mémoire. Cette limite comprend la mémoire utilisée par le cache ainsi que la mémoire normal surcharge à partir de l’application en cours d’exécution. Une valeur égale à zéro indique qu’ASP.NET utilisera son propre heuristique pour déterminer le moment auquel démarrer la libération de la mémoire. |
| **percentagePhysicalMemoryUsedLimit** | Facultatif **Int32** attribut. Obtient ou définit une valeur qui indique le pourcentage maximal de mémoire physique d’un ordinateur qui peut être utilisé par une application avant que le cache commence à vider les éléments expirés et tente de récupérer de la mémoire, que cette utilisation de la mémoire inclut la mémoire utilisée par le cache comme l’utilisation de la mémoire normal de l’application en cours d’exécution. Une valeur égale à zéro indique qu’ASP.NET utilisera son propre heuristique pour déterminer le moment auquel démarrer la libération de la mémoire. |
| **privateBytesPollTime** | Facultatif **TimeSpan** attribut. Obtient ou définit une valeur qui indique l’intervalle de temps entre les interrogations de l’utilisation de la mémoire des octets privés de l’application. |

### <a name="the-ltoutputcachegt-element"></a>Le &lt;outputCache&gt; élément

Les attributs suivants sont disponibles pour le &lt;outputCache&gt; élément.

| **Attribut** | **Description** |
| --- | --- |
| **enableOutputCache** | Facultatif **Boolean** attribut. Active/désactive le cache de sortie de page. Si désactivé, aucune page n’est mis en cache, indépendamment des paramètres déclaratives ou par programme. Valeur par défaut est **true**. |
| **enableFragmentCache** | Facultatif **Boolean** attribut. Active/désactive le cache de fragment d’application. Si désactivé, aucune page n’est mis en cache, quel que soit le [@ OutputCache](https://msdn.microsoft.com/library/hdxfb6cy.aspx) directive ou la mise en cache de profil utilisé. Inclut un en-tête cache-control indiquant que les serveurs proxy en amont, ainsi que des clients de navigateur ne doivent pas essayer de sortie de la page du cache. Valeur par défaut est **false**. |
| **sendCacheControlHeader** | Facultatif **Boolean** attribut. Obtient ou définit une valeur indiquant si le **cache-contrôle : private** en-tête est envoyé par le module de cache de sortie par défaut. Valeur par défaut est **false**. |
| **omitVaryStar** | Facultatif **Boolean** attribut. Active/désactive l’envoi d’un Http «**Vary : \*** « en-tête dans la réponse. Avec le paramètre par défaut de la valeur est false, un «**Vary : \*** « en-tête est envoyé pour les pages mises en cache de sortie. Lorsque l’en-tête Vary est envoyé, il permet pour différentes versions doit être mis en cache en fonction de ce qui est spécifié dans l’en-tête Vary. Par exemple, *Vary : utilisateur-Agents* stockera les différentes versions d’une page en fonction de l’agent utilisateur qui émet la demande. Valeur par défaut est **false**. |

### <a name="the-ltoutputcachesettingsgt-element"></a>Le &lt;outputCacheSettings&gt; élément

Le &lt;outputCacheSettings&gt; élément permet la création de profils de cache comme décrit précédemment. Le seul élément enfant pour le &lt;outputCacheSettings&gt; élément est la &lt;outputCacheProfiles&gt; élément pour configurer des profils de cache.

### <a name="the-ltsqlcachedependencygt-element"></a>Le &lt;sqlCacheDependency&gt; élément

Les attributs suivants sont disponibles pour le &lt;sqlCacheDependency&gt; élément.

| **Attribut** | **Description** |
| --- | --- |
| **enabled** | Requis **Boolean** attribut. Indique si les modifications sont interrogées pour. |
| **pollTime** | Facultatif **Int32** attribut. Définit la fréquence à laquelle SqlCacheDependency interroge les modifications dans la table de base de données. Cette valeur correspond au nombre de millisecondes entre des interrogations consécutives. La valeur ne doit pas être inférieure à 500 millisecondes. Valeur par défaut est 1 minute. |

### <a name="more-information"></a>Informations complémentaires

Il existe d’autres informations que vous devez connaître concernant la configuration du cache.

- Si la limite d’octets privés de processus de travail n’est pas définie, le cache utilise une des limites suivantes : 

    - x86 2 Go : 800 Mo ou 60 % de RAM physique, si elle est inférieure
    - x86 3 Go : 1800 Mo ou 60 % de RAM physique, si elle est inférieure
    - x 64 : 1 téraoctet ou 60 % de RAM physique, si elle est inférieure
- Si les deux le processus de travail privé octets limitent et &lt;cache privateBytesLimit /&gt; sont définis, le cache utilisera la valeur minimale des deux.
- Tout comme dans 1.x, nous supprimer les entrées du cache et appelez GC. Collecter pour deux raisons : 

    - Nous sommes très proche de la limite d’octets privés
    - La mémoire disponible est proche ou moins de 10 %
- Vous pouvez efficacement désactiver trim et mettre en cache pour les conditions de mémoire disponible insuffisante en définissant &lt;cache percentagePhysicalMemoryUseLimit /&gt; à 100.
- Contrairement aux 1.x, 2.0 interrompt les appels de découpage et collecter si le dernier GC. Collecter réduit pas les octets privés ou la taille du tas managés par plus de 1 % de la limite de mémoire (cache).

## <a name="lab1-custom-cache-dependencies"></a>Lab1 : Dépendances de Cache personnalisé

1. Créer un nouveau site Web.
2. Ajouter un nouveau fichier XML appelé cache.xml et enregistrez-la à la racine de l’application Web.
3. Ajoutez le code suivant à la Page\_Load (méthode) dans le code-behind de default.aspx : 

    [!code-csharp[Main](caching/samples/sample15.cs)]
4. En haut de default.aspx en mode source, ajoutez le code suivant : 

    [!code-aspx[Main](caching/samples/sample16.aspx)]
5. Parcourir Default.aspx. Que dire le temps ?
6. Actualisez le navigateur. Que dire le temps ?
7. Ouvrez cache.xml et ajoutez le code suivant : 

    [!code-xml[Main](caching/samples/sample17.xml)]
8. Enregistrer cache.xml.
9. Actualisez votre navigateur. Que dire le temps ?
10. Expliquez pourquoi la mise à jour au lieu d’afficher les valeurs mises en cache précédemment :

## <a name="lab-2-using-polling-based-cache-dependencies"></a>Atelier 2 : Dépendances de Cache basées sur l’interrogation à l’aide

Cet atelier utilise le projet que vous avez créé dans le module précédent qui permet de modifier des données dans la base de données Northwind via un contrôle GridView et DetailsView.

1. Ouvrez le projet dans Visual Studio 2005.
2. Exécuter le compte aspnet\_utilitaire regsql par rapport à la base de données Northwind pour activer la base de données et la table Products. Utilisez la commande suivante dans une invite de commandes Visual Studio : 

    [!code-console[Main](caching/samples/sample18.cmd)]
3. Ajoutez le code suivant à votre fichier web.config : 

    [!code-xml[Main](caching/samples/sample19.xml)]
4. Ajouter un nouveau formulaire Web appelé showdata.aspx.
5. Ajoutez le code suivant à la directive outputcache la page showdata.aspx : 

    [!code-aspx[Main](caching/samples/sample20.aspx)]
6. Ajoutez le code suivant à la Page\_charge de showdata.aspx : 

    [!code-html[Main](caching/samples/sample21.html)]
7. Ajouter un nouveau contrôle SqlDataSource pour showdata.aspx et configurez-le pour utiliser la connexion de base de données Northwind. Cliquez sur Suivant.
8. Activez les cases à cocher ProductName et ProductID et cliquez sur Suivant.
9. Cliquez sur Terminer.
10. Ajouter un nouveau GridView à la page showdata.aspx.
11. Dans la liste déroulante, choisissez SqlDataSource1.
12. Enregistrez et parcourir showdata.aspx. Prenez note de l’heure affichée.
