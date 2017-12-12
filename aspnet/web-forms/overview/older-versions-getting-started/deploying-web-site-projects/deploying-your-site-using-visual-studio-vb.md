---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/deploying-your-site-using-visual-studio-vb
title: "Déploiement de votre Site à l’aide de Visual Studio (VB) | Documents Microsoft"
author: rick-anderson
description: "Visual Studio inclut des outils de déploiement d’un site Web. Plus d’informations sur ces outils dans ce didacticiel."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/01/2009
ms.topic: article
ms.assetid: 977105f3-7987-4e50-8be7-afb53b4ca28a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/deploying-your-site-using-visual-studio-vb
msc.type: authoredcontent
ms.openlocfilehash: af4257a91c08efc498c86aceac6fa7f64e527a74
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="deploying-your-site-using-visual-studio-vb"></a>Déploiement de votre Site à l’aide de Visual Studio (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/4/5/F/45F815EC-8B0E-46D3-9FB8-2DC015CCA306/ASPNET_Hosting_Tutorial_04_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/E/8/9/E8920AE6-D441-41A7-8A77-9EF8FF970D8B/aspnet_tutorial04_DeployingViaVS_vb.pdf)

> Visual Studio inclut des outils de déploiement d’un site Web. Plus d’informations sur ces outils dans ce didacticiel.


## <a name="introduction"></a>Introduction

Le didacticiel précédent examiné comment déployer une application de web ASP.NET simple pour un fournisseur d’hébergement web. Plus précisément, le didacticiel vous a montré comment utiliser un client FTP comme FileZilla pour transférer les fichiers nécessaires à partir de l’environnement de développement à l’environnement de production. Visual Studio offre également des outils intégrés pour faciliter le déploiement à un fournisseur d’hébergement web. Ce didacticiel examine deux de ces outils : l’outil Copier le Site Web, où vous pouvez déplacer les fichiers vers et depuis un serveur web à distance à l’aide de FTP ou les Extensions serveur FrontPage ; et l’outil de publication, qui copie tout le site Web à un emplacement spécifié.


> [!NOTE]
> Autres outils liées au déploiement proposés par Visual Studio incluent [projets d’installation Web](https://msdn.microsoft.com/en-us/library/wx3b589t.aspx) et [projets de déploiement Web](https://www.microsoft.com/downloads/details.aspx?FamilyId=0AA30AE8-C73B-4BDD-BB1B-FE697256C459&amp;displaylang=en) Add-In. Projets d’installation Web empaqueter le contenu d’un site Web et les informations de configuration dans un seul fichier MSI. Cette option est particulièrement utile pour les sites Web qui sont déployés dans un intranet ou pour les entreprises qui vendent une application web des clients installer sur leurs serveurs web. Le Web déploiement de projets est qu'un Visual Studio complément, qui facilite la spécification des différences de configuration entre les builds pour les environnements de développement et les environnements de production. Projets d’installation Web ne sont pas abordés dans cette série de didacticiels ; Projets de déploiement Web sont résumées dans le [ *les différences entre développement Configuration commune et Production* ](common-configuration-differences-between-development-and-production-vb.md) didacticiel.


## <a name="deploying-your-site-using-the-copy-web-site-tool"></a>Déploiement de votre Site à l’aide de l’outil Copier le Site Web

Outil Copier le Site Web de Visual Studio est des fonctionnalités similaires à un client FTP autonome. En bref, l’outil Copier le Site Web vous permet de se connecter à un site web à distance via FTP ou les Extensions serveur FrontPage. Comme pour l’interface utilisateur de FileZilla, l’interface utilisateur de copier le Site Web se compose de deux volets : le volet gauche répertorie les fichiers locaux, tandis que le volet droit répertorie ces fichiers sur le serveur de destination.

> [!NOTE]
> L’outil Copier le Site Web est uniquement disponible pour les projets de Site Web. Cet outil n’offre pas Visual Studio lorsque vous travaillez avec un projet d’Application Web.


Examinons à l’aide de l’outil Copier le Site Web pour publier l’application critique en production. Étant donné que l’outil Copier le Site Web fonctionne uniquement avec les projets qui utilisent le modèle de projet de Site Web que nous pouvons examiner uniquement à l’aide de cet outil avec le projet BookReviewsWSP. Ouvrir ce projet.

Lancer le projet d’outil Copier le Site Web en cliquant sur l’icône Copier le Site Web dans l’Explorateur de solutions (cette icône est entourée dans la Figure 1) ; ou bien, vous pouvez sélectionner l’option Copier le Site Web à partir du menu du site Web. Chacune de ces approches lance l’interface utilisateur de copier le Site Web indiqué dans la Figure 1 ; seul le volet de gauche dans la Figure 1 est rempli, car nous avons encore pour vous connecter à un serveur distant.


[![Interface utilisateur de l’outil Copier le Site Web est divisée en deux volets.](deploying-your-site-using-visual-studio-vb/_static/image2.png)](deploying-your-site-using-visual-studio-vb/_static/image1.png)

**Figure 1**: Interface utilisateur de l’outil de la copie Site Web est divisé en deux volets ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-visual-studio-vb/_static/image3.png))


