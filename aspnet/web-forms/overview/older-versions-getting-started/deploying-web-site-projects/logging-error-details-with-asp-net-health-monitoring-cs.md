---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/logging-error-details-with-asp-net-health-monitoring-cs
title: "Journalisation des détails de l’erreur avec le contrôle (c#) d’état ASP.NET | Documents Microsoft"
author: rick-anderson
description: "Système de surveillance de l’intégrité de Microsoft fournit un moyen simple et personnalisable pour se connecter à différents événements web, y compris les exceptions non gérées. Ce didacticiel Guide transfert..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/09/2009
ms.topic: article
ms.assetid: b1abb452-642a-4ff3-8504-37b85590ff79
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/logging-error-details-with-asp-net-health-monitoring-cs
msc.type: authoredcontent
ms.openlocfilehash: 06f8b57c8973fff5c07e82100cd43f6757d454f9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="logging-error-details-with-aspnet-health-monitoring-c"></a>Journalisation des détails de l’erreur avec le contrôle (c#) d’état ASP.NET
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/1/0/C/10CC829F-A808-4302-97D3-59989B8F9C01/ASPNET_Hosting_Tutorial_13_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/5/C/5/5C57DB8C-5DEA-4B3A-92CA-4405544D313B/aspnet_tutorial13_HealthMonitoring_cs.pdf)

> Système de surveillance de l’intégrité de Microsoft fournit un moyen simple et personnalisable pour se connecter à différents événements web, y compris les exceptions non gérées. Ce didacticiel décrit comment configurer le contrôle d’intégrité système de surveillance pour enregistrer les exceptions non gérées dans une base de données et pour avertir les développeurs par courrier électronique.


## <a name="introduction"></a>Introduction

La journalisation est un outil utile pour surveiller l’intégrité d’une application déployée et pour diagnostiquer les problèmes qui peuvent survenir. Il est particulièrement important consigner les erreurs qui se produisent dans une application déployée afin qu’il peuvent être résolus. Le `Error` événement est déclenché chaque fois qu’une exception non gérée se produit dans une application ASP.NET ; le [didacticiel précédent](processing-unhandled-exceptions-cs.md) a montré comment avertir un développeur d’une erreur et les journaux de ses détails en créant un gestionnaire d’événements pour le `Error` événement. Toutefois, en créant un `Error` Gestionnaire d’événements pour consigner les détails de l’erreur et de notifier un développeur n’est pas nécessaire, car cette tâche peut être effectuée par ASP. De NET *contrôle d’intégrité système de surveillance*.

Le contrôle d’intégrité système de surveillance a été introduit dans ASP.NET 2.0 et est conçu pour surveiller l’intégrité d’une application ASP.NET déployée par la journalisation des événements qui se produisent pendant la durée de vie de la demande ou de l’application. Les événements enregistrés par le contrôle d’intégrité système de surveillance sont appelés *les événements de contrôle d’intégrité* ou *événements Web*et incluent :

- Événements de durée de vie d’application, telles que lorsqu’une application démarre ou s’arrête
- Événements de sécurité, y compris l’échec de tentatives de connexion et les demandes d’autorisation d’URL
- Erreurs d’application, y compris les exceptions non gérées, afficher l’état de l’analyse des exceptions, les exceptions de validation de demande et les erreurs de compilation, parmi les autres types d’erreurs.

Lorsqu’un contrôle d’intégrité analyse d’événement est déclenché il peut être connecté à n’importe quel nombre de spécifié *connecter des sources de*. Le contrôle d’intégrité système de surveillance est fourni avec des sources de journal du journal des événements Web à une base de données Microsoft SQL Server, dans le journal des événements Windows ou par courrier électronique, entre autres. Vous pouvez également créer vos propres sources de journal.

Les événements de journaux de l’intégrité système de surveillance, ainsi que les sources de journal utilisées, sont définis dans `Web.config`. Avec quelques lignes de balisage de la configuration, vous pouvez utiliser pour vous connecter toutes les exceptions non gérées dans une base de données et pour vous informer de l’exception par courrier électronique de contrôle d’intégrité.

## <a name="exploring-the-health-monitoring-systems-configuration"></a>Exploration de la Configuration du système de contrôle d’état

