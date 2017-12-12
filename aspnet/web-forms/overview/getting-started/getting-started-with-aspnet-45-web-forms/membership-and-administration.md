---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/membership-and-administration
title: "L’appartenance et l’Administration | Documents Microsoft"
author: Erikre
description: "Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/08/2014
ms.topic: article
ms.assetid: 732a2316-e49f-4f72-becd-0cd72f14457e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/membership-and-administration
msc.type: authoredcontent
ms.openlocfilehash: a10dbfe1ca49baee1604aac8dd9a1f93ccfcb7f9
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="membership-and-administration"></a>L’appartenance et l’Administration
====================
Par [Erik Reitan](https://github.com/Erikre)

[Télécharger Wingtip Toys exemple de projet (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [télécharger des livres (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

> Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour le Web. Un Visual Studio 2013 [projet avec le code source c#](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) est disponible pour accompagner cette série de didacticiels.


Ce didacticiel vous montre comment mettre à jour de l’exemple d’application Wingtip Toys pour ajouter un rôle personnalisé et utiliser l’identité de ASP.NET. Il montre également comment implémenter une page d’administration à partir duquel l’utilisateur avec un rôle personnalisé peut ajouter et supprimer des produits depuis le site Web.

[ASP.NET Identity](../../../../identity/overview/getting-started/introduction-to-aspnet-identity.md) est le système d’appartenance utilisé pour générer l’application web ASP.NET et est disponible dans ASP.NET 4.5. Identité ASP.NET est utilisée dans le modèle de projet Web Forms de Visual Studio 2013, ainsi que les modèles de [ASP.NET MVC](../../../../mvc/index.md), [API Web ASP.NET](../../../../web-api/index.md), et [Application à Page unique ASP.NET](../../../../single-page-application/index.md). Vous pouvez également installer le système d’identité ASP.NET à l’aide de NuGet lorsque vous démarrez avec une application Web vide. Toutefois, dans cette série de didacticiels que vous utilisez le **Web Forms**projecttemplate, qui inclut le système d’identité ASP.NET. ASP.NET Identity rend facile à intégrer les données de profil d’utilisateur avec les données d’application. En outre, ASP.NET Identity vous permet de choisir le modèle de persistance pour les profils utilisateur dans votre application. Vous pouvez stocker les données dans une base de données SQL Server ou une autre banque de données, y compris *NoSQL* stocke les données telles que les Tables de stockage Windows Azure.

Ce didacticiel s’appuie sur le didacticiel précédent intitulé « Extraction et paiement avec PayPal » dans la série de didacticiels Wingtip Toys.

## <a name="what-youll-learn"></a>Ce que vous allez apprendre :

- Comment utiliser du code pour ajouter un rôle personnalisé et un utilisateur à l’application.
- Comment restreindre l’accès à la page et le dossier d’administration.
- Comment fournir une navigation de l’utilisateur qui appartient au rôle personnalisé.
- Comment utiliser la liaison de modèle pour remplir un [DropDownList](https://msdn.microsoft.com/library/system.web.ui.webcontrols.dropdownlist(v=vs.110).aspx) contrôle avec des catégories de produits.
- Comment télécharger un fichier dans l’application web à l’aide du [FileUpload](https://msdn.microsoft.com/library/system.web.ui.webcontrols.fileupload(v=vs.110).aspx) contrôle.
- Comment utiliser des contrôles de validation pour implémenter la validation d’entrée.
- Comment ajouter et supprimer des produits à partir de l’application.

## <a name="these-features-are-included-in-the-tutorial"></a>Ces fonctionnalités sont incluses dans le didacticiel :

- ASP.NET Identity
- Configuration et autorisation
- Liaison de modèle
- Validation non obstrusive

ASP.NET Web Forms fournit des capacités d’appartenance. À l’aide du modèle par défaut, vous disposez des fonctionnalités d’appartenance intégrées que vous pouvez utiliser immédiatement lorsque l’application s’exécute. Ce didacticiel vous montre comment utiliser ASP.NET Identity pour ajouter un rôle personnalisé et attribuer un utilisateur à ce rôle. Vous allez apprendre comment restreindre l’accès au dossier d’administration. Vous allez ajouter une page dans le dossier d’administration qui permet à un utilisateur avec un rôle personnalisé pour ajouter et supprimer des produits et afficher un aperçu d’un produit une fois qu’il a été ajouté.

## <a name="adding-a-custom-role"></a>Ajout d’un rôle personnalisé

À l’aide d’identité ASP.NET, vous pouvez ajouter un rôle personnalisé et attribuer un utilisateur à ce rôle à l’aide de code.

1. Dans **l’Explorateur de solutions**, avec le bouton droit sur le *logique* dossier et créer une nouvelle classe.
2. Nommez la nouvelle classe *RoleActions.cs*.
3. Modifiez le code afin qu’il apparaisse comme suit :  

    [!code-csharp[Main](membership-and-administration/samples/sample1.cs?highlight=8)]
4. Dans **l’Explorateur de solutions**, ouvrez le *Global.asax.cs* fichier.
5. Modifier la *Global.asax.cs* fichier en ajoutant le code mis en surbrillance en jaune afin qu’il apparaisse comme suit :  

    [!code-csharp[Main](membership-and-administration/samples/sample2.cs?highlight=11,26-28)]
6. Notez que `AddUserAndRole` est souligné en rouge. Double-cliquez sur le code AddUserAndRole.  
 La lettre « A » au début de la méthode de mise en surbrillance est soulignée.
7. Placez le curseur sur la lettre « A » et cliquez sur l’interface utilisateur qui vous permet de générer un stub de méthode pour le `AddUserAndRole` (méthode). 

    ![L’appartenance et Advministration - générer le Stub de méthode](membership-and-administration/_static/image1.png)
8. Cliquez sur l’option intitulée :  
    `Generate method stub for "AddUserAndRole" in "WingtipToys.Logic.RoleActions"`
9. Ouvrez le *RoleActions.cs* à partir de la *logique* dossier.  
 Le `AddUserAndRole` méthode a été ajoutée au fichier de classe.
10. Modifier la *RoleActions.cs* fichier en supprimant le `NotImplementedeException` et en ajoutant le code mis en surbrillance en jaune, afin qu’il apparaisse comme suit :  

    [!code-csharp[Main](membership-and-administration/samples/sample3.cs?highlight=5-7,15-51)]

Tout d’abord, le code ci-dessus établit un contexte de base de données pour la base de données d’appartenance. La base de données d’appartenance est également stockée comme un *.mdf* de fichiers dans le *application\_données* dossier. Vous serez en mesure d’afficher cette base de données une fois le premier utilisateur est connecté à cette application web. 

> [!NOTE] 
> 
> Si vous souhaitez stocker les données d’appartenance, ainsi que les données de produit, vous pouvez envisager d’utiliser le même **DbContext** que vous avez utilisé pour stocker les données de produit dans le code ci-dessus.


 Le *interne* mot clé est un modificateur d’accès pour les types (tels que les classes) et les membres de type (par exemple, les méthodes ou propriétés). Types internes ou les membres sont accessibles uniquement dans les fichiers contenus dans le même assembly *(.dll* fichier). Lorsque vous générez votre application, un fichier d’assembly *(.dll*) est créé qui contient le code qui est exécuté lorsque vous exécutez votre application. 

A `RoleStore` objet qui fournit la gestion des rôles, est créé en fonction du contexte de base de données.

> [!NOTE] 
> 
> Notez que lorsque la `RoleStore` objet est créé qu’il utilise un type générique `IdentityRole` type. Cela signifie que la `RoleStore` est uniquement autorisée à contenir `IdentityRole` objets. Également par l’utilisation de génériques, les ressources en mémoire sont plus facile à gérer.


Ensuite, le `RoleManager` , est créé selon la `RoleStore` objet que vous venez de créer. le `RoleManager` objet expose rôle API associée qui peut être utilisé pour enregistrer automatiquement les modifications apportées à la `RoleStore`. Le `RoleManager` est uniquement autorisée à contenir `IdentityRole` objets, car le code utilise le `<IdentityRole>` type générique.

Vous appelez le `RoleExists` méthode pour déterminer si le rôle « canEdit » est présent dans la base de données d’appartenance. Si elle n’est pas le cas, vous créez le rôle.

Création de la `UserManager` objet semble être plus complexe que le `RoleManager` contrôler, toutefois, il est presque les mêmes. Il est simplement codé sur une ligne plutôt que plusieurs. Ici, le paramètre que vous passez est instanciation comme un nouvel objet contenu dans les parenthèses.

Ensuite vous créez l’utilisateur « canEditUser » en créant un nouveau `ApplicationUser` objet. Ensuite, si vous créez avec succès de l’utilisateur, vous devez ajouter l’utilisateur au nouveau rôle.

> [!NOTE] 
> 
> La gestion des erreurs seront mise à jour au cours du didacticiel « Gestion d’erreurs ASP.NET » plus loin dans cette série de didacticiels.


Lors du prochain démarrage de l’application, l’utilisateur nommé « canEditUser » sera ajouté en tant que rôle nommé « canEdit » de l’application. Plus loin dans ce didacticiel, vous va se connecter en tant que l’utilisateur « canEditUser » pour afficher les fonctionnalités supplémentaires qui vous serez ajoutés au cours de ce didacticiel. Pour plus d’informations de l’API sur ASP.NET Identity, consultez le [Microsoft.AspNet.Identity Namespace](https://msdn.microsoft.com/library/microsoft.aspnet.identity(v=vs.111).aspx). Pour plus d’informations sur l’initialisation du système d’identité ASP.NET, consultez le [AspnetIdentitySample](https://github.com/rustd/AspnetIdentitySample/blob/master/AspnetIdentitySample/App_Start/IdentityConfig.cs).

### <a name="restricting-access-to-the-administration-page"></a>Restreindre l’accès à la Page d’Administration

L’exemple d’application Wingtip Toys permet aux utilisateurs anonymes et les utilisateurs connectés à afficher et acheter des produits. Toutefois, l’utilisateur connecté qui a le rôle personnalisé « canEdit » peut accéder à une page à accès restreint pour ajouter et supprimer des produits.

#### <a name="add-an-administration-folder-and-page"></a>Ajouter un dossier d’Administration et de la Page

Ensuite, vous allez créer un dossier nommé *Admin* exemple d’application pour l’utilisateur « canEditUser » appartenant au rôle personnalisé de la Wingtip Toys.

1. Cliquez sur le nom du projet (**Wingtip Toys**) dans **l’Explorateur de solutions** et sélectionnez **ajouter**  - &gt; **nouveau dossier**.
2. Nommez le nouveau dossier *Admin*.
3. Avec le bouton droit le *Admin* dossier, puis sélectionnez **ajouter**  - &gt; **un nouvel élément**.   
 La boîte de dialogue **Ajouter un nouvel élément** s’affiche.
4. Sélectionnez le **Visual C#** - &gt; **Web** groupe de modèles sur la gauche. Dans la liste du milieu, sélectionnez **Web Form avec Page maître**, nommez-le *AdminPage.aspx***,** , puis sélectionnez **ajouter**.
5. Sélectionnez le *Site.Master* de fichiers en tant que la page maître, puis choisissez **OK**.

#### <a name="add-a-webconfig-file"></a>Ajouter un fichier Web.config

En ajoutant un *Web.config* de fichiers à la *Admin* dossier, vous pouvez restreindre l’accès à la page contenue dans le dossier.

1. Avec le bouton droit le *Admin* et sélectionnez **ajouter**  - &gt; **un nouvel élément**.  
 La boîte de dialogue **Ajouter un nouvel élément** s’affiche.
2. Dans la liste des modèles de web Visual c#, sélectionnez **fichier de Configuration Web**à partir de la liste du milieu, acceptez le nom par défaut *Web.config***,** , puis sélectionnez **Ajouter**.
3. Remplacez le document XML contenu dans le *Web.config* fichier avec les éléments suivants :  

    [!code-xml[Main](membership-and-administration/samples/sample4.xml)]

Enregistrer le *Web.config* fichier. Le *Web.config* fichier Spécifie que seul l’utilisateur appartenant au rôle « canEdit » de l’application peut accéder à la page contenue dans le *Admin* dossier.

### <a name="including-custom-role-navigation"></a>Y compris la Navigation de rôle personnalisé

Pour permettre à l’utilisateur du rôle personnalisé « canEdit » accéder à la section administration de l’application, vous devez ajouter un lien vers le *Site.Master* page. Seuls les utilisateurs qui appartiennent au rôle « canEdit » seront en mesure de voir les **Admin** lier et accéder à la section administration.

1. Dans l’Explorateur de solutions, recherchez et ouvrez le *Site.Master* page.
2. Pour créer un lien pour l’utilisateur du rôle « canEdit », ajoutez le balisage mis en surbrillance en jaune dans la liste non triée suivante `<ul>` élément afin que la liste s’affiche comme suit :  

    [!code-html[Main](membership-and-administration/samples/sample5.html?highlight=2-3)]
3. Ouvrez le *Site.Master.cs* fichier. Rendre le **Admin** lien visible uniquement à l’utilisateur « canEditUser » en ajoutant le code mis en surbrillance en jaune pour le `Page_Load` gestionnaire. Le `Page_Load` gestionnaire s’affiche comme suit :   

    [!code-csharp[Main](membership-and-administration/samples/sample6.cs?highlight=3-6)]

Lors du chargement de la page, le code vérifie si l’utilisateur connecté a le rôle de « canEdit ». Si l’utilisateur appartient au rôle « canEdit », l’élément span qui contient le lien vers le *AdminPage.aspx* page (et, par conséquent, le lien à l’intérieur de l’étendue) sont rendues visibles.

### <a name="enabling-product-administration"></a>L’activation de produit Administration

Jusqu'à présent, vous avez créé le rôle « canEdit » et ajouté un utilisateur « canEditUser », un dossier d’administration et une page d’administration. Vous avez défini des droits d’accès pour le dossier d’administration et de la page et que vous avez ajouté un lien de navigation de l’utilisateur du rôle « canEdit » à l’application. Vous allez ensuite ajouter le balisage à la *AdminPage.aspx* page et le code vers le *AdminPage.aspx.cs* fichier code-behind qui permettre à l’utilisateur avec le rôle « canEdit » ajouter et supprimer des produits.

1. Dans **l’Explorateur de solutions**, ouvrez le *AdminPage.aspx* de fichiers à partir de la *Admin* dossier.
2. Remplacez la balise existante avec les éléments suivants :  

    [!code-aspx[Main](membership-and-administration/samples/sample7.aspx)]
3. Ensuite, ouvrez le *AdminPage.aspx.cs* fichier code-behind en double-cliquant sur le *AdminPage.aspx* et en cliquant sur **afficher le Code**.
4. Remplacez le code existant dans le *AdminPage.aspx.cs* fichier code-behind par le code suivant :  

    [!code-csharp[Main](membership-and-administration/samples/sample8.cs)]

Dans le code que vous avez entré pour le *AdminPage.aspx.cs* fichier code-behind, une classe appelée `AddProducts` effectue le travail de l’ajout de produits à la base de données. Cette classe n’existe pas encore, vous allez donc créer il maintenant.

1. Dans **l’Explorateur de solutions**, avec le bouton droit le *logique* dossier, puis sélectionnez **ajouter**  - &gt; **un nouvel élément**.   
 La boîte de dialogue **Ajouter un nouvel élément** s’affiche.
2. Sélectionnez le **Visual C#**  - &gt; **Code** groupe de modèles sur la gauche. Ensuite, sélectionnez **classe**à partir du milieu de liste et nommez-le *AddProducts.cs*.   
 Le nouveau fichier de classe s’affiche.
3. Remplacez le code existant par le code ci-dessous :  

    [!code-csharp[Main](membership-and-administration/samples/sample9.cs)]

Le *AdminPage.aspx* page permet à l’utilisateur appartenant au rôle « canEdit » ajouter et supprimer des produits. Lorsqu’un nouveau produit est ajouté, les détails du produit sont validées et ensuite entrées dans la base de données. Le nouveau produit est immédiatement disponible pour tous les utilisateurs de l’application web.

#### <a name="unobtrusive-validation"></a>Validation non obstrusive

Les détails du produit fournies par l’utilisateur sur le *AdminPage.aspx* page sont validés à l’aide de contrôles de validation (`RequiredFieldValidator` et `RegularExpressionValidator`). Ces contrôles utilisent automatiquement la validation non obstrusive. Validation non obstrusive permet les contrôles de validation à utiliser JavaScript pour la logique de validation côté client, ce qui signifie que la page ne requiert pas un aller-retour vers le serveur à valider. Par défaut, la validation discrète est incluse dans le *Web.config* fichier basé sur le paramètre de configuration suivantes :

[!code-xml[Main](membership-and-administration/samples/sample10.xml)]

#### <a name="regular-expressions"></a>Expressions régulières

Le prix du produit sur le *AdminPage.aspx* page est validée à l’aide un **RegularExpressionValidator** contrôle. Ce contrôle vérifie si la valeur du contrôle d’entrée associé (la zone de texte « AddProductPrice ») correspond au modèle spécifié par l’expression régulière. Une expression régulière est une notation de correspondance qui vous permet de rapidement rechercher des modèles de caractères spécifiques. Le **RegularExpressionValidator** contrôle inclut une propriété nommée `ValidationExpression` qui contient l’expression régulière utilisée pour valider une entrée de prix, comme indiqué ci-dessous :

[!code-aspx[Main](membership-and-administration/samples/sample11.aspx)]

#### <a name="fileupload-control"></a>Contrôle fileUpload

Outre les contrôles d’entrée et de validation, vous avez ajouté le **FileUpload** le contrôle à la *AdminPage.aspx* page. Ce contrôle permet de télécharger des fichiers. Dans ce cas, vous autorisez uniquement les fichiers image à télécharger. Dans le fichier code-behind (*AdminPage.aspx.cs*), lorsque le `AddProductButton` est activé, le code vérifie la `HasFile` propriété de la **FileUpload** contrôle. Si le contrôle dispose d’un fichier et si le type de fichier (basé sur l’extension de fichier) est autorisé, l’image est enregistrée sur le *Images* dossier et le *Images/pouces* dossier de l’application.

#### <a name="model-binding"></a>Liaison de modèle

Plus haut dans cette série de didacticiels vous permet de liaison de modèle pour remplir un **ListView** (contrôle), un **FormsView** (contrôle), un **GridView** (contrôle) et un  **DetailView** contrôle. Dans ce didacticiel, vous utilisez liaison de modèle pour remplir un **DropDownList** contrôle avec une liste des catégories de produits.

Le balisage que vous avez ajouté à la *AdminPage.aspx* fichier contient un **DropDownList** contrôle appelé `DropDownAddCategory`:

[!code-aspx[Main](membership-and-administration/samples/sample12.aspx)]

Liaison de modèle vous permet de remplir ce **DropDownList** en définissant le `ItemType` attribut et le `SelectMethod` attribut. Le `ItemType` attribut indique que vous utilisez le `WingtipToys.Models.Category` lors du remplissage du contrôle de type. Définie de ce type au début de cette série de didacticiels en créant la `Category` classe (voir ci-dessous). Le `Category` classe se trouve dans le *modèles* dossier à l’intérieur de la *Category.cs* fichier.

[!code-csharp[Main](membership-and-administration/samples/sample13.cs)]

Le `SelectMethod` attribut de la **DropDownList** contrôle indique que vous utilisez le `GetCategories` (méthode) (voir ci-dessous) qui est inclus dans le fichier code-behind (*AdminPage.aspx.cs*).

[!code-csharp[Main](membership-and-administration/samples/sample14.cs)]

Cette méthode spécifie qu’un `IQueryable` interface est utilisée pour évaluer une requête sur un `Category` type. La valeur retournée est utilisée pour remplir la **DropDownList** dans le balisage de la page (*AdminPage.aspx*).

Le texte affiché pour chaque élément dans la liste est spécifié en définissant le `DataTextField` attribut. Le `DataTextField` attribut utilise le `CategoryName` de la `Category` classe (illustré ci-dessus) pour afficher chaque catégorie dans le **DropDownList** contrôle. La valeur réelle est passée lorsqu’un élément est sélectionné dans le **DropDownList** contrôle est basé sur le `DataValueField` attribut. Le `DataValueField` attribut est défini sur le `CategoryID` que la définition de la `Category` classe (illustré ci-dessus).

### <a name="how-the-application-will-work"></a>Fonctionne de l’Application

Lorsque l’utilisateur appartenant au rôle « canEdit » accède à la page pour la première fois, le `DropDownAddCategory` **DropDownList** est rempli comme décrit ci-dessus. Le `DropDownRemoveProduct` **DropDownList** contrôle est également rempli avec les produits à l’aide de la même approche. Sélectionne le type de catégorie de l’utilisateur appartenant au rôle « canEdit » et ajoute les détails du produit (**nom**, **Description**, **prix**, et **le fichier Image**). Lorsque l’utilisateur appartenant au rôle « canEdit » clique le **ajouter un produit** bouton, le `AddProductButton_Click` Gestionnaire d’événements est déclenché. Le `AddProductButton_Click` Gestionnaire d’événements se trouve dans le fichier code-behind (*AdminPage.aspx.cs*) vérifie le fichier image pour vous assurer qu’il correspond aux types de fichiers autorisés *(.gif*, *.png*, *.jpeg*, ou *.jpg*). Ensuite, l’image est enregistrée dans un dossier de l’exemple d’application Wingtip Toys. Ensuite, le nouveau produit est ajouté à la base de données. Pour accomplir l’ajout d’un nouveau produit, une nouvelle instance de la `AddProducts` classe est créée et nommée produits. Le `AddProducts` classe a une méthode nommée `AddProduct`, et l’objet de produits appelle cette méthode pour ajouter des produits à la base de données.

[!code-csharp[Main](membership-and-administration/samples/sample15.cs)]

Si le code ajoute correctement le nouveau produit à la base de données, la page est rechargée avec la valeur de chaîne de requête `ProductAction=add`.

[!code-csharp[Main](membership-and-administration/samples/sample16.cs)]

Lorsque la page se recharge, la chaîne de requête est incluse dans l’URL. Rechargez la page, l’utilisateur appartenant au rôle « canEdit » voir immédiatement les mises à jour dans le **DropDownList** contrôle sur le *AdminPage.aspx* page. En outre, en incluant la chaîne de requête avec l’URL, la page peut afficher un message de réussite à l’utilisateur appartenant au rôle « canEdit ».

Lorsque le *AdminPage.aspx* page recharge, le `Page_Load` événement est appelé.

[!code-csharp[Main](membership-and-administration/samples/sample17.cs)]

Le `Page_Load` Gestionnaire d’événements vérifie la valeur de chaîne de requête et détermine s’il faut afficher un message de réussite.

## <a name="running-the-application"></a>Exécution de l'application

Vous pouvez exécuter l’application maintenant pour voir comment vous pouvez ajouter, supprimer et les éléments de mise à jour dans le panier d’achat. Le total de panier d’achat reflète le coût total de tous les éléments dans le panier d’achat.

1. Dans l’Explorateur de solutions, appuyez sur **F5** pour exécuter l’exemple d’application Wingtip Toys.  
 Le navigateur s’ouvre et affiche le *Default.aspx* page.
2. Cliquez sur le **connecter** lien en haut de la page. 

    ![L’appartenance et l’Administration - connectez-vous de lien](membership-and-administration/_static/image2.png)

 Le *Login.aspx* page s’affiche.
3. Utilisez le nom d’utilisateur suivant et le mot de passe :  
 Nom d’utilisateur :canEditUser@wingtiptoys.com  
 Mot de passe : Pa$ $word1 

    ![L’appartenance et l’Administration - Page de connexion](membership-and-administration/_static/image3.png)
4. Cliquez sur le **connecter** bouton vers le bas de la page.
5. En haut de la page suivante, sélectionnez le **Admin** lien pour accéder à la *AdminPage.aspx* page. 

    ![L’appartenance et l’Administration - lien d’administration](membership-and-administration/_static/image4.png)
6. Pour tester la validation des entrées, cliquez sur le **ajouter un produit** bouton sans ajouter les détails du produit. 

    ![L’appartenance et l’Administration - Page d’administration](membership-and-administration/_static/image5.png)

 Notez que les messages de champ obligatoire sont affichés.
7. Ajouter les détails d’un nouveau produit, puis cliquez sur le **ajouter un produit** bouton. 

    ![L’appartenance et l’Administration - ajouter un produit](membership-and-administration/_static/image6.png)
8. Sélectionnez **produits** à partir du menu de navigation supérieur pour afficher le nouveau produit que vous avez ajouté. 

    ![L’appartenance et l’Administration - afficher le nouveau produit](membership-and-administration/_static/image7.png)
9. Cliquez sur le **Admin** lien pour revenir à la page d’administration.
10. Dans le **supprimer le produit** section de la page, sélectionnez le nouveau produit que vous avez ajouté à la **DropDownListBox**.
11. Cliquez sur le **supprimer le produit** bouton pour supprimer le nouveau produit de l’application. 

    ![L’appartenance et l’Administration - Remove produit](membership-and-administration/_static/image8.png)
12. Sélectionnez **produits** à partir du menu de navigation supérieure pour confirmer que le produit a été supprimé.
13. Cliquez sur **session** d’exister en mode d’administration.   
 Notez que le volet de navigation supérieure n’affiche plus le **Admin** élément de menu.

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous ajouté un rôle personnalisé et un utilisateur appartenant au rôle personnalisé, un accès limité à la page et le dossier d’administration et fourni la navigation de l’utilisateur appartenant au rôle personnalisé. Liaison de modèle vous permet de remplir un **DropDownList** contrôle de données. Vous implémenté la **FileUpload** contrôle et les contrôles de validation. En outre, vous avez appris comment ajouter et supprimer des produits à partir d’une base de données. Dans l’étape suivante du didacticiel, vous allez apprendre à implémenter le routage ASP.NET.

## <a name="additional-resources"></a>Ressources supplémentaires

[Web.config - authorization, élément](https://msdn.microsoft.com/library/8d82143t(v=vs.100).aspx)  
[Identité ASP.NET](../../../../identity/overview/getting-started/introduction-to-aspnet-identity.md)  
[Déployer une application de formulaires Web ASP.NET sécurisée avec base de données SQL, OAuth et l’appartenance à un Site Web Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/)  
[Microsoft Azure - version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)

>[!div class="step-by-step"]
[Précédent](checkout-and-payment-with-paypal.md)
[Suivant](url-routing.md)
