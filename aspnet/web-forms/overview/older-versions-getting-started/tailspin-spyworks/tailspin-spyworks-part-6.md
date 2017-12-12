---
uid: web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-6
title: "Partie 6 : L’appartenance d’ASP.NET | Documents Microsoft"
author: JoeStagner
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 6 ajoute l’appartenance d’ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/21/2010
ms.topic: article
ms.assetid: f70a310c-9557-4743-82cb-655265676d39
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/tailspin-spyworks/tailspin-spyworks-part-6
msc.type: authoredcontent
ms.openlocfilehash: efb0e2bed1172f42c7f1539f016fba305c47e3eb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-6-aspnet-membership"></a>Partie 6 : L’appartenance d’ASP.NET
====================
par [Joe Stagner](https://github.com/JoeStagner)

> Tailspin Spyworks montre comment extrêmement simple est de créer des applications puissantes et évolutives pour la plateforme .NET. Il illustre comment utiliser les nouvelles fonctionnalités dans ASP.NET 4 pour créer un magasin en ligne, y compris les achats, l’extraction et l’administration.
> 
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application Tailspin Spyworks. Partie 6 ajoute l’appartenance d’ASP.NET.


## <a id="_Toc260221672"></a>Utilisation de l’appartenance d’ASP.NET

![](tailspin-spyworks-part-6/_static/image1.png)

Cliquez sur la sécurité

![](tailspin-spyworks-part-6/_static/image1.jpg)

Assurez-vous que nous utilisons l’authentification par formulaire.

![](tailspin-spyworks-part-6/_static/image2.jpg)

Pour créer deux utilisateurs, utilisez le lien « Create User ».

![](tailspin-spyworks-part-6/_static/image3.jpg)

Lorsque vous avez terminé, reportez-vous à la fenêtre de l’Explorateur de solutions et actualiser l’affichage.

![](tailspin-spyworks-part-6/_static/image2.png)

Notez que le fichier ASPNETDB. MDF fine a été créé. Ce fichier contient les tables pour prendre en charge des services ASP.NET de base telles que l’appartenance.

Maintenant, nous pouvons commencer le processus de validation de mise en œuvre.

Commencez par créer une page CheckOut.aspx.

La page CheckOut.aspx doit uniquement être disponible pour les utilisateurs connectés sont donc nous limitent l’accès à enregistrées dans les utilisateurs et redirige les utilisateurs qui ne sont pas connectés à la page de connexion.

Pour ce faire, nous allons ajouter les éléments suivants à la section de configuration de notre fichier web.config.

[!code-xml[Main](tailspin-spyworks-part-6/samples/sample1.xml)]

Le modèle pour les applications ASP.NET Web Forms automatiquement ajouté une section de l’authentification sur le fichier web.config et établie la page de connexion par défaut.

[!code-xml[Main](tailspin-spyworks-part-6/samples/sample2.xml)]

Nous devons modifier le Login.aspx fichier code-behind pour migrer un panier d’achat anonyme lorsque l’utilisateur se connecte. Modifier la Page\_charge l’événement comme suit.

[!code-csharp[Main](tailspin-spyworks-part-6/samples/sample3.cs)]

Ajoutez un gestionnaire d’événements « LoggedIn » comme suit pour définir le nom de session pour l’utilisateur qui vient d’être connecté et modifier l’id de session temporaire dans le panier d’achat à celle de l’utilisateur en appelant la méthode MigrateCart dans notre classe MyShoppingCart. (Implémentée dans le fichier .cs)

[!code-csharp[Main](tailspin-spyworks-part-6/samples/sample4.cs)]

Implémentez la méthode MigrateCart() comme suit.

[!code-csharp[Main](tailspin-spyworks-part-6/samples/sample5.cs)]

Dans checkout.aspx nous allons utiliser un contrôle EntityDataSource et un GridView dans notre page, vérifiez que nous l’avons fait dans notre page de panier d’achat.

[!code-aspx[Main](tailspin-spyworks-part-6/samples/sample6.aspx)]

Notez que notre contrôle GridView spécifie un gestionnaire d’événements « ondatabound » nommé MyList\_RowDataBound par conséquent, nous allons implémenter ce gestionnaire d’événements comme suit.

[!code-csharp[Main](tailspin-spyworks-part-6/samples/sample7.cs)]

Cela maintient méthode en cours d’exécution total de l’achat panier car chaque ligne est liée et met à jour la ligne en bas du contrôle GridView.

À ce stade, nous avons implémenté une « « présentation de la commande doit être placé.

Nous allons traiter un scénario de panier vide en ajoutant quelques lignes de code à notre Page\_événement de chargement :

[!code-csharp[Main](tailspin-spyworks-part-6/samples/sample8.cs)]

Lorsque l’utilisateur clique sur le bouton « Submit » nous exécutera le code suivant dans le Gestionnaire d’événement de Click de bouton Envoyer.

[!code-csharp[Main](tailspin-spyworks-part-6/samples/sample9.cs)]

La « substance » du processus de soumission de commande doit être implémentée dans la méthode SubmitOrder() de notre classe MyShoppingCart.

SubmitOrder sera :

- Prendre tous les articles dans le panier d’achat et de les utiliser pour créer un nouvel enregistrement de commande et les enregistrements OrderDetails associés.
- Calculer la Date d’expédition.
- Désactivez le panier d’achat.


[!code-csharp[Main](tailspin-spyworks-part-6/samples/sample10.cs)]

Dans le cadre de cet exemple d’application, nous allons calculer une date d’expédition par simple ajout de deux jours à la date actuelle.

[!code-csharp[Main](tailspin-spyworks-part-6/samples/sample11.cs)]

L’application maintenant en cours d’exécution permettra de nous pour tester le processus d’achat à partir du début à la fin.

>[!div class="step-by-step"]
[Précédent](tailspin-spyworks-part-5.md)
[Suivant](tailspin-spyworks-part-7.md)
