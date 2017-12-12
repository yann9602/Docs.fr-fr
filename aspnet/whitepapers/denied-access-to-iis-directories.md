---
uid: whitepapers/denied-access-to-iis-directories
title: "Accès refusé à ASP.NET pour les répertoires IIS | Documents Microsoft"
author: rick-anderson
description: "Ce livre blanc décrit ce que vous devez faire si une demande à votre application ASP.NET renvoie l’erreur « accès refusé au répertoire du nom du répertoire. Impossible de s..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/10/2010
ms.topic: article
ms.assetid: 3cb27b8a-354f-4332-bfe0-232b13bbf8aa
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/denied-access-to-iis-directories
msc.type: content
ms.openlocfilehash: 64118ac7a5f280775106d2dc7636923b08f28d89
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-denied-access-to-iis-directories"></a>Accès refusé à ASP.NET pour les répertoires IIS
====================
> Ce livre blanc décrit ce que vous devez faire si une demande à votre application ASP.NET retourne l’erreur, « refuser l’accès à *Nom_répertoire* active. Échec de démarrage de l’analyse du répertoire chaanges. »
> 
> S’applique à ASP.NET 1.0 et 1.1 d’ASP.NET.


Version RTM de V1 ASP.NET s’exécute maintenant à l’aide d’une moins privilégié du compte windows - inscrit en tant que le compte « ASPNET » sur un ordinateur local.

Sur certains systèmes verrouillés, ce compte peut pas par défaut ont accès en lecture sécurité pour les répertoires de contenu d’un site Web, le répertoire racine de l’application ou le répertoire racine du site web. Dans ce cas, vous recevrez l’erreur suivante lors de la demande de pages à partir d’une application web donnée :

![](denied-access-to-iis-directories/_static/image1.jpg)

Pour résoudre ce problème, vous devrez modifier les autorisations de sécurité sur les répertoires appropriés.

Plus précisément, ASP.NET nécessite une lecture, exécuter et liste d’accès pour le compte ASPNET pour la racine du site web (par exemple : c:\inetpub\wwwroot ou n’importe quel répertoire autre site que vous avez peut-être configuré dans IIS), le répertoire de contenu et le répertoire racine de l’application pour surveiller les modifications de fichier de configuration. La racine de l’application correspond au chemin de dossier associé au répertoire virtuel d’application dans l’outil d’Administration IIS (inetmgr).

Par exemple, considérez la hiérarchie d’application suivant sous le dossier wwwroot.

`C:\inetpub\wwwroot\myapp\default.aspx`

Pour cet exemple, le compte ASPNET doit les autorisations de lecture définies ci-dessus pour le contenu dans le myapp et le répertoire wwwroot. Une ACL unique ou sur le dossier racine peut également éventuellement servir pour les deux répertoires si elles sont imbriquées.

Pour ajouter des autorisations pour un répertoire, procédez comme suit :

- À l’aide de l’Explorateur Windows, accédez au répertoire
- Cliquez avec le bouton droit sur le dossier de répertoire et choisissez « Propriétés »
- Accédez à l’onglet « Sécurité » dans la boîte de dialogue propriété
- Cliquez sur le bouton « Ajouter » et entrez le nom d’ordinateur suivi du nom de compte ASPNET. Par exemple, sur un ordinateur nommé « webdev », vous entrez webdev\ASPNET et appuyez sur « OK ».
- Assurez-vous que le compte ASPNET possède le « en lecture &amp; Execute », « Afficher le contenu du dossier » et les cases à cocher « Lecture » vérifiée.
- Appuyez sur OK pour fermer la boîte de dialogue et enregistrer les modifications.

![](denied-access-to-iis-directories/_static/image2.jpg)

Si vous le souhaitez, ces modifications peuvent être automatisées à l’aide de scripts ou l’outil « cacls.exe » qui est fourni avec Windows. Pour plus d’informations sur le compte ASPNET, veuillez consulter le [document FAQ](https://go.microsoft.com/fwlink/?LinkId=5828).

Si une application web donnée repose sur la disponibilité d’écriture ou modifier les autorisations dans un fichier ou un dossier particulier, celui-ci peut être octroyé en suivant la même procédure et en activant les cases à cocher « Écriture » et/ou « Modifier ».

Sur les ordinateurs qui permettent à tout le monde ou au groupe utilisateurs lecture l’accès à ces répertoires (qui est la configuration par défaut), aucun problème n’est détecté, et les étapes ci-dessus ne sont pas requis.
