---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/deploying-your-site-using-an-ftp-client-cs
title: "Déploiement de votre Site à l’aide d’un Client FTP (c#) | Documents Microsoft"
author: rick-anderson
description: "Pour déployer une application ASP.NET, la plus simple consiste à copier manuellement les fichiers nécessaires à partir de l’environnement de développement à l’environnement de production. THI..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/01/2009
ms.topic: article
ms.assetid: a3599cf7-8474-4006-954a-3bc693736b66
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/deploying-your-site-using-an-ftp-client-cs
msc.type: authoredcontent
ms.openlocfilehash: 1edd53b1005449c060ff92fc7ebd02dbe7fa6ac2
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="deploying-your-site-using-an-ftp-client-c"></a>Déploiement de votre Site à l’aide d’un Client FTP (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/4/5/F/45F815EC-8B0E-46D3-9FB8-2DC015CCA306/ASPNET_Hosting_Tutorial_03_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/E/8/9/E8920AE6-D441-41A7-8A77-9EF8FF970D8B/aspnet_tutorial03_DeployingViaFTP_cs.pdf)

> Pour déployer une application ASP.NET, la plus simple consiste à copier manuellement les fichiers nécessaires à partir de l’environnement de développement à l’environnement de production. Ce didacticiel montre comment un client FTP permet d’obtenir les fichiers à partir de votre bureau sur le fournisseur d’hôte web.


## <a name="introduction"></a>Introduction

Le didacticiel précédent a introduit une application web de livre révision ASP.NET simple, qui est constituée d’un certain nombre de pages ASP.NET, une page maître, une base personnalisée `Page` classe, un nombre d’images, et les feuilles de style CSS de trois. Nous sommes maintenant prêts à déployer cette application sur un fournisseur d’hôte web, à partir de laquelle l’application sera accessible à toute personne avec une connexion à Internet.


À partir de notre discussions dans le [ *déterminer quels fichiers doivent être déployés* ](determining-what-files-need-to-be-deployed-cs.md) didacticiel, nous savons quels fichiers doivent être copiés vers le fournisseur de serveur web. (Souvenez-vous que les fichiers sont copiés varie selon que votre application est explicitement ou automatiquement compilée). Mais comment faire pour que les fichiers à partir de l’environnement de développement (notre bureau) jusqu'à l’environnement de production (le serveur web géré par le fournisseur d’hôte web) ? Le [ **F** ile **T** transfert de fichier **P** rotocol (FTP)](http://en.wikipedia.org/wiki/File_Transfer_Protocol) est un protocole couramment utilisé pour copier des fichiers à partir d’un ordinateur à un autre sur un réseau. Une autre option consiste aux Extensions serveur FrontPage (FPSE). Ce didacticiel se concentre sur l’utilisation d’un logiciel client FTP autonome pour déployer les fichiers nécessaires à partir de l’environnement de développement à l’environnement de production.

> [!NOTE]
> Visual Studio inclut des outils de publication de sites Web via FTP ; Ces outils, ainsi que d’examiner les outils qui utilisent les extensions serveur FrontPage, est traité dans l’étape suivante du didacticiel.


