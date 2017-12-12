---
uid: visual-studio/overview/2012/windows-azure-authentication
title: Windows Azure Authentication | Documents Microsoft
author: Rick-Anderson
description: "Outils Microsoft ASP.NET pour Windows Azure Active Directory facilite activer l’authentification pour les applications web hébergées sur les Sites Web Windows Azure..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2013
ms.topic: article
ms.assetid: a3cef801-a54b-4ebd-93c3-55764e2e14b1
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /visual-studio/overview/2012/windows-azure-authentication
msc.type: authoredcontent
ms.openlocfilehash: 1cb38d66bd0373159e54abf822fba9c5829774ac
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="windows-azure-authentication"></a>Windows Azure Authentication
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Des outils Microsoft ASP.NET pour Windows Azure Active Directory facilite l’activer l’authentification pour les applications web hébergées sur [Sites Web Windows Azure](https://www.windowsazure.com/en-us/home/features/web-sites/). Vous pouvez utiliser l’authentification Windows Azure pour authentifier les utilisateurs d’Office 365 à partir de votre organisation, les comptes d’entreprise synchronisés à partir de votre annuaire Active Directory sur site ou les utilisateurs créés dans votre domaine personnalisé de Windows Azure Active Directory. Activer l’authentification de Windows Azure configure votre application pour authentifier les utilisateurs à l’aide d’un seul [Windows Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) client.
> 
> L’outil ASP.NET Windows Azure Authentication n’est pas prise en charge pour les rôles web dans un service cloud, mais nous prévoyons de le faire dans une version ultérieure. [Windows Identity Foundation](https://msdn.microsoft.com/en-us/library/hh291066(v=VS.110).aspx) (WIF) est pris en charge dans les rôles web Windows Azure.
> 
> Pour plus d’informations sur la façon de configurer la synchronisation entre votre annuaire Active Directory local et votre locataire Windows Azure Active Directory consultez [utiliser AD FS 2.0 pour implémenter et gérer l’authentification unique](https://technet.microsoft.com/en-us/library/jj205462.aspx).
> 
> Windows Azure Active Directory n’est actuellement disponible en tant qu’un [aperçu service gratuit](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).


## <a name="requirements"></a>Configuration requise :

- Visual Studio 2012 ou [Visual Studio Express 2012](https://www.microsoft.com/visualstudio/11/en-us/products/express)
- [Extensions pour Visual Studio 2012 d’outils Web](https://go.microsoft.com/fwlink/?LinkID=282228&amp;clcid=0x409) ou [Extensions des outils Web pour Visual Studio Express 2012](https://go.microsoft.com/fwlink/?LinkID=282231&amp;clcid=0x409)
- [Microsoft ASP.NET Tools pour Windows Azure Active Directory – Visual Studio 2012](https://go.microsoft.com/fwlink/?LinkID=282306) ou [des outils Microsoft ASP.NET pour Windows Azure Active Directory – Visual Studio Express 2012 pour le Web](https://go.microsoft.com/fwlink/?LinkId=282652)

## <a name="create-an-aspnet-web-application-with-visual-studio-2012"></a>Créer une Application Web ASP.NET avec Visual Studio 2012

Vous pouvez créer une Application Web avec Visual Studio 2012, ce didacticiel utilise le modèle d’intranet ASP.NET MVC.

1. Créer une nouvelle Application Intranet ASP.NET MVC 4 et acceptez les valeurs par défaut. (Il doit être un **tra** net et non dans **RET** projet net).  
     ![](windows-azure-authentication/_static/image1.png)

## <a name="enable-window-azure-authentication-when-you-are-a-global-administrator-of-the-tenet"></a>Activer l’authentification de Windows Azure (quand vous êtes un administrateur Global de la doctrine)

Si vous ne disposez pas d’un locataire Windows Azure Active Directory existant (par exemple, via un compte Office 365 existant), vous pouvez créer un nouveau client en vous connectant à un [nouveau compte Windows Azure Active Directory](http://g.microsoftonline.com/0AX00en/5).

1. Dans le menu projet, sélectionnez **activer l’authentification Windows Azure**:  
  
 ![](windows-azure-authentication/_static/image2.png)

2. Entrez le domaine de votre locataire Windows Azure Active Directory (par exemple, contoso.onmicrosoft.com) et cliquez sur **activer**:

![](windows-azure-authentication/_static/image3.png)

3. Dans l’authentification Web boîte de dialogue Ouvrir une session en tant qu’administrateur pour votre locataire Windows Azure Active Directory :  
  
 ![](windows-azure-authentication/_static/image4.png)

![](windows-azure-authentication/_static/image5.png)

## <a name="enable-window-azure-by-a-non-administrator-of-the-tenet"></a>Activer Windows Azure par un utilisateur non administrateur du principe

Si vous n’avez pas de privilèges d’administrateur Global pour votre locataire Windows Azure Active Directory, vous pouvez décocher la case à cocher pour la configuration de l’application.

![](windows-azure-authentication/_static/image6.png)

La boîte de dialogue affiche le **domaine**, **Id du Principal de l’Application** et **URL de réponse** qui sont requis pour la configuration de l’application avec Azure Active Directory principe. Vous devez donner à ces informations à une personne qui dispose des privilèges suffisants pour configurer l’application. Consultez[comment implémenter l’authentification unique avec Windows Azure Active Directory - Application ASP.NET](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) pour plus d’informations sur la façon d’utiliser l’applet de commande pour créer le principal du service manuellement.  
Une fois que l’application a été correctement configurée, vous pouvez cliquer sur **continuer mettre à jour le fichier web.config avec les paramètres sélectionnés**. Si vous souhaitez continuer à développer l’application lors de l’attente pour l’approvisionnement se produisent, vous pouvez cliquer sur **fermer pour mémoriser les paramètres dans le fichier projet**. La prochaine fois que vous appeler activer l’authentification Windows Azure et que vous désactivez la case à cocher de configuration, vous voyez les mêmes paramètres et vous pouvez cliquer sur **continuer**, puis cliquez sur, **appliquer ces paramètres dans le fichier web.config**.

1. Patienter pendant que votre application est configurée pour l’authentification Windows Azure et dotée de Windows Azure Active Directory.
2. Une fois l’authentification Windows Azure a été activée pour votre application, cliquez sur **fermer :** 

    ![](windows-azure-authentication/_static/image7.png)
3. Appuyez sur F5 pour exécuter votre application. Il est possible que vous devez obtenir automatiquement redirigé vers la page de connexion. Utilisez les informations d’identification de répertoire doctrine utilisateur à se connecter à l’application...  

    ![](windows-azure-authentication/_static/image1.jpg)
4. Étant donné que votre application utilise actuellement un certificat auto-signé de test vous recevez un avertissement à partir du navigateur le certificat n’a pas été émis par une autorité de certification approuvée.

    Cet avertissement peut être ignoré en toute sécurité pendant le développement local en cliquant sur **poursuivre sur ce site Web :** 

    ![](windows-azure-authentication/_static/image8.png)
5. Vous avez maintenant est connecté à votre application à l’aide de l’authentification Windows Azure !

    ![](windows-azure-authentication/_static/image2.jpg)

L’activation de Windows Azure authentication apporte les modifications suivantes à votre application :

- Une falsification de requête Anti-site à Site ([CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))) classe ( *application\_Start\AntiXsrfConfig.cs* ) est ajouté à votre projet.
- Les packages NuGet `System.IdentityModel.Tokens.ValidatingIssuerNameRegistry` est ajouté à votre projet.
- Paramètres de Windows Identity Foundation dans votre application seront configurés pour accepter les jetons de sécurité de votre locataire Windows Azure Active Directory. Cliquez sur l’image ci-dessous pour afficher une vue développée des modifications apportées à la *Web.config* fichier.  
  
     ![](windows-azure-authentication/_static/image9.png)
- Un principal de service pour votre application dans votre locataire Windows Azure Active Directory peut être configuré.
- HTTPS est activée.

## <a name="deploy-the-application-to-windows-azure"></a>Déployer l’application sur Windows Azure

Pour plus d’informations, consultez [déploiement d’Application Web ASP.NET sur un Site Web de Windows Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

Pour publier une application à l’aide de l’authentification Windows Azure à un Site Web de Azure :

1. Cliquez avec le bouton droit sur votre application et sélectionnez **publier :** 

    ![](windows-azure-authentication/_static/image3.jpg)
2. À partir de la boîte de dialogue Publier le site Web télécharger et importer un profil de publication pour votre Site Web de Azure.

    ![](windows-azure-authentication/_static/image4.jpg)
3. Le **connexion** onglet montre le **URL de Destination** (l’URL exposés au public de votre application). Cliquez sur **valider la connexion** pour tester votre connexion :

    ![](windows-azure-authentication/_static/image5.jpg)
4. Si vous avez publié sur ce Site Web de Azure précédemment pensez à vérifier le **supprimer les fichiers supplémentaires à la destination** paramètre pour garantir votre application publie correctement. Notez le **activer l’authentification Windows Azure** case à cocher est sélectionné.  

    ![](windows-azure-authentication/_static/image10.png)
5. Facultatif : Dans le **aperçu** onglet, cliquez sur **démarrer la version préliminaire** pour voir les fichiers déployés.

    ![](windows-azure-authentication/_static/image6.jpg)
6. Cliquez sur **publier.**

    Vous devrez activer l’authentification Windows Azure pour l’hôte cible. Cliquez sur **activer** pour continuer :

    ![](windows-azure-authentication/_static/image11.png)
7. Entrez vos informations d’identification d’administrateur pour votre locataire Windows Azure Active Directory :

    ![](windows-azure-authentication/_static/image7.jpg)
8. Une fois que votre application a été publiée avec succès, un navigateur s’ouvre sur le site web publié.

    > [!NOTE]
    > Il peut prendre jusqu'à cinq minutes (généralement doit moins) pour votre application soit entièrement configuré avec Windows Azure Active Directory après l’activation de l’authentification Windows Azure pour l’hôte cible. Lorsque vous exécutez tout d’abord votre application si vous recevez l’erreur ACS50001 : partie de confiance avec le nom '[domaine]' n’a pas été trouvé, patientez quelques minutes, puis essayez à nouveau l’application en cours d’exécution.
9. Lorsque vous y êtes invité, connectez-vous en tant qu’utilisateur dans votre annuaire :

    ![](windows-azure-authentication/_static/image8.jpg)
10. Vous êtes maintenant correctement connecté à votre Azure hébergé l’application à l’aide de l’authentification Windows Azure.  
  
     ![](windows-azure-authentication/_static/image9.jpg)

## <a name="known-issues"></a>Problèmes connus

#### <a name="role-based-authorization-fails-when-using-windows-azure-authenticationopop"></a>Autorisation basée sur le rôle échoue lors de l’utilisation de l’authentification Windows Azure < o : p >< / o : p >

L’authentification Windows Azure ne fournit pas actuellement de la revendication de rôle nécessaires afin que l’autorisation par rôle peut être effectuée. Le rôle de l’utilisateur authentifié doit être récupéré manuellement à partir de Windows Azure Active Directory. < o : p >< / o : p >

#### <a name="browsing-to-an-application-with-windows-azure-authentication-results-in-the-error-acs20016-the-domain-of-the-logged-in-user-livecom-does-not-match-any-allowed-domain-of-this-stsopop"></a>Navigation à une application avec des résultats de l’authentification Windows Azure dans l’erreur « ACS20016 le domaine de l’utilisateur connecté (live.com) ne correspond pas à toute autorisé domaine de ce STS » < o : p >< / o : p >

Si vous êtes déjà connecté à un Account Microsoft (par exemple hotmail.com, live.com, outlook.com) et que vous essayez d’accéder à une application qui a activé l’authentification Windows Azure vous pouvez obtenir une réponse de 400 erreur, car le domaine de votre Account Microsoft n’est pas reconnu par Windows Azure Active Directory. Pour vous connecter à l’application, fermez la session à partir de votre Account Microsoft tout d’abord. < o : p >< / o : p >

#### <a name="logging-into-an-application-with-windows-azure-authentication-enabled-and-a-x509certificatevalidationmode-other-than-none-results-in-certificate-validation-errors-for-the-accountsaccesscontrolwindowsnet-certificateopop"></a>Journalisation dans une application avec l’authentification Windows Azure est activée et un X509CertificateValidationMode autre que None entraîne des erreurs de validation de certificat pour le certificat accounts.accesscontrol.windows.net < o : p >< / o : p >

Validation de certificat n’est pas obligatoire et doit être laissée désactivé. L’empreinte numérique du certificat de l’émetteur est validé par le WSFederationAuthenticationModule. < o : p >< / o : p >

#### <a name="when-attempting-to-enable-windows-azure-authentication-the-web-authentication-dialog-shows-the-error-acs20016-the-domain-of-the-logged-in-user-contosoonmicrosoftcom-does-not-match-any-allowed-domain-of-this-stsopop"></a>Lorsque vous tentez d’activer l’authentification de Windows Azure la boîte de dialogue d’authentification Web affiche l’erreur « ACS20016 : le domaine de l’utilisateur connecté (contoso.onmicrosoft.com) ne correspond pas à aucun domaine autorisé de ce STS. » < o : p >< / o : p >

Vous pouvez voir cette erreur lorsque vous avez précédemment connecté à l’aide d’un compte Windows Azure Active Directory différent de dans le même processus Visual Studio. Fermez la session du compte spécifié ou redémarrez Visual Studio. Si vous précédemment connecté et sélectionniez l’option « Maintenir la connexion », vous devrez peut-être effacer les cookies du navigateur. < o : p >< / o : p >

## <a name="acs20012-the-request-is-not-a-valid-ws-federation-protocol-message-opop"></a>ACS20012 : La requête n’est pas un message de protocole WS-Federation valid < o : p >< / o : p >

Cela peut se produire si vous êtes déjà connecté avec certains autres ID Microsoft à un des services Azure. Fenêtre de navigateur d’utilisation privée telles que la navigation InPrivate dans Internet Explorer ou Incognito dans Chrome ou effacer tous les cookies. < o : p >< / o : p >

## <a name="additional-resources"></a>Ressources supplémentaires

- [Microsoft ASP.NET Tools pour Windows Azure Active Directory – Visual Studio 2012](https://blogs.msdn.com/b/vbertocci/archive/2013/02/18/microsoft-asp-net-tools-for-windows-azure-active-directory-visual-studio-2012.aspx) – Vittorio Bertocci
- [Windows Azure fonctionnalités : identité](https://docs.microsoft.com/azure/active-directory/)
- [TechNet : Windows Azure Active Directory](https://technet.microsoft.com/en-us/library/hh967619.aspx)
- [Windows Azure Active Directory : développer des applications pour votre organisation](https://activedirectory.windowsazure.com/Develop/Single-Tenant.aspx)
- [Windows Azure Active Directory : développer des applications pour plusieurs organisations](https://activedirectory.windowsazure.com/Develop/Multi-Tenant.aspx)
- [Comment implémenter l’authentification unique avec Windows Azure Active Directory](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect)
- [L’authentification unique avec Windows Azure Active Directory : présentation approfondie](https://blogs.msdn.com/b/vbertocci/archive/2012/07/05/single-sign-on-with-windows-azure-active-directory-a-deep-dive.aspx) – Vittorio Bertocci
- [Utiliser les services AD FS 2.0 pour implémenter et gérer l’authentification unique](https://technet.microsoft.com/en-us/library/jj205462.aspx)
