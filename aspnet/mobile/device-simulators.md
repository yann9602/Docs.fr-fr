---
uid: mobile/device-simulators
title: Simuler des appareils mobiles courants pour le test | Documents Microsoft
author: rick-anderson
description: "Vous pouvez télécharger des émulateurs pour les navigateurs et les appareils mobiles courants en suivant ces liens."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/28/2011
ms.topic: article
ms.assetid: bfb5612e-c3ec-4f28-b43b-63d781aa2272
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /mobile/device-simulators
msc.type: content
ms.openlocfilehash: a8293a5bff9ed73f177be2d9928d8d686c4f311d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="simulate-popular-mobile-devices-for-testing"></a>Simuler des appareils mobiles courants pour les tests
====================
> Vous pouvez télécharger des émulateurs pour les navigateurs et les appareils mobiles courants en suivant ces liens.


| Appareil ou navigateur | Émulateur / simulateur |
| --- | --- |
| BrowserStack hébergé virtualisation du navigateur ![BrowserStack hébergé virtualisation du navigateur](device-simulators/_static/image1.png) | [Virtualisation de navigateur hébergé BrowserStack](http://browserstack.com) tester votre environnement local ou de production dans n’importe quel navigateur sur n’importe quelle plateforme. Vous pouvez créer un tunnel entre votre ordinateur et le réseau BrowserStack dans votre propre ordinateur virtuel hébergé. Assurez-vous d’obtenir le [BrowserStack Extension de Visual Studio](https://visualstudiogallery.msdn.microsoft.com/2dfa32b1-3c47-439d-b1c5-9e28be18b81c) pour une expérience plus transparente. |
| Windows Phone | [Téléchargements du Kit de développement logiciel Windows Phone](https://dev.windowsphone.com/downloadsdk) le Windows Phone Software Development Kit (SDK) inclut tous les outils dont vous avez besoin pour développer des applications et des jeux pour Windows Phone |
| iPhone / iPod / iPad | [Electric Plum](http://www.electricplum.com/studio.aspx) iPhone et iPad simulateurs pour Windows, ainsi qu’une réactivité outil de conception. Peut s’intégrer avec l’option « Parcourir avec.. » de Visual Studio 2012. |
| Android | [Page d’accueil du Kit de développement logiciel Android](https://developer.android.com/sdk) |
| Opera Mobile / Opera Mini | Versions les plus récentes : [les outils de développement Opera accueil](http://www.opera.com/developer/tools/) Opera Mini 4.2 : [basée sur Java en ligne le simulateur](http://www.opera.com/mobile/demo/?ver=4) |
| Windows Mobile 6.5.3 | [Kit d’outils de développement de Windows Mobile 6.5.3](https://www.microsoft.com/downloads/en/details.aspx?FamilyID=c0213f68-2e01-4e5c-a8b2-35e081dcf1ca&amp;displaylang=en) Notez que pour accorder l’accès au réseau de téléphone, vous devez également la carte de réseau virtuel inclus dans [Virtual PC 2007](https://www.microsoft.com/downloads/en/details.aspx?FamilyID=04d26402-3199-48a3-afa2-2dc0b40a73b6&amp;DisplayLang=en). Pour vous connecter à Internet Explorer sur le téléphone à votre serveur de développement Visual Studio, consultez [billet de blog de Kiran Patil](http://kiranpatils.wordpress.com/2009/11/19/access-internetlocal-website-from-your-windows-mobile-device-emulators/). |
| Windows Mobile 6.1 | [Images d’émulateur pour Visual Studio 2005/2008](https://www.microsoft.com/downloads/en/details.aspx?FamilyID=3d6f581e-c093-4b15-ab0c-a2ce5bffdb47) |

Notez que si vous souhaitez afficher votre application sur un véritable appareil mobile (qui est la seule option pour tester entièrement les iPhone ou iPad, car il n’existe aucune véritable émulateur pour Windows), vous aurez besoin héberger votre application dans IIS ou IIS Express. Vous ne peut pas facilement utiliser le serveur web intégré de Visual Studio pour ce faire, car il ne sera pas répondre aux requêtes à partir d’autres ordinateurs.
