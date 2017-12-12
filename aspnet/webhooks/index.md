---
uid: webhooks/index
title: "Vue d’ensemble ASP.NET WebHooks | Documents Microsoft"
author: rick-anderson
description: "Introduction à ASP.NET WebHooks."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: 5e2843f0-f499-448f-a712-33d4e9858321
ms.technology: 
ms.prod: .net-framework
ms.openlocfilehash: 52399c23cdf393a2f7f94661fd48098ced65948c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="aspnet-webhooks-overview"></a>Vue d’ensemble d’ASP.NET WebHooks

WebHooks est un modèle HTTP léger en fournissant un modèle simple pub/sub pour associer ensemble les services API Web et SaaS. Lorsqu’un événement se produit dans un service, une notification est envoyée sous la forme d’une requête HTTP POST aux abonnés inscrits. La demande de publication contient des informations sur l’événement qui rend possible pour le récepteur d’agir en conséquence.

En raison de leur simplicité, WebHooks sont déjà exposés par un grand nombre de services, y compris [Dropbox](http://dropbox.com/), [GitHub](http://www.github.com/), [Bitbucket](https://bitbucket.org/), [MailChimp ](http://www.mailchimp.com/), [PayPal](http://www.paypal.com/), [Slack](http://www.slack.com), [Stripe](http://www.stripe.com), [Trello](http://www.trello.com/)et bien plus encore. Par exemple, un WebHook peut indiquer qu’un fichier a été modifié dans [Dropbox](http://dropbox.com/), une modification du code ait été validée dans GitHub ou un paiement a été initié dans [PayPal](http://www.paypal.com/), ou une carte a été créée dans [ Trello](http://www.trello.com/). Les possibilités sont illimitées !

Microsoft ASP.NET WebHooks rend plus facile à envoyer et recevoir des WebHooks dans le cadre de votre application ASP.NET :

* Côté réception, il fournit un modèle commun pour recevoir et traiter des WebHooks à partir de n’importe quel nombre de fournisseurs de WebHook. Il s’agit de prêtes à l’emploi avec prise en charge pour [Dropbox](http://dropbox.com/), [GitHub](http://www.github.com/), [Bitbucket](https://bitbucket.org/), [MailChimp](http://www.mailchimp.com/), [PayPal](http://www.paypal.com/), [Pusher](http://www.pusher.com), [Salesforce](http://www.salesforce.com), [Slack](http://www.slack.com), [Stripe](http://www.stripe.com), [Trello](http://www.trello.com/),[ WordPress](http://www.wordpress.com) et [Zendesk](https://www.zendesk.com/) , mais il est facile d’ajouter la prise en charge pour plus d’informations.

* Sur le côté envoi, il prend en charge pour la gestion et le stockage des abonnements, ainsi que pour l’envoi de notifications d’événements pour l’ensemble approprié d’abonnés. Cela vous permet de définir votre propre jeu d’événements que les abonnés peuvent s’abonner à et les notifier en cas de choses.

Les deux parties peuvent servir ensemble ou éloignés selon votre scénario. Si vous devez uniquement recevoir des WebHooks à partir d’autres services, vous pouvez utiliser uniquement la partie destinataire ; Si vous souhaitez uniquement exposer le WebHooks pour d’autres à utiliser, puis vous pouvez le faire uniquement.

Le code cible ASP.NET Web API 2 et ASP.NET MVC 5 et est disponible en tant que [OSS sur GitHub](https://github.com/aspnet/WebHooks).

## <a name="webhooks-overview"></a>Vue d’ensemble de le WebHooks

WebHooks est un modèle, ce qui signifie qu’il varie en fonction de leur utilisation à partir de service au service, mais l’idée de base est la même. Vous pouvez considérer WebHooks comme un modèle simple pub/sub où un utilisateur peut s’abonner aux événements qui se produit ailleurs. Les notifications d’événements sont propagées en tant que les requêtes HTTP POST contenant des informations sur l’événement lui-même.

La requête HTTP POST contient généralement un objet JSON ou les données de formulaire HTML déterminées par l’expéditeur de WebHook, y compris des informations sur l’événement à l’origine du WebHook pour déclencher. Par exemple, un exemple d’un corps de demande POST de WebHook de [GitHub](http://www.github.com/) ressemble à ceci à la suite d’un nouveau problème ouvert dans un référentiel particulier :

```json
{
  "action": "opened",
  "issue": {
      "url": "https://api.github.com/repos/octocat/Hello-World/issues/1347",
      "number": 1347,
      ...
  },
  "repository": {
      "id": 1296269,
      "full_name": "octocat/Hello-World",
      "owner": {
          "login": "octocat",
          "id": 1
          ...
      },
      ...
  },
  "sender": {
      "login": "octocat",
      "id": 1,
      ...
  }
}
```

Pour vous assurer que le WebHook est bien de l’expéditeur initial, la requête POST est sécurisée d’une certaine façon et ensuite vérifiée par le récepteur. Par exemple, [GitHub WebHooks](https://developer.github.com/webhooks/) inclut un *X-Hub-Signature* en-tête HTTP avec un hachage du corps de la demande qui est vérifié par l’implémentation du récepteur, vous n’avez pas à vous soucier d’elle.

Le flux de WebHook passe généralement quelque chose comme ceci :

* L’expéditeur WebHook expose un client peut s’abonner aux événements. Les événements décrivent les modifications observables dans le système, par exemple un nouvel élément de données ayant été insérée, la fin d’un processus ou autre chose.

* Le récepteur de WebHook s’abonne à l’inscription d’un WebHook composé de quatre éléments :

     1. Un URI pour lequel la notification d’événement doit être validée sous la forme d’une requête HTTP POST ;

     2. Un ensemble de filtres qui décrit les événements pour lesquels le WebHook doit être déclenché ;

     3. Une clé secrète qui est utilisée pour signer la demande HTTP POST ;

     4. Données supplémentaires qui doit être inclus dans la requête HTTP POST. Cela peut par exemple être des champs d’en-tête HTTP supplémentaires ou des propriétés incluses dans le corps de la demande HTTP POST.

* Une fois qu’un événement se produit, les enregistrements de WebHook correspondants sont trouvés et que les requêtes HTTP POST sont envoyées. En règle générale, la génération des demandes HTTP POST sont retentées plusieurs fois si pour une raison quelconque, que le destinataire ne répond pas ou les résultats de la requête HTTP POST dans une réponse d’erreur.

## <a name="webhooks-processing-pipeline"></a>Pipeline de traitement des WebHooks

Le pipeline de traitement de Microsoft ASP.NET WebHooks pour le WebHooks entrant ressemble à ceci :

![Pipeline de traitement ASP.NET WebHooks](_static/WebHookReceivers.png)

Les deux concepts clés sont *récepteurs* et *gestionnaires*:

* *Récepteurs* sont chargés pour la gestion de la version particulière de WebHook d’un expéditeur donné et d’appliquer les vérifications de sécurité pour vous assurer que la demande de WebHook est en effet à partir de l’expéditeur initial.

* *Gestionnaires* sont généralement où le code utilisateur s’exécute le traitement du WebHook particulier.

Dans les nœuds suivants, ces concepts sont décrits plus en détails.