Pour déployer notre site, que vous devez tout d’abord vous connecter au fournisseur de serveur web. Cliquez sur le bouton se connecter en haut de l’interface utilisateur de copier le Site Web. Cela affiche la boîte de dialogue Ouvrir le Site Web indiquée dans la Figure 2.

Vous pouvez vous connecter au site Web de destination en sélectionnant une des quatre options de la gauche :

- **Système de fichiers** -Sélectionnez cette option pour déployer votre site à un dossier ou un partage réseau accessible à partir de votre ordinateur.
- **Serveur IIS local** -Utilisez cette option pour déployer le site sur le serveur web IIS installé sur votre ordinateur.
- **FTP Site** -se connecter à un site web à distance à l’aide de FTP.
- **Site distant** -se connecter à un site Web distant, à l’aide des Extensions serveur FrontPage.

La plupart des fournisseurs d’hébergement web prend en charge FTP, mais moins prennent en charge les extensions serveur FrontPage. Pour cette raison, j’ai sélectionné l’option de FTP Site et ensuite entré les informations de connexion comme indiqué dans la Figure 2.


[![Spécifiez le site Web de Destination](deploying-your-site-using-visual-studio-vb/_static/image5.png)](deploying-your-site-using-visual-studio-vb/_static/image4.png)

**Figure 2**: spécifiez le site Web de Destination ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-visual-studio-vb/_static/image6.png))


Après vous être connecté, l’outil Copier le Site Web charge les fichiers sur le site distant dans le volet droit et indique l’état de chaque fichier : nouveaux, supprimés, modifiés ou Unchanged. Vous pouvez copier un fichier à partir du site local vers le site distant, ou inversement a.

Nous allons ajouter une nouvelle page au projet BookReviewsWSP, puis le déployer afin que nous pouvons voir l’outil Copier le Site Web en action. Créer une nouvelle page ASP.NET dans Visual Studio dans le répertoire racine nommé `Privacy.aspx`. Utilisent la page de la page maître `Site.master` et ajoutez la déclaration de confidentialité de votre site à cette page. La figure 3 montre Visual Studio une fois que cette page a été créée.


[![Ajouter une nouvelle Page nommée &lt;code&gt;Privacy.aspx&lt;code&gt; au dossier racine du site Web de](deploying-your-site-using-visual-studio-vb/_static/image8.png)](deploying-your-site-using-visual-studio-vb/_static/image7.png)

**Figure 3**: ajouter une nouvelle Page nommée `Privacy.aspx` au dossier racine du site Web de ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-visual-studio-vb/_static/image9.png))


Ensuite, retournez à l’interface utilisateur de copier le Site Web. Comme le montre la Figure 4, le volet gauche inclut désormais les nouveaux fichiers - `Policy.aspx` et `Policy.aspx.vb`. De plus, ces fichiers sont marqués avec une icône de flèche et un état de nouveau, indiquant qu’ils existent sur le site local, mais pas sur le site distant.


[![L’outil Copier le Site Web inclut le nouveau &lt;code&gt;Privacy.aspx&lt;code&gt; Page dans le volet de gauche](deploying-your-site-using-visual-studio-vb/_static/image11.png)](deploying-your-site-using-visual-studio-vb/_static/image10.png)

**Figure 4**: l’outil Copier le Site Web inclut le nouveau `Privacy.aspx` Page dans le volet de gauche ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-visual-studio-vb/_static/image12.png))


Pour déployer les nouveaux fichiers, sélectionnez-les et cliquez sur l’icône de flèche pour les transférer vers le site distant. Une fois le transfert terminé le `Policy.aspx` et `Policy.aspx.vb` fichiers existent sur les deux sites locaux et distants avec l’état inchangé.

