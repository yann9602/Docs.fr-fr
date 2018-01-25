---
uid: visual-studio/overview/2013/aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes
title: ASP.NET et Web Tools 2013.2 pour les Notes de publication Visual Studio 2013 | Documents Microsoft
author: microsoft
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/06/2014
ms.topic: article
ms.assetid: 7ef5f73c-ca60-43c1-bdb2-702800347e7e
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /visual-studio/overview/2013/aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes
msc.type: authoredcontent
ms.openlocfilehash: 0e7ad52662f7ceaa1f087d007d0b14b610f90bee
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-and-web-tools-20132--for-visual-studio-2013-release-notes"></a>ASP.NET et outils Web 2013.2 pour les Notes de publication Visual Studio 2013
====================
par [Microsoft](https://github.com/microsoft)

## <a name="installation-notes"></a>Notes d’installation

ASP.NET et Web Tools pour Visual Studio 2013.2 sont regroupés dans le programme d’installation principal et peut être téléchargé dans le cadre de [Visual Studio 2013 Update 2](https://go.microsoft.com/fwlink/?LinkId=390521).

## <a name="documentation"></a>Documentation

Didacticiels et autres informations sur ASP.NET et Web Tools pour Visual Studio 2013.2 sont disponibles à partir de la [site web ASP.NET](https://www.asp.net/).

## <a name="software-requirements"></a>Configuration logicielle

ASP.NET et Web Tools pour Visual Studio 2013.2 requiert Visual Studio 2013.

## <a name="new-features-in-aspnet-and-web-tools-for-visual-studio-20132"></a>Nouvelles fonctionnalités dans ASP.NET et Web Tools pour Visual Studio 2013.2

Les sections suivantes décrivent les fonctionnalités qui ont été introduites dans la version.

- [Modèles d’un projet ASP.NET](#oneaspnet)
- [Prend en charge SSL lors du lancement d’Applications Web sur IIS Express](#ssl)
- [Améliorations de l’éditeur de Visual Studio Web](#vswebeditor)
- [Lien du navigateur](#browserlink)
- [Prise en charge pour les applications Web du Service d’applications Azure dans Visual Studio](#waws)
- [Créer des ressources Azure à distance lors de la création d’un projet Web](#AzureResources)
- [Améliorations de la publication sur le Web](#webpublish)
- [Génération de modèles automatique ASP.NET](#scaffolding)
- [NuGet 2.8.1](#nuget)
- [Web Forms ASP.NET](#webforms)
- [ASP.NET MVC 5.1.2](#mvc)
- [ASP.NET Web API 2.1.2](#webapi)
- [3.1.2 les Pages Web ASP.NET](#webpages)
- [Entity Framework 6.1](#ef)
- [ASP.NET Identity 2.0.0](#identity)
- [Composants Microsoft OWIN](#owin)
- [ASP.NET SignalR 2.0.2](#signalr)

<a id="oneaspnet"></a>
### <a name="one-aspnet-project-templates"></a>Modèles d’un projet ASP.NET

- Mises à jour des modèles de projet ASP.NET pour prendre en charge la confirmation du compte et le mot de passe réinitialisé.
- Modèle d’API Web ASP.NET de mise à jour pour prendre en charge l’authentification à l’aide sur site comptes professionnels.
- Le modèle ASP.NET SPA contient à présent l’authentification basée sur les vues côté MVC et le serveur. Le modèle a un contrôleur WebAPI qui est accessible par les utilisateurs authentifiés.

<a id="ssl"></a>
### <a name="support-ssl-when-launching-web-applications-on-iis-express"></a>Prend en charge SSL lors du lancement d’Applications Web sur IIS Express

Pour éliminer l’avertissement de sécurité lors de la navigation et de débogage de HTTPS sur localhost, nous avons ajouté une boîte de dialogue pour permettre à Internet Explorer et Chrome pour approuver auto-signé IIS express certificat SSL.

Par exemple, une propriété de projet web peut être définie pour utiliser SSL. Cliquez sur F4 pour afficher la boîte de dialogue Propriétés. Modification **SSL activé** sur true. Copiez l’URL SSL.

![La propriété Enabled de SSL](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image1.png)

L’onglet web page de propriété de projet web pour utiliser le protocole HTTPS basé URL (URL SSL seront `https://localhost:44300/` , sauf si vous avez déjà créé des Sites Web SSL.)

![Définir l’URL du projet (HTTPS)](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image2.png)

Appuyez sur CTRL+F5 pour exécuter l'application. Suivez les instructions pour approuver le certificat auto-signé IIS Express a généré.

![Avertissement SSL](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image3.png)

Lecture la **avertissement de sécurité** boîte de dialogue, puis cliquez sur **Oui** si vous souhaitez installer le certificat de localhost.

![Avertissement de sécurité](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image4.png)

Le site s’affiche dans Internet Explorer ou Chrome sans l’avertissement de certificat dans le navigateur.

![Page HTTPS sans avertissement](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image5.png)

Firefox utilise son propre magasin de certificats, il affiche un avertissement.

<a id="vswebeditor"></a>
### <a name="visual-studio-web-editor-enhancements"></a>Améliorations de l’éditeur de Visual Studio Web

- **Nouvel élément de projet JSON et éditeur**: nous avons ajouté un élément de projet JSON et l’éditeur de Visual Studio. Fonctionnalités de l’éditeur JSON actuelles incluent la colorisation, validation de la syntaxe, saisie semi-automatique des accolades, mode plan, paramètre de l’option outils et bien plus encore.

    ![Éditeur JSON](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image6.png)

    Prend en charge IntelliSense [schéma JSON](http://json-schema.org/) v3 et v4. Il existe une zone de liste déroulante de schéma pour choisir des schémas existants, modifier le chemin d’accès local de schéma, ou simplement glisser déposer un fichier JSON de projet de manière à obtenir le chemin d’accès relatif.

    ![JSON Intellisense](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image7.png)    ![Éditeur de schéma JSON](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image8.png)
- **Nouvel éditeur Sass (SCSS)**: nous avons ajouté inférieur dans VS2013 RTM, et nous disposons désormais d’un élément de projet Sass et l’éditeur. Éditeur de sass fonctionnalités sont comparables à l’éditeur LESS et incluent la colorisation, variable et Mixins IntelliSense, / ne pas commenter des commentaires, Infos, mise en forme, validation de la syntaxe, mode plan, atteindre la définition, sélecteur de couleurs, les outils d’option paramètre etc.

    ![Ajouter un nouvel élément : Feuille de Style SCSS](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image9.png)    ![Éditeur de feuilles de style](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image10.png)
- **Nouveau sélecteur d’URL dans le code HTML, Razor, CSS, inférieure et documents de Sass :** VS 2013 est livré avec aucun sélecteur d’URL en dehors des pages Web Forms. Le nouveau sélecteur d’URL pour HTML, Razor, CSS, LESS et Sass éditeurs est un sélecteur de frappe fluent, libérer à la boîte de dialogue comprend '.' fichier de filtres listes et appropriée pour les liens et les balises img.

    ![Sélecteur d’URL pour la balise d’image](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image11.png)    ![Sélecteur d’URL pour les vues](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image12.png)    ![Sélecteur d’URL pour CSS](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image13.png)
- **Mises à jour de l’éditeur LESS en ajoutant davantage de fonctionnalités**
- **Mise à niveau de Knockout Intellisense**: nous avons ajouté une syntaxe KnockOut non standard pour Visual Studio intelliSense, « ko-vs-éditeur viewModel : « syntaxe. Il peut être utilisé pour lier à plusieurs modèles d’affichage sur une page à l’aide des commentaires sous la forme :

    ![Knockout Intellisense](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image14.png)

    Nous avons également ajouté la prise en charge pour imbriquée ViewModel IntelliSense, afin de vous pouvez explorer des objets fortement imbriquées sur ViewModel.

    `<div data-bind="text: foo.bar.baz.etc" />`

    Le IntelilSense affiché est IntelliSense complète de l’objet JavaScript.

    ![Objet de JavaScript IntelliSense affichage complet](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image15.png)
- **Nouveau sélecteur d’URL dans le code HTML, Razor, CSS, inférieure et documents de Sass**: VS 2013 est livré avec aucun sélecteur d’URL en dehors des pages Web Forms. Le nouveau sélecteur d’URL pour HTML, Razor, CSS, LESS et Sass éditeurs est un sélecteur de frappe fluent, libérer à la boîte de dialogue comprend '.' fichier de filtres listes et appropriée pour les liens et les balises img.

    ![Sélecteur d’URL pour la balise d’image](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image16.png)    ![Sélecteur d’URL pour les vues](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image17.png)    ![Sélecteur d’URL pour CSS](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image18.png)

<a id="browserlink"></a>
### <a name="browser-link"></a>Lien du navigateur

- Lien du navigateur prend en charge les connexions HTTPS maintenant et répertorie que dans le tableau de bord avec d’autres connexions tant que le certificat est approuvé par le navigateur.
- Mappage de la source HTML statique
- SPA prend en charge pour les données de mappage
- Les données de mappage de mise à jour automatique

<a id="waws"></a>
### <a name="support-for-azure-app-service-web-apps-in-visual-studio"></a>Prise en charge pour les applications Web du Service d’applications Azure dans Visual Studio

- **Prise en charge Azure se connecter.**
- **Débogage distant et l’affichage à distance pour les applications web**: nous prennent désormais en charge [le débogage distant pour les applications web dans Azure App Service](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio) et l’affichage à distance des fichiers de contenu de l’application dans l’Explorateur de serveurs web.

<a id="AzureResources"></a>
### <a name="create-remote-azure-resources-when-creating-a-new-web-project"></a>Créer des ressources Azure à distance lors de la création d’un projet Web

Nous avons ajouté un Azure [« Créer des ressources distantes »](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet) case à cocher sur le nouveau dialogue d’application web. En la sélectionnant, vous serez en mesure d’intégrer l’expérience de création d’une application web, configurer le site de publication Azure pour les tests et créer un profil de publication en quelques étapes simples.

![Nouveau projet avec des ressources Azure](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image19.png)![Publication sur Azure](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image20.png)

<a id="webpublish"></a>
### <a name="web-publish-enhancements"></a>Améliorations de la publication sur le Web

- Améliorer l’expérience utilisateur pour la publication.

<a id="scaffolding"></a>
### <a name="aspnet-scaffolding"></a>Génération de modèles automatique ASP.NET

- **Prise en charge de l’enum :** si votre modèle utilise les énumérations, puis le Scaffolder MVC générera une liste déroulante pour Enum. Cette méthode utilise les programmes d’assistance de l’Enum dans MVC.
- **Prise en charge des données d’amorçage**: mise à jour les modèles de EditorFor dans la structure de MVC afin qu’elles utilisent les classes d’amorçage.
- **Prise en charge du package**: MVC et les générations de modèles automatiques Web API ajoute des 5.1 packages pour MVC et les API Web

Les captures d’écran suivantes illustrent les modèles de génération de modèles automatique.

- Code de modèle :

     ![Code de modèle](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image21.png)
- Compiler le code du modèle, avec le bouton droit, puis sélectionnez **ajouter**, **un nouvel élément structuré**.

     ![Ajouter un nouvel élément de modèle généré automatiquement](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image22.png)
- Choisissez **contrôleur MVC5 avec vues, utilisant Entity Framework**:

     ![Ajouter un nouveau contrôleur MVC5 avec des vues](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image23.png)
- **Ajouter un contrôleur** à l’aide du modèle :

    ![](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image24.png)
- Vérifiez le code généré, par exemple Views/WeekdayModels/Edit.cshtml contient `@Html.EnumDropDownListFor`: ![afficher contenant EnumDropDownListFor](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image25.png)
- Exécutez la page pour voir le combobox enum généré, notez que si une valeur peut être null, une chaîne vide peut être choisie pour la zone de liste déroulante. Par exemple, le **créer** page affiche les informations suivantes :

    ![Zone de liste déroulante permettant une chaîne vide](aspnet-and-web-tools-20132-preview-for-visual-studio-2013-release-notes/_static/image26.png)

<a id="nuget"></a>
### <a name="nuget-281"></a>NuGet 2.8.1

NuGet 2.8.1 que RTM sera publié en avril 2014. Voici les points importants dans les notes de publication, mais vérifiez la [notes de version complet](http://docs.nuget.org/docs/release-notes/nuget-2.8) pour plus d’informations sur ces modifications.

- **Cibler les Applications Windows Phone 8.1**: NuGet 2.8.1 prend désormais en charge le ciblage de Windows Phone 8.1 Applications utilisant les monikers du framework cible 'WindowsPhoneApp', 'WPA', 'WindowsPhoneApp81' et 'WPA81'.
- **Correctif de la résolution des dépendances**: lors de la résolution des dépendances du package, NuGet a implémenté par le passé d’une stratégie de sélection de la version majeure et mineure package le plus bas qui satisfait aux dépendances sur le package. Toutefois, contrairement à la version majeure et mineure, la version du correctif a été résolue toujours à la version la plus élevée. Bien que le comportement a été bien intentionné, il créé un manque de déterminisme pour l’installation de packages avec des dépendances.
- **Commutateur de DependencyVersion**: si NuGet 2.8 modifie le *par défaut* comportement pour la résolution des dépendances, il ajoute également un contrôle plus précis sur le processus de résolution de dépendance via le commutateur - DependencyVersion dans le console du Gestionnaire de package. Le commutateur permet la résolution des dépendances à la version la plus basse possible (comportement par défaut), la version la plus élevée possible, ou le mineur le plus élevé ou la version du correctif. Ce commutateur fonctionne uniquement pour le package d’installation dans la commande powershell.
- **Attribut de DependencyVersion**: outre le commutateur - DependencyVersion détaillés plu haut, NuGet a également autorisés pour la capacité à définir un nouvel attribut dans le fichier nuget.config définir ce qu’est la valeur par défaut, si la DependencyVersion - commutateur n’est pas spécifié dans un appel de package d’installation. Cette valeur sera également être respectée par la boîte de dialogue Gestionnaire de Package NuGet pour les opérations de package d’installation. Pour définir cette valeur, ajoutez l’attribut ci-dessous à votre fichier nuget.config :

    `<config> <add key="dependencyversion" value="Highest" /> </config>`
- **Afficher un aperçu des opérations de NuGet avec - whatif**: les packages NuGet certains peuvent avoir des graphiques de dépendance approfondie, et par conséquent, il peut être utile lors de l’installation, désinstaller ou mettre à jour de l’opération d’abord visualiser ce qui se produira. NuGet 2.8 ajoute le PowerShell standard : que se passe-t-il si basculer vers les commandes de package d’installation, le package de désinstallation et package de mise à jour pour permettre la visualisation de la fermeture d’ensemble des packages qui la commande sera appliquée.
- **Mettre à niveau le Package**: il n’est pas rare d’installer une version préliminaire d’un package afin d’étudier les nouvelles fonctionnalités et décider ensuite de revenir à la dernière version stable. Avant de NuGet 2.8, il s’agissait d’un processus en plusieurs étapes de la version préliminaire package et ses dépendances, installation et la désinstallation puis la version antérieure. Avec NuGet 2.8, toutefois, le package de mise à jour maintenant annule la fermeture de l’ensemble du package (par exemple, arborescence des dépendances du package) à la version précédente.
- **Dépendances de développement**: différents types de fonctionnalités peuvent être remis sous forme de packages NuGet - y compris les outils utilisés pour l’optimisation du processus de développement. Ces composants, pendant qu’ils peuvent vous aider dans le développement d’un nouveau package, ne doivent pas être considérés publié par une dépendance du nouveau package lorsqu’il est plus loin. NuGet 2.8 permet à un package pour s’identifier dans le fichier .nuspec comme un developmentDependency. Lors de l’installation, ces métadonnées également figurera dans le fichier packages.config du projet dans lequel le package a été installé. Lorsque ce fichier packages.config est analysé ultérieurement au cours du pack de nuget.exe pour les dépendances de NuGet, il exclut ces dépendances marquées en tant que dépendances de développement.
- **Fichiers packages.config individuelles pour les différentes plateformes**: lors du développement d’applications pour plusieurs plateformes cibles, il est courant d’avoir des fichiers de projet différent pour chacun des environnements de build respectifs. Il est également courant de consommer les différents packages NuGet dans différents fichiers projet, comme packages ont des niveaux de prise en charge pour les différentes plateformes. NuGet 2.8 fournit la prise en charge améliorée pour ce scénario en créant des fichiers de packages.config différents pour les fichiers de projet spécifiques à la plateforme différent.
- **Secours pour le Cache Local**: les packages NuGet si sont généralement consommées à partir d’une galerie à distance tels que le [galerie NuGet](http://www.nuget.org) à l’aide d’une connexion réseau, il existe plusieurs scénarios où le client n’est pas connecté. Sans connexion réseau, le client NuGet n’a pas pu installer correctement les packages - même lorsque ces packages étaient déjà présents sur l’ordinateur client dans le cache local NuGet. NuGet 2.8 ajoute un cache de secours automatique pour la console du Gestionnaire de package.

    La fonctionnalité de secours du cache ne nécessite pas d’arguments de commande spécifique. En outre, cache secours actuellement fonctionne uniquement dans la console du Gestionnaire de package, le comportement ne fonctionne pas actuellement dans la boîte de dialogue de gestionnaire de package.
- **Correctifs de bogues**: amélioration des performances dans le package de mise à jour de l’une des correctifs de bogues majeures apportées était-réinstaller la commande.

    Outre ces fonctionnalités et la correction des performances mentionnées précédemment, cette version de NuGet inclut également plusieurs autres correctifs de bogues. Il existait des problèmes de total 181 traités dans la mise en production. Pour obtenir la liste complète des travaux les éléments corrigés dans NuGet 2.8, veuillez vue le [NuGet Issue Tracker](https://nuget.codeplex.com/workitem/list/advanced?release=NuGet%202.8&status=all) pour cette version.

<a id="webforms"></a>
### <a name="aspnet-web-forms"></a>ASP.NET Web Forms

- Les modèles Web Forms permet de montrer comment effectuer la Confirmation du compte et le mot de passe réinitialisé pour ASP.NET Identity.
- Le contrôle de Source de données d’entité et le fournisseur de données dynamiques pour Entity Framework 6. Pour plus d’informations, consultez le blog MSDN suivant : [fournisseur de données dynamique et le contrôle EntityDataSource pour Entity Framework 6](https://blogs.msdn.com/b/webdev/archive/2014/01/30/announcing-preview-of-dynamic-data-provider-and-entitydatasource-control-for-entity-framework-6.aspx).

<a id="mvc"></a>
### <a name="aspnet-mvc-512"></a>ASP.NET MVC 5.1.2

- [Améliorations du routage d’attribut](../../../mvc/overview/releases/mvc51-release-notes.md#AttributeRouting)
- [Prise en charge d’amorçage pour les modèles de l’éditeur](../../../mvc/overview/releases/mvc51-release-notes.md#Bootstrap)
- [Prise en charge de l’énumération dans les vues](../../../mvc/overview/releases/mvc51-release-notes.md#Enum)
- [Prise en charge de Unobstrusive de MinLength / MaxLength attributs](../../../mvc/overview/releases/mvc51-release-notes.md#Unobtrusive)
- [Prise en charge le contexte de 'THI' dans Ajax discrète](../../../mvc/overview/releases/mvc51-release-notes.md#thisContext)
- Divers [correctifs de bogues](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&status=Closed&type=All&priority=All&release=v5.1%20Preview%7cv5.1%20RTM&assignedTo=All&component=MVC&sortField=AssignedTo&sortDirection=Ascending&page=0&reasonClosed=Fixed)

<a id="webapi"></a>
### <a name="aspnet-web-api-212"></a>ASP.NET Web API 2.1.2

- [Gestion des erreurs global](../../../web-api/overview/releases/whats-new-in-aspnet-web-api-21.md#global-error)
- [Améliorations de routage d’attribut](../../../web-api/overview/releases/whats-new-in-aspnet-web-api-21.md#attribute-routing)
- [Améliorations de la page](../../../web-api/overview/releases/whats-new-in-aspnet-web-api-21.md#help-page)
- [IgnoreRoute support](../../../web-api/overview/releases/whats-new-in-aspnet-web-api-21.md#ignoreroute)
- [Formateur de type de média BSON](../../../web-api/overview/releases/whats-new-in-aspnet-web-api-21.md#bson)
- [Meilleure prise en charge pour les filtres d’async](../../../web-api/overview/releases/whats-new-in-aspnet-web-api-21.md#async-filters)
- [Analyse du client de mise en forme de la bibliothèque de la requête](../../../web-api/overview/releases/whats-new-in-aspnet-web-api-21.md#query-parsing)
- Divers [correctifs de bogues](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&status=Closed&type=All&priority=All&release=v5.1%20Preview%7cv5.1%20RTM&assignedTo=All&component=Web%20API%7cWeb%20API%20OData&sortField=AssignedTo&sortDirection=Ascending&page=0&reasonClosed=Fixed)

<a id="webpages"></a>
### <a name="aspnet-web-pages-312"></a>3.1.2 les Pages Web ASP.NET

- Divers [correctifs de bogues](https://aspnetwebstack.codeplex.com/workitem/list/advanced?keyword=&status=Closed&type=All&priority=All&release=v5.1%20Preview%7cv5.1%20RTM&assignedTo=All&component=Web%20Pages/Razor&sortField=AssignedTo&sortDirection=Ascending&page=0&reasonClosed=Fixed)

<a id="ef"></a>
### <a name="entity-framework-61"></a>Entity Framework 6.1

Entity Framework a été mis à jour à la version 6.1 du runtime et outils. 6.1 Entity Framework (EF) est une mise à jour mineure Entity Framework 6 et inclut un certain nombre de correctifs de bogues et de nouvelles fonctionnalités. Pour plus d’informations sur EF6.1, y compris des liens vers la documentation pour les nouvelles fonctionnalités, consultez [Entity Framework Version historique](https://msdn.microsoft.com/data/jj574253). Les nouvelles fonctionnalités dans cette version sont les suivantes :

- **Consolidation des outils** fournit un moyen cohérent de créer un nouveau modèle EF. Cette fonctionnalité s’étend de l’Assistant Entity Data Model ADO.NET pour prendre en charge la création de modèles de Code First, y compris l’ingénierie à rebours à partir de la base de données existante. Ces fonctionnalités étaient précédemment disponibles dans la qualité bêta dans EF Power Tools.
- **Gestion des échecs de validation de transaction** propose les nouvelles [System.Data.Entity.Infrastructure.CommitFailureHandler](https://msdn.microsoft.com/library/system.data.entity.infrastructure.commitfailurehandler(v=vs.113).aspx) qui utilise la capacité récemment introduite pour intercepter des opérations de transaction. Le **CommitFailureHandler** permet la récupération automatique des échecs de connexion lors de la validation d’une transaction.
- **IndexAttribute** autorise les index d’être spécifiés en plaçant un attribut sur une propriété (ou propriétés) dans votre modèle Code First. Code d’abord crée ensuite un index correspondant dans la base de données.
- **L’API de mappage publique** fournit l’accès aux EF d’informations sur la façon dont les propriétés et les types sont mappés à des colonnes et tables de la base de données. Dans les versions précédentes cette API a été interne.
- **Possibilité de configurer des intercepteurs via le fichier App/Web.config**(autorisant les intercepteurs doit être ajouté sans avoir à recompiler l’application).
- **DatabaseLogger** est un intercepteur de qui facilite la tâche à enregistrer toutes les opérations de base de données dans un fichier. En combinaison avec la fonction précédente, cela vous permet de basculer facilement la journalisation des opérations de base de données pour une application déployée, sans avoir à recompiler.
- **Détection de modification de modèle de migrations** a été améliorée afin que les migrations avec génération de modèles sont plus précises ; les performances du processus de détection de modification a été considérablement amélioré.
- **Améliorations des performances** y compris les opérations de réduction de la base de données lors de l’initialisation, les optimisations pour la comparaison d’égalité null dans les requêtes LINQ, afficher plus rapidement génération (création de modèles) dans d’autres scénarios et plus efficace matérialisation d’entités suivies avec plusieurs associations.

<a id="identity"></a>
### <a name="aspnet-identity-200"></a>ASP.NET Identity 2.0.0

- **Authentification à deux facteurs**: ASP.NET Identity prend désormais en charge l’authentification à deux facteurs. Authentification à deux facteurs fournit une couche supplémentaire de sécurité à vos comptes d’utilisateur dans le cas où votre mot de passe est compromis. Il existe également une protection pour les attaques en force brute contre les codes d’authentification à deux facteurs.
- **Le verrouillage de compte :** permet de verrouiller l’utilisateur si l’utilisateur entre son mot de passe ou des codes de deux facteurs incorrectement. Le nombre de tentatives non valides et l’intervalle de temps pour les utilisateurs sont verrouillés peut être configuré. Un développeur peut éventuellement désactiver le verrouillage de compte pour certains comptes d’utilisateurs qu’ils ne devez.
- **Confirmation du compte :** le système d’identité ASP.NET prend désormais en charge la Confirmation du compte. Il s’agit d’un scénario assez courant dans la plupart des sites Web aujourd'hui où, lorsque vous inscrivez pour un nouveau compte sur le site Web, vous devez confirmer votre adresse de messagerie avant de faire quoi que ce soit dans le site Web. E-mail de Confirmation est utile, car elle empêche la création des comptes factices. Cela est particulièrement utile si vous utilisez le courrier électronique en tant que méthode de communication avec les utilisateurs de votre site Web telles que les sites, bancaire, Commerce électronique ou des sites web sociaux Forum.
- **Réinitialisation du mot de passe :** réinitialisation de mot de passe est une fonctionnalité où l’utilisateur peut réinitialiser leurs mots de passe, s’ils ont oublié leur mot de passe.
- **Tampon de sécurité (déconnexion partout) :** prend en charge une méthode pour régénérer le jeton de sécurité de l’utilisateur dans le cas lorsque l’utilisateur modifie son mot de passe ou toute autre sécurité liées à des informations telles que la suppression d’une connexion associée (tels que Facebook, Google, Compte Microsoft et ainsi de suite). Cela est nécessaire pour s’assurer que les jetons générés avec l’ancien mot de passe sont invalidés. Dans l’exemple de projet, si vous modifiez le mot de passe ensuite un nouveau jeton est généré pour l’utilisateur et tous les jetons précédentes sont invalidés. Cette fonctionnalité fournit une couche supplémentaire de sécurité à votre application depuis lorsque vous modifiez votre mot de passe, vous allez être déconnecté en tout lieu où vous vous êtes connecté à cette application (tous les autres navigateurs).
- **Vérifiez le type de clé primaire être extensible pour les utilisateurs et rôles**: dans ASP.NET Identity 1.0, le type de clé primaire pour les rôles et les utilisateurs de la table a été chaînes. Cela signifie que lorsque le système d’identité ASP.NET a été rendue persistante dans SQL Server à l’aide d’Entity Framework, nous avons à l’aide de nvarchar. Sont nombreuses discussions autour de cette implémentation par défaut du débordement de pile et basées sur les commentaires entrant. Nous avons fourni un raccordement d’extensibilité où vous pouvez spécifier ce qui doit être la clé primaire de votre table d’utilisateurs et des rôles. Ce point d’extensibilité est particulièrement utile que si vous migrez votre application et de l’application a été UserIds de stockage sont des GUID ou ints.
- **Prend en charge IQueryable sur les utilisateurs et les rôles**: prise en charge supplémentaire pour IQueryable sur UsersStore et RolesStore, vous pouvez facilement obtenir la liste des utilisateurs et des rôles.
- **Opération de suppression de prise en charge par le biais du UserManager**
- **L’indexation sur le nom d’utilisateur**: implémentation de dans ASP.NET Identity Entity Framework, nous avons ajouté un index unique sur le nom d’utilisateur à l’aide de la nouvelle IndexAttribute dans EF 6.1.0. Cela permet de s’assurer que les noms d’utilisateur sont toujours uniques et il n’a aucune condition de concurrence dans laquelle vous pourriez finir avec des noms d’utilisateur en double.
- **Validateur de mot de passe améliorée :** le validateur de mot de passe qui a été expédié dans ASP.NET Identity 1.0 a été un validateur de mot de passe assez basique qui a été valider uniquement la longueur minimale. Il est un nouveau mot de passe du programme de validation qui vous donne davantage de contrôle sur la complexité du mot de passe. Notez que même si vous activez tous les paramètres de ce mot de passe, nous vous encourageons à activer l’authentification à deux facteurs pour les comptes d’utilisateur.
- **Intergiciel (middleware) IdentityFactory / CreatePerOwinContext**:

    - **Le Gestionnaire des utilisateurs**: vous pouvez utiliser l’implémentation de la fabrique pour obtenir une instance de UserManager à partir du contexte OWIN. Ce modèle est similaire à ce que nous utilisons pour l’obtention de AuthenticationManager à partir du contexte OWIN de la connexion et déconnexion. Il s’agit d’une méthode recommandée permettant d’obtenir une instance de UserManager par demande pour l’application.
    - **DbContextFactory**: ASP.NET Identity utilise Entity Framework pour rendre le système d’identité dans SQL Server. Pour ce faire le système d’identité a une référence à la ApplicationDbContext. Le DbContextFactory Middleware retourne une instance de la ApplicationDbContext par demande que vous pouvez utiliser dans votre application.
- **ASP.NET Identity exemples NuGet package**: l’exemples package NuGet peut rendre plus facile à installer et exécuter les exemples pour ASP.NET Identity et suivez les meilleures pratiques. Il s’agit d’un application ASP.NET MVC d’exemple. Modifiez le code pour l’adapter à votre application avant de déployer en production. L’exemple doit être installé dans une application ASP.NET vide. Pour plus d’informations sur le package, accédez à la publication de blog suivante : [annonce RTM d’ASP.NET Identity 2.0.0](https://blogs.msdn.com/b/webdev/archive/2014/03/20/test-announcing-rtm-of-asp-net-identity-2-0-0.aspx)

<a id="owin"></a>
### <a name="microsoft-owin-components"></a>Composants Microsoft OWIN

A un grand nombre de bogues qui ont été corrigés dans cette version. Consultez le [notes de publication pour le 2.1.0 version](https://katanaproject.codeplex.com/releases/view/113281) pour plus d’informations.

<a id="signalr"></a>
### <a name="aspnet-signalr-202"></a>ASP.NET SignalR 2.0.2

A un grand nombre de bogues qui ont été corrigés dans cette version. Consultez le [notes de publication pour le 2.0.2 version](https://github.com/SignalR/SignalR/releases/tag/2.0.2) pour plus d’informations.
