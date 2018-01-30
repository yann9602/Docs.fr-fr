---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/processing-unhandled-exceptions-vb
title: "Traitement des Exceptions non gérées (VB) | Documents Microsoft"
author: rick-anderson
description: "Lorsqu’une erreur d’exécution se produit sur une application web en production, il est important pour avertir un développeur et d’enregistrer l’erreur afin qu’il peut être diagnostiqué à un la..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/09/2009
ms.topic: article
ms.assetid: 051296f0-9519-4e78-835c-d868da13b0a0
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/processing-unhandled-exceptions-vb
msc.type: authoredcontent
ms.openlocfilehash: c5a4d2e3468c9b7db5d3acf9f59fc13a6b791497
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
<a name="processing-unhandled-exceptions-vb"></a>Traitement des Exceptions non gérées (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/1/0/C/10CC829F-A808-4302-97D3-59989B8F9C01/ASPNET_Hosting_Tutorial_12_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/5/C/5/5C57DB8C-5DEA-4B3A-92CA-4405544D313B/aspnet_tutorial12_ErrorHandling_vb.pdf)

> Lorsqu’une erreur d’exécution se produit sur une application web en production, il est important pour avertir un développeur et d’enregistrer l’erreur afin qu’il peut être diagnostiqué à un moment ultérieur dans le temps. Ce didacticiel fournit une vue d’ensemble du mode de ASP.NET traite les erreurs d’exécution et examine une façon d’exécuter un code personnalisé chaque fois qu’un bulles d’exception non gérée à l’exécution d’ASP.NET.


## <a name="introduction"></a>Introduction

Lorsqu’une exception non gérée se produit dans une application ASP.NET, il se propage jusqu'à l’exécution d’ASP.NET, ce qui déclenche le `Error` événement et affiche la page d’erreur approprié. Il existe trois différents types de pages d’erreur : le Runtime erreur jaune écran de décès (YSOD) ; Détails de l’Exception YSOD ; et les pages d’erreurs personnalisées. Dans le [didacticiel précédent](displaying-a-custom-error-page-vb.md) nous avons configuré l’application pour utiliser une page d’erreur personnalisée pour les utilisateurs distants et les YSOD détails de l’Exception pour les utilisateurs visitant localement.

À l’aide d’une page d’erreur personnalisée de convivial qui correspond à l’apparence du site est préférée à la valeur par défaut YSOD d’erreur de Runtime, mais l’affichage d’une page d’erreur personnalisés n'est qu’une partie d’une solution de gestion d’erreur complet. Lorsqu’une erreur se produit dans une application en production, il est important que les développeurs sont avertis de l’erreur afin qu’ils peuvent donneront la cause de l’exception et corriger le problème. En outre, les détails de l’erreur doivent être enregistrés pour que l’erreur peut être examinée et de diagnostiquer les problèmes plus loin dans le temps.

Ce didacticiel montre comment accéder aux détails d’une exception non gérée afin qu’ils peuvent être enregistrés et un développeur averti. Les deux didacticiels suivent Explorer des bibliothèques de journalisation erreur qui, après un peu de configuration, avertir les développeurs d’erreurs d’exécution et consigner les détails de leurs automatiquement.

> [!NOTE]
> Les informations examinées dans ce didacticiel sont très utiles si vous avez besoin traiter les exceptions non gérées d’une manière unique ou personnalisée. Dans les cas où vous pouvez enregistrer l’exception et de notifier un développeur, à l’aide d’une bibliothèque de journalisation d’erreur est la marche à suivre. Les deux didacticiels fournissent une vue d’ensemble de deux de ces bibliothèques.


## <a name="executing-code-when-theerrorevent-is-raised"></a>L’exécution de Code lorsque le`Error`événement est déclenché.