En même temps que la liste de nouveaux fichiers, l’outil Copier le Site Web met en surbrillance tous les fichiers qui diffèrent entre les sites locaux et distants. Pour le voir en action, revenez à la `Privacy.aspx` page et ajoutez quelques mots à la politique de confidentialité. Enregistrez la page, puis revenez à l’outil Copier le Site Web. Comme le montre la Figure 5, le `Privacy.aspx` page dans le volet de gauche a un état modifié indiquant qu’il est synchronisé avec le site distant.


[![L’outil Copier le Site Web indique que le &lt;code&gt;Privacy.aspx&lt;code&gt; Page a été modifiée.](deploying-your-site-using-visual-studio-vb/_static/image14.png)](deploying-your-site-using-visual-studio-vb/_static/image13.png)

**Figure 5**: l’outil Copier le Site Web indique que le `Privacy.aspx` Page a été modifiée ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-visual-studio-vb/_static/image15.png))


L’outil Copier le Site Web indique également si un fichier a été supprimé depuis la dernière opération de copie. Supprimer le `Privacy.aspx` à partir du projet local et l’actualisation de l’outil Copier le Site Web. Le `Privacy.aspx` et `Privacy.aspx.vb` fichiers restent répertoriés dans le volet gauche, mais ont un état supprimé indiquant qu’ils ont été supprimés depuis la dernière opération de copie.

## <a name="publishing-a-web-application"></a>Publication d’une Application Web

Une autre façon de déployer votre application web à partir de Visual Studio est d’utiliser l’option de publication, qui est accessible via le menu Générer. L’option Publier explicitement compile l’application, puis copie tous les fichiers nécessaires sur le site distant spécifié. Comme nous le verrons dans quelques instants, l’option de publication est plus pointue que l’outil Copier le Site Web. Alors que l’outil Copier le Site Web vous permet d’examiner les fichiers sur les sites locaux et distants et vous permet de charger ou télécharger des fichiers individuels en fonction des besoins, l’option Publier déploie l’application web entière.


En plus de copier tous les fichiers nécessaires sur le site distant spécifié, l’option Publier compile également explicitement l’application. Étant donné que les projets d’Application Web doivent être compilés explicitement qu’il doit être pas étonnant que l’option de publication est disponible pour les projets d’Application Web. Ce qui peut être un peu surprenant est que l’option Publier est également disponible pour les projets de Site Web. Comme indiqué dans le [ *déterminer quels fichiers doivent être déployés* ](determining-what-files-need-to-be-deployed-vb.md) didacticiel, les projets de Site Web peut être compilée explicitement via un processus appelé *précompilation*. Ce didacticiel se concentre sur l’utilisation de l’option publier avec les projets d’Application Web ; un didacticiel futures explorerons précompilation, alors que nous allons retourner pour consulter l’aide de l’option publier avec les projets de Site Web.

> [!NOTE]
> Alors que l’option de publication est disponible dans Visual Studio pour les projets de Site Web et les projets d’Application Web, Visual Web Developer offre uniquement la possibilité de publier des projets d’Application Web.


Examinons à présent déployer l’application critiques à l’aide de l’option de publication. Commencez par ouvrir BookReviewsWAP (le projet d’Application Web) dans Visual Studio. Dans le menu Publier choisir le projet de générer des BookReviewsWAP. Cela affiche une boîte de dialogue qui invite à indiquer l’emplacement cible, entre autres options de configuration (voir Figure 6). Beaucoup comme avec l’outil Copier le Site Web vous pouvez entrer un emplacement qui pointe vers un dossier local, un site Web local sur IIS, un site Web à distance qui prend en charge les Extensions serveur FrontPage, ou une adresse de serveur FTP. Vous pouvez choisir de remplacer les fichiers sur le serveur web à distance avec les fichiers déployés ou pour supprimer tout le contenu sur le site distant avant la publication. Vous pouvez également spécifier s’il faut copier :

- Seuls les fichiers dans le projet nécessaires pour exécuter l’application, qui omet le code source inutiles et les fichiers liés au projet.
- Tous les fichiers de projet, qui inclut les fichiers de code source et les fichiers projet Visual Studio, tels que le fichier Solution.
- Tous les fichiers dans le dossier source du projet, qui copie tous les fichiers dans le dossier source du projet, quelle que soit qu’ils sont inclus dans le projet.

Il existe également une option pour télécharger le contenu de la `App_Data` dossier.


