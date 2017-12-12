---
uid: web-api/overview/security/external-authentication-services
title: "Service d’authentification externe avec l’API Web ASP.NET (c#) | Documents Microsoft"
author: rmcmurray
description: "Décrit l’utilisation des Services d’authentification externe dans l’API Web ASP.NET."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2013
ms.topic: article
ms.assetid: 3bb8eb15-b518-44f5-a67d-a27e051aedc6
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/external-authentication-services
msc.type: authoredcontent
ms.openlocfilehash: 5d6e6727f387d047e7b41a6efa0d2dadf467558e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="external-authentication-services-with-aspnet-web-api-c"></a>Service d’authentification externe avec l’API Web ASP.NET (c#)
====================
par [Robert McMurray](https://github.com/rmcmurray)

Visual Studio 2013 et ASP.NET 4.5.1 développer les options de sécurité pour [Single Page Applications](../../../single-page-application/index.md) (SPA) et [API Web](../../index.md) des services d’authentification externe, qui incluent plusieurs services OAuth/OpenID et les services d’authentification des médias sociaux : Accounts Microsoft, Twitter, Facebook et Google.

### <a name="in-this-walkthrough"></a>Dans cette procédure pas à pas

- [À l’aide des Services d’authentification externe](#USING)
- [Création de l’exemple d’Application Web](#SAMPLE)
- [L’activation de l’authentification Facebook](#FACEBOOK)
- [Activation de l’authentification de Google](#GOOGLE)
- [Activer l’authentification Microsoft](#MICROSOFT)
- [L’activation de l’authentification Twitter](#TWITTER)
- [Informations supplémentaires](#MOREINFO)

    - [Combinaison de Services d’authentification externe](#COMBINE)
    - [Configuration d’IIS Express à utiliser un nom de domaine complet](#FQDN)
    - [Comment obtenir les paramètres de votre Application pour l’authentification Microsoft](#OBTAIN)
    - [Facultatif : Désactiver l’inscription du Local](#DISABLE)

### <a name="prerequisites"></a>Conditions préalables

Pour suivre les exemples de cette procédure pas à pas, vous devez disposer des éléments suivants :

- Visual Studio 2013
- Un compte pour au moins un des services d’authentification externes suivants :

    - Un compte d’utilisateur Google
    - Un compte de développeur avec l’identificateur de l’application et de la clé secrète pour un des services sociaux d’authentification suivants :

        - Les comptes Microsoft ([https://go.microsoft.com/fwlink/?LinkID=144070](https://go.microsoft.com/fwlink/?LinkID=144070))
        - Twitter ([https://dev.twitter.com/](https://dev.twitter.com/))
        - Facebook ([https://developers.facebook.com/](https://developers.facebook.com/))

<a id="USING"></a>
## <a name="using-external-authentication-services"></a>À l’aide des Services d’authentification externe

La multitude de services d’authentification externe qui sont actuellement disponibles à l’aide les développeurs web afin de réduire le développement du temps lors de la création d’applications web. Les utilisateurs Web ont généralement plusieurs comptes existants pour les services web les plus courants et des sites Web sociaux, par conséquent, lorsqu’un web application implémente l’authentification des services à partir d’un service web externe ou un site Web de médias sociaux, il enregistre le temps de développement qui serait ont été passé à la création d’une implémentation d’authentification. À l’aide d’un service d’authentification externe enregistre les utilisateurs finaux de créer un autre compte pour votre application web, ainsi que d’avoir à mémoriser un autre nom d’utilisateur et mot de passe.

Dans le passé, les développeurs ont dû deux possibilités : créer leur propre implémentation de l’authentification ou de savoir comment intégrer un service d’authentification externe dans leurs applications. Niveau de base au plus, le diagramme suivant illustre un flux de requête simple d’un agent utilisateur (navigateur web) qui demande des informations à partir d’une application web qui est configurée pour utiliser un service d’authentification externes :

[![](external-authentication-services/_static/image2.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image1.png)

Dans le diagramme précédent, l’agent utilisateur (ou un navigateur web dans cet exemple) effectue une demande à une application web, qui redirige le navigateur web à un service d’authentification externe. L’agent utilisateur envoie ses informations d’identification au service d’authentification externe, et si l’agent utilisateur a été authentifié, le service d’authentification externe redirige l’agent utilisateur à l’application web d’origine avec une certaine forme de jeton dont la agent utilisateur envoie à l’application web. L’application web utilisera le jeton pour vérifier que l’agent utilisateur a bien été authentifié par le service d’authentification externe, et l’application web peut utiliser le jeton pour collecter plus d’informations sur l’agent utilisateur. Une fois que l’application est terminée le traitement des informations de l’agent utilisateur, l’application web retourne la réponse adéquate à l’agent utilisateur en fonction de ses paramètres d’autorisation.

Dans ce deuxième exemple, l’agent utilisateur négocie avec l’application web et le serveur d’autorisation externes et l’application web effectue une communication supplémentaire avec le serveur d’autorisation externes pour extraire des informations supplémentaires sur l’utilisateur agent :

[![](external-authentication-services/_static/image4.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image3.png)

Visual Studio 2013 et ASP.NET 4.5.1 facilitent l’intégration avec les services d’authentification externe pour les développeurs en fournissant une intégration intégrée pour les services d’authentification suivants :

- Facebook
- Google
- Microsoft Accounts (comptes Windows Live ID)
- Twitter

Les exemples de cette procédure pas à pas va vous montrer comment configurer chacun des services d’authentification externes pris en charge en utilisant le nouveau modèle d’Application Web ASP.NET qui est fourni avec Visual Studio 2013.

> [!NOTE]
> Si nécessaire, vous devrez peut-être ajouter votre nom de domaine complet pour les paramètres de votre service d’authentification externe. Cette condition est basée sur les contraintes de sécurité pour certains services d’authentification externe qui requièrent le nom de domaine complet dans vos paramètres d’application faire correspondre le nom de domaine complet qui est utilisée par vos clients. (Les étapes de ce varient considérablement pour chaque service d’authentification externe ; vous devez consulter la documentation pour chaque service d’authentification externe pour voir si cela est nécessaire et comment configurer ces paramètres). Si vous avez besoin de configurer IIS Express à utiliser un nom de domaine complet pour le test de cet environnement, consultez le [configuration IIS Express à utiliser un nom de domaine complet](#FQDN) section plus loin dans cette procédure pas à pas.


<a id="SAMPLE"></a>
## <a name="creating-a-sample-web-application"></a>Création d’un exemple d’Application Web

La procédure suivante vous guide dans la création d’un exemple d’application en utilisant le modèle d’Application Web ASP.NET, et que vous utiliserez cet exemple d’application pour chacun des services d’authentification externe plus loin dans cette procédure pas à pas.

Démarrez Visual Studio 2013 sélectionnez **nouveau projet** à partir de la page de démarrage. Ou, à partir de la **fichier** menu, sélectionnez **nouveau** , puis **projet**.

[![](external-authentication-services/_static/image6.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image5.png)

Lorsque le **nouveau projet** boîte de dialogue s’affiche, sélectionnez **installé** **modèles** et développez **Visual C#**. Sous **Visual C#**, sélectionnez **Web**. Dans la liste des modèles de projet, sélectionnez **Application Web ASP.NET**. Entrez un nom pour votre projet et cliquez sur **OK**.

[![](external-authentication-services/_static/image8.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image7.png)

Lorsque le **nouveau projet ASP.NET** est affichée, sélectionnez le **SPA** modèle et cliquez sur **créer un projet de**.

[![](external-authentication-services/_static/image10.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image9.png)

Attendez que Visual Studio 2013 crée votre projet.

[![](external-authentication-services/_static/image12.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image11.png)

Lorsque Visual Studio 2013 a terminé la création de votre projet, ouvrez le *Startup.Auth.cs* fichier qui se trouve dans le **application\_Démarrer** dossier.

[![](external-authentication-services/_static/image14.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image13.png)

Lorsque vous créez le projet, aucun des services d’authentification externe sont activées dans *Startup.Auth.cs* fichier ; ce qui suit illustre ce que votre code doit ressembler, avec les sections en surbrillance où vous devriez autoriser service d’authentification externe et tous les paramètres appropriés pour pouvoir utiliser l’authentification Microsoft Accounts, Twitter, Facebook ou Google avec votre application ASP.NET :

[!code-csharp[Main](external-authentication-services/samples/sample1.cs)]

Lorsque vous appuyez sur F5 pour générer et déboguer votre application web, il affiche un écran de connexion où vous verrez qu’aucun service d’authentification externe n’a été défini.

[![](external-authentication-services/_static/image16.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image15.png)

Dans les sections suivantes, vous allez apprendre à activer le service d’authentification externe qui est fournies avec ASP.NET dans Visual Studio 2013.

<a id="FACEBOOK"></a>
## <a name="enabling-facebook-authentication"></a>L’activation de l’authentification Facebook

À l’aide de Facebook, vous devez créer un compte de développeur Facebook et votre projet nécessite un ID d’application et de la clé secrète à partir de Facebook pour pouvoir fonctionner. Pour plus d’informations sur la création d’un compte de développeur Facebook et l’obtention de votre ID d’application et de la clé secrète, consultez [https://go.microsoft.com/fwlink/?LinkID=252166](https://go.microsoft.com/fwlink/?LinkID=252166).

Une fois, vous avez obtenu votre ID d’application et de la clé secrète, procédez comme suit pour activer l’authentification Facebook pour votre application web :

1. Lorsque votre projet est ouvert dans Visual Studio 2013, ouvrez le *Startup.Auth.cs* fichier :

    [![](external-authentication-services/_static/image18.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image17.png)
2. Recherchez la section de code en surbrillance :

    [!code-csharp[Main](external-authentication-services/samples/sample2.cs)]
3. Supprimer le &quot; // &quot; caractères pour ne pas commenter les lignes en surbrillance du code, puis ajoutez votre ID d’application et de la clé secrète. Une fois que vous avez ajouté ces paramètres, vous pouvez recompiler votre projet :

    [!code-csharp[Main](external-authentication-services/samples/sample3.cs)]
4. Lorsque vous appuyez sur F5 pour ouvrir votre application web dans votre navigateur web, vous verrez que Facebook a été défini comme un service d’authentification externes :

    [![](external-authentication-services/_static/image20.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image19.png)
5. Lorsque vous cliquez sur le **Facebook** bouton, votre navigateur sera redirigé vers la page de connexion Facebook :

    [![](external-authentication-services/_static/image22.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image21.png)
6. Une fois que vous entrez vos informations d’identification de Facebook, cliquez sur **connecter**, votre navigateur web sera redirigé vers votre application web, qui vous invite à entrer le **nom d’utilisateur** que vous souhaitez associer à votre Compte Facebook :

    [![](external-authentication-services/_static/image24.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image23.png)
7. Après avoir entré votre nom d’utilisateur et un clic sur le **inscrire** bouton, votre application web affichera la valeur par défaut **page d’accueil** pour votre compte Facebook :

    [![](external-authentication-services/_static/image26.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image25.png)

<a id="GOOGLE"></a>
## <a name="enabling-google-authentication"></a>Activation de l’authentification de Google

Google est de loin la plus simple des services d’authentification externe à activer, car il ne nécessite pas un compte de développeur, et aucune des informations supplémentaires telles que votre ID d’application ou d’une clé secrète que les autres services d’authentification externe nécessitent.

Pour activer l’authentification de Google pour votre application web, procédez comme suit :

1. Lorsque votre projet est ouvert dans Visual Studio 2013, ouvrez le *Startup.Auth.cs* fichier :

    [![](external-authentication-services/_static/image28.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image27.png)
2. Recherchez la section de code en surbrillance :

    [!code-csharp[Main](external-authentication-services/samples/sample4.cs)]
3. Supprimer le &quot; // &quot; caractères et ne commentez pas la ligne de code en surbrillance, puis recompiler votre projet :

    [!code-csharp[Main](external-authentication-services/samples/sample5.cs)]
4. Lorsque vous appuyez sur F5 pour ouvrir votre application web dans votre navigateur web, vous verrez que Google a été défini comme un service d’authentification externes :

    [![](external-authentication-services/_static/image30.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image29.png)
5. Lorsque vous cliquez sur le **Google** bouton, votre navigateur sera redirigé vers la page de connexion de Google :

    [![](external-authentication-services/_static/image32.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image31.png)
6. Une fois que vous entrez vos informations d’identification Google et cliquez sur **connecter**, Google vous invite à vérifier que votre application web dispose des autorisations pour accéder à votre compte Google :

    [![](external-authentication-services/_static/image34.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image33.png)
7. Lorsque vous cliquez sur **accepter**, votre navigateur web sera redirigé vers votre application web, qui vous invite à entrer le **nom d’utilisateur** que vous souhaitez associer à votre compte Google :

    [![](external-authentication-services/_static/image36.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image35.png)
8. Après avoir entré votre nom d’utilisateur et un clic sur le **inscrire** bouton, votre application web affichera la valeur par défaut **page d’accueil** pour votre compte Google :

    [![](external-authentication-services/_static/image38.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image37.png)

<a id="MICROSOFT"></a>
## <a name="enabling-microsoft-authentication"></a>Activer l’authentification Microsoft

Microsoft, vous devez créer un compte de développeur, et nécessite un ID client et la clé secrète du client afin de fonctionner. Pour plus d’informations sur la création d’un compte de développeur Microsoft et obtenir votre ID client et la clé secrète du client, consultez [https://go.microsoft.com/fwlink/?LinkID=144070](https://go.microsoft.com/fwlink/?LinkID=144070).

Une fois, vous avez obtenu votre clé de consommateur et de la question secrète du client, procédez comme suit pour activer l’authentification de Microsoft pour votre application web :

1. Lorsque votre projet est ouvert dans Visual Studio 2013, ouvrez le *Startup.Auth.cs* fichier :

    [![](external-authentication-services/_static/image40.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image39.png)
2. Recherchez la section de code en surbrillance :

    [!code-csharp[Main](external-authentication-services/samples/sample6.cs)]
3. Supprimer le &quot; // &quot; caractères pour ne pas commenter les lignes en surbrillance du code, puis ajoutez votre ID client et la clé secrète du client. Une fois que vous avez ajouté ces paramètres, vous pouvez recompiler votre projet :

    [!code-csharp[Main](external-authentication-services/samples/sample7.cs)]
4. Lorsque vous appuyez sur F5 pour ouvrir votre application web dans votre navigateur web, vous verrez que Microsoft a été défini comme un service d’authentification externes :

    [![](external-authentication-services/_static/image42.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image41.png)
5. Lorsque vous cliquez sur le **Microsoft** bouton, votre navigateur sera redirigé vers la page de connexion de Microsoft :

    [![](external-authentication-services/_static/image44.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image43.png)
6. Une fois que vous entrez vos informations d’identification de Microsoft, cliquez sur **connecter**, vous devrez vérifier que votre application web dispose des autorisations pour accéder à votre compte Microsoft :

    [![](external-authentication-services/_static/image46.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image45.png)
7. Lorsque vous cliquez sur **Oui**, votre navigateur web sera redirigé vers votre application web, qui vous invite à entrer le **nom d’utilisateur** que vous souhaitez associer à votre compte Microsoft :

    [![](external-authentication-services/_static/image48.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image47.png)
8. Après avoir entré votre nom d’utilisateur et un clic sur le **inscrire** bouton, votre application web affichera la valeur par défaut **page d’accueil** pour votre compte Microsoft :

    [![](external-authentication-services/_static/image50.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image49.png)

<a id="TWITTER"></a>
## <a name="enabling-twitter-authentication"></a>L’activation de l’authentification Twitter

Twitter, vous devez créer un compte de développeur, et nécessite une clé de consommateur et le secret de consommateur afin de fonctionner. Pour plus d’informations sur la création d’un compte de développeur Twitter et l’obtention de votre clé de consommateur et de la question secrète du client, consultez [https://go.microsoft.com/fwlink/?LinkID=252166](https://go.microsoft.com/fwlink/?LinkID=252166).

Une fois, vous avez obtenu votre clé de consommateur et de la question secrète du client, procédez comme suit pour activer l’authentification Twitter pour votre application web :

1. Lorsque votre projet est ouvert dans Visual Studio 2013, ouvrez le *Startup.Auth.cs* fichier :

    [![](external-authentication-services/_static/image52.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image51.png)
2. Recherchez la section de code en surbrillance :

    [!code-csharp[Main](external-authentication-services/samples/sample8.cs)]
3. Supprimer le &quot; // &quot; caractères pour ne pas commenter les lignes en surbrillance du code, puis ajoutez votre clé de consommateur et le secret de consommateur. Une fois que vous avez ajouté ces paramètres, vous pouvez recompiler votre projet :

    [!code-csharp[Main](external-authentication-services/samples/sample9.cs)]
4. Lorsque vous appuyez sur F5 pour ouvrir votre application web dans votre navigateur web, vous verrez que Twitter a été défini comme un service d’authentification externes :

    [![](external-authentication-services/_static/image54.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image53.png)
5. Lorsque vous cliquez sur le **Twitter** bouton, votre navigateur sera redirigé vers la page de connexion Twitter :

    [![](external-authentication-services/_static/image56.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image55.png)
6. Une fois que vous entrez vos informations d’identification Twitter, cliquez sur **Authorize application**, votre navigateur web sera redirigé vers votre application web, qui vous invite à entrer le **nom d’utilisateur** que vous souhaitez associer à votre compte Twitter :

    [![](external-authentication-services/_static/image58.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image57.png)
7. Après avoir entré votre nom d’utilisateur et un clic sur le **inscrire** bouton, votre application web affichera la valeur par défaut **page d’accueil** pour votre compte Twitter :

    [![](external-authentication-services/_static/image60.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image59.png)

<a id="MOREINFO"></a>
## <a name="additional-information"></a>Informations supplémentaires

Pour plus d’informations sur la création d’applications qui utilisent OAuth et OpenID, consultez les URL suivantes :

- [https://go.Microsoft.com/fwlink/?LinkId=252166](https://go.microsoft.com/fwlink/?LinkID=252166)
- [https://go.Microsoft.com/fwlink/?LinkId=243995](https://go.microsoft.com/fwlink/?LinkID=243995)

<a id="COMBINE"></a>
### <a name="combining-external-authentication-services"></a>Combinaison de Services d’authentification externe

Pour plus de souplesse, vous pouvez définir plusieurs services d’authentification externe en même temps - cela permet à votre site web des utilisateurs de l’application d’utiliser un compte à partir des services d’authentification externe activé :

[![](external-authentication-services/_static/image62.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image61.png)

<a id="FQDN"></a>
### <a name="configuring-iis-express-to-use-a-fully-qualified-domain-name"></a>Configuration d’IIS Express à utiliser un nom de domaine complet

Certains fournisseurs d’authentification externe ne gèrent pas le test de votre application à l’aide d’une adresse HTTP comme `http://localhost:port/`. Pour contourner ce problème, vous pouvez ajouter un mappage de nom de domaine complet (FQDN) statique à votre fichier HOSTS et configurer les options de votre projet dans Visual Studio 2013 d’utiliser le nom de domaine complet pour le test et de déboguer. Pour ce faire, procédez comme suit :

- Ajoutez un nom de domaine complet statique mappage de votre fichier HOSTS :

    1. Ouvrez une invite de commandes avec élévation de privilèges dans Windows.
    2. Tapez la commande suivante :

        <kbd>Notepad %WinDir%\system32\drivers\etc\hosts</kbd>
    3. Dans le fichier d’hôtes, ajoutez une entrée semblable à la suivante :

        <kbd>127.0.0.1 www.wingtiptoys.com</kbd>
    4. Enregistrez et fermez votre fichier HOSTS.
- Configurer votre projet Visual Studio pour utiliser le nom de domaine complet :

    1. Lorsque votre projet est ouvert dans Visual Studio 2013, cliquez sur le **projet** menu, puis sélectionnez les propriétés de votre projet. Par exemple, vous pouvez sélectionner **WebApplication1 propriétés**.
    2. Sélectionnez le **Web** onglet.
    3. Entrez votre nom de domaine complet pour le **Url de projet**. Par exemple, vous devez entrer <kbd>http://www.wingtiptoys.com</kbd> si c’était le mappage de nom de domaine complet que vous avez ajouté à votre fichier HOSTS.
- Configurer IIS Express pour utiliser le nom de domaine complet de votre application :

    1. Ouvrez une invite de commandes avec élévation de privilèges dans Windows.
    2. Tapez la commande suivante à votre dossier IIS Express :

        <kbd>CD /d &quot;%ProgramFiles%\IIS Express&quot;</kbd>
    3. Tapez la commande suivante pour ajouter le nom de domaine complet à votre application :

        <kbd>définir la configuration de appcmd.exe-section:system.applicationHost/sites / +&quot;[nom = 'WebApplication1'] .bindings. [ protocole = « http », bindingInformation ='* :80:www.wingtiptoys.com']&quot; /commit:apphost</kbd>

 Où **WebApplication1** est le nom de votre projet et **bindingInformation** contient le numéro de port et le nom de domaine complet que vous souhaitez utiliser pour votre test.

<a id="OBTAIN"></a>
### <a name="how-to-obtain-your-application-settings-for-microsoft-authentication"></a>Comment obtenir les paramètres de votre Application pour l’authentification Microsoft

Liaison d’une application Windows Live pour Microsoft Authentication est un processus simple. Si vous n’avez pas déjà lié une application Windows Live, vous pouvez utiliser les étapes suivantes :

1. Accédez à [https://go.microsoft.com/fwlink/?LinkID=144070](https://go.microsoft.com/fwlink/?LinkID=144070) et entrez votre nom de compte Microsoft et le mot de passe lorsque vous y êtes invité, puis cliquez sur **connectez-vous**:

    [![](external-authentication-services/_static/image64.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image63.png)
2. Entrez le nom et la langue de votre application lorsque vous y êtes invité, puis cliquez sur **J’accepte**:

    [![](external-authentication-services/_static/image66.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image65.png)
3. Sur le **paramètres de l’API** pour votre application, entrez le domaine de redirection pour votre application et la copie la **ID Client** et **question secrète du Client** pour votre projet, puis Cliquez sur **enregistrer**:

    [![](external-authentication-services/_static/image68.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image67.png)

<a id="DISABLE"></a>
### <a name="optional-disable-local-registration"></a>Facultatif : Désactiver l’inscription du Local

La fonctionnalité de l’inscription du local ASP.NET actuelle n’empêche pas les programmes automatiques (robots) à partir de la création de membres comptes ; par exemple, en utilisant une technologie de prévention des robots et la validation comme [CAPTCHA](../../../web-pages/overview/security/16-adding-security-and-membership.md). Pour cette raison, vous devez supprimer le lien de formulaire et d’enregistrement de connexion locale sur la page de connexion. Pour ce faire, ouvrez le  *\_Login.cshtml* page dans votre projet, puis commentez les lignes pour le panneau de connexion d’accès local et le lien d’inscription. La page résultante doit se présenter comme à l’exemple de code suivant :

[!code-html[Main](external-authentication-services/samples/sample10.html)]

Une fois que le panneau de connexion d’accès local et le lien d’inscription ont été désactivées, votre page de connexion affiche uniquement les fournisseurs d’authentification externe que vous avez activés :

[![](external-authentication-services/_static/image70.png "Cliquez pour développer l’Image")](external-authentication-services/_static/image69.png)
