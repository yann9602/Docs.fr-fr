---
uid: web-forms/overview/moving-to-aspnet-20/profiles-themes-and-web-parts
title: "Les profils, les thèmes et les composants WebPart | Documents Microsoft"
author: microsoft
description: "Voici les principales modifications de configuration et d’instrumentation dans ASP.NET 2.0. La nouvelle API de configuration ASP.NET permet des modifications de configuration doit être effectuée pr..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 92df4051-77c6-492c-bd34-23d24189cea4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/profiles-themes-and-web-parts
msc.type: authoredcontent
ms.openlocfilehash: c9fe97dbd5fe10cbde25b9daf5ddd35b2d7eaab5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="profiles-themes-and-web-parts"></a>Les profils, les thèmes et les composants WebPart
====================
par [Microsoft](https://github.com/microsoft)

> Voici les principales modifications de configuration et d’instrumentation dans ASP.NET 2.0. La nouvelle API de configuration ASP.NET permet des modifications de configuration doit être effectuée par programme. En outre, existent de nombreux nouveaux paramètres de configuration permettant de nouvelles configurations et d’instrumentation.


ASP.NET 2.0 représente une amélioration importante dans la zone de sites Web personnalisés. Outre les fonctionnalités d’appartenance nous avons déjà couvertes, profils ASP.NET, les thèmes et les composants WebPart considérablement améliorer la personnalisation dans les sites Web.

## <a name="aspnet-profiles"></a>Profils ASP.NET

Les profils ASP.NET sont similaires aux sessions. La différence est qu’un profil est persistant, alors qu’une session est perdue lorsque le navigateur est fermé. Une autre grande différence entre les sessions et les profils est que les profils sont fortement typées, par conséquent vous fournir IntelliSense pendant le processus de développement.

Un profil est défini dans le fichier de configuration d’ordinateurs ou sur le fichier web.config de l’application. (Vous ne pouvez pas définir un profil dans un fichier web.config de sous-dossiers). Le code suivant définit un profil pour stocker les visiteurs du site Web tout d’abord les nom et prénom.

[!code-xml[Main](profiles-themes-and-web-parts/samples/sample1.xml)]

Le type de données par défaut pour une propriété de profil est System.String. Dans l’exemple ci-dessus, aucun type de données a été spécifié. Par conséquent, les propriétés FirstName et LastName sont tous deux de type chaîne. Comme mentionné précédemment, profil de propriétés sont fortement typées. Le code ci-dessous ajoute une nouvelle propriété pour l’âge est de type Int32.

[!code-xml[Main](profiles-themes-and-web-parts/samples/sample2.xml)]

Les profils sont généralement utilisés avec l’authentification par formulaire ASP.NET. Lorsqu’il est utilisé en association avec l’authentification par formulaire, chaque utilisateur possède un profil distinct associé à son ID utilisateur. Toutefois, il est également possible pour permettre l’utilisation de profils dans une application anonyme en utilisant le &lt;anonymousIdentification&gt; élément dans le fichier de configuration avec la **allowAnonymous** sous la forme Vous trouverez ci-dessous :

[!code-xml[Main](profiles-themes-and-web-parts/samples/sample3.xml)]

Lorsqu’un utilisateur anonyme parcourt le site, ASP.NET crée une instance de **ProfileCommon** pour l’utilisateur. Ce profil utilise un ID unique, stocké dans un cookie sur le navigateur pour identifier l’utilisateur en tant qu’un visiteur unique. De cette façon, vous pouvez stocker des informations de profil pour les utilisateurs qui parcourent de manière anonyme.

## <a name="profile-groups"></a>Groupes de profils

Il est possible pour les propriétés du groupe de profils. Par les propriétés de regroupement, il est possible de simuler plusieurs profils pour une application spécifique.

La configuration suivante configure une propriété FirstName et LastName pour les deux groupes ; Les acheteurs et les Prospects.

[!code-xml[Main](profiles-themes-and-web-parts/samples/sample4.xml)]

Ensuite, il est possible de définir des propriétés sur un groupe particulier comme suit :

[!code-csharp[Main](profiles-themes-and-web-parts/samples/sample5.cs)]

## <a name="storing-complex-objects"></a>Le stockage d’objets Complex

Jusqu'à présent, les exemples, que nous avons couvert ont stocké des types de données simples dans un profil. Il est également possible de stocker des types de données complexes dans un profil en spécifiant la méthode de sérialisation à l’aide du **serializeAs** attribut comme suit :

[!code-xml[Main](profiles-themes-and-web-parts/samples/sample6.xml)]

Dans ce cas, le type est PurchaseInvoice. La classe PurchaseInvoice doit être marqué comme sérialisable et peut contenir n’importe quel nombre de propriétés. Par exemple, si PurchaseInvoice a une propriété appelée **NumItemsPurchased**, vous pouvez faire référence à cette propriété dans le code comme suit :

[!code-css[Main](profiles-themes-and-web-parts/samples/sample7.css)]

## <a name="profile-inheritance"></a>Héritage de profil

Il est possible de créer un profil à utiliser dans plusieurs applications. En créant une classe de profil qui dérive de ProfileBase, vous pouvez réutiliser un profil dans plusieurs applications à l’aide de la **hérite** attribut comme indiqué ci-dessous :

[!code-xml[Main](profiles-themes-and-web-parts/samples/sample8.xml)]

Dans ce cas, la classe **PurchasingProfile** se présenterait comme suit :

[!code-csharp[Main](profiles-themes-and-web-parts/samples/sample9.cs)]

## <a name="profile-providers"></a>Fournisseurs de profils

Les profils ASP.NET utilisent le modèle de fournisseur. Le fournisseur par défaut stocke les informations dans une base de données SQL Server Express dans l’application\_dossier de données de l’application Web à l’aide du fournisseur SqlProfileProvider. Si la base de données n’existe pas, ASP.NET crée automatiquement lorsque le profil tente de stocker les informations.

Dans certains cas, toutefois, vous souhaiterez développer votre propre fournisseur de profil. La fonctionnalité de profil ASP.NET vous permet d’utiliser facilement des fournisseurs différents.

Vous créez un fournisseur de profils personnalisé lorsque :

- Vous avez besoin stocker les informations de profil dans une source de données, telles que dans une base de données ou dans une base de données Oracle, qui ne prend pas en charge les fournisseurs de profils inclus avec le .NET Framework.
- Vous devez gérer les informations de profil à l’aide d’un schéma de base de données qui est différent du schéma de la base de données utilisé par les fournisseurs inclus avec le .NET Framework. Un exemple courant est que vous souhaitez intégrer des informations de profil avec des données utilisateur dans une base de données SQL Server existante.

### <a name="required-classes"></a>Classes obligatoire

Pour implémenter un fournisseur de profils, vous créez une classe qui hérite de la classe abstraite System.Web.Profile.ProfileProvider. Le **ProfileProvider** classe abstraite hérite à son tour de la classe abstraite System.Configuration.SettingsProvider, qui hérite de la classe abstraite System.Configuration.Provider.ProviderBase. En raison de cette chaîne d’héritage, outre les membres requis de la **ProfileProvider** (classe), vous devez implémenter les membres requis de la **SettingsProvider** et  **ProviderBase** classes.

Les tableaux suivants décrivent les propriétés et méthodes que vous devez implémenter à partir de la **ProviderBase**, **SettingsProvider**, et **ProfileProvider** abstraite classes.

### <a name="providerbase-members"></a>Membres ProviderBase

| **Membre** | **Description** |
| --- | --- |
| Initialize (méthode) | Prend comme entrée le nom de l’instance du fournisseur et une collection NameValueCollection des paramètres de configuration. Utilisé pour définir les options et les valeurs de propriété pour l’instance de fournisseur, y compris les valeurs spécifiques à l’implémentation et les options spécifiées dans la configuration de l’ordinateur ou le fichier Web.config. |

### <a name="settingsprovider-members"></a>Membres SettingsProvider

| **Membre** | **Description** |
| --- | --- |
| Propriété ApplicationName | Le nom de l’application qui est stocké avec chaque profil. Le fournisseur de profil utilise le nom de l’application pour stocker les informations de profil séparément pour chaque application. Cela permet à plusieurs applications ASP.NET d’utiliser la même source de données sans conflit si le même nom d’utilisateur est créé dans différentes applications. Vous pouvez également plusieurs applications ASP.NET peuvent partager une source de données de profil en spécifiant le même nom d’application. |
| GetPropertyValues (méthode) | Prend comme entrée un SettingsContext et un objet SettingsPropertyCollection. Le **SettingsContext** fournit des informations sur l’utilisateur. Vous pouvez utiliser les informations comme clé primaire pour récupérer des informations sur les propriétés de profil pour l’utilisateur. Utilisez le **SettingsContext** objet pour obtenir le nom d’utilisateur et que l’utilisateur est authentifié ou anonyme. Le **SettingsPropertyCollection** contient une collection d’objets de SettingsProperty. Chaque **SettingsProperty** objet fournit le nom et le type de la propriété, ainsi que des informations supplémentaires telles que la valeur par défaut pour la propriété et si la propriété est en lecture seule. Le **GetPropertyValues** méthode remplit une collection SettingsPropertyValueCollection avec des objets SettingsPropertyValue basés sur le **SettingsProperty** objets fournis en tant qu’entrée. Les valeurs à partir de la source de données pour l’utilisateur spécifié sont assignées aux propriétés PropertyValue pour chaque **SettingsPropertyValue** objet et la collection entière est retournée. Appeler la méthode met également à jour la valeur de LastActivityDate pour le profil d’utilisateur spécifié à la date et heure actuelles. |
| SetPropertyValues (méthode) | Prend comme entrée un **SettingsContext** et un **collection SettingsPropertyValueCollection** objet. Le **SettingsContext** fournit des informations sur l’utilisateur. Vous pouvez utiliser les informations comme clé primaire pour récupérer des informations sur les propriétés de profil pour l’utilisateur. Utilisez le **SettingsContext** objet pour obtenir le nom d’utilisateur et que l’utilisateur est authentifié ou anonyme. Le **collection SettingsPropertyValueCollection** contient une collection de **SettingsPropertyValue** objets. Chaque **SettingsPropertyValue** objet fournit le nom, le type et la valeur de la propriété, ainsi que des informations supplémentaires telles que la valeur par défaut pour la propriété et si la propriété est en lecture seule. Le **SetPropertyValues** méthode met à jour les valeurs de propriété de profil dans la source de données pour l’utilisateur spécifié. Appeler la méthode également les mises à jour le **LastActivityDate** et LastUpdatedDate des valeurs pour le profil d’utilisateur spécifié à la date et heure actuelles. |

### <a name="profileprovider-members"></a>Membres ProfileProvider

| **Membre** | **Description** |
| --- | --- |
| DeleteProfiles (méthode) | Prend comme entrée d’utilisateur, un tableau de chaînes de noms et supprime de la source de données toutes les valeurs d’informations et de la propriété de profil pour les noms spécifiés où le nom d’application correspond à la **ApplicationName** valeur de propriété. Si votre source de données prend en charge les transactions, il est recommandé d’inclure toutes les opérations de suppression dans une transaction et de restaurer la transaction et de lever une exception si une opération de suppression échoue. |
| DeleteProfiles (méthode) | Prend comme entrée un ensemble de ProfileInfo objets et supprime de la source de données toutes les valeurs d’informations et de la propriété de profil pour chaque profil où le nom d’application correspond à la **ApplicationName** valeur de propriété. Si votre source de données prend en charge les transactions, il est recommandé d’inclure toutes les opérations de suppression dans une transaction et de restaurer la transaction et de lever une exception si une opération de suppression échoue. |
| DeleteInactiveProfiles (méthode) | Prend comme entrée une valeur ProfileAuthenticationOption et un objet DateTime et les suppressions à partir des données de source de toutes les informations de profil et les valeurs de propriété où la dernière date d’activité est inférieure ou égale à la date et heure spécifiées et le nom de l’application correspond à la **ApplicationName** valeur de propriété. Le **ProfileAuthenticationOption** paramètre spécifie si seuls les profils anonymes, les profils authentifiés, ou tous les profils sont à supprimer. Si votre source de données prend en charge les transactions, il est recommandé d’inclure toutes les opérations de suppression dans une transaction et de restaurer la transaction et de lever une exception si une opération de suppression échoue. |
| GetAllProfiles (méthode) | Prend comme entrée un **ProfileAuthenticationOption** valeur, un entier qui spécifie l’index de page, un entier qui spécifie la taille de page et une référence à un entier dont la valeur est le nombre total de profils. Retourne une ProfileInfoCollection contienne **ProfileInfo** objets pour tous les profils de la source de données où le nom d’application correspond à la **ApplicationName** valeur de propriété. Le **ProfileAuthenticationOption** paramètre spécifie si seuls les profils anonymes, les profils authentifiés, ou tous les profils doivent être retournés. Les résultats retournés par la **GetAllProfiles** méthode sont limitées par les index de la page et les valeurs de taille de page. La valeur de taille de page spécifie le nombre maximal de **ProfileInfo** objets à retourner dans le **ProfileInfoCollection**. La valeur d’index de page spécifie la page de résultats à retourner, 1 correspondant à la première page. Le paramètre de nombre total d’enregistrements est un paramètre de sortie (vous pouvez utiliser **ByRef** en Visual Basic) qui est défini sur le nombre total de profils. Par exemple, si le magasin de données contient 13 profils pour l’application et la valeur d’index de page est de 2 pour une taille de page de 5, le **ProfileInfoCollection** retournée contient les sixième à dixième profils. La valeur du nombre total d’enregistrements est définie sur 13 lorsque la méthode retourne. |
| GetAllInactiveProfiles (méthode) | Prend comme entrée un **ProfileAuthenticationOption** valeur, un **DateTime** objet, un entier qui spécifie l’index de page, un entier qui spécifie la taille de page et une référence à un entier dont la valeur le nombre total de profils. Retourne un **ProfileInfoCollection** contenant **ProfileInfo** objets pour tous les profils de la source de données où la dernière date d’activité est inférieure ou égale à spécifié **DateTime**  et où le nom d’application correspond à la **ApplicationName** valeur de propriété. Le **ProfileAuthenticationOption** paramètre spécifie si seuls les profils anonymes, les profils authentifiés, ou tous les profils doivent être retournés. Les résultats retournés par la **GetAllInactiveProfiles** méthode sont limitées par les index de la page et les valeurs de taille de page. La valeur de taille de page spécifie le nombre maximal de **ProfileInfo** objets à retourner dans le **ProfileInfoCollection**. La valeur d’index de page spécifie la page de résultats à retourner, 1 correspondant à la première page. Le paramètre de nombre total d’enregistrements est un paramètre de sortie (vous pouvez utiliser **ByRef** en Visual Basic) qui est défini sur le nombre total de profils. Par exemple, si le magasin de données contient 13 profils pour l’application et la valeur d’index de page est de 2 pour une taille de page de 5, le **ProfileInfoCollection** retournée contient les sixième à dixième profils. La valeur du nombre total d’enregistrements est définie sur 13 lorsque la méthode retourne. |
| FindProfilesByUserName (méthode) | Prend comme entrée un **ProfileAuthenticationOption** valeur, une chaîne contenant un nom d’utilisateur, un entier qui spécifie l’index de page, un entier qui spécifie la taille de page et une référence à un entier dont la valeur est le nombre total de profils. Retourne un **ProfileInfoCollection** contenant **ProfileInfo** objets pour tous les profils de données source dans lequel le nom d’utilisateur correspond au nom d’utilisateur spécifié et où le nom d’application correspond à la **ApplicationName** valeur de propriété. Le **ProfileAuthenticationOption** paramètre spécifie si seuls les profils anonymes, les profils authentifiés, ou tous les profils doivent être retournés. Si votre source de données prend en charge les fonctions de recherche supplémentaires, telles que des caractères génériques, vous pouvez fournir des fonctions de recherche plus étendues pour les noms d’utilisateur. Les résultats retournés par la **FindProfilesByUserName** méthode sont limitées par les index de la page et les valeurs de taille de page. La valeur de taille de page spécifie le nombre maximal de **ProfileInfo** objets à retourner dans le **ProfileInfoCollection**. La valeur d’index de page spécifie la page de résultats à retourner, 1 correspondant à la première page. Le paramètre de nombre total d’enregistrements est un paramètre de sortie (vous pouvez utiliser **ByRef** en Visual Basic) qui est défini sur le nombre total de profils. Par exemple, si le magasin de données contient 13 profils pour l’application et la valeur d’index de page est de 2 pour une taille de page de 5, le **ProfileInfoCollection** retournée contient les sixième à dixième profils. La valeur du nombre total d’enregistrements est définie sur 13 lorsque la méthode retourne. |
| FindInactiveProfilesByUserName (méthode) | Prend comme entrée un **ProfileAuthenticationOption** valeur, une chaîne contenant un nom d’utilisateur, un **DateTime** objet, un entier qui spécifie l’index de page, un entier qui spécifie la taille de page et un référence à un entier dont la valeur est le nombre total de profils. Retourne un **ProfileInfoCollection** contenant **ProfileInfo** objets pour tous les profils de la source de données où le nom d’utilisateur correspond au nom d’utilisateur spécifié, où la dernière date d’activité est inférieur à ou égal à spécifié **DateTime**, et où le nom d’application correspond à la **ApplicationName** valeur de propriété. Le **ProfileAuthenticationOption** paramètre spécifie si seuls les profils anonymes, les profils authentifiés, ou tous les profils doivent être retournés. Si votre source de données prend en charge les fonctions de recherche supplémentaires, telles que des caractères génériques, vous pouvez fournir des fonctions de recherche plus étendues pour les noms d’utilisateur. Les résultats retournés par la **FindInactiveProfilesByUserName** méthode sont limitées par les index de la page et les valeurs de taille de page. La valeur de taille de page spécifie le nombre maximal de **ProfileInfo** objets à retourner dans le **ProfileInfoCollection**. La valeur d’index de page spécifie la page de résultats à retourner, 1 correspondant à la première page. Le paramètre de nombre total d’enregistrements est un paramètre de sortie (vous pouvez utiliser **ByRef** en Visual Basic) qui est défini sur le nombre total de profils. Par exemple, si le magasin de données contient 13 profils pour l’application et la valeur d’index de page est de 2 pour une taille de page de 5, le **ProfileInfoCollection** retournée contient les sixième à dixième profils. La valeur du nombre total d’enregistrements est définie sur 13 lorsque la méthode retourne. |
| GetNumberOfInActiveProfiles (méthode) | Prend comme entrée un **ProfileAuthenticationOption** valeur et un **DateTime** de l’objet et retourne le nombre de tous les profils dans la source de données où la dernière date d’activité est inférieure ou égale à la spécifié **DateTime** et où le nom d’application correspond à la **ApplicationName** valeur de propriété. Le **ProfileAuthenticationOption** paramètre spécifie si seuls les profils anonymes, les profils authentifiés, ou tous les profils sont à compter. |

### <a name="applicationname"></a>ApplicationName

Étant donné que les fournisseurs de profils stockent les informations de profil séparément pour chaque application, vous devez vous assurer que votre schéma de données inclut le nom de l’application et que les requêtes et les mises à jour incluent également le nom de l’application. Par exemple, la commande suivante est utilisée pour récupérer une valeur de propriété à partir d’une base de données basée sur le nom d’utilisateur et si le profil est anonyme et garantit que le **ApplicationName** valeur est incluse dans la requête.

[!code-sql[Main](profiles-themes-and-web-parts/samples/sample10.sql)]

## <a name="aspnet-themes"></a>Thèmes ASP.NET

## <a name="what-are-aspnet-20-themes"></a>Quelles sont les thèmes ASP.NET 2.0 ?

Un des aspects plus importants d’une application Web est une apparence cohérente entre le site. Les développeurs ASP.NET 1.x utilisent généralement des feuilles de Style en cascade (CSS) pour implémenter une apparence et convivialité cohérentes. ASP.NET 2.0 thèmes améliorent considérablement CSS, car ils permettent au développeur d’ASP.NET pour définir l’apparence des contrôles serveur ASP.NET, ainsi que des éléments HTML. Thèmes ASP.NET peuvent être appliqués à des contrôles individuels, une page Web spécifique ou un ensemble de l’application Web. Thèmes utilisent une combinaison des fichiers CSS, un fichier d’apparence facultatif et un répertoire d’Images facultatif si les images sont nécessaires. Le fichier d’apparence contrôle l’apparence visuelle des contrôles serveur ASP.NET.

## <a name="where-are-themes-stored"></a>Où sont stockées des thèmes ?

L’emplacement où sont stockés les thèmes diffère en fonction de leur étendue. Les thèmes qui peuvent être appliquées à n’importe quelle application sont stockés dans le dossier suivant :

`C:\WINDOWS\Microsoft.NET\Framework\v2.x.xxxxx\ASP.NETClientFiles\Themes\<Theme_Name>`

Un thème est spécifique à une application particulière est stocké dans une application\_thèmes\&lt ; Thème\_nom&gt; répertoire à la racine du site Web.

> [!NOTE]
> Un fichier d’apparence doit modifier uniquement les propriétés du contrôle serveur qui affectent l’apparence.

Un thème global est un thème peut être appliqué à toute application ou un site Web en cours d’exécution sur le serveur Web. Ces thèmes sont stockés par défaut dans le répertoire ASP.NETClientfiles\Themes qui se trouve dans le répertoire v2.x.xxxxx. Vous pouvez également déplacer les fichiers de thème dans le compte aspnet\_/système client\_web / [version] /Themes/ [thème\_nom] dossier à la racine de votre site Web.

Les thèmes spécifiques à l’application peuvent uniquement être appliquées à l’application dans lesquels résident les fichiers. Ces fichiers sont stockés dans l’application\_thèmes /&lt;thème\_nom&gt; répertoire à la racine du site Web.

## <a name="the-components-of-a-theme"></a>Les composants d’un thème

Un thème est constitué d’un ou plusieurs fichiers CSS, un fichier d’apparence facultatif et un dossier Images facultatif. Les fichiers CSS peuvent être n’importe quel nom que vous souhaitez (default.css ou Theme.CSS, etc.) et devez se trouver dans la racine du dossier de thèmes. Les fichiers CSS permettent de définir les classes CSS ordinaires et les attributs de sélecteurs spécifiques. Pour appliquer une des classes CSS à un élément de page, le **CSSClass** propriété est utilisée.

Le fichier d’apparence est un fichier XML qui contient les définitions de propriété pour les contrôles serveur ASP.NET. Le code ci-dessous est un exemple de fichier d’apparence.

[!code-aspx[Main](profiles-themes-and-web-parts/samples/sample11.aspx)]

**Figure 1** ci-dessous montre une page ASP.NET petite consultés sans un thème appliqué. **Figure 2** montre le même fichier avec un thème appliqué. La couleur d’arrière-plan et la couleur du texte sont configurés via un fichier CSS. L’apparence du bouton et la zone de texte sont configurés à l’aide du fichier d’apparence répertorié ci-dessus.


![Aucun thème](profiles-themes-and-web-parts/_static/image1.gif)

**Figure 1**: aucun thème


![Thème appliqué](profiles-themes-and-web-parts/_static/image2.gif)

**Figure 2**: thème appliqué


Le fichier d’apparence répertorié ci-dessus définit une apparence par défaut pour tous les contrôles de zone de texte et les contrôles de bouton. Cela signifie que chaque zone de texte et le contrôle bouton inséré sur une page prendra sur cet aspect. Vous pouvez également définir une apparence qui peut être appliquée à des instances spécifiques de ces contrôles à l’aide de la **SkinID** propriété du contrôle.

Le code ci-dessous définit une apparence d’un contrôle bouton. Contrôles de bouton uniquement avec un **SkinID** propriété du **goButton** aura l’apparence de l’apparence.

[!code-aspx[Main](profiles-themes-and-web-parts/samples/sample12.aspx)]

Vous ne pouvez avoir une apparence par défaut par le type de contrôle de serveur. Si vous avez besoin des apparences supplémentaires, vous devez utiliser la propriété SkinID.

## <a name="applying-themes-to-pages"></a>Appliquer des thèmes à des Pages

Un thème peut être appliqué à l’aide de l’une des méthodes suivantes :

- Dans le &lt;pages&gt; élément du fichier web.config
- Dans la @Page directive d’une page
- Par programmation

## <a name="applying-a-theme-in-the-configuration-file"></a>Appliquer un thème dans le fichier de Configuration

Pour appliquer un thème dans le fichier de configuration d’applications, utilisez la syntaxe suivante :

[!code-xml[Main](profiles-themes-and-web-parts/samples/sample13.xml)]

Le nom du thème spécifié ici doit correspondre au nom du dossier de thèmes. Ce dossier peut exister dans l’un des emplacements indiqués plus haut dans ce cours. Si vous essayez d’appliquer un thème qui n’existe pas, une erreur de configuration se produira.

## <a name="applying-a-theme-in-the-page-directive"></a>Appliquer un thème dans la Directive de Page

Vous pouvez également appliquer un thème dans la directive @ Page. Cette méthode vous permet d’utiliser un thème pour une page spécifique.

Pour appliquer un thème dans le @Page directive, utilisez la syntaxe suivante :

[!code-aspx[Main](profiles-themes-and-web-parts/samples/sample14.aspx)]

Une fois encore, le thème spécifié ici doit correspondre au dossier des thèmes comme mentionné précédemment. Si vous essayez d’appliquer un thème qui n’existe pas, un échec de génération se produit. Visual Studio également mettre en surbrillance de l’attribut et vous avertir qu’Aucun thème n’existe.

## <a name="applying-a-theme-programmatically"></a>Application d’un thème par programmation

Pour appliquer un thème par programme, vous devez spécifier le **thème** propriété pour la page dans le **Page\_PreInit** (méthode).

Pour appliquer un thème par programme, utilisez la syntaxe suivante :

[!code-csharp[Main](profiles-themes-and-web-parts/samples/sample15.cs)]

Il est nécessaire appliquer le thème dans la méthode PreInit en raison du cycle de vie de page. Si vous l’appliquez à ce stade, le thème de pages est ont déjà été appliqué par le runtime et une modification à ce stade est trop tard dans le cycle de vie. Si vous appliquez un thème qui n’existe pas, un **HttpException** se produit. Un thème est appliqué par programmation, un avertissement de génération se produit si des contrôles serveur ont une propriété SkinID spécifiée. Cet avertissement est destiné à vous informer qu’aucun thème n’est appliquée de façon déclarative et il peut être ignoré.

## <a name="exercise-1--applying-a-theme"></a>Exercice 1 : Application d’un thème

Dans cet exercice, vous allez appliquer un thème ASP.NET à un site Web.

> [!IMPORTANT]
> Si vous utilisez Microsoft Word pour entrer des informations dans un fichier d’apparence, assurez-vous que vous ne remplacez pas les guillemets par des guillemets. Guillemets entraînerait des problèmes avec les fichiers d’apparence.

1. Créez un site web ASP.NET.
2. Avec le bouton droit sur le projet dans l’Explorateur de solutions et choisissez Ajouter un nouvel élément.
3. Choisissez le fichier de Configuration Web à partir de la liste des fichiers et cliquez sur Ajouter.
4. Avec le bouton droit sur le projet dans l’Explorateur de solutions et choisissez Ajouter un nouvel élément.
5. Choisissez le fichier d’apparence et cliquez sur Ajouter.
6. Cliquez sur Oui lorsque le système demande si vous souhaitez placer le fichier à l’intérieur de l’application\_dossier thèmes.
7. Avec le bouton droit sur le dossier SkinFile à l’intérieur de l’application\_dossier thèmes dans l’Explorateur de solutions et choisissez Ajouter un nouvel élément.
8. Choisissez la feuille de Style à partir de la liste des fichiers et cliquez sur Ajouter. Vous avez maintenant tous les fichiers nécessaires à l’implémentation de votre nouveau thème. Toutefois, Visual Studio a nommée votre dossier de thèmes SkinFile. Avec le bouton droit sur ce dossier et remplacez le nom CoolTheme.
9. Ouvrez le fichier SkinFile.skin et ajoutez le code suivant la fin du fichier : 

    [!code-aspx[Main](profiles-themes-and-web-parts/samples/sample16.aspx)]
10. Enregistrez le fichier SkinFile.skin.
11. Ouvrez le StyleSheet.css.
12. Remplacez tout le texte qu’elle contient les éléments suivants : 

    [!code-css[Main](profiles-themes-and-web-parts/samples/sample17.css)]
13. Enregistrez le fichier StyleSheet.css.
14. Ouvrez la page Default.aspx.
15. Ajouter un contrôle de zone de texte et un contrôle bouton.
16. Enregistrez la page. Maintenant, recherchez la page Default.aspx. Il doit afficher comme un formulaire Web standard.
17. Ouvrez le fichier web.config.
18. Ajoutez le code suivant directement sous l’ouverture `<system.web>` balise : 

    [!code-xml[Main](profiles-themes-and-web-parts/samples/sample18.xml)]
19. Enregistrez le fichier web.config. Maintenant, recherchez la page Default.aspx. Il doit afficher avec le thème appliqué.
20. Si elle n’est pas déjà ouverte, ouvrez la page Default.aspx dans Visual Studio.
21. Sélectionnez le bouton.
22. Modifier la **SkinID** propriété goButton. Notez que Visual Studio fournit une liste déroulante avec les valeurs SkinID valides pour un contrôle bouton.
23. Enregistrez la page. Maintenant afficher un aperçu de la page dans votre navigateur à nouveau. Le bouton doit à présent indiquer « go » et doit être plus large en apparence.

À l’aide de la **SkinID** propriété, vous pouvez facilement configurer différentes apparences pour plusieurs instances d’un type particulier de contrôle serveur.

## <a name="the-stylesheettheme-property"></a>La propriété StyleSheetTheme

Jusqu'à présent, nous avons parlé uniquement appliquant des thèmes à l’aide de la propriété de thème. Lorsque vous utilisez la propriété de thème, le fichier d’apparence remplaceront les paramètres déclaratives pour les contrôles serveur. Par exemple, dans l’exercice 1, vous avez spécifié un SkinID de « goButton » pour le contrôle bouton et qui modifié le texte du bouton pour « go ». Vous avez peut-être remarqué que la propriété de texte du bouton dans le concepteur a été définie sur « Bouton », mais le thème a substitué qui. Le thème remplacent toujours les paramètres de propriété dans le concepteur.

Si vous voulez être en mesure de substituer les propriétés définies dans le fichier d’apparence du thème avec les propriétés spécifiées dans le concepteur, vous pouvez utiliser la **StyleSheetTheme** propriété au lieu de la propriété de thème. La propriété StyleSheetTheme est identique à la propriété de thème, sauf qu’il ne remplace pas tous les paramètres de propriété explicites comme le fait de la propriété de thème.

Pour le voir en action, ouvrez le fichier web.config du projet dans l’exercice 1 et modifier la &lt;pages&gt; élément à ce qui suit :

[!code-xml[Main](profiles-themes-and-web-parts/samples/sample19.xml)]

Maintenant rechercher la page Default.aspx et vous verrez que le contrôle a une propriété de texte « Bouton » à nouveau. C’est parce que le paramètre de propriété explicite dans le concepteur substitue la propriété de texte définie par le goButton SkinID.

## <a name="overriding-themes"></a>Substitution de thèmes

Un thème global peut être substitué en appliquant un thème portant le même nom dans l’application\_dossier thèmes de l’application. Toutefois, le thème n’est pas appliqué dans un scénario de remplacement de la valeur true. Si l’exécution rencontre les fichiers de thème dans l’application\_dossier thèmes, il applique le thème à l’aide de ces fichiers et ignore le thème global.

La propriété StyleSheetTheme est substituable et peut être remplacée dans le code comme suit :

[!code-csharp[Main](profiles-themes-and-web-parts/samples/sample20.cs)]

## <a name="web-parts"></a>WebParts

Composants WebPart ASP.NET est un ensemble de contrôles de création de sites Web qui permettent aux utilisateurs finaux de modifier le contenu, l’apparence et comportement de pages Web directement à partir d’un navigateur. Les modifications peuvent être appliquées à tous les utilisateurs sur le site ou à des utilisateurs individuels. Lorsque les utilisateurs modifient des pages et des contrôles, les paramètres peuvent être enregistrés pour conserver les préférences d’un utilisateur via de futures sessions de navigateur, une fonctionnalité appelée personnalisation. Ces fonctionnalités WebPart signifient que les développeurs peuvent autoriser des utilisateurs finaux à personnaliser une application Web dynamique, sans intervention de l’administrateur ou de développeur.

Le jeu de composants WebPart, en tant que développeur permettent aux utilisateurs finaux de :

- Personnaliser le contenu de la page. Les utilisateurs peuvent ajouter de nouveaux contrôles WebPart à une page, les supprimer, les masquer ou les réduire comme des fenêtres ordinaires.
- Personnaliser la mise en page. Les utilisateurs peuvent faire glisser un contrôle WebPart à une zone différente sur une page ou modifier ses propriétés, l’apparence et le comportement.
- Exporter et importer des contrôles. Les utilisateurs peuvent importer ou exporter les paramètres de contrôle WebPart pour une utilisation dans d’autres pages ou les sites, en conservant les propriétés, l’apparence et même les données dans les contrôles. Cela réduit les demandes de données entrée et la configuration sur les utilisateurs finaux.
- Créer des connexions. Les utilisateurs peuvent établir des connexions entre les contrôles, afin que, par exemple, un contrôle de graphique peut afficher un graphique des données dans un contrôle de cotations boursières. Les utilisateurs pourraient personnaliser non seulement la connexion elle-même, mais l’apparence et les détails de la façon dont le contrôle chart affiche les données.
- Gérer et personnaliser les paramètres au niveau du site. Les utilisateurs autorisés peuvent configurer des paramètres au niveau du site, déterminer qui peut accéder à un site ou une page, définir l’accès en fonction du rôle à des contrôles et ainsi de suite. Par exemple, un utilisateur dans un rôle d’administrateur peut définir un contrôle de composants WebPart à être partagé par tous les utilisateurs et empêcher les utilisateurs qui ne sont pas des administrateurs de personnaliser le contrôle partagé.

Vous utiliserez généralement avec les composants WebPart de trois manières : création de pages qui utilisent des contrôles WebPart, la création de contrôles WebPart individuels ou la création d’applications Web complètes, personnalisables, par exemple un portail.

## <a name="page-development"></a>Développement de page

Les développeurs de page peuvent utiliser les outils de design visuel tel que Microsoft Visual Studio 2005 pour créer des pages qui utilisent des composants WebPart. L’un des avantages à l’aide d’un outil tel que Visual Studio est que le jeu de contrôles WebPart fournit des fonctionnalités de création de glisser-déplacer et la configuration de contrôles WebPart dans un concepteur visuel. Par exemple, vous pouvez utiliser le concepteur pour faire glisser une zone WebPart ou un contrôle WebPart de l’éditeur, l’aire de conception et ensuite configurer le contrôle à droite dans le concepteur à l’aide de l’interface utilisateur fournie par les composants WebPart jeu de contrôles. Vous pouvez accélérer le développement des applications de composants WebPart et réduire la quantité de code à écrire.

## <a name="control-development"></a>Développement de contrôles

Vous pouvez utiliser n’importe quel contrôle ASP.NET existant comme un contrôle WebPart, y compris les contrôles serveur Web standard, les contrôles serveur personnalisés et les contrôles utilisateur. Pour optimiser le contrôle par programmation de votre environnement, vous pouvez également créer des contrôles WebPart personnalisés qui dérivent de la classe de composant WebPart. Pour le développement de contrôle des composants WebPart, vous serez en général, soit créer un contrôle utilisateur et l’utiliser comme un contrôle WebPart ou développer un contrôle personnalisé de composants WebPart.

Par exemple de développement d’un contrôle personnalisé de composants WebPart, vous pouvez créer un contrôle pour fournir les fonctionnalités fournies par d’autres contrôles serveur ASP.NET qui peuvent être utiles de package comme un contrôle WebPart personnalisable : calendriers, des listes, des informations financières, News, calculatrices, contrôles de texte enrichi pour mettre à jour des grilles de contenu, modifiable qui se connectent aux bases de données, graphiques qui leurs affichages, de mettre à jour dynamiquement ou météo et informations de voyage. Si vous fournissez un concepteur visuel avec votre contrôle, puis les développeurs de page à l’aide de Visual Studio peuvent simplement faites glisser votre contrôle dans une zone WebPart et configurer au moment du design sans devoir écrire du code supplémentaire.

La personnalisation est le fondement de la fonctionnalité des composants WebPart. Il permet aux utilisateurs de modifier ou de personnaliser la disposition, l’apparence et comportement de contrôles WebPart sur une page. Les paramètres personnalisés sont durables : qu’ils sont rendus persistants non seulement pendant la session de navigateur actuelle (comme avec l’état d’affichage), mais également dans le stockage à long terme, afin que les paramètres d’un utilisateur pour les sessions ultérieures du navigateur. Personnalisation est activée par défaut pour les pages WebPart.

Les composants structurels d’interface utilisateur comptent sur la personnalisation et fournissent la structure de base et les services requis par tous les contrôles WebPart. Un composant structurel d’interface utilisateur requis sur chaque page WebPart est le contrôle WebPartManager. Bien que jamais visible, ce contrôle a la tâche critique de coordonner tous les contrôles WebPart sur une page. Par exemple, il effectue le suivi de tous les contrôles WebPart individuels. Il gère des zones WebPart (régions qui contiennent des contrôles WebPart sur une page), et qui sont des contrôles dans les zones. Il effectue le suivi et contrôle les différents modes d’affichage qu'une page peut être dans, Parcourir, de vous connecter, de modifier ou de catalogue, et si les modifications de personnalisation s’appliquent à tous les utilisateurs ou à des utilisateurs individuels. Enfin, il lance et effectue le suivi des connexions et la communication entre les contrôles WebPart.

Le deuxième type de composant structurel d’interface utilisateur est la zone. Zones agissent en tant que gestionnaires de présentation sur une page WebPart. Ils contiennent et organisent les contrôles qui dérivent de la classe du composant (contrôles part) et fournissent la possibilité d’effectuer la mise en page modulaires en orientation horizontale ou verticale. Les zones offrent également des éléments d’interface utilisateur communes et cohérentes (par exemple, le style d’en-tête et pied de page, titre, style de bordure, des boutons d’action et ainsi de suite) pour chaque contrôle qu’elles contiennent, ces éléments en commun sont appelées le chrome d’un contrôle. Plusieurs types spécialisés de zones sont utilisées dans les différents modes d’affichage et avec différents contrôles. Les différents types de zones sont décrites dans la section contrôles WebPart essentiels ci-dessous.

Les contrôles WebPart d’interface utilisateur, qui dérivent de la **partie** de classe, composent l’interface utilisateur principale sur une page WebPart. Le jeu de contrôles WebPart est flexible et inclus dans les options proposées pour la création de contrôles WebPart. En plus de créer vos propres contrôles WebPart personnalisés, vous pouvez également utiliser des contrôles serveur ASP.NET existants, des contrôles utilisateur ou des contrôles serveur personnalisés comme contrôles WebPart. Les contrôles essentiels qui sont couramment utilisés pour créer des pages de composants WebPart sont décrits dans la section suivante.

## <a name="web-parts-essential-controls"></a>Contrôles WebPart essentiels

Le jeu de contrôles WebPart est complet, mais certains contrôles sont essentiels, car ils sont requis pour les composants WebPart travailler, ou parce qu’ils sont les contrôles les plus courants sur les pages WebPart. Comme vous commencez à l’aide de composants WebPart et création de pages de composants WebPart de base, il est utile de se familiariser avec les contrôles WebPart essentiels décrits dans le tableau suivant.

| **Composants WebPart** | **Description** |
| --- | --- |
| WebPartManager | Gère tous les contrôles WebPart sur une page. Un (et un seul) **WebPartManager** contrôle est requis pour chaque page WebPart. |
| CatalogZone | Contient des contrôles CatalogPart. Utilisez cette zone pour créer un catalogue de contrôles WebPart à partir de laquelle les utilisateurs peuvent sélectionner des contrôles à ajouter à une page. |
| EditorZone | Contient des contrôles EditorPart. Utilisez cette zone pour permettre aux utilisateurs de modifier et personnaliser les contrôles WebPart sur une page. |
| WebPartZone | Contient et fournit la disposition générale pour les contrôles WebPart qui composent l’interface utilisateur principale d’une page. Utilisez cette zone lorsque vous créez des pages avec les contrôles WebPart. Les pages peuvent contenir une ou plusieurs zones. |
| ConnectionsZone | Contient des contrôles WebPartConnection et fournit une interface utilisateur pour la gestion des connexions. |
| Composant WebPart (GenericWebPart) | Restitue l’interface utilisateur principale ; la plupart des contrôles d’interface utilisateur WebPart appartiennent à cette catégorie. Pour optimiser le contrôle par programmation, vous pouvez créer des contrôles WebPart personnalisés qui dérivent de la base de **WebPart** contrôle. Vous pouvez également utiliser des contrôles serveur existants, les contrôles utilisateur ou les contrôles personnalisés comme contrôles WebPart. Chaque fois qu’un de ces contrôles sont placé dans une zone, le **WebPartManager** contrôle les encapsule automatiquement avec **GenericWebPart** contrôle au moment de l’exécution afin que vous puissiez les utiliser avec la fonctionnalité des composants WebPart. |
| CatalogPart | Contient une liste de contrôles WebPart disponibles que les utilisateurs peuvent ajouter à la page. |
| WebPartConnection | Crée une connexion entre deux contrôles WebPart sur une page. La connexion définit un des contrôles WebPart en tant que fournisseur (de données) et l’autre comme un consommateur. |
| EditorPart | Sert de classe de base pour les contrôles d’édition spécialisés. |
| Contrôles EditorPart (AppearanceEditorPart LayoutEditorPart, BehaviorEditorPart et PropertyGridEditorPart) | Autoriser les utilisateurs à personnaliser différents aspects des contrôles d’interface utilisateur WebPart sur une page |

## <a name="lab-create-a-web-part-page"></a>Atelier pratique : Créer une Page de composants WebPart

Dans cet atelier, vous allez créer une page WebPart qui rend persistantes les informations via les profils ASP.NET.

### <a name="creating-a-simple-page-with-web-parts"></a>Création d’une Page Simple avec composants WebPart

Dans cette partie de la procédure pas à pas, vous créez une page qui utilise des contrôles WebPart pour afficher le contenu statique. La première étape de l’utilisation des composants WebPart est de créer une page avec deux éléments structurels requis. Tout d’abord, une page WebPart requiert un contrôle WebPartManager pour suivre et coordonner tous les contrôles WebPart. En second lieu, une page WebPart a besoin d’une ou plusieurs zones, qui sont des contrôles composites qui contiennent le composant WebPart ou autres contrôles serveur et occupent une région spécifiée d’une page.

> [!NOTE]
> Vous n’avez pas besoin de faire quelque chose pour activer la personnalisation des WebParts ; Il est activé par défaut pour le jeu de composants WebPart. Lorsque vous exécutez tout d’abord une page WebPart sur un site, ASP.NET définit un fournisseur de personnalisations par défaut pour stocker les paramètres de personnalisation utilisateur. Pour plus d’informations sur la personnalisation, consultez Vue d’ensemble de personnalisation de parties Web.


### <a name="to-create-a-page-for-containing-web-parts-controls"></a>Pour créer une page contenant des contrôles WebPart

1. Fermez la page par défaut et ajouter une nouvelle page au site nommé WebPartsDemo.aspx.
2. Basculez vers **conception** vue.
3. À partir de la **vue** menu, assurez-vous que le **contrôles Non visuels** et **détails** options sont sélectionnées afin de voir les balises de disposition et les contrôles qui n’ont pas d’une interface utilisateur.
4. Placez le point d’insertion avant le  **&lt;div&gt;**  balises sur l’aire de conception et appuyez sur ENTRÉE pour ajouter une nouvelle ligne. Placez le point d’insertion avant le caractère de nouvelle ligne, cliquez sur le **Format du bloc** dans le menu de contrôle de liste déroulante, puis sélectionnez le **titre 1** option. Dans l’en-tête, ajoutez le texte **Page de démonstration WebPart**.
5. À partir de la **WebParts** onglet de la boîte à outils, faites glisser un **WebPartManager** contrôle sur la page, en plaçant juste après le caractère de nouvelle ligne et avant la  **&lt;div&gt;**  balises.   
  
 Le **WebPartManager** contrôle ne restitue une sortie, donc elle apparaît sous la forme d’une zone grise sur l’aire du concepteur.
6. Placez le point d’insertion dans la  **&lt;div&gt;**  balises.
7. Dans le **disposition** menu, cliquez sur **insérer un tableau**et créer une table qui comporte une ligne et trois colonnes. Cliquez sur le **propriétés de cellule** bouton, sélectionnez **haut** à partir de la **Alignement Vertical** la liste déroulante, cliquez sur **OK**et cliquez sur **OK** à nouveau pour créer la table.
8. Dans la colonne de table de gauche, faites glisser un contrôle WebPartZone. Cliquez sur le **WebPartZone** contrôler, choisissez **propriétés**et définissez les propriétés suivantes :   
  
 ID : SidebarZone   
  
 HeaderText : barre latérale
9. Faites glisser un deuxième **WebPartZone** un contrôle dans la colonne de table intermédiaire et définissez les propriétés suivantes :   
  
 ID : MainZone   
  
 HeaderText : principal
10. Enregistrez le fichier.

Votre page dispose maintenant de deux zones distinctes que vous pouvez contrôler séparément. Toutefois, aucune zone n’a aucun contenu, afin de la création de contenu est l’étape suivante. Pour cette procédure pas à pas, vous travaillez avec les contrôles WebPart qui affichent uniquement le contenu statique.

La disposition d’une zone WebPart est spécifiée par un  **&lt;zonetemplate&gt;**  élément. Dans le modèle de zone, vous pouvez ajouter n’importe quel contrôle ASP.NET, s’il s’agit d’un contrôle WebPart personnalisé, un contrôle utilisateur ou un contrôle serveur existant. Notez qu’ici, vous utilisez le contrôle d’étiquette, et que vous ajoutez simplement un texte statique. Lorsque vous placez un contrôle serveur classique dans une **WebPartZone** zone, ASP.NET traite le contrôle comme un contrôle WebPart au moment de l’exécution, ce qui active des fonctions WebPart sur le contrôle.

**Pour créer du contenu pour la zone principale**

1. Dans **conception** afficher, faites glisser un **étiquette** contrôle depuis la **Standard** onglet de la boîte à outils dans la zone de contenu de la zone dont **ID** propriété a la valeur MainZone.
2. Basculez vers **Source** vue. Notez qu’un  **&lt;zonetemplate&gt;**  élément a été ajouté pour encapsuler le **étiquette** contrôle dans MainZone.
3. Ajoutez un attribut nommé **titre** à la  **&lt;asp : label&gt;**  élément et définissez sa valeur sur le contenu. Supprimez le texte = attribut « Label » à partir de la  **&lt;asp : label&gt;**  élément. Entre les balises d’ouverture et fermeture de la  **&lt;asp : label&gt;**  élément, ajoutez du texte tel que **Bienvenue sur la Page d’accueil** au sein d’une paire de  **&lt;h2 &gt;**  balises d’élément. Votre code doit se présenter comme suit. 

    [!code-aspx[Main](profiles-themes-and-web-parts/samples/sample21.aspx)]
4. Enregistrez le fichier.

Ensuite, créez un contrôle utilisateur qui peut également être ajouté à la page, comme un contrôle WebPart.

### <a name="to-create-a-user-control"></a>Pour créer un contrôle utilisateur

1. Ajouter le nouveau contrôle utilisateur Web à votre site comme un contrôle de recherche. Désélectionnez l’option **placer le code source dans un fichier distinct**. Ajoutez-le dans le même répertoire que la page WebPartsDemo.aspx et nommez-le SearchUserControl.ascx.   
  
    > [!NOTE]
    > Le contrôle utilisateur pour cette procédure pas à pas n’implémente pas les fonctionnalités de recherche réelles ; Il est utilisé uniquement pour illustrer des fonctionnalités WebPart.
2. Basculez vers **conception** vue. À partir de la **Standard** onglet de la boîte à outils, faites glisser un contrôle de zone de texte sur la page.
3. Placez le curseur après la zone de texte que vous venez d’ajouter et appuyez sur ENTRÉE pour ajouter une nouvelle ligne.
4. Faites glisser un contrôle de bouton sur la page sur la nouvelle ligne au-dessous de la zone de texte que vous venez d’ajouter.
5. Basculez vers **Source** vue. Assurez-vous que le code source pour le contrôle utilisateur ressemble à l’exemple suivant. 

    [!code-aspx[Main](profiles-themes-and-web-parts/samples/sample22.aspx)]
6. Enregistrez et fermez le fichier.

Maintenant, vous pouvez ajouter des contrôles WebPart à la zone de la barre latérale. Vous ajoutez deux contrôles à la zone de la barre latérale, chacune contenant une liste de liens et un autre qui est le contrôle utilisateur que vous avez créé dans la procédure précédente. Les liens sont ajoutés en tant que norme **étiquette** contrôle serveur, semblable à la façon dont vous avez créé le texte statique pour la zone principale. Toutefois, bien que les contrôles serveur contenus dans le contrôle utilisateur peut être contenu directement dans la zone (comme le contrôle label), dans ce cas ils ne sont pas. À la place, ils font partie du contrôle utilisateur que vous avez créé dans la procédure précédente. Cela montre une méthode courante d’empaqueter tous les contrôles et fonctionnalités supplémentaires que vous souhaitez dans un contrôle utilisateur, puis de référencer ce contrôle dans une zone comme un contrôle WebPart.

Au moment de l’exécution, le jeu de composants WebPart inclut des contrôles avec des contrôles de GenericWebPart. Lorsqu’un **GenericWebPart** contrôle encapsule un contrôle serveur Web, le contrôle part générique est le contrôle parent et vous pouvez accéder au contrôle serveur via la propriété de ChildControl du contrôle parent. Cette utilisation de contrôles WebPart générique permet à des contrôles serveur Web standard d’avoir le même comportement de base et les attributs en tant que les contrôles WebPart qui dérivent de la **WebPart** classe.

### <a name="to-add-web-parts-controls-to-the-sidebar-zone"></a>Pour ajouter des contrôles WebPart à la zone de la barre latérale

1. Ouvrez la page WebPartsDemo.aspx.
2. Basculez vers **conception** vue.
3. Faites glisser la page de contrôle utilisateur que vous avez créé, SearchUserControl.ascx, de **l’Explorateur de solutions** dans la zone dont **ID** propriété a la valeur SidebarZone et placez-le à cet endroit.
4. Enregistrez la page WebPartsDemo.aspx.
5. Basculez vers **Source** vue.
6. À l’intérieur de la  **&lt;asp : webpartzone&gt;**  , élément pour SidebarZone, juste au-dessus de la référence à votre contrôle utilisateur, ajoutez un  **&lt;asp : label&gt;**  élément avec des liens de relation contenant-contenus, comme indiqué dans l’exemple suivant. En outre, ajoutez un **titre** la balise du contrôle utilisateur, avec une valeur de l’attribut **recherche**, comme indiqué. 

    [!code-aspx[Main](profiles-themes-and-web-parts/samples/sample23.aspx)]
7. Enregistrez et fermez le fichier.

Vous pouvez maintenant tester votre page en parcourant dans votre navigateur. La page affiche les deux zones. La capture d’écran suivante montre la page.

**Page de démonstration WebPart avec deux zones**


![Capture d’écran de procédure pas à pas 1 Web VS des WebParts](profiles-themes-and-web-parts/_static/image3.gif)

**Figure 3**: Web VS des WebParts capture d’écran procédure pas à pas 1


Dans le titre de la barre de chaque contrôle est une flèche vers le bas qui permet d’accéder à un menu de verbes d’actions disponibles, que vous pouvez effectuer sur un contrôle. Cliquez sur le menu des verbes pour un des contrôles, puis cliquez sur le **réduire** verbe et notez que le contrôle est réduit. Dans le menu d’actions verbales, cliquez sur **restaurer**, et le contrôle retourne à sa taille normale.

### <a name="enabling-users-to-edit-pages-and-change-layout"></a>Permettre aux utilisateurs de modifier des Pages et de modifier la disposition

Composants WebPart permet aux utilisateurs de modifier la disposition des contrôles WebPart en les faisant glisser à partir d’une zone à l’autre. En plus de permettre aux utilisateurs de déplacer **WebPart** contrôles d’une zone à l’autre, vous pouvez autoriser les utilisateurs de modifier différentes caractéristiques des contrôles, y compris leur apparence, la disposition et le comportement. Fournit des fonctionnalités de modification de base pour le jeu de contrôles WebPart **WebPart** contrôles. Bien que vous n’aurez pas à le faire dans cette procédure pas à pas, vous pouvez également créer des contrôles d’édition personnalisés qui permettent aux utilisateurs de modifier les fonctionnalités de **WebPart** contrôles. Comme la modification de l’emplacement d’un **WebPart** (contrôle), modifier les propriétés d’un contrôle s’appuie sur la personnalisation ASP.NET pour enregistrer les modifications apportées par les utilisateurs.

Dans cette partie de la procédure pas à pas, vous ajoutez la possibilité aux utilisateurs de modifier les caractéristiques de n’importe quel **WebPart** contrôle sur la page. Pour activer ces fonctionnalités, vous ajoutez un autre contrôle utilisateur personnalisé à la page, avec une  **&lt;asp : editorzone&gt;**  élément et deux contrôles d’édition.

### <a name="to-create-a-user-control-that-enables-changing-page-layout"></a>Pour créer un contrôle utilisateur qui permet la modification de mise en page

1. Dans Visual Studio, sur le **fichier** menu, sélectionnez le **nouveau** sous-menu, puis cliquez sur le **fichier** option.
2. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez **contrôle utilisateur Web**. Nommez le nouveau fichier DisplayModeMenu.ascx. Désélectionnez l’option **placer le code source dans un fichier distinct**.
3. Cliquez sur Ajouter pour créer le nouveau contrôle.
4. Basculez vers **Source** vue.
5. Supprimer tout le code existant dans le nouveau fichier et collez le code suivant. Ce code de contrôle utilisateur utilise des fonctionnalités du jeu de composants WebPart qui permettent à une page de modifier son mode d’affichage et vous permet également de modifier l’apparence physique et disposition de la page se trouvent dans certains modes d’affichage. 

    [!code-aspx[Main](profiles-themes-and-web-parts/samples/sample24.aspx)]
6. Enregistrez le fichier en cliquant sur l’enregistrement icône dans la barre d’outils ou en sélectionnant **enregistrer** sur la **fichier** menu.

### <a name="to-enable-users-to-change-the-layout"></a>Pour permettre aux utilisateurs de modifier la disposition

1. Ouvrez la page WebPartsDemo.aspx et basculer vers **conception** vue.
2. Placez le point d’insertion dans la **conception** afficher juste après le **WebPartManager** contrôle que vous avez ajouté précédemment. Ajoute un retour chariot après le texte pour qu’il y a un saut de ligne après le **WebPartManager** contrôle. Placez le point d’insertion sur la ligne vide.
3. Faites glisser le contrôle utilisateur que vous venez de créer (le fichier est nommé DisplayModeMenu.ascx) dans le WebPartsDemo.aspx page et déposez-la sur la ligne vide.
4. Faites glisser un contrôle EditorZone à partir de la **WebParts** section de la boîte à outils vers la cellule d’ouvrir la table restante dans la page WebPartsDemo.aspx.
5. À partir de la **WebParts** section de la boîte à outils, faites glisser un contrôle AppearanceEditorPart et un contrôle LayoutEditorPart dans le **EditorZone** contrôle.
6. Basculez vers **Source** vue. Le code résultant dans la cellule de table doit ressembler au code suivant. 

    [!code-aspx[Main](profiles-themes-and-web-parts/samples/sample25.aspx)]
7. Enregistrez le fichier WebPartsDemo.aspx. Vous avez créé un contrôle utilisateur qui vous permet de modifier les modes d’affichage et de modifier la mise en page, et vous avez référencé le contrôle sur la page Web principal.

Vous pouvez maintenant tester la capacité de modifier des pages et mise en page.

### <a name="to-test-layout-changes"></a>Pour tester les modifications de disposition

1. Charger la page dans un navigateur.
2. Cliquez sur le **Mode d’affichage** menu déroulant, puis sélectionnez **modifier**. Les titres de zone sont affichés.
3. Faites glisser le **Mes liens** contrôle par sa barre de titre de la zone de la barre latérale vers le bas de la zone principale. Votre page doit ressembler à la capture d’écran suivante.

### <a name="web-parts-demo-page-with-my-links-control-moved"></a>Page de démonstration WebPart avec contrôle de mes liens déplacé


![Capture d’écran de procédure pas à pas 2 Web VS des WebParts](profiles-themes-and-web-parts/_static/image4.gif)

**Figure 4**: Web VS des WebParts capture d’écran procédure pas à pas 2


1. Cliquez sur le **Mode d’affichage** menu déroulant, puis sélectionnez **Parcourir**. La page est actualisée, les noms de zones disparaissent et le **Mes liens** contrôle reste où vous l’avez positionné.
2. Pour démontrer que la personnalisation fonctionne, fermez le navigateur, puis rechargez la page. Les modifications apportées sont enregistrées pour les futures sessions de navigateur.
3. À partir de la **Mode d’affichage** menu, sélectionnez **modifier**.   
  
 Chaque contrôle sur la page est maintenant affiché avec une flèche vers le bas dans la barre de titre, qui contient la liste déroulante d’actions verbales.
4. Cliquez sur la flèche pour afficher le menu d’actions verbales sur le **Mes liens** contrôle. Cliquez sur le **modifier** verbe.   
  
 Le **EditorZone** contrôle apparaît, affichant le EditorPart contrôle vous avez ajouté.
5. Dans le **apparence** section du contrôle d’édition, modifier le **titre** dans Mes favoris, utilisez le **Type de Chrome** liste déroulante pour sélectionner **titre uniquement**, puis cliquez sur **appliquer**. La capture d’écran suivante montre la page en mode édition.

### <a name="web-parts-demo-page-in-edit-mode"></a>Page de démonstration WebPart en mode édition


![Capture d’écran de procédure pas à pas 3 Web VS des WebParts](profiles-themes-and-web-parts/_static/image5.gif)

**Figure 5**: capture d’écran procédure pas à pas 3 VS des WebParts de Web


1. Cliquez sur le **Mode d’affichage** , puis sélectionnez **Parcourir** pour revenir au mode de navigation.
2. Le contrôle a maintenant un titre mis à jour et aucune bordure, comme indiqué dans la capture d’écran suivante.

### <a name="edited-web-parts-demo-page"></a>Page de démonstration WebPart modifiée


![Capture d’écran de procédure pas à pas 4 Web VS des WebParts](profiles-themes-and-web-parts/_static/image6.gif)

**Figure 4**: capture d’écran procédure pas à pas 4 VS des WebParts de Web


### <a name="adding-web-parts-at-run-time"></a>Ajout de composants WebPart en cours d’exécution

Vous pouvez également autoriser les utilisateurs à ajouter des contrôles WebPart à leur page au moment de l’exécution. Pour ce faire, configurez la page avec un catalogue de composants WebPart, qui contient une liste de contrôles WebPart que vous souhaitez rendre disponibles aux utilisateurs.

**Pour permettre aux utilisateurs d’ajouter des composants WebPart en cours d’exécution**

1. Ouvrez la page WebPartsDemo.aspx et basculer vers **conception** vue.
2. À partir de la **WebParts** onglet de la boîte à outils, faites glisser un contrôle CatalogZone dans la colonne de droite de la table, sous la **EditorZone** contrôle.   
  
 Les deux contrôles peuvent être dans la même cellule de table, car ils seront afficheront pas en même temps.
3. Dans le volet Propriétés, affectez à la chaîne **ajouter des WebParts** à la propriété HeaderText de la **CatalogZone** contrôle.
4. À partir de la **WebParts** section de la boîte à outils, faites glisser un contrôle DeclarativeCatalogPart dans la zone de contenu de la **CatalogZone** contrôle.
5. Cliquez sur la flèche dans le coin supérieur droit de la **DeclarativeCatalogPart** de contrôle pour exposer son menu tâches, puis sélectionnez **modifier les modèles**.
6. À partir de la **Standard** section de la boîte à outils, faites glisser un **FileUpload** contrôle et un **calendrier** un contrôle dans le **WebPartsTemplate** section de la **DeclarativeCatalogPart** contrôle.
7. Basculez vers **Source** vue. Inspecter le code source de la  **&lt;asp : catalogzone&gt;**  élément. Notez que la **DeclarativeCatalogPart** contrôle contient un  **&lt;webpartstemplate&gt;**  élément avec les deux contrôles serveur délimités que vous serez en mesure d’ajouter à votre page. à partir du catalogue.
8. Ajouter un **titre** propriété à chacun des contrôles ajoutés au catalogue, à l’aide de la valeur de chaîne affichée pour chaque titre de l’exemple de code ci-dessous. Même si le titre n’est pas une propriété vous pouvez définir normalement sur ces deux contrôles serveur au moment du design, lorsqu’un utilisateur ajoute ces contrôles à un **WebPartZone** zone à partir du catalogue en cours d’exécution, ils sont chacun inclus dans un  **GenericWebPart** contrôle. Cela leur permet d’agir en tant que contrôles WebPart, par conséquent, ils pourront afficher les titres.   
  
 Le code pour les deux contrôles contenus dans le **DeclarativeCatalogPart** contrôle doit se présenter comme suit. 

    [!code-aspx[Main](profiles-themes-and-web-parts/samples/sample26.aspx)]
9. Enregistrez la page.

Vous pouvez désormais tester le catalogue.

### <a name="to-test-the-web-parts-catalog"></a>Pour tester le catalogue de composants WebPart

1. Charger la page dans un navigateur.
2. Cliquez sur le **Mode d’affichage** menu déroulant, puis sélectionnez **catalogue**.   
  
 Le catalogue intitulé **ajouter des WebParts** s’affiche.
3. Faites glisser le **Mes favoris** contrôler à partir de la zone principale vers le haut de la zone de la barre latérale et placez-le à cet endroit.
4. Dans le **ajouter des WebParts** de catalogue, sélectionnez les deux cases à cocher, puis **principal** à partir de la liste déroulante qui contient les zones disponibles.
5. Cliquez sur **ajouter** dans le catalogue. Les contrôles sont ajoutés à la zone principale. Si vous le souhaitez, vous pouvez ajouter plusieurs instances de contrôles à partir du catalogue à votre page.   
  
 La capture d’écran suivante montre la page avec le contrôle de téléchargement de fichier et le calendrier dans la zone principale. 

![Contrôles ajoutés à la zone Main à partir du catalogue](profiles-themes-and-web-parts/_static/image7.gif)

    **Figure 5**: Controls added to Main zone from the catalog
6. Cliquez sur le **Mode d’affichage** menu déroulant, puis sélectionnez **Parcourir**. Le catalogue disparaît et la page est actualisée.
7. Fermez le navigateur. Recharger la page. Les modifications apportées persistent.