[![Spécifiez le site Web de Destination](deploying-your-site-using-visual-studio-vb/_static/image17.png)](deploying-your-site-using-visual-studio-vb/_static/image16.png)

**Figure 6**: spécifiez le site Web de Destination ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-visual-studio-vb/_static/image18.png))


Pour l’application critique du site distant contient les fichiers déployés lors de la copie du projet BookReviewsWSP via l’outil Copier le Site Web. Nous allons donc l’option Publier commencer par supprimer tous les contenus existants. En outre, nous allons simplement copier les fichiers nécessaires au lieu d’encombrer l’environnement de production avec les fichiers de projet et de code source inutiles. Après la spécification de ces options, cliquez sur le bouton Publier. Sur les prochaines secondes Visual Studio déploiera les fichiers nécessaires sur le site de destination, afficher sa progression dans la fenêtre Sortie.

Figure 7 montre les fichiers sur le site FTP après que l’opération de publication est terminée. Notez que seules les pages de balisage et les fichiers de support nécessaire serveur et côté client ont été téléchargés.


[![Uniquement les fichiers nécessaires ont été publiés dans l’environnement de Production](deploying-your-site-using-visual-studio-vb/_static/image20.png)](deploying-your-site-using-visual-studio-vb/_static/image19.png)

**Figure 7**: uniquement le nécessaire fichiers ont été publiés dans l’environnement de Production ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-visual-studio-vb/_static/image21.png))


L’option de publication est un outil moins subtils que l’outil Copier le Site Web. Alors que l’outil Copier le Site Web vous permet d’inspecter les fichiers sur les sites locaux et distants et de voir à quoi ils diffèrent, l’option Publier ne fournit aucun interface. En outre, l’outil Copier le Site Web permet d’apporter des modifications uniques, télécharger ou supprimer des fichiers individuels. L’option Publier n’autorise pas de contrôle de granularité fin ; au lieu de cela, elle publie le *entière* application. Ce comportement a ses avantages et inconvénients. De plus, vous savez quand vous utilisez l’option de publication que vous ne sera pas oublier pour télécharger un fichier important. Considérez que se passe-t-il que si vous avez apporté une petite modification à un site Web très volumineux - avec l’option Publier vous ne peut pas mettre à jour cette page ou deux qui a été modifié, mais au lieu de cela, vous devez patienter pendant que Visual Studio déploie l’intégralité du site.

Il n’est pas rare d’avoir certains fichiers dont le contenu est différente entre la production et les environnements de développement. Un exemple de clé est le fichier de configuration de l’application, `Web.config`. Étant donné que l’option Publier copie les fichiers d’application web qu’il remplace les fichiers de configuration personnalisée de l’environnement de production avec la version dans l’environnement de développement. Le didacticiel suivant traite de cette rubrique et propose des conseils pour le déploiement d’une application web lorsqu’il existent de telles différences.

## <a name="summary"></a>Résumé

Déploiement d’un site Web implique la copie des fichiers nécessaires à partir de l’environnement de développement à l’environnement de production. Le didacticiel précédent a montré comment transférer des fichiers à l’aide d’un client FTP comme FileZilla. Ce didacticiel examiné deux outils de déploiement dans Visual Studio : l’outil Copier le Site Web et l’option de publication. L’outil Copier le Site Web est similaire à un client FTP car il a une interface à deux volets de l’énumération des fichiers sur l’ordinateur local et un ordinateur distant spécifié qui vous permettent de télécharger des fichiers entre les deux ordinateurs. L’option de publication est un outil plus pointu explicitement compile le projet, puis déploie l’application entière vers la destination spécifiée.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Copie de Site Web avec l’outil Copier le Site Web](https://msdn.microsoft.com/en-us/library/1cc82atw.aspx)
- [Comment faire : déployer un Site Web à l’aide de l’outil Copier le Site Web](../../../videos/how-do-i/how-do-i-deploy-a-web-site-using-the-copy-web-site-tool.md) (vidéo)
- [Comment : Publier des projets d’Application Web](https://msdn.microsoft.com/en-us/library/aa983453.aspx)
- [Comment : Publier des Sites Web](https://msdn.microsoft.com/en-us/library/20yh9f1b.aspx)
- [Le programme d’installation et de déploiement des projets dans Visual Studio](https://msdn.microsoft.com/en-us/library/wx3b589t.aspx)

>[!div class="step-by-step"]
[Précédent](deploying-your-site-using-an-ftp-client-vb.md)
[Suivant](common-configuration-differences-between-development-and-production-vb.md)