Événements fournissent un mécanisme de signalisation que quelque chose d’intéressant s’est produite et d’un autre objet afin d’exécuter du code en réponse à un objet. En tant que développeur d’ASP.NET, vous êtes habitué à penser en termes d’événements. Si vous souhaitez exécuter du code lorsque l’utilisateur clique sur un bouton particulier, vous créez un gestionnaire d’événements pour ce bouton `Click` événement et votre code il. Étant donné que le runtime ASP.NET déclenche son [ `Error` événement](https://msdn.microsoft.com/library/system.web.httpapplication.error.aspx) chaque fois qu’une exception non gérée se produit, il s’ensuit que le code pour enregistrer les détails de l’erreur va passer dans un gestionnaire d’événements. Mais comment créer un gestionnaire d’événements pour le `Error` événement ?

Le `Error` événement est un des nombreux événements dans le [ `HttpApplication` classe](https://msdn.microsoft.com/library/system.web.httpapplication.aspx) qui sont déclenchés à certaines étapes dans le pipeline HTTP pendant la durée de vie d’une demande. Par exemple, le `HttpApplication` de classe [ `BeginRequest` événement](https://msdn.microsoft.com/library/system.web.httpapplication.beginrequest.aspx) est déclenché au début de chaque demande ; son [ `AuthenticateRequest` événement](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx) est déclenché lorsqu’un module de sécurité a identifié le demandeur. Ces `HttpApplication` événements fournissent au développeur de pages permet d’exécuter une logique personnalisée aux divers points dans la durée de vie d’une demande.

Gestionnaires d’événements pour le `HttpApplication` événements peuvent être placées dans un fichier spécial nommé `Global.asax`. Pour créer ce fichier dans votre site Web, ajouter un nouvel élément à la racine de votre site Web à l’aide du modèle de classe d’Application globale portant le nom `Global.asax`.

[![](processing-unhandled-exceptions-vb/_static/image2.png)](processing-unhandled-exceptions-vb/_static/image1.png)

**Figure 1**: ajoutez `Global.asax` à votre Application Web  
 ([Cliquez pour afficher l’image en taille réelle](processing-unhandled-exceptions-vb/_static/image3.png))

Le contenu et la structure de le `Global.asax` fichier créé par Visual Studio diffèrent légèrement en fonction de si vous utilisez un projet d’Application Web (WAP) ou un projet de Site Web (WSP). Avec un point d’accès, le `Global.asax` est implémenté en tant que deux fichiers distincts - `Global.asax` et `Global.asax.vb`. Le `Global.asax` fichier ne contient rien mais un `@Application` directive qui fait référence à la `.vb` fichier ; l’événement gestionnaires d’intérêt sont définis dans le `Global.asax.vb` fichier. Pour WSPs, qu’un seul fichier est créé, `Global.asax`, et les gestionnaires d’événements sont définis dans un `<script runat="server">` bloc.

Le `Global.asax` fichier créé dans un point d’accès par le modèle de classe d’Application globale de Visual Studio inclut des gestionnaires d’événements nommés `Application_BeginRequest`, `Application_AuthenticateRequest`, et `Application_Error`, qui sont des gestionnaires d’événements pour le `HttpApplication` événements `BeginRequest`, `AuthenticateRequest`, et `Error`, respectivement. Il existe également des gestionnaires d’événements nommés `Application_Start`, `Session_Start`, `Application_End`, et `Session_End`, qui sont des gestionnaires d’événements qui se déclenchent lorsque l’application web démarre, lorsque démarre une nouvelle session, lors de l’application se termine, et quand une session se termine, respectivement. Le `Global.asax` fichier créé dans un WSP par Visual Studio contient uniquement le `Application_Error`, `Application_Start`, `Session_Start`, `Application_End`, et `Session_End` gestionnaires d’événements.

> [!NOTE]
> Lors du déploiement de l’application ASP.NET, vous devez la copier le `Global.asax` fichier à l’environnement de production. Le `Global.asax.vb` fichier, qui est créé dans le proxy d’application Web, n’a pas besoin être copié vers la production, car ce code est compilé dans l’assembly du projet.


Les gestionnaires d’événements créés par le modèle de classe d’Application globale de Visual Studio ne sont pas exhaustives. Vous pouvez ajouter un gestionnaire d’événements pour les `HttpApplication` événement par le Gestionnaire d’événements d’affectation de noms `Application_EventName`. Par exemple, vous pouvez ajouter le code suivant à la `Global.asax` pour créer un gestionnaire d’événements pour le [ `AuthorizeRequest` événement](https://msdn.microsoft.com/library/system.web.httpapplication.authorizerequest.aspx):

[!code-vb[Main](processing-unhandled-exceptions-vb/samples/sample1.vb)]

De même, vous pouvez supprimer les gestionnaires d’événements créés par le modèle de classe d’Application globale qui ne sont pas nécessaires. Pour ce didacticiel, nous avons besoin uniquement un gestionnaire d’événements pour le `Error` événement ; hésitez pas à supprimer les autres gestionnaires d’événements à partir de la `Global.asax` fichier.

> [!NOTE]
> *Les Modules HTTP* offrent une autre manière de définir des gestionnaires d’événements pour `HttpApplication` événements. Les Modules HTTP sont créés en tant qu’un fichier de classe qui peut être placé directement dans le projet d’application web ou répartie dans une bibliothèque de classes distinct. Car ils peuvent séparés dans une bibliothèque de classes, les Modules HTTP offrent un modèle réutilisable et plus flexible pour la création de `HttpApplication` gestionnaires d’événements. Alors que le `Global.asax` fichier est spécifique à l’application web où il réside, Modules HTTP peuvent être compilés dans des assemblys, moment auquel ajouter le HTTP Module à un site Web est aussi simple que la suppression de l’assembly le `Bin` dossier et en inscrivant le Module de `Web.config`. Ce didacticiel ne recherche pas dans la création et l’utilisation des Modules HTTP, mais les bibliothèques de journalisation des erreurs deux utilisés dans les deux didacticiels suivants sont implémentés en tant que Modules HTTP. Pour plus d’informations sur les avantages des Modules HTTP, reportez-vous à [à l’aide des Modules HTTP et des gestionnaires à créer des composants enfichables ASP.NET](https://msdn.microsoft.com/library/aa479332.aspx).


## <a name="retrieving-information-about-the-unhandled-exception"></a>Récupérer des informations sur l’Exception non gérée

À ce stade, nous avons un fichier Global.asax avec une `Application_Error` Gestionnaire d’événements. Lors de l’exécution de ce gestionnaire d’événements nous devons avertir un développeur de l’erreur et les journaux de ses détails. Pour accomplir ces tâches, que nous devons d’abord déterminer les détails de l’exception qui a été déclenché. Utilisez l’objet serveur [ `GetLastError` méthode](https://msdn.microsoft.com/library/system.web.httpserverutility.getlasterror.aspx) pour récupérer les détails de l’exception non gérée qui a provoqué le `Error` l’événement.

[!code-vb[Main](processing-unhandled-exceptions-vb/samples/sample2.vb)]

Le `GetLastError` méthode retourne un objet de type `Exception`, qui est le type de base pour toutes les exceptions dans le .NET Framework. Toutefois, dans le code ci-dessus je suis cast de l’objet de l’Exception retournée par `GetLastError` dans un `HttpException` objet. Si le `Error` événement est déclenché, car une exception a été levée lors du traitement d’une ressource ASP.NET, puis l’exception qui a été levée est encapsulée dans un `HttpException`. Pour obtenir de l’exception réelle qui a déclenché l’utilisation d’événements erreur le `InnerException` propriété. Si le `Error` événement a été déclenché en raison d’une exception basée sur HTTP, telle qu’une demande pour une page inexistante, un `HttpException` est levée, mais il ne dispose pas d’une exception interne.

Le code suivant utilise la `GetLastErrormessage` pour récupérer les informations relatives à l’exception qui a déclenché la `Error` événement, le stockage du `HttpException` dans une variable nommée `lastErrorWrapper`. Puis il stocke le type, le message et la trace de la pile de l’exception d’origine dans les trois variables de chaîne, de vérifier si le `lastErrorWrapper` est l’exception réelle qui a déclenché la `Error` événement (dans le cas d’exceptions basé sur HTTP) ou si elle est simplement un wrapper pour une exception a été levée lors du traitement de la demande.

[!code-vb[Main](processing-unhandled-exceptions-vb/samples/sample3.vb)]

À ce stade, vous avez toutes les informations dont vous avez besoin d’écrire du code qui enregistre les détails de l’exception dans une table de base de données. Vous pouvez créer une table de base de données avec des colonnes pour chacun des détails de l’erreur d’intérêt - le type, le message, la trace de la pile et ainsi de suite - ainsi que d’autres renseignements utiles, telles que l’URL de la page demandée et le nom de l’utilisateur actuellement connecté. Dans la `Application_Error` Gestionnaire d’événements vous ensuite se connecter à la base de données et insérer un enregistrement dans la table. De même, vous pouvez ajouter le code pour un développeur de l’erreur par courrier électronique d’alerte.

Les bibliothèques de journalisation erreur examinées dans les deux didacticiels fournissent ces fonctionnalités prêtes à l’emploi, il est donc inutile pour générer cette journalisation des erreurs et notification vous-même. Toutefois, pour montrer que le `Error` événement est déclenché et que le `Application_Error` Gestionnaire d’événements peut être utilisé pour consigner les détails de l’erreur et informer un développeur, nous allons ajouter du code qui notifie un développeur lorsqu’une erreur se produit.

## <a name="notifying-a-developer-when-an-unhandled-exception-occurs"></a>Notification d’un développeur lorsqu’une Exception non gérée se produit.

Lorsqu’une exception non gérée se produit dans l’environnement de production, il est important avertir l’équipe de développement afin qu’ils peuvent évaluer l’erreur et déterminer les actions à entreprendre. Par exemple, s’il existe une erreur dans la connexion à la base de données, vous devez en double Vérifiez votre chaîne de connexion et, éventuellement, ouvrez un ticket de support avec votre société d’hébergement web. Si l’exception s’est produite en raison d’une erreur de programmation, code supplémentaire ou la logique de validation peut doivent être ajoutés afin d’éviter ces erreurs dans le futur.

Les classes .NET Framework dans le [ `System.Net.Mail` espace de noms](https://msdn.microsoft.com/library/system.net.mail.aspx) permet d’envoyer un courrier électronique. Le [ `MailMessage` classe](https://msdn.microsoft.com/library/system.net.mail.mailmessage.aspx) représente un message électronique et possède des propriétés comme `To`, `From`, `Subject`, `Body`, et `Attachments`. Le `SmtpClass` est utilisé pour envoyer un `MailMessage` l’objet à l’aide d’un serveur SMTP spécifié ; les paramètres du serveur SMTP peuvent être spécifiés par programme ou de façon déclarative dans le [ `<system.net>` élément](https://msdn.microsoft.com/library/6484zdc1.aspx) dans le `Web.config file`. Pour plus d’informations sur l’envoi de courrier électronique les messages dans une application ASP.NET vérifier mon article, [envoi de courrier électronique dans ASP.NET](http://aspnet.4guysfromrolla.com/articles/072606-1.aspx)et le [System.Net.Mail FAQ](http://systemnetmail.com/).

> [!NOTE]
> Le `<system.net>` élément contient les paramètres du serveur SMTP utilisés par le `SmtpClient` classe lors de l’envoi d’un message électronique. Votre entreprise d’hébergement web possède un serveur SMTP que vous pouvez utiliser pour envoyer un courrier électronique à partir de votre application. Pour plus d’informations sur les paramètres du serveur SMTP que vous devez utiliser dans votre application web, consultez la section de prise en charge de votre hôte web.


Ajoutez le code suivant à la `Application_Error` Gestionnaire d’événements à envoyer à un développeur un courrier électronique lorsqu’une erreur se produit :

[!code-vb[Main](processing-unhandled-exceptions-vb/samples/sample4.vb)]

Alors que le code ci-dessus est assez longs, la majeure partie de celui-ci crée le code HTML qui s’affiche dans l’e-mail envoyé au développeur. Le code commence par référencer le `HttpException` retournée par le `GetLastError` (méthode) (`lastErrorWrapper`). L’exception réelle qui a été levée par la demande est récupérée `lastErrorWrapper.InnerException` et est affectée à la variable `lastError`. Le type, le message et la pile des informations de trace sont extraites de `lastError` et stockées dans trois variables de chaîne.

Ensuite, un `MailMessage` objet nommé `mm` est créé. Le corps du message électronique est au format HTML et affiche l’URL de la page demandée, le nom de l’utilisateur actuellement connecté et des informations sur l’exception (type, message et trace de la pile). Un des aspects plus intéressants du `HttpException` classe est que vous pouvez générer le code HTML utilisé pour créer l’Exception détails jaune écran de décès (YSOD) en appelant le [GetHtmlErrorMessage méthode](https://msdn.microsoft.com/library/system.web.httpexception.gethtmlerrormessage.aspx). Cette méthode est utilisée ici pour récupérer le balisage YSOD des détails de l’Exception et l’ajouter au message électronique en tant que pièce jointe. Un mot d’attention : si l’exception qui a déclenché la `Error` événement a une exception basée sur HTTP (par exemple, une demande pour une page inexistante) le `GetHtmlErrorMessage` méthode retournera `null`.

L’étape finale consiste à envoyer le `MailMessage`. Cela est obtenu en créant un nouveau `SmtpClient` (méthode) et en appelant sa `Send` (méthode).

> [!NOTE]
> Avant d’utiliser ce code dans votre application web que vous souhaitez modifier les valeurs dans le `ToAddress` et `FromAddress` constantes de support@example.com pour le courrier électronique adresse l’e-mail de notification d’erreur doit être envoyé à et issus de. Vous devez également spécifier les paramètres du serveur SMTP dans le `<system.net>` section `Web.config`. Consultez votre fournisseur d’hébergement web pour déterminer les paramètres du serveur SMTP à utiliser.


Chaque fois qu’une erreur s’est le développeur est envoyé par le code en place un message électronique qui récapitule l’erreur et inclut le YSOD. Dans le didacticiel précédent, nous l’avons démontré une erreur d’exécution en visitant Genre.aspx et en passant un n’est pas valide `ID` valeur via la chaîne de requête, tel que `Genre.aspx?ID=foo`. Sur la page avec le `Global.asax` fichier sur place produit la même expérience utilisateur, comme dans le didacticiel précédent - dans l’environnement de développement vous allez continuer à voir l’Exception détails jaune écran de décès, tandis que dans l’environnement de production, vous allez consultez la page d’erreurs personnalisée. En plus de ce comportement existant, le développeur est envoyé un message électronique.

**Figure 2** montre l’e-mail reçu lors de la visite `Genre.aspx?ID=foo`. Le corps du message électronique résume les informations sur l’exception, tandis que la `YSOD.htm` pièce jointe affiche le contenu qui est indiqué dans le YSOD détails de l’Exception (voir **Figure 3**).

[![](processing-unhandled-exceptions-vb/_static/image5.png)](processing-unhandled-exceptions-vb/_static/image4.png)

**Figure 2**: le développeur est envoyé une Notification par courrier électronique chaque fois qu’il existe une Exception non gérée  
 ([Cliquez pour afficher l’image en taille réelle](processing-unhandled-exceptions-vb/_static/image6.png))

[![](processing-unhandled-exceptions-vb/_static/image8.png)](processing-unhandled-exceptions-vb/_static/image7.png)

**Figure 3**: la Notification par courrier électronique inclut les détails de l’Exception YSOD comme pièce jointe  
 ([Cliquez pour afficher l’image en taille réelle](processing-unhandled-exceptions-vb/_static/image9.png))

## <a name="what-about-using-the-custom-error-page"></a>Qu’en est-il à l’aide de la Page d’erreur personnalisée ?

Ce didacticiel vous a montré comment utiliser `Global.asax` et `Application_Error` Gestionnaire d’événements pour exécuter du code lorsqu’une exception non gérée se produit. Plus précisément, nous avons utilisé ce gestionnaire d’événements pour informer le développeur d’une erreur ; Nous avons l’étendre pour enregistrer également les détails de l’erreur dans une base de données. La présence de la `Application_Error` Gestionnaire d’événements n’affecte pas l’expérience de l’utilisateur final. Ils voient la page d’erreurs configuré, soit le YSOD détails de l’erreur, le YSOD erreur Runtime ou la page d’erreurs personnalisée.

Il est naturel pour vous demander si les `Global.asax` fichier et `Application_Error` événement n’est nécessaire lors de l’utilisation d’une page d’erreurs personnalisée. Lorsqu’une erreur se produit l’utilisateur est indiqué à la page d’erreur personnalisés, pourquoi ne pouvons pas nous placer le code pour informer le développeur et les journaux des détails de l’erreur dans la classe code-behind de la page d’erreur personnalisés ? Pendant que vous pouvez certainement ajouter du code à la classe code-behind de la page erreur personnalisée vous n’avez pas d’accès aux détails de l’exception qui a déclenché la `Error` événement lors de l’utilisation de la technique que nous avons examinés dans le didacticiel précédent. Appel de la `GetLastError` méthode à partir de la page d’erreur personnalisée retourne `Nothing`.

La raison de ce comportement est, car la page d’erreur personnalisée est atteinte via une redirection. Lorsqu’une exception non gérée atteint le runtime ASP.NET, le moteur ASP.NET déclenche son `Error` événement (qui exécute le `Application_Error` Gestionnaire d’événements), puis *redirige* l’utilisateur à la page d’erreur personnalisée en émettant un `Response.Redirect(customErrorPageUrl)`. Le `Response.Redirect` méthode envoie une réponse au client avec un code d’état HTTP 302 demandant le navigateur pour demander une nouvelle URL, à savoir la page d’erreurs personnalisée. Le navigateur demande ensuite automatiquement cette nouvelle page. Vous pouvez indiquer que la page d’erreur personnalisée a été demandée séparément à partir de la page d’origine de l’erreur, car la barre d’adresses du navigateur change à l’URL de page d’erreur personnalisée (consultez **Figure 4**).

[![](processing-unhandled-exceptions-vb/_static/image11.png)](processing-unhandled-exceptions-vb/_static/image10.png)

**Figure 4**: lorsqu’une erreur se produit le navigateur est redirigé vers l’URL de Page d’erreur personnalisée  
 ([Cliquez pour afficher l’image en taille réelle](processing-unhandled-exceptions-vb/_static/image12.png))

L’effet net est que la requête où l’exception non gérée s’est produite se termine lorsque le serveur répond avec la redirection HTTP 302. La requête suivante à la page d’erreur personnalisée est une toute nouvelle demande ; à ce stade ASP.NET moteur a rejeté les informations d’erreur et, en outre, n’a aucun moyen d’associer l’exception non gérée dans la demande précédente avec la nouvelle demande de la page d’erreurs personnalisée. C’est pourquoi `GetLastError` retourne `null` lorsqu’elle est appelée à partir de la page d’erreurs personnalisée.

Toutefois, il est possible que la page d’erreur personnalisée exécutée pendant la même requête qui a provoqué l’erreur. Le [ `Server.Transfer(url)` ](https://msdn.microsoft.com/library/system.web.httpserverutility.transfer.aspx) méthode transfère l’exécution à l’URL spécifiée et le traite dans la même demande. Vous pouvez déplacer le code le `Application_Error` Gestionnaire d’événements à la classe code-behind de la page erreur personnalisée, en remplaçant dans `Global.asax` par le code suivant :

[!code-vb[Main](processing-unhandled-exceptions-vb/samples/sample5.vb)]

Maintenant lorsqu’une exception non gérée se produit le `Application_Error` Gestionnaire d’événements transfère le contrôle à la page d’erreur personnalisée appropriée en fonction du code d’état HTTP. Étant donné que le contrôle a été transféré, la page d’erreur personnalisée a accès aux informations d’exception non gérée via `Server.GetLastError` et peut informer un développeur de l’erreur et des journaux de ses détails. Le `Server.Transfer` appel s’arrête le moteur ASP.NET à partir de la redirection de l’utilisateur à la page d’erreurs personnalisée. Au lieu de cela, le contenu de la page d’erreur personnalisée est retourné en réponse à la page qui a généré l’erreur.

## <a name="summary"></a>Récapitulatif

Lorsqu’une exception non gérée se produit dans une application web ASP.NET le runtime ASP.NET déclenche le `Error` événement et affiche la page d’erreurs configuré. Nous pouvons le développeur de l’erreur, ouvrez une session ses détails ou traitement dans une autre façon, en créant un gestionnaire d’événements pour l’événement d’erreur. Il existe deux façons de créer un gestionnaire d’événements `HttpApplication` événements tels que `Error`: dans le `Global.asax` fichier ou à partir d’un HTTP Module. Ce didacticiel vous a montré comment créer un `Error` Gestionnaire d’événements dans le `Global.asax` fichier qui notifie les développeurs d’une erreur au moyen d’un message électronique.

Création d’un `Error` Gestionnaire d’événements est utile si vous avez besoin traiter les exceptions non gérées d’une manière unique ou personnalisée. Toutefois, créer votre propre `Error` Gestionnaire d’événements à enregistrer l’exception ou pour avertir un développeur n’est pas l’utilisation la plus efficace de votre temps qu’il existe déjà des bibliothèques de journalisation erreur gratuit et facile à utiliser qui peuvent être configurés dans quelques minutes. Les deux didacticiels examiner deux de ces bibliothèques.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Vue d’ensemble des gestionnaires HTTP et de Modules HTTP ASP.NET](https://support.microsoft.com/kb/307985)
- [Normalement répondre aux Exceptions non gérées - traitement des Exceptions non gérées](http://aspnet.4guysfromrolla.com/articles/091306-1.aspx)
- [`HttpApplication`Classe et l’objet d’Application ASP.NET](http://www.eggheadcafe.com/articles/20030211.asp)
- [Les gestionnaires HTTP et des Modules HTTP dans ASP.NET](http://www.15seconds.com/Issue/020417.htm)
- [Envoi de courrier électronique dans ASP.NET](http://aspnet.4guysfromrolla.com/articles/072606-1.aspx)
- [Présentation de le `Global.asax` fichier](http://aspalliance.com/1114_Understanding_the_Globalasax_file.all)
- [À l’aide des Modules HTTP et des gestionnaires pour créer des composants enfichables ASP.NET](https://msdn.microsoft.com/library/aa479332.aspx)
- [Utilisation avec ASP.NET `Global.asax` fichier](http://articles.techrepublic.com.com/5100-10878_11-5771721.html)
- [Utilisation de `HttpApplication` Instances](https://msdn.microsoft.com/library/a0xez8f2.aspx)

>[!div class="step-by-step"]
[Précédent](displaying-a-custom-error-page-vb.md)
[Suivant](logging-error-details-with-asp-net-health-monitoring-vb.md)