Le comportement du système de contrôle d’état est défini par ses informations de configuration, ce qui se trouve dans le [ `<healthMonitoring>` élément](https://msdn.microsoft.com/en-us/library/2fwh2ss9.aspx) dans `Web.config`. Cette section de configuration définit, entre autres choses, les trois éléments importants d’information :

1. Les événements de contrôle d’état qui, quand elle est déclenchée, doivent être enregistrés,
2. Les sources de journal, et
3. Comment chaque contrôle d’intégrité analyse l’événement défini dans (1) est mappée aux sources de journal défini dans (2).

Ces informations sont spécifiées dans les éléments de configuration de trois enfants : [ `<eventMappings>` ](https://msdn.microsoft.com/en-us/library/yc5yk01w.aspx), [ `<providers>` ](https://msdn.microsoft.com/en-us/library/zaa41kz1.aspx), et [ `<rules>` ](https://msdn.microsoft.com/en-us/library/fe5wyxa0.aspx), respectivement.

Vous trouverez les informations de configuration système de contrôle d’état par défaut dans le `Web.config` fichier `%WINDIR%\Microsoft.NET\Framework\version\CONFIG` dossier. Ces informations de configuration par défaut, avec certaines balises supprimées par souci de concision, sont indiquées ci-dessous :

[!code-xml[Main](logging-error-details-with-asp-net-health-monitoring-cs/samples/sample1.xml)]

L’état d’intégrité analyse les événements d’intérêt sont définis dans le `<eventMappings>` élément, ce qui donne un nom convivial à une classe d’événements de contrôle d’intégrité. Dans le balisage ci-dessus, le `<eventMappings>` élément attribue le nom convivial « Toutes les erreurs » à l’intégrité de surveillance des événements de type `WebBaseErrorEvent` et le nom « audits des échecs » pour les événements de type de contrôle d’intégrité `WebFailureAuditEvent`.

Le `<providers>` élément définit les sources de journal, en leur donnant un nom convivial et la spécification de toutes les informations de configuration spécifiques à la source du journal. La première `<add>` élément définit le fournisseur « EventLogProvider », qui consigne l’événements à l’aide d’analyse du fonctionnement spécifié la `EventLogWebEventProvider` classe. La `EventLogWebEventProvider` classe consigne l’événement dans le journal des événements Windows. La seconde `<add>` élément définit le fournisseur « SqlWebEventProvider », les journaux des événements à une base de données Microsoft SQL Server via la `SqlWebEventProvider` classe. La configuration « SqlWebEventProvider » spécifie la chaîne de connexion de la base de données (`connectionStringName`) parmi les autres options de configuration.

Le `<rules>` élément mappe les événements spécifiés dans le `<eventMappings>` élément pour connecter des sources de la `<providers>` élément. Par défaut, les applications web ASP.NET consigner toutes les exceptions non gérées et auditer les échecs dans le journal des événements Windows.

## <a name="logging-events-to-a-database"></a>Journalisation des événements dans une base de données

Le contrôle de configuration du système par défaut d’état peut être personnalisé pour les applications de web-par-application web en ajoutant un `<healthMonitoring>` section à l’application `Web.config` fichier. Vous pouvez inclure des éléments supplémentaires dans le `<eventMappings>`, `<providers>`, et `<rules>` sections à l’aide de la `<add>` élément. Pour supprimer un paramètre à partir de l’utilisation de la configuration par défaut le `<remove>` élément, ou utilisez `<clear />` pour supprimer toutes les valeurs par défaut d’une de ces sections. Permet de configurer l’application web critiques pour enregistrer les exceptions de tous les non prise en charge pour une base de données Microsoft SQL Server à l’aide la `SqlWebEventProvider` classe.

La `SqlWebEventProvider` classe fait partie de l’intégrité système de surveillance et consigne un événement à une base de données SQL Server spécifiée de contrôle d’état. Le `SqlWebEventProvider` classe attend que la base de données inclut une procédure stockée nommée `aspnet_WebEvent_LogEvent`. Cette procédure stockée est passée les détails de l’événement et est chargée de stocker les détails de l’événement. La bonne nouvelle est que vous n’avez pas besoin de créer cette procédure procédure ni la table pour stocker les détails de l’événement. Vous pouvez ajouter ces objets dans votre base de données en utilisant le `aspnet_regsql.exe` outil.

> [!NOTE]
> Le `aspnet_regsql.exe` outil a été traité dans le [ *configuration d’un site Web qu’utilise les Services d’Application* didacticiel](configuring-a-website-that-uses-application-services-cs.md) lorsque nous avons ajouté la prise en charge ASP. Services d’application de NET. Par conséquent, base de données du site Web critiques contient déjà le `aspnet_WebEvent_LogEvent` procédure stockée, qui stocke les informations d’événement dans une table nommée `aspnet_WebEvent_Events`.


Une fois que la procédure stockée nécessaire et la table ajoutée à votre base de données, il ne reste qu’à indiquer pour consigner toutes les exceptions non gérées dans la base de données de contrôle d’intégrité. Cela en ajoutant le balisage suivant à votre site Web `Web.config` fichier :

[!code-xml[Main](logging-error-details-with-asp-net-health-monitoring-cs/samples/sample2.xml)]

Le contrôle d’intégrité analyse le balisage de configuration ci-dessus utilise `<clear />` éléments à réinitialiser l’intégrité prédéfinie qui analyse les informations de configuration de la `<eventMappings>`, `<providers>`, et `<rules>` sections. Il ajoute ensuite une entrée unique pour chacune de ces sections.

- Le `<eventMappings>` élément définit un événement d’intérêt nommé « Toutes les erreurs », ce qui est déclenché chaque fois qu’une exception non gérée se produit de contrôle d’état unique.
- Le `<providers>` élément définit une source de journal unique nommée « SqlWebEventProvider » qui utilise le `SqlWebEventProvider` classe. Le `connectionStringName` attribut a été défini sur « ReviewsConnectionString », qui est le nom de notre connexion chaîne définie dans la `<connectionStrings>` section.
- Enfin, le &lt;règles&gt; élément indique que lorsqu’un événement de « Toutes les erreurs » est apparu qu’il doit être enregistré à l’aide du fournisseur « SqlWebEventProvider ».

Ces informations de configuration fait en sorte que le contrôle d’intégrité système pour consigner toutes les exceptions non gérées dans la base de données critiques de surveillance.

> [!NOTE]
> Le `WebBaseErrorEvent` événement est déclenché uniquement pour les erreurs de serveur ; il n’est pas déclenché pour les erreurs HTTP, telle qu’une demande pour une ressource d’ASP.NET est introuvable. Ce comportement diffère de la `HttpApplication` de classe `Error` événement, qui est déclenché pour le serveur et les erreurs HTTP.


Pour afficher le contrôle d’intégrité système dans l’action d’analyse, visitez le site Web et générer une erreur d’exécution en vous rendant sur `Genre.aspx?ID=foo`. Vous devez voir la page d’erreur appropriée - Exception détails jaune écran de décès (lors de la visite localement) ou de la page d’erreur personnalisée (lors de la visite du site de production). En arrière-plan, le contrôle d’intégrité système de surveillance enregistrés les informations d’erreur dans la base de données. Il doit y avoir un enregistrement dans le `aspnet_WebEvent_Events` table (consultez **Figure 1**) ; cet enregistrement contient des informations sur l’erreur d’exécution qui vient de se produire.

[![](logging-error-details-with-asp-net-health-monitoring-cs/_static/image2.png)](logging-error-details-with-asp-net-health-monitoring-cs/_static/image1.png)

**Figure 1**: détails de l’erreur ont été consignés à le `aspnet_WebEvent_Events` Table  
([Cliquez pour afficher l’image en taille réelle](logging-error-details-with-asp-net-health-monitoring-cs/_static/image3.png))

### <a name="displaying-the-error-log-in-a-web-page"></a>Afficher le journal des erreurs dans une Page Web

Avec la configuration du site Web en cours, le contrôle d’intégrité système de surveillance enregistre toutes les exceptions non gérées dans la base de données. Toutefois, le contrôle d’intégrité ne fournit pas tout mécanisme pour afficher le journal des erreurs via une page web. Toutefois, vous pouvez générer une page ASP.NET qui affiche ces informations à partir de la base de données. (Comme nous allons bientôt, vous pouvez choisir pour que les détails d’erreur envoyés dans un message électronique.)

Si vous créez une page de ce type, veillez à que prendre des mesures pour autoriser uniquement les utilisateurs autorisés à afficher les détails de l’erreur. Si votre site utilise déjà des comptes d’utilisateur, vous pouvez utiliser des règles d’autorisation d’URL pour restreindre l’accès à la page pour certains utilisateurs ou des rôles. Pour plus d’informations sur comment autoriser ou restreindre l’accès aux pages web en fonction de l’utilisateur connecté, reportez-vous à mon [didacticiels de sécurité du site Web](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md).

> [!NOTE]
> Le didacticiel suivant explore un système de journalisation et de notification autre erreur nommé ELMAH. ELMAH inclut un mécanisme intégré pour afficher le journal des erreurs depuis les deux une page web et comme un flux RSS.


## <a name="logging-events-to-e-mail"></a>Journalisation des événements de messagerie

Le contrôle d’intégrité système de surveillance comprend un fournisseur de source de journal qui « consigne » d’un événement à un message électronique. La source du journal inclut les mêmes informations qui sont connectées à la base de données dans le corps du message. Vous pouvez utiliser cette source de journaux pour avertir un développeur lorsqu’un certain événement de contrôle d’intégrité se produit.

Nous allons mettre à jour de la critiques de configuration du site Web afin que nous recevons un message électronique chaque fois qu’une exception se produit. Pour ce faire, nous devons effectuer trois tâches :

1. Configurer l’application web ASP.NET pour envoyer des messages électroniques. Cela est accompli en spécifiant la façon dont les messages électroniques sont envoyés le `<system.net>` élément de configuration. Pour plus d’informations sur l’envoi de courrier électronique les messages dans une application ASP.NET font référence à [envoi de courrier électronique dans ASP.NET](http://aspnet.4guysfromrolla.com/articles/072606-1.aspx) et [System.Net.Mail FAQ](http://systemnetmail.com/).
2. Inscrire le fournisseur de source de journal par courrier électronique dans le `<providers>` élément, et
3. Ajouter une entrée à la `<rules>` élément qui mappe l’événement « Toutes les erreurs » dans le fournisseur de source de journal ajouté à l’étape (2).

Le contrôle d’intégrité système de surveillance comprend deux classes du fournisseur de source journal par courrier électronique : `SimpleMailWebEventProvider` et `TemplatedMailWebEventProvider`. Le [ `SimpleMailWebEventProvider` classe](https://msdn.microsoft.com/en-us/library/system.web.management.simplemailwebeventprovider.aspx) envoie un message électronique en texte brut qui inclut l’événement détails et permet une personnalisation peu de corps du message électronique. Avec la [ `TemplatedMailWebEventProvider` classe](https://msdn.microsoft.com/en-us/library/system.web.management.templatedmailwebeventprovider.aspx) vous spécifiez une page ASP.NET dont le balisage rendu est utilisé en tant que le corps du message électronique. Le [ `TemplatedMailWebEventProvider` classe](https://msdn.microsoft.com/en-us/library/system.web.management.templatedmailwebeventprovider.aspx) vous donne un contrôle plus le contenu et le format du message électronique, mais nécessite un peu plus de travail initial que vous devez créer la page ASP.NET qui génère le corps du message de courrier électronique. Ce didacticiel se concentre sur l’utilisation de la `SimpleMailWebEventProvider` classe.

Mettre à jour de l’intégrité système de surveillance `<providers>` élément dans le `Web.config` fichier à inclure une source de journal pour la `SimpleMailWebEventProvider` classe :

[!code-xml[Main](logging-error-details-with-asp-net-health-monitoring-cs/samples/sample3.xml)]

Le balisage ci-dessus utilise la `SimpleMailWebEventProvider` classe en tant que le fournisseur de source de journal et lui attribue le nom convivial « EmailWebEventProvider ». En outre, le `<add>` attribut inclut des options de configuration supplémentaires, telles que la ligne à et à partir d’adresses du message électronique.

Avec la source de journal de messagerie définie, il ne reste que pour indiquer à l’intégrité système pour utiliser cette source pour « journal » des exceptions non gérées de surveillance. Cela est accompli en ajoutant une nouvelle règle dans la `<rules>` section :

[!code-xml[Main](logging-error-details-with-asp-net-health-monitoring-cs/samples/sample4.xml)]

Le `<rules>` comprend maintenant deux règles. Le, nommé « Toutes les erreurs de courrier électronique », envoie toutes les exceptions non gérées à la source du journal « EmailWebEventProvider ». Cette règle a pour effet d’envoyer les détails des erreurs sur le site Web spécifié à l’adresse. La règle « Toutes les erreurs de base de données » enregistre les détails de l’erreur à la base de données du site. Par conséquent, chaque fois qu’une exception non gérée produit sur le site, les détails sont tous deux enregistrés dans la base de données et envoyés à l’adresse de messagerie spécifiée.

**Figure 2** affiche le message électronique généré par le `SimpleMailWebEventProvider` lors de la visite de la classe `Genre.aspx?ID=foo`.

[![](logging-error-details-with-asp-net-health-monitoring-cs/_static/image5.png)](logging-error-details-with-asp-net-health-monitoring-cs/_static/image4.png)

**Figure 2**: les détails de l’erreur sont envoyées dans un Message électronique  
([Cliquez pour afficher l’image en taille réelle](logging-error-details-with-asp-net-health-monitoring-cs/_static/image6.png))

## <a name="summary"></a>Résumé

Le système de surveillance de l’intégrité ASP.NET est conçu pour permettre aux administrateurs d’analyser l’intégrité d’une application web déployée. Événements de contrôle d’intégrité sont déclenchés lorsque le déroulement de certaines actions, telles que lorsque l’application s’arrête, quand un utilisateur se connecte au site, ou lorsqu’une exception non gérée se produit. Ces événements peuvent être consignées dans n’importe quel nombre de sources de journal. Ce didacticiel vous a montré comment consigner les détails d’exceptions non gérées à une base de données et un message électronique.

Ce didacticiel consacré à l’aide pour enregistrer les exceptions non gérées, mais n’oubliez pas que le contrôle d’intégrité est conçu pour mesurer l’intégrité globale d’une application ASP.NET déployée et comprend de nombreux événements de contrôle d’intégrité et les journaux sources pas de contrôle d’intégrité Explorer ici. Par ailleurs, vous pouvez créer votre propre contrôle d’intégrité analyse les événements et les sources de journal, en cas de besoin surviennent. Si vous souhaitez en savoir plus sur le contrôle d’intégrité, la première étape est de lire [Erik Reitan](https://blogs.msdn.com/erikreitan/archive/2006/05/22/603586.aspx)de [Forum aux questions de contrôle d’intégrité](https://blogs.msdn.com/erikreitan/archive/2006/05/22/603586.aspx). Après cela, consultez [comment faire : utiliser le contrôle d’état dans ASP.NET 2.0](https://msdn.microsoft.com/en-us/library/ms998306.aspx).

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Vue d’ensemble du contrôle d’état ASP.NET](https://msdn.microsoft.com/en-us/library/bb398933.aspx)
- [Configuration et la personnalisation de l’intégrité système de ASP.NET de surveillance](http://dotnetslackers.com/articles/aspnet/ConfiguringAndCustomizingTheHealthMonitoringSystemOfASPNET.aspx)
- [Forum aux questions - analyse d’intégrité dans ASP.NET 2.0](https://blogs.msdn.com/erikreitan/archive/2006/05/22/603586.aspx)
- [Comment : Envoyer un courrier électronique pour les Notifications de contrôle d’intégrité](https://msdn.microsoft.com/en-us/library/ms227553.aspx)
- [Comment : Utiliser l’analyse du fonctionnement dans ASP.NET](https://msdn.microsoft.com/en-us/library/ms998306.aspx)
- [Analyse d’intégrité dans ASP.NET](http://aspnet.4guysfromrolla.com/articles/031407-1.aspx)

>[!div class="step-by-step"]
[Précédent](processing-unhandled-exceptions-cs.md)
[Suivant](logging-error-details-with-elmah-cs.md)