Pour copier les fichiers à l’aide de FTP, nous devons un *client FTP* sur l’environnement de développement. Un client FTP est une application qui est conçue pour copier les fichiers à partir de l’ordinateur, il est installé sur un ordinateur qui exécute un *serveur FTP*. (Si votre fournisseur d’hébergement web prend en charge les transferts de fichiers via FTP, comme la plupart, puis il est un serveur FTP en cours d’exécution sur leurs serveurs web.) Il existe un nombre d’applications de client FTP. Votre navigateur web peut même double comme un client FTP. Mon client FTP favori et l’autre que j’utilise pour ce didacticiel, est [FileZilla](http://filezilla-project.org/), un client FTP libre et open source qui n’est disponible pour Windows, Linux et Mac. N’importe quel client FTP fonctionne, cependant, c’est le cas hésitez pas à utiliser le client vous conviennent le mieux.

Si vous suivez le long de vous avez besoin pour créer un compte avec un fournisseur d’hébergement web avant de vous peut terminera ce didacticiel ou les conditions suivantes. Comme indiqué dans le didacticiel précédent, il existe une poignée de sociétés de fournisseur d’hôte web avec une large gamme de prix, les fonctionnalités et la qualité de service. Pour cette série de didacticiels j’utiliserai [ASP.NET de remise](http://discountasp.net) comme mon hôte web fournisseur, mais vous pouvez le suivre n’importe quel fournisseur d’hébergement web tant qu’ils prennent en charge votre site est développé dans la version d’ASP.NET. (Ces didacticiels ont été créées à l’aide d’ASP.NET 3.5). En outre, étant donné que nous allons copier les fichiers au fournisseur de serveur web à l’aide de FTP dans ce didacticiel et dans les futures celles il est impératif que votre fournisseur d’hébergement web prend en charge l’accès à leurs serveurs web FTP. Presque tous les fournisseurs d’hébergement web offrent cette fonctionnalité, mais vous devez vérifier avant de vous inscrire.

## <a name="deploying-the-book-review-web-application-project"></a>Déployer le projet d’Application Web Book révision

Rappelez-vous qu’il existe deux versions de l’application web critique : une implémentation à l’aide du modèle de projet d’Application Web (BookReviewsWAP) et l’autre en utilisant le modèle de projet de Site Web (BookReviewsWSP). Le type de projet détermine si le site est compilé automatiquement ou explicitement, et ce modèle de compilation détermine quels fichiers doivent être déployés. Par conséquent, nous allons examiner à déployer les projets BookReviewsWAP et BookReviewsWSP séparément, en commençant par le BookReviewsWAP. Prenez un moment pour télécharger ces deux applications ASP.NET si vous n'avez pas déjà fait.

Lancer le projet BookReviewsWAP en accédant à la `BookReviewsWAP` dossier, en double-cliquant sur le `BookReviewsWAP.sln` fichier. Avant de déployer le projet, il est important de le générer pour vous assurer que toutes les modifications au code source sont incluses dans l’assembly compilé. Pour générer le projet dans le menu Build et choisissez l’option de menu BookReviewsWAP de Build. Le code source dans le projet est compilé dans un assembly unique, `BookReviewsWAP.dll`, qui est placé dans le `Bin` dossier.

Nous sommes maintenant prêts à déployer les fichiers nécessaires. Lancez votre client FTP et connectez-vous au serveur web à votre fournisseur d’hébergement web. (Cela inclut l’adresse du serveur FTP ainsi que d’un nom d’utilisateur et mot de passe, lorsque vous vous inscrivez avec une société d’hébergement web il enverra un message électronique vous plus d’informations sur la façon de se connecter au serveur FTP.)

Copiez les fichiers suivants à partir de votre bureau vers le dossier du site Web racine à votre fournisseur d’hébergement web. Lorsque vous FTP sur le serveur web sur le site web hôte fournisseur, vous êtes susceptible de dans le répertoire du site Web racine. Toutefois, certains fournisseurs d’hébergement web ont un sous-dossier nommé `www` ou `wwwroot` qui sert le dossier racine pour les fichiers de votre site Web. Enfin, lorsque vous FTPing les fichiers vous devrez peut-être créer la structure de dossiers correspondants sur l’environnement de production - la `Bin` dossier, le `Fiction` dossier, le `Images` dossier et ainsi de suite.

- `~/Default.aspx`
- `~/About.aspx`
- `~/Site.master`
- `~/Web.config`
- `~/Web.sitemap`
- Le contenu complet de le `Styles` dossier
- Le contenu complet de le `Images` dossier (et son sous-dossier, `BookCovers`)
- `~/Fiction/Default.aspx`
- `~/Fiction/Blaze.aspx`
- `~/Tech/Default.aspx`
- `~/Tech/CYOW.aspx`
- `~/Tech/TYASP35.aspx`
- `~/Bin/BookReviewsWAP.dll`

La figure 1 montre FileZilla après avoir copié les fichiers nécessaires. FileZilla affiche les fichiers sur l’ordinateur local sur la gauche et les fichiers sur l’ordinateur distant sur la droite. Comme le montre la Figure 1, les fichiers de code source ASP.NET, tels que `About.aspx.cs`, sont sur l’ordinateur local (l’environnement de développement), mais n’ont pas été copiés au fournisseur de serveur web (l’environnement de production) car vous n’avez pas besoin d’être déployées lors de l’utilisation des fichiers de code compilation explicite.

> [!NOTE]
> Il n’existe pas de risque que les fichiers de code source sur le serveur de production, car ils sont ignorés. ASP.NET refuse les demandes HTTP vers les fichiers de code source par défaut afin que même si les fichiers de code source sont présents sur le serveur de production sont inaccessibles pour les visiteurs de votre site Web. (Autrement dit, si un utilisateur tente à visiter `http://www.yoursite.com/Default.aspx.cs` qu’ils obtiendront une page d’erreur qui explique que ces types de fichiers - `.cs` fichiers - sont interdites.)


[![Utilisez un Client FTP pour copier les fichiers nécessaires à partir de votre bureau sur le serveur Web au niveau du fournisseur d’hôte Web](deploying-your-site-using-an-ftp-client-cs/_static/image2.png)](deploying-your-site-using-an-ftp-client-cs/_static/image1.png)

**Figure 1**: utiliser un FTP Client pour copier les fichiers nécessaires à partir de votre bureau sur le serveur Web au fournisseur de serveur Web ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-an-ftp-client-cs/_static/image3.png))


