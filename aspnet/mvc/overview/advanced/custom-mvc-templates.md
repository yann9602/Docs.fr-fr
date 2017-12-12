---
uid: mvc/overview/advanced/custom-mvc-templates
title: "Modèle MVC personnalisé | Documents Microsoft"
author: joeloff
description: "Créer un modèle comme extension VSIX."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/10/2012
ms.topic: article
ms.assetid: b0a214c7-2f38-4dbc-b47f-bd7bd9df97bd
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/advanced/custom-mvc-templates
msc.type: authoredcontent
ms.openlocfilehash: a1fe1844e582f402a1eed9ddf10ee249e856b083
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="custom-mvc-template"></a>Modèle MVC personnalisée
====================
par [Jacques Eloff](https://github.com/joeloff)

La version de MVC 3 Tools Update pour Visual Studio 2010 a introduit un Assistant projet distinct pour les projets MVC. La modification a été dus à deux facteurs. Tout d’abord, l’introduction de nouveaux modèles dans MVC 3 et la prise en charge pour les moteurs d’affichage supplémentaires tels que Razor entraîner surpeuplement la boîte de dialogue Nouveau projet dans Visual Studio. En second lieu, clients avaient été demandé pour les points d’extensibilité et de l’Assistant Nouveau projet MVC nous offre l’opportunité de répondre à ces demandes.

L’ajout de modèles personnalisés était un processus complexe qui s’appuyaient sur l’utilisation du Registre pour que les nouveaux modèles visible par l’Assistant de projet MVC. L’auteur d’un nouveau modèle devait placez-le à l’intérieur d’un fichier MSI pour vous assurer que les entrées du Registre nécessaires sont créées au moment de l’installation. L’alternative était de créer un fichier ZIP contenant le modèle disponible et avoir l’utilisateur final de créer manuellement les entrées de Registre nécessaires.

Aucun des approches citée plus haut est idéale afin de nous avons décidé de tirer parti de l’infrastructure fournie par [VSIX](https://msdn.microsoft.com/en-us/library/ff363239.aspx) extensions pour faciliter leur auteur, distribuer et installer des modèles MVC personnalisés à partir de MVC 4 pour Visual Studio 2012. Certains des avantages fournis par cette approche sont :

- Une extension VSIX peut contenir plusieurs modèles qui prennent en charge différentes langues (c# et Visual Basic) et plusieurs moteurs d’affichage (ASPX et Razor).
- Une extension VSIX peut cibler plusieurs références (SKU) de Visual Studio, y compris les références (SKU) Express.
- Le [galerie Visual Studio](https://visualstudiogallery.msdn.microsoft.com/) facilite la distribution de l’extension à un large public.
- Extensions VSIX peuvent être mis à niveau, ce qui la rend plus facile de créer des corrections et mises à jour de vos modèles personnalisés.

## <a name="prerequisites"></a>Conditions préalables

- Les utilisateurs ont besoin de se familiariser avec la création de modèles de projet, y compris le balisage requis pour les fichiers vstemplate, etc.
- Les utilisateurs doivent disposer de Visual Studio Professional et ultérieure. Références SKU Express ne permettent pas de créer des projets VSIX.
- [Visual Studio 2012 SDK](https://www.microsoft.com/download/details.aspx?id=30668) installé.

## <a name="example"></a>Exemple

La première étape consiste à créer un projet VSIX à l’aide de c# ou Visual Basic. Sélectionnez **fichier > Nouveau projet**, puis cliquez sur **extensibilité** dans le volet gauche, sélectionnez le **projet VSIX**.

![Nouveau projet](custom-mvc-templates/_static/image1.jpg)

Une fois que le projet est créé, le concepteur VSIX s’ouvre.

![Métadonnées de Concepteur de projet](custom-mvc-templates/_static/image2.jpg)

Le concepteur peut être utilisé pour modifier certaines des propriétés générales de l’extension qui sera affiché aux utilisateurs lorsqu’ils installer l’extension ou parcourir les extensions installées dans Visual Studio (**Outils > Extensions et mises à jour**). Une fois que vous avez terminé les informations générales, cliquez sur dans le **onglet de cibles d’installation**.

![Cibles d’installation Concepteur de projet](custom-mvc-templates/_static/image3.jpg)

Cet onglet permet de spécifier les références (SKU) et les versions de Visual Studio qui sont prises en charge par votre extension. Activez la case à cocher pour **ce VSIX est installé pour tous les utilisateurs** pour activer l’installation par ordinateur et de l’extension VSIX. Cliquez sur le **nouveau** bouton à droite pour ajouter des références supplémentaires tels que Web Developer Express (VWD).

![Ajouter la nouvelle cible d’Installation](custom-mvc-templates/_static/image4.jpg)

Si vous envisagez de prendre en charge tous les les SKU Professional et versions ultérieures (Professional, Premium et Ultimate) vous devez seulement sélectionner la référence (SKU) minimale de la famille, **Microsoft.VisualStudio.Pro**. Pensez à enregistrer toutes vos modifications une fois que vous avez terminé les cibles d’installation.

![Cibles d’installation Concepteur de projet](custom-mvc-templates/_static/image5.jpg)

Le **actifs** onglet est utilisé pour ajouter tous vos fichiers de contenu à l’extension VSIX. Étant donné que MVC requiert des métadonnées personnalisées que vous aurez à modifier le code XML brut du fichier manifeste VSIX au lieu d’utiliser le **actifs** onglet pour ajouter du contenu. Commencez par ajouter le contenu du modèle pour le projet VSIX. Il est important que la structure du dossier et le contenu de mise en miroir de la disposition du projet. L’exemple ci-dessous contient quatre modèles de projet qui ont été dérivées à partir du modèle de projet MVC de base. Assurez-vous que tous les fichiers qui composent votre modèle de projet (tous les éléments sous le dossier ProjectTemplates) sont ajoutés à la **contenu** itemgroup dans le VSIX fichier projet et que chaque élément contient le  **CopyToOutputDirectory** et **IncludeInVsix** métadonnées définies comme indiqué dans l’exemple ci-dessous.

&lt;Inclure du contenu =&quot;ProjectTemplates\MyMvcWebApplicationProjectTemplate.csaspx\BasicWeb.config&quot;&gt;

&lt;CopyToOutputDirectory&gt;toujours&lt;/CopyToOutputDirectory&gt;

&lt;IncludeInVSIX&gt;true&lt;/IncludeInVSIX&gt;

&lt;/ Contenu&gt;

Si ce n’est pas le cas, l’IDE tente de se compiler le contenu du modèle lorsque vous générez l’extension VSIX et vous verrez probablement une erreur. Fichiers de code dans les modèles contiennent souvent spéciaux [les paramètres de modèle](https://msdn.microsoft.com/en-us/library/eehb4faa(v=vs.110).aspx) utilisé par Visual Studio lorsque le modèle de projet est instancié et par conséquent ne peut pas être compilé dans l’IDE.

![Explorateur de solutions](custom-mvc-templates/_static/image6.jpg)

Fermer le concepteur VSIX, cliquez avec le bouton droit sur le **source.extension.manifest** de fichiers dans **l’Explorateur de solutions** et sélectionnez **ouvrir avec** et choisissez la **() XML Éditeur de texte)** option.

![Ouvrir avec la boîte de dialogue](custom-mvc-templates/_static/image7.jpg)

Créer un  **&lt;actifs&gt;**  élément et ajouter un  **&lt;Asset&gt;**  élément pour chaque fichier doit être inclus dans le VSIX. Le **Type** attribut de chaque  **&lt;Asset&gt;**  élément doit être défini sur **Microsoft.VisualStudio.Mvc.Template**. Il s’agit d’un espace de noms personnalisé seulement l’Assistant de projet MVC comprend. Reportez-vous à la documentation de schéma de VSIX 2.0 pour plus d’informations sur la structure et la disposition du fichier manifeste.

Ajouter simplement les fichiers à l’extension VSIX n’est pas suffisant pour enregistrer les modèles avec l’Assistant MVC. Vous devez fournir des informations telles que le nom du modèle, une description, moteurs d’affichage pris en charge et langage de programmation à l’Assistant MVC. Cette information est contenue dans les attributs personnalisés associés à la  **&lt;Asset&gt;**  élément pour chaque **vstemplate** fichier.

&lt;Asset d:VsixSubPath =&quot;ProjectTemplates\MyMvcWebApplicationProjectTemplate.csaspx&quot;

Type =&quot;Microsoft.VisualStudio.Mvc.Template&quot;

d:source =&quot;fichier&quot;

Chemin d’accès =&quot;ProjectTemplates\MyMvcWebApplicationProjectTemplate.csaspx\BasicMvcWebApplicationProjectTemplate.11.csaspx.vstemplate&quot;

ProjectType =&quot;MVC&quot;

Language =&quot;c#&quot;

ViewEngine =&quot;Aspx&quot;

TemplateId =&quot;MyMvcApplication&quot;

Titre =&quot;Application Web de base personnalisée&quot;

Description =&quot;dérivée d’un modèle personnalisé à partir d’une application web de MVC de base (Razor)&quot;

Version =&quot;4.0&quot;/&gt;

Voici une explication des attributs personnalisés qui doivent être présents :

- **ProjectType** doit être définie sur MVC.
- **Langue** désigne le langage de développement pris en charge par le modèle. Les valeurs valides sont c# ou VB.
- **ViewEngine** désigne le moteur d’affichage pris en charge par le modèle, telles que Aspx ou Razor. Vous pouvez spécifier une valeur personnalisée pour ce champ.
- **TemplateId** est utilisé pour regrouper les modèles. Si la valeur correspond à un ID de modèle existant, il sera remplacent les modèles précédemment inscrits avec l’Assistant MVC.
- **Titre** désigne la description courte affichée dans l’Assistant MVC sous chaque modèle de projet.
- **Description** désigne une description plus détaillée du modèle.

Une fois que vous avez ajouté tous les fichiers du manifeste et enregistré, vous remarquerez que la **actifs** onglet dans le concepteur affiche tous les fichiers, mais pas les attributs personnalisés vous avez ajouté à la  **&lt;Asset&gt;**  éléments pour le **vstemplate** fichiers.

![Composants de Concepteur de projet](custom-mvc-templates/_static/image8.jpg)

Il reste maintenant qu’à compiler le projet VSIX et l’installer.

S’assurer que toutes les instances de Visual Studio sont fermées sur l’ordinateur où vous avez l’intention de tester l’extension VSIX. Visual Studio recherche de nouvelles extensions lors du démarrage, si l’IDE est ouvert lors de l’installation d’une extension VSIX, vous devrez redémarrer Visual Studio. Dans l’Explorateur de solutions, double-cliquez sur le fichier VSIX pour lancer le **programme d’installation de VSIX**, cliquez sur **installer** , puis lancez Visual Studio.

![Programme d’installation VSIX](custom-mvc-templates/_static/image9.jpg)

Dans le menu, sélectionnez **Outils > Extensions et mises à jour** pour vérifier que votre extension a été installée. Si le programme d’installation de VSIX a signalé des erreurs lors de l’installation de l’extension, vous pouvez afficher le journal du programme d’installation de VSIX pour plus d’informations. Le journal est généralement créé dans le **%temp%** dossier de l’utilisateur qui a installé l’extension, par exemple **C:\Users\Bob\AppData\Local\Temp**.

![Extensions et mises à jour](custom-mvc-templates/_static/image10.jpg)

Après la fermeture de la fenêtre, vous pouvez créer un projet MVC 4 pour voir si vos nouveaux modèles sont affichés dans l’Assistant MVC.

![Nouveau projet ASP.NET MVC 4](custom-mvc-templates/_static/image11.jpg)

## <a name="limitations"></a>Limitations

1. L’Assistant MVC ne gère pas les modèles localisés.
2. L’Assistant ne signale pas les erreurs en cas d’échec localiser des modèles personnalisés. Si un des attributs personnalisés requis sont absent, le modèle est simplement exclure à partir de l’Assistant.
