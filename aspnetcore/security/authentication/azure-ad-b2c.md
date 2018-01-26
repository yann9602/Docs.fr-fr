---
title: Authentification cloud avec Azure Active Directory B2C
author: camsoper
description: "Découvrez comment configurer l’authentification d’Azure Active Directory B2C avec ASP.NET Core."
ms.author: casoper
manager: wpickett
ms.date: 01/12/2018
ms.topic: tutorial
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/azure-ad-b2c
custom: mvc
ms.openlocfilehash: 5c4716022c61e33b0301fa0077f911dcc4b3628c
ms.sourcegitcommit: 459cb3289741a3f46325e605a617dc926ee0563d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="cloud-authentication-with-azure-active-directory-b2c"></a>Authentification cloud avec Azure Active Directory B2C

Auteur : [Cam Soper](https://twitter.com/camsoper)

[Azure B2C Active Directory](/azure/active-directory-b2c/active-directory-b2c-overview) (B2C Active Directory de Azure) est une solution de gestion des identités de cloud pour vos applications web et mobiles. Le service fournit l’authentification pour les applications hébergées dans le cloud et locales. Types d’authentification incluent les comptes individuels, les comptes de réseau social et fédéré de comptes d’entreprise.  En outre, Azure AD B2C peut fournir l’authentification multifacteur avec une configuration minimale.

Dans ce didacticiel, vous devez savoir comment :

> [!div class="checklist"]
> * Créer un client Azure Active Directory B2C
> * Inscrire une application dans Azure AD B2C
> * Utiliser Visual Studio pour créer une application de web ASP.NET Core configurée pour utiliser le locataire Azure AD B2C pour l’authentification
> * Configurer des stratégies de contrôle du comportement du client Azure AD B2C

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour cette procédure pas à pas :

* [Abonnement Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
* [Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (toute édition)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Créer le client Azure Active Directory B2C

Créer un client Azure Active Directory B2C [comme décrit dans la documentation](/azure/active-directory-b2c/active-directory-b2c-get-started). Lorsque vous y êtes invité, associant le locataire d’un abonnement Azure est facultatif pour ce didacticiel.

## <a name="register-the-app-in-azure-ad-b2c"></a>Inscrire l’application dans Azure AD B2C

Dans le locataire Azure AD B2C nouvellement créé, inscrire votre application à l’aide de [les étapes décrites dans la documentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) sous le **inscrire une application web** section. Arrêter à la **créer une question secrète du client web application** section. Une clé secrète client n’est pas nécessaire pour ce didacticiel. 

Utilisez les valeurs suivantes :

| Paramètre                       | Value                     | Notes                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                      | *\<nom de l’application\>*            | Entrez un **nom** pour l’application qui décrivent votre application aux consommateurs.                                                                                                                                 |
| **Inclure l’application web ou des API** | Oui                       |                                                                                                                                                                                                    |
| **Autoriser les flux implicites**       | Oui                       |                                                                                                                                                                                                    |
| **URL de réponse**                 | `https://localhost:44300` | URL de réponse sont les points de terminaison où Azure AD B2C retourne tout jeton de demande de votre application. Visual Studio fournit l’URL de réponse à utiliser. Pour l’instant, entrez `https://localhost:44300` pour remplir le formulaire. |
| **URI ID d’application**                | Laissez vide               | Non requis pour ce didacticiel.                                                                                                                                                                    |
| **Inclure le client natif**     | Non                        |                                                                                                                                                                                                    |

> [!WARNING]
> Si la configuration d’une URL de réponse non-localhost, vous devez connaître le [contraintes sur ce qui est autorisé dans la liste des URL de réponse](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url). 

Une fois que l’application est enregistrée, la liste des applications dans le client s’affiche. Sélectionnez l’application qui a été enregistrée. Sélectionnez le **copie** icône à droite de la **ID d’Application** champ pour copier l’ID d’Application dans le Presse-papiers.

Rien de plus d’informations peuvent être configurés dans le locataire Azure AD B2C à ce stade, mais laissez la fenêtre de navigateur ouverte. Il existe davantage de configuration après la création de l’application ASP.NET Core.

## <a name="create-an-aspnet-core-app-in-visual-studio-2017"></a>Créer une application ASP.NET Core dans Visual Studio 2017

Le modèle d’Application Web de Visual Studio peut être configuré pour utiliser le locataire Azure AD B2C pour l’authentification.

Dans Visual Studio :

1. Créez une application web ASP.NET Core. 
2. Sélectionnez **Application Web** à partir de la liste des modèles.
3. Sélectionnez le **modifier l’authentification** bouton.
    
    ![Bouton de modifier l’authentification](./azure-ad-b2c/_static/changeauth.png)

4. Dans le **modifier l’authentification** boîte de dialogue, sélectionnez **comptes d’utilisateur individuels**, puis sélectionnez **se connecter à un magasin d’utilisateur existant dans le cloud** dans la liste déroulante. 
    
    ![Boîte de dialogue Modifier l’authentification](./azure-ad-b2c/_static/changeauthdialog.png)

5. Remplissez le formulaire avec les valeurs suivantes :
    
    | Paramètre                       | Value                                             |
    |-------------------------------|---------------------------------------------------|
    | **Nom de domaine**               | *\<le nom de domaine de votre client B2C\>*          |
    | **ID d’application**            | *\<Collez l’ID d’Application à partir du Presse-papiers\>* |
    | **Chemin d’accès de rappel**             | *\<la valeur par défaut\>*                       |
    | **Stratégie d’inscription ou de la connexion** | `B2C_1_SiUpIn`                                    |
    | **Stratégie de réinitialisation du mot de passe**     | `B2C_1_SSPR`                                      |
    | **Modifier la stratégie de profil**       | *\<Laissez vide\>*                                 |

    Sélectionnez le **copie** situé en regard **réponse URI** pour copier l’URI de réponse dans le Presse-papiers. Sélectionnez **OK** pour fermer la **modifier l’authentification** boîte de dialogue. Sélectionnez **OK** pour créer l’application web.

## <a name="finish-the-b2c-app-registration"></a>Terminer l’inscription d’une application B2C

Revenir à la fenêtre de navigateur avec les propriétés de l’application B2C toujours ouvertes. Modifier la fichier temporaire **URL de réponse** spécifié précédemment pour la valeur copiée à partir de Visual Studio. Sélectionnez **enregistrer** en haut de la fenêtre.

> [!TIP]
> Si vous n’avez pas de copier l’URL de réponse, utilisez l’adresse SSL à partir de l’onglet débogage dans les propriétés du projet web et ajouter la **CallbackPath** valeur *appsettings.json*.

## <a name="configure-policies"></a>Configurer des stratégies

Utilisez les étapes de la documentation d’Azure AD B2C à [créer une stratégie d’inscription ou connectez-vous](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy), puis [créer une stratégie de réinitialisation de mot de passe](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy). Utilisez les valeurs de l’exemple fournis dans la documentation de **fournisseurs d’identité**, **des attributs d’abonnement**, et **revendications de l’Application**. À l’aide de la **exécuter maintenant** pour tester les stratégies, comme décrit dans la documentation est facultative.

> [!WARNING]
> Vérifiez les noms de stratégie sont exactement comme décrit dans la documentation, ces stratégies ont été utilisés dans le **modifier l’authentification** boîte de dialogue dans Visual Studio. Les noms de stratégie peuvent être vérifiés dans *appsettings.json*.

## <a name="run-the-app"></a>Exécuter l'application

Dans Visual Studio, appuyez sur **F5** pour générer et exécuter l’application. Après le lancement de l’application web, sélectionnez **connectez-vous**.

![Connectez-vous à l’application](./azure-ad-b2c/_static/signin.png)

Le navigateur redirige vers le locataire Azure AD B2C. Connectez-vous avec un compte existant (si un a été créé les stratégies de test) ou sélectionnez **s’inscrire maintenant** pour créer un nouveau compte. Le **votre mot de passe oublié ?** lien est utilisé pour réinitialiser un mot de passe oublié.

![Connexion de Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

Une fois connecté avec succès, le navigateur effectue une redirection vers l’application web.

![Opération réussie](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous sera appris comment :

> [!div class="checklist"]
> * Créer un client Azure Active Directory B2C
> * Inscrire une application dans Azure AD B2C
> * Utiliser Visual Studio pour créer une Application de Web ASP.NET Core configuré pour utiliser le locataire Azure AD B2C pour l’authentification
> * Configurer des stratégies de contrôle du comportement du client Azure AD B2C

Maintenant que l’application ASP.NET Core est configurée pour utiliser Azure AD B2C pour l’authentification, le [attribut Authorize](xref:security/authorization/simple) peut être utilisé pour sécuriser votre application. Continuer à développer votre application par l’apprentissage à :

* [Personnaliser l’interface utilisateur de Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Configurer les exigences de complexité de mot de passe](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Activer l’authentification multifacteur](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Configurer les fournisseurs d’identité supplémentaires, telles que [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)et d’autres.
* [Utiliser l’API Azure AD Graph](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) pour récupérer des informations supplémentaires, telles que l’appartenance au groupe, du locataire Azure AD B2C.