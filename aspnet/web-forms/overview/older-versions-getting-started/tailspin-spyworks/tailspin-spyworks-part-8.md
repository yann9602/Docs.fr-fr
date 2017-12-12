---
uid: web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-8
title: "Partie 8 : Pages Final, la gestion des exceptions et Conclusion | Documents Microsoft"
author: JoeStagner
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 8 ajoute une page de contact, sur la page et l’exception en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2010
ms.topic: article
ms.assetid: 5aeadf8f-39f3-4f07-a78f-1c310c64fb23
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-8
msc.type: authoredcontent
ms.openlocfilehash: 0dd1717ff1051f18a78fe77402c7603008b9b486
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-8-final-pages-exception-handling-and-conclusion"></a>Partie 8 : Pages Final, la gestion des exceptions et Conclusion
====================
par [Joe Stagner](https://github.com/JoeStagner)

> Tailspin Spyworks montre comment extrêmement simple est de créer des applications puissantes et évolutives pour la plateforme .NET. Il illustre comment utiliser les nouvelles fonctionnalités dans ASP.NET 4 pour créer un magasin en ligne, y compris les achats, l’extraction et l’administration.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 8 ajoute une page de contact, sur la page et la gestion des exceptions. Il s’agit de la fin de la série.


## <a id="_Toc260221680"></a>Contactez Page (envoi par courrier électronique à partir d’ASP.NET)

Créer une nouvelle page nommée ContactUs.aspx

Le Concepteur de créer la forme suivante pris note spécial pour inclure le ToolkitScriptManager et le contrôle de l’éditeur à partir de la AjaxdControlToolkit. .

![](tailspin-spyworks-part-8/_static/image1.jpg)

Double-cliquez sur le bouton « Submit » pour générer un gestionnaire d’événements dans le fichier code-behind et implémentez une méthode pour envoyer les informations de contact en tant qu’un message électronique.

[!code-csharp[Main](tailspin-spyworks-part-8/samples/sample1.cs)]

Ce code nécessite que votre fichier web.config contient une entrée dans la section de configuration qui spécifie le serveur SMTP à utiliser pour l’envoi du message électronique.

[!code-xml[Main](tailspin-spyworks-part-8/samples/sample2.xml)]

## <a id="_Toc260221681"></a>Sur la Page

Créer une page nommée AboutUs.aspx et ajoutez le contenu que vous le souhaitez.

## <a id="_Toc260221682"></a>Gestionnaire d’exceptions global

Enfin, tout au long de l’application nous avons levée des exceptions et il existe des circonstances imprévues autrement à froid également cause non gérée des exceptions dans l’application web.

Nous ne souhaitez jamais une exception non gérée à afficher pour le visiteur du site web.

![](tailspin-spyworks-part-8/_static/image2.jpg)

Outre une expérience utilisateur horribles les exceptions non gérées peuvent également être un problème de sécurité.

Pour résoudre ce problème, nous implémenterons un gestionnaire d’exceptions global.

Pour ce faire, ouvrez le fichier Global.asax et notez le Gestionnaire d’événements prégénéré suivant.

[!code-csharp[Main](tailspin-spyworks-part-8/samples/sample3.cs)]

Ajoutez du code pour implémenter l’Application\_Gestionnaire d’erreurs comme suit.

[!code-csharp[Main](tailspin-spyworks-part-8/samples/sample4.cs)]

Ensuite, ajoutez une page nommée Error.aspx à la solution et ajoutez cet extrait de balisage.

[!code-aspx[Main](tailspin-spyworks-part-8/samples/sample5.aspx)]

Désormais, dans la Page\_charger extrait de gestionnaire d’événements les messages d’erreur de l’objet Request.

[!code-csharp[Main](tailspin-spyworks-part-8/samples/sample6.cs)]

## <a id="_Toc260221683"></a>Conclusion

Nous avons vu que ce Web Forms ASP.NET facilite la création d’un site Web sophistiqué avec un accès de base de données, l’appartenance, AJAX, etc. assez rapidement.

Nous espérons que ce didacticiel vous a accordé les outils que vous avez besoin pour commencer à créer votre propre Web Forms ASP.NET applications !

>[!div class="step-by-step"]
[Précédent](tailspin-spyworks-part-7.md)