Après avoir déployé votre site prendre quelques instants pour tester le site. Si vous avez acheté un nom de domaine et les paramètres DNS configurés correctement, vous pouvez visiter le site en entrant votre nom de domaine. Vous pouvez également votre fournisseur d’hébergement web doit avoir fourni vous avec une URL vers votre site, qui ressemble à ce qui *accountname*. *webhostprovider*.com ou *webhostprovider*.com /*accountname*. Par exemple, l’URL de mon compte sur ASP.NET de remise est : `http://httpruntime.web703.discountasp.net`.

Figure 2 montre le site critiques déployé. Notez que je suis l’affichage sur ASP de remise. Les serveurs du réseau, à `http://httpruntime.web703.discountasp.net`. À ce stade, toute personne disposant d’une connexion à Internet peut afficher mon site Web ! Comme nous l’attend, le site recherche et se comporte comme s’il était au moment de la tester dans l’environnement de développement.

> [!NOTE]
> Si vous obtenez une erreur lors de l’affichage de votre application de prendre un moment pour vous assurer que vous avez déployé le bon jeu de fichiers. Ensuite, vérifiez le message d’erreur pour voir si elle révèle des indices sur le problème. Vous pouvez activer au service d’assistance de votre entreprise hôte web ou publiez votre question sur le forum approprié à la [Forums ASP.NET](https://forums.asp.net/).


[![Le Site de révisions du livre est désormais Accessible à toute personne disposant d’une connexion Internet](deploying-your-site-using-an-ftp-client-cs/_static/image5.png)](deploying-your-site-using-an-ftp-client-cs/_static/image4.png)

**Figure 2**: le Site de révisions du livre est désormais Accessible à toute personne disposant d’une connexion Internet ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-an-ftp-client-cs/_static/image6.png))


## <a name="deploying-the-book-review-web-site-project"></a>Déploiement du projet de Site Web de révision livre

Lorsque vous déployez une application ASP.NET qui utilise une compilation automatique, telles que le projet de Site Web BookReviewsWSP, il n’existe aucun assembly compilé dans le `Bin` dossier. Par conséquent, les fichiers de code source de l’application web doivent être déployés dans l’environnement de production. Examinons ce processus.

Comme avec le projet d’Application Web, il est judicieux de première génération de l’application avant de le déployer. Lors de la génération d’un projet de Site Web ne crée pas un assembly, il vérifie les erreurs de compilation dans la page. Supérieure pour rechercher ces erreurs plutôt que d’avoir un visiteur de votre site de leur découverte pour vous !

Une fois que vous avez correctement créé le projet, utilisez votre client FTP pour copier les fichiers suivants au dossier racine du site Web à votre fournisseur d’hébergement web. Vous devrez peut-être créer la structure de dossiers correspondants sur l’environnement de production.

> [!NOTE]
> Si vous déjà déployé le projet mais que vous voulez essayer le déploiement du projet BookReviewsWSP de BookReviewsWAP, tout d’abord supprimer tous les fichiers sur le serveur web qui ont été téléchargés lors du déploiement BookReviewsWAP, puis déployez les fichiers pour BookReviewsWSP.


- `~/Default.aspx`
- `~/Default.aspx.cs`
- `~/About.aspx`
- `~/About.aspx.cs`
- `~/Site.master`
- `~/Site.master.cs`
- `~/Web.config`
- `~/Web.sitemap`
- Le contenu complet de le `Styles` dossier
- Le contenu complet de le `Images` dossier (et son sous-dossier, `BookCovers`)
- `~/App_Code/BasePage.cs`
- `~/Fiction/Default.aspx`
- `~/Fiction/Default.aspx.cs`
- `~/Fiction/Blaze.aspx`
- `~/Fiction/Blaze.aspx.cs`
- `~/Tech/Default.aspx`
- `~/Tech/Default.aspx.cs`
- `~/Tech/CYOW.aspx`
- `~/Tech/CYOW.aspx.cs`
- `~/Tech/TYASP35.aspx`
- `~/Tech/TYASP35.aspx.cs`

La figure 3 montre FileZilla après avoir copié les fichiers nécessaires. Comme vous pouvez le voir, ASP.NET source tels que les fichiers de code, `About.aspx.cs`, sont présents sur l’ordinateur local (l’environnement de développement) et le fournisseur de serveur web (l’environnement de production), car les fichiers de code doivent être déployées lors de l’utilisation automatique compilation.


[![Utilisez un Client FTP pour copier les fichiers nécessaires à partir de votre bureau sur le serveur Web au niveau du fournisseur d’hôte Web](deploying-your-site-using-an-ftp-client-cs/_static/image8.png)](deploying-your-site-using-an-ftp-client-cs/_static/image7.png)

**Figure 3**: utiliser un FTP Client pour copier les fichiers nécessaires à partir de votre bureau sur le serveur Web au fournisseur de serveur Web ([cliquez pour afficher l’image en taille réelle](deploying-your-site-using-an-ftp-client-cs/_static/image9.png))


L’expérience utilisateur n’est pas affectée par le modèle de compilation de l’application. Les mêmes pages ASP.NET sont accessibles et rechercher et se comportent de la même si le site Web a été créé à l’aide du modèle de projet d’Application Web ou le modèle de projet de Site Web.

### <a name="updating-a-web-application-on-production"></a>Mise à jour d’une Application Web de Production

Développement d’applications web et de déploiement ne sont pas un processus unique. Par exemple, lors de la création du site Web critique j’ai créé les différentes pages et a écrit le code qui l’accompagne sur mon ordinateur (l’environnement de développement). Après avoir atteint un certain état stable, j’ai déployé mon application pour que d’autres Impossible de visiter le site et de mon lisons. Mais le déploiement ne marque pas la fin de mon travail de développement sur ce site. Puis-je ajouter plus critiques ou implémenter de nouvelles fonctionnalités, par exemple pour autoriser mes visiteurs à la documentation de taux ou laisser leurs commentaires. Ces améliorations seraient être développées sur l’environnement de développement et lorsque terminée, devra être déployé. Développement et déploiement, par conséquent, sont cycliques. Vous développez une application, puis déployez. Alors que le site est en ligne et en production, de nouvelles fonctionnalités sont ajoutées et bogues résolus au fil du temps, ce qui nécessite le redéploiement de l’application. Et ainsi de suite et ainsi de suite.

Comme prévu, lors du redéploiement sur une application web que vous devez seulement copier les fichiers nouveaux et modifiés. Il est inutile de redéployer les pages inchangées ou fichiers de support du côté serveur ou côté client (bien qu’il n’existe aucun risque ainsi).

> [!NOTE]
> Une chose à prendre en compte lors de l’utilisation de compilation explicite est que chaque fois que vous ajoutez une nouvelle page ASP.NET pour le projet ou apportez des modifications associées à du code, vous devez régénérer votre projet, qui met à jour de l’assembly dans le `Bin` dossier. Par conséquent, vous devez copier cet assembly mis à jour vers la production lors de la mise à jour d’une application web sur la production (ainsi que d’autres contenu nouveau et mis à jour).


Également comprendre que les modifications de la `Web.config` ou les fichiers dans le `Bin` active s’arrête et redémarre le Pool d’applications du site Web. Si votre état de session est stocké à l’aide de la `InProc` mode (par défaut), puis les visiteurs de votre site perdent leur état de session chaque fois que les fichiers de ces clés sont modifiées. Pour éviter cet inconvénient, envisagez de stocker la session à l’aide du `StateServer` ou `SQLServer` modes. Pour plus d’informations sur cette rubrique lire [Modes d’état de Session](https://msdn.microsoft.com/library/ms178586.aspx).

Enfin, gardez à l’esprit que le nouveau déploiement d’une application peut prendre de quelques secondes à quelques minutes, selon le nombre et la taille des fichiers qui doivent être copiés dans l’environnement de production. Pendant ce temps aux utilisateurs visitant votre site peuvent rencontrer des erreurs ou un comportement étrange. Vous pouvez « désactiver » toute votre application en ajoutant une page nommée `App_Offline.htm` au répertoire racine de votre application qui explique à vos utilisateurs que le site est arrêté pour maintenance (ou tout autre) et qu’il va être sauvegarder sous peu. Lorsque le `App_Offline.htm` fichier est présent, le runtime ASP.NET redirige toutes les demandes entrantes vers cette page.

## <a name="summary"></a>Récapitulatif

Déploiement d’une application web implique la copie des fichiers nécessaires à partir de l’environnement de développement à l’environnement de production. L’approche la plus courante à laquelle les fichiers sont transférés via un réseau est le fichier FTP (Transfer Protocol), et la plupart des fournisseurs d’hébergement web prennent en charge l’accès à leurs serveurs web FTP. Dans ce didacticiel, nous avons vu comment utiliser un client FTP pour déployer les fichiers nécessaires sur le serveur web. Une fois déployé, le site Web peut être consulté par toute personne avec une connexion à Internet.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Application\_Offline.htm et contourner la fonctionnalité « Erreurs conviviale d’Internet Explorer »](https://weblogs.asp.net/scottgu/App_5F00_Offline.htm-and-working-around-the-_2200_IE-Friendly-Errors_2200_-feature)
- [Modes d’état de session](https://msdn.microsoft.com/library/ms178586.aspx)

>[!div class="step-by-step"]
[Précédent](determining-what-files-need-to-be-deployed-cs.md)
[Suivant](deploying-your-site-using-visual-studio-cs.md)
