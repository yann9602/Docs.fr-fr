---
title: "Authentification de cloud de site web d’API avec Azure Active Directory B2C"
author: camsoper
description: "Découvrez comment configurer l’authentification d’Azure Active Directory B2C avec l’API Web ASP.NET principale. Tester l’API avec Postman de web authentifié."
ms.author: casoper
manager: wpickett
ms.date: 01/25/2018
ms.topic: tutorial
ms.technology: aspnet
ms.prod: asp.net-core
ms.custom: mvc
uid: security/authentication/azure-ad-b2c-webapi
ms.openlocfilehash: a63bfc26bb6b0f5ea1c64641d6f57a3555d7f401
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="cloud-authentication-in-web-apis-with-azure-active-directory-b2c"></a>Authentification de cloud de site web d’API avec Azure Active Directory B2C

Auteur : [Cam Soper](https://twitter.com/camsoper)

[Azure B2C Active Directory](/azure/active-directory-b2c/active-directory-b2c-overview) (B2C Active Directory de Azure) est une solution de gestion des identités de cloud pour les applications web et mobiles. Le service fournit l’authentification pour les applications hébergées dans le cloud et locales. Types d’authentification incluent les comptes individuels, les comptes de réseau social et fédéré de comptes d’entreprise. En outre, Azure AD B2C peut fournir l’authentification multifacteur avec une configuration minimale.

> [!TIP]
> Azure Active Directory (Azure AD) Azure Active Directory B2C sont des offres de produits distincts. Un locataire Azure AD représente une organisation, alors qu’un locataire Azure AD B2C représente une collection d’identités à utiliser avec les applications de confiance. Pour plus d’informations, consultez [Azure AD B2C : Forum aux questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

Étant donné que l’API web ont aucune interface utilisateur, ils ne peuvent pas rediriger l’utilisateur vers un service de jeton sécurisé comme Azure AD B2C. Au lieu de cela, l’API est passé un jeton de support à partir de l’application appelante qui a déjà authentifié l’utilisateur avec Azure Active Directory B2C. L’API puis valide le jeton sans intervention de l’utilisateur directe.

Dans ce didacticiel, vous devez savoir comment :

> [!div class="checklist"]
> * Créer un locataire Azure Active Directory B2C.
> * Enregistrer une API Web dans Azure AD B2C.
> * Utiliser Visual Studio pour créer une API Web configuré pour utiliser le locataire Azure AD B2C pour l’authentification.
> * Configurer des stratégies de contrôle du comportement du client Azure Active Directory B2C.
> * Postman utilisé pour simuler une application web qui présente une boîte de dialogue de connexion, récupère un jeton et l’utilise pour effectuer une demande dans l’API web.

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour cette procédure pas à pas :

* [Abonnement Microsoft Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (toute édition)
* [Postman](https://www.getpostman.com/postman)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Créer le client Azure Active Directory B2C

Créer un client Azure AD B2C [comme décrit dans la documentation](/azure/active-directory-b2c/active-directory-b2c-get-started). Lorsque vous y êtes invité, associant le locataire d’un abonnement Azure est facultatif pour ce didacticiel.

## <a name="configure-a-sign-up-or-sign-in-policy"></a>Configurer une stratégie d’inscription ou de la connexion

Utilisez les étapes de la documentation d’Azure AD B2C à [créer une stratégie d’inscription ou connectez-vous](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy). Nom de la stratégie **SiUpIn**.  Utilisez les valeurs de l’exemple fournis dans la documentation de **fournisseurs d’identité**, **des attributs d’abonnement**, et **revendications de l’Application**. À l’aide de la **exécuter maintenant** bouton pour tester la stratégie, comme décrit dans la documentation est facultative.

## <a name="register-the-api-in-azure-ad-b2c"></a>Registre de l’API dans Azure AD B2C

Dans le locataire Azure AD B2C nouvellement créé, inscrire votre à l’aide de l’API [les étapes décrites dans la documentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-api) sous le **enregistrer une API web** section.

Utilisez les valeurs suivantes :

| Paramètre                       | Value               | Notes                                                                                  |
|-------------------------------|---------------------|----------------------------------------------------------------------------------------|
| **Name**                      | *&lt;Nom de l’API&gt;*  | Entrez un **nom** pour l’application qui décrivent votre application aux consommateurs.                     |
| **Inclure l’application web ou des API** | Oui                 |                                                                                        |
| **Autoriser les flux implicites**       | Oui                 |                                                                                        |
| **URL de réponse**                 | `https://localhost` | URL de réponse sont les points de terminaison où Azure AD B2C retourne tout jeton de demande de votre application. |
| **URI ID d’application**                | *api*               | L’URI n’a pas besoin de correspondre à une adresse physique. Il doit être unique.     |
| **Inclure le client natif**     | Non                  |                                                                                        |

Une fois que l’API est inscrit, la liste des applications et des API dans le client s’affiche. Sélectionnez l’API a été enregistré. Sélectionnez le **copie** icône à droite de la **ID d’Application** champ pour le copier dans le Presse-papiers. Sélectionnez **publié étendues** et vérifiez la valeur par défaut *user_impersonation* étendue est présente.

## <a name="create-an-aspnet-core-app-in-visual-studio-2017"></a>Créer une application ASP.NET Core dans Visual Studio 2017

Le modèle d’Application Web de Visual Studio peut être configuré pour utiliser le locataire Azure AD B2C pour l’authentification.

Dans Visual Studio :

1. Créez une application web ASP.NET Core. 
2. Sélectionnez **API Web** à partir de la liste des modèles.
3. Sélectionnez le **modifier l’authentification** bouton.
    
    ![Bouton de modifier l’authentification](./azure-ad-b2c-webapi/change-auth-button.png)

4. Dans le **modifier l’authentification** boîte de dialogue, sélectionnez **comptes d’utilisateur individuels**, puis sélectionnez **se connecter à un magasin d’utilisateur existant dans le cloud** dans la liste déroulante. 
    
    ![Boîte de dialogue Modifier l’authentification](./azure-ad-b2c-webapi/change-auth-dialog.png)

5. Remplissez le formulaire avec les valeurs suivantes :
    
    | Paramètre                       | Value                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Nom de domaine**               | *&lt;le nom de domaine de votre client B2C&gt;*          |
    | **ID d’application**            | *&lt;Collez l’ID d’Application à partir du Presse-papiers&gt;* |
    | **Stratégie d’inscription ou de la connexion** | `B2C_1_SiUpIn`                                        |
    
    Sélectionnez **OK** pour fermer la **modifier l’authentification** boîte de dialogue. Sélectionnez **OK** pour créer l’application web.

Visual Studio crée l’API web avec un contrôleur nommé *le fichier ValuesController.cs* qui retourne des valeurs codées en dur pour les demandes GET. La classe est décorée avec le [attribut Authorize](xref:security/authorization/simple), de sorte que toutes les demandes nécessitent l’authentification.

## <a name="run-the-web-api"></a>Exécution de l’API web

Dans Visual Studio, exécutez l’API. Visual Studio lance un navigateur pointé URL racine de l’API. Notez l’URL dans la barre d’adresses et laissez l’API en cours d’exécution en arrière-plan.

> [!NOTE]
> Comme il n’existe aucun contrôleur défini pour l’URL racine, le navigateur affiche alors une erreur 404 (page introuvable). Ce comportement est normal.

## <a name="use-postman-to-get-a-token-and-test-the-api"></a>Postman permet d’obtenir un jeton et l’API de test

[Postman](https://getpostman.com/postman) est un outil de test web API. Pour ce didacticiel, Postman simule une application web qui accède à l’API web part de l’utilisateur.

### <a name="register-postman-as-a-web-app"></a>Inscrire Postman comme une application web

Étant donné que Postman simule une application web qui peut obtenir des jetons provenant du locataire Azure AD B2C, elle doit être inscrite dans le client comme une application web. Enregistrer à l’aide de Postman [les étapes décrites dans la documentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) sous le **inscrire une application web** section. Arrêter à la **créer une question secrète du client web application** section. Une clé secrète client n’est pas nécessaire pour ce didacticiel. 

Utilisez les valeurs suivantes :

| Paramètre                       | Value                            | Notes                           |
|-------------------------------|----------------------------------|---------------------------------|
| **Name**                      | Postman                          |                                 |
| **Inclure l’application web ou des API** | Oui                              |                                 |
| **Autoriser les flux implicites**       | Oui                              |                                 |
| **URL de réponse**                 | `https://getpostman.com/postman` |                                 |
| **URI ID d’application**                | *&lt;Laissez vide&gt;*            | Non requis pour ce didacticiel. |
| **Inclure le client natif**     | Non                               |                                 |

L’application web qui vient d’être inscrit nécessite une autorisation pour accéder à l’API web part de l’utilisateur.  

1. Sélectionnez **Postman** dans la liste des applications, puis sélectionnez **l’accès aux API** à partir du menu de gauche.
2. Sélectionnez **+ ajouter**.
3. Dans le **sélectionner une API** liste déroulante, sélectionnez le nom de l’API web.
4. Dans le **étendues** liste déroulante, assurez-vous que toutes les étendues sont sélectionnées.
5. Sélectionnez **Ok**.

Notez l’ID d’Application de l’application Postman, est nécessaire pour obtenir un jeton de support.

### <a name="create-a-postman-request"></a>Créer une demande de Postman

Lancez Postman. Par défaut, Postman affiche le **créer un nouveau** boîte de dialogue après le lancement. Si la boîte de dialogue n’est pas affichée, sélectionnez le **+ nouveau** bouton dans le coin supérieur gauche.

À partir de la **créer un nouveau** boîte de dialogue :

1. Sélectionnez **demande**.
    
    ![Bouton de demande](./azure-ad-b2c-webapi/postman-create-new.png)

2. Entrez *obtenir les valeurs* dans les **nom de la demande** boîte.
3. Sélectionnez **+ créer une Collection de** pour créer une nouvelle collection pour stocker la demande. Nom de la collection *les didacticiels ASP.NET Core* , puis sélectionnez la coche.
    
    ![Création d’un regroupement](./azure-ad-b2c-webapi/postman-create-collection.png)

4. Sélectionnez le **enregistrer dans les didacticiels ASP.NET Core** bouton.

### <a name="test-the-web-api-withoutauthentication"></a>Tester l’API web withoutauthentication

Pour vérifier que l’API web nécessite une authentification, tout d’abord effectuer une demande sans authentification.

1. Dans le **Entrez l’URL de demande** , entrez l’URL pour `ValuesController`. L’URL est identique à celle affichée dans le navigateur avec **api/valeurs** ajouté. Un exemple serait `https://localhost:44375/api/values`.
2. Sélectionnez le **envoyer** bouton.
3. Notez l’état de la réponse est *401 non autorisé*.

    ![réponse non autorisé 401](./azure-ad-b2c-webapi/postman-401-status.png)

### <a name="obtain-a-bearer-token"></a>Obtenir un jeton de support

Pour rendre une demande authentifiée à l’API web, un jeton de support est nécessaire. Postman permet de facilement se connecter au locataire Azure AD B2C et obtenir un jeton.

1. Sur le **autorisation** sous l’onglet du **TYPE** liste déroulante, sélectionnez **OAuth 2.0**. Dans le **ajouter des données d’autorisation** liste déroulante, sélectionnez **en-têtes de la requête**. Sélectionnez **accéder nouveau jeton**.
    
    ![Onglet autorisation avec des paramètres](./azure-ad-b2c-webapi/postman-auth-tab.png)

2. Terminer la **obtenir nouveau jeton d’accès** boîte de dialogue comme suit :
    
    | Paramètre                   | Value                                                                                         | Notes                                                                                      |
    |---------------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
    | **Nom du jeton**            | *&lt;nom du jeton&gt;*                                                                          | Entrez un nom descriptif pour le jeton.                                                    |
    | **Type d’accès**            | Implicite                                                                                      |                                                                                            |
    | **URL de rappel**          | `https://getpostman.com/postman`                                                              |                                                                                            |
    | **URL d’authentification**              | `https://login.microsoftonline.com/<tenant domain name>/oauth2/v2.0/authorize?p=B2C_1_SiUpIn` | Remplacez  *&lt;nom_domaine_client&gt;*  avec le nom de domaine du locataire sans crochets pointus. |
    | **ID de client**             | *&lt;Entrez l’application Postman <b>ID d’Application</b>&gt;*                                       |                                                                                            |
    | **Question secrète du client**         | *&lt;Laissez vide&gt;*                                                                         |                                                                                            |
    | **Portée**                 | `https://<tenant domain name>/api/user_impersonation openid offline_access`                   | Remplacez  *&lt;nom_domaine_client&gt;*  avec le nom de domaine du locataire sans crochets pointus. |
    | **Authentification du client** | Envoyer des informations d’identification du client dans le corps                                                               |                                                                                            |
    
3. Sélectionnez le **demander un jeton** bouton.

4. Postman ouvre une nouvelle fenêtre contenant la boîte de dialogue de connexion du locataire Azure AD B2C. Connectez-vous avec un compte existant (si un a été créé les stratégies de test) ou sélectionnez **s’inscrire maintenant** pour créer un nouveau compte. Le **votre mot de passe oublié ?** lien est utilisé pour réinitialiser un mot de passe oublié.

5. Une fois connecté avec succès, la fenêtre se ferme et le **gérer les jetons d’accès** boîte de dialogue s’affiche. Faites défiler vers le bas et sélectionnez le **utilisez jeton** bouton.
    
    ![Où trouver le bouton « Token usage »](./azure-ad-b2c-webapi/postman-access-token.png)

### <a name="test-the-web-api-with-authentication"></a>L’API web avec l’authentification de test

Sélectionnez le **envoyer** bouton Envoyer la demande à nouveau. Cette fois, l’état de réponse est *200 OK* et la charge utile JSON est visible sur la réponse **corps** onglet.
    
![État de réussite et de charge utile](./azure-ad-b2c-webapi/postman-success.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un locataire Azure Active Directory B2C.
> * Enregistrer une API Web dans Azure AD B2C.
> * Utiliser Visual Studio pour créer une API Web configuré pour utiliser le locataire Azure AD B2C pour l’authentification.
> * Configurer des stratégies de contrôle du comportement du client Azure Active Directory B2C.
> * Postman utilisé pour simuler une application web qui présente une boîte de dialogue de connexion, récupère un jeton et l’utilise pour effectuer une demande dans l’API web.

Continuer à développer votre API par l’apprentissage à :

* [Sécuriser une ASP.NET Core application web à l’aide d’Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* [Appeler une API web de .NET à partir d’une application web de .NET à l’aide d’Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).
* [Personnaliser l’interface utilisateur de Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Configurer les exigences de complexité de mot de passe](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Activer l’authentification multifacteur](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Configurer les fournisseurs d’identité supplémentaires, telles que [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)et d’autres.
* [Utiliser l’API Azure AD Graph](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) pour récupérer des informations supplémentaires, telles que l’appartenance au groupe, du locataire Azure AD B2C.