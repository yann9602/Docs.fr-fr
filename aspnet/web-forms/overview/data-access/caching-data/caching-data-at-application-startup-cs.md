---
uid: web-forms/overview/data-access/caching-data/caching-data-at-application-startup-cs
title: "Mise en cache de données au démarrage de l’Application (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans n’importe quelle application Web certaines données sont fréquemment utilisées, et certaines données seront rarement utilisées. Nous pouvons améliorer les performances de notre b d’application ASP.NET..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/30/2007
ms.topic: article
ms.assetid: 22ca8efa-7cd1-45a7-b9ce-ce6eb3b3ff95
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/caching-data/caching-data-at-application-startup-cs
msc.type: authoredcontent
ms.openlocfilehash: ccf22f9e72777242ca0239aee69045ab03d56960
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="caching-data-at-application-startup-c"></a>Mise en cache de données au démarrage de l’Application (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le PDF](caching-data-at-application-startup-cs/_static/datatutorial60cs1.pdf)

> Dans n’importe quelle application Web certaines données sont fréquemment utilisées, et certaines données seront rarement utilisées. Nous pouvons améliorer les performances de votre application ASP.NET en chargeant les données fréquemment utilisées, une technique appelée à l’avance. Ce didacticiel illustre une approche proactive lors du chargement, qui consiste à charger des données dans le cache au démarrage de l’application.


## <a name="introduction"></a>Introduction

Les deux didacticiels précédents étudié la mise en cache des données dans la présentation et les couches de mise en cache. Dans [mise en cache des données avec ObjectDataSource](caching-data-with-the-objectdatasource-cs.md), nous avons examiné à l’aide de la s ObjectDataSource fonctionnalités en cache des données dans la couche de présentation de la mise en cache. [La mise en cache des données dans l’Architecture](caching-data-in-the-architecture-cs.md) examiné la mise en cache dans une couche séparée mise en cache. Les deux de ces didacticiels utilisés *chargement réactif* dans l’utilisation du cache de données. Avec le réactif de chargement, de chaque fois que les données sont demandées, le système vérifie d’abord si elle s dans le cache. Si ce n’est pas le cas, il extrait les données de la source d’origine, telles que la base de données et les stocke dans le cache. Le principal avantage de chargement réactif est sa simplicité d’implémentation. Un de ses inconvénients est ses performances inégale entre les demandes. Imaginez une page qui utilise la couche de mise en cache du didacticiel précédent pour afficher des informations sur le produit. Lorsque cette page est visitée pour la première fois ou visitée pour la première fois, une fois que les données mises en cache a été supprimées en raison de contraintes de mémoire ou l’expiration spécifiée ayant été atteint, les données doivent être récupérées à partir de la base de données. Par conséquent, ces demandes d’utilisateurs seront plus long que les demandes des utilisateurs qui peuvent être servies par le cache.

*Chargement proactive* fournit une stratégie de gestion du cache de remplacement qui lisse les performances entre les requêtes en chargeant les données mises en cache avant leur s nécessité. En règle générale, le chargement proactive utilise un processus qui vérifie périodiquement ou est averti en cas d’une mise à jour les données sous-jacentes. Ce processus met à jour le cache pour conserver la nouvelle. Chargement proactive est particulièrement utile si les données sous-jacentes provient d’une connexion lente de la base de données, un service Web ou une autre source de données particulièrement lente. Mais cette approche proactive chargement est plus difficile à implémenter, car elle nécessite la création, la gestion et le déploiement d’un processus pour vérifier les modifications et mettre à jour le cache.

Une autre version de chargement proactive et le type que nous allons Explorer dans ce didacticiel, charge les données dans le cache au démarrage de l’application. Cette approche est particulièrement utile pour la mise en cache des données statiques, telles que les enregistrements des tables de recherche de base de données.

> [!NOTE]
> Pour une plus approfondies sur les différences entre proactif et réactif de chargement, ainsi que les listes de professionnels de le, les inconvénients et les recommandations de mise en œuvre, reportez-vous à la [gestion du contenu d’un Cache](https://msdn.microsoft.com/en-us/library/ms978503.aspx) section de la [ Guide d’Architecture pour les Applications .NET Framework de la mise en cache](https://msdn.microsoft.com/en-us/library/ms978498.aspx).


## <a name="step-1-determining-what-data-to-cache-at-application-startup"></a>Étape 1 : Déterminer les données à mettre en Cache au démarrage de l’Application

Les exemples de mise en cache à l’aide de chargement réactives que nous avons examiné dans le travail de deux didacticiels précédents bien avec les données qui peuvent changer régulièrement et ne prendront pas exorbitantly long à générer. Mais si les données mises en cache ne changent jamais, l’expiration utilisée par chargement réactif est superflue. De même, si les données en cours de mise en cache prend un temps extrêmement long pour générer, les utilisateurs dont les demandes de trouver le cache vide devra subir une longue attente alors que les données sous-jacentes sont récupérées. Envisagez la mise en cache des données statiques et les données qui prend énormément de temps pour générer au démarrage de l’application.

Bases de données ont des nombreux dynamiques, valeurs changent fréquemment, la plupart ont également une grande quantité de données statiques. Par exemple, pratiquement tous les modèles de données ont une ou plusieurs colonnes qui contiennent une valeur particulière à partir d’un ensemble fixe de choix. A `Patients` table de base de données peut avoir un `PrimaryLanguage` colonne, dont l’ensemble de valeurs peut être en anglais, espagnol, Français, russe, japonais et ainsi de suite. Souvent, ces types de colonnes sont implémentées à l’aide de *tables de recherche*. Au lieu de stockage de la chaîne en anglais ou Français dans le `Patients` table, une deuxième table est créée avec, généralement, deux colonnes - identificateur unique et une description de chaîne - avec un enregistrement pour chaque valeur possible. Le `PrimaryLanguage` colonne dans la `Patients` table stocke l’identificateur unique correspondant dans la table de recherche. Dans la Figure 1, patient John Doe s principal langue est anglais, tandis que Ed Johnson s est russe.


![La Table de langues est une Table de recherche utilisé par la Table Patients](caching-data-at-application-startup-cs/_static/image1.png)

**Figure 1**: le `Languages` Table est une Table de recherche utilisé par le `Patients` Table


L’interface utilisateur pour la modification ou la création d’un nouveau patient comprend une liste déroulante des langues autorisées remplie par les enregistrements dans la `Languages` table. Sans mise en cache, chaque fois que cette interface est visité le système doit interroger le `Languages` table. Il s’agit superflus et inutiles car les valeurs de table de recherche changent très rarement, voire jamais.

Nous pouvons mettre en cache le `Languages` données en utilisant les mêmes techniques de chargement réactive examinés dans les didacticiels précédents. Réactive le chargement, cependant, utilise une expiration basée sur le temps, ce qui n’est pas nécessaire pour les données de table de recherche statiques. Tandis que l’utilisation du chargement réactif la mise en cache est meilleure que tout aucune mise en cache, la meilleure approche serait de façon proactive charger les données de table de recherche dans le cache au démarrage de l’application.

Dans ce didacticiel, nous allons examiner comment les données du cache de la table de recherche et d’autres informations statiques.

## <a name="step-2-examining-the-different-ways-to-cache-data"></a>Étape 2 : Examiner les différentes façons de mettre en Cache des données

Informations peuvent être mis en cache par programmation dans une application ASP.NET à l’aide de plusieurs méthodes. Nous avons déjà vu comment utiliser le cache de données dans les didacticiels précédents. Vous pouvez également les objets peuvent être par programmation mis en cache à l’aide de *membres statiques* ou *état de l’application*.

Lorsque vous travaillez avec une classe, en général, la classe doit être instanciée avant que ses membres sont accessibles. Par exemple, pour appeler une méthode à partir d’une des classes dans la couche de logique métier, nous devons d’abord créer une instance de la classe :


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample1.cs)]

Avant que nous pouvons appeler *SomeMethod* ou fonctionnent avec *SomeProperty*, nous devons d’abord créer une instance de la classe à l’aide de la `new` (mot clé). *SomeMethod* et *SomeProperty* sont associés à une instance particulière. La durée de vie de ces membres est liée à la durée de vie de leur objet associé. *Les membres statiques*, quant à eux, sont des variables, propriétés et méthodes qui sont partagées entre *tous les* instances de la classe et, par conséquent, ont une durée de vie tant que la classe. Les membres statiques sont signalées par le mot clé `static`.

En plus de membres statiques, données peuvent être mises en cache en utilisant l’état de l’application. Chaque application ASP.NET gère une collection nom/valeur s partagées entre tous les utilisateurs et les pages de l’application. Cette collection est accessible à l’aide de la [ `HttpContext` classe](https://msdn.microsoft.com/en-us/library/system.web.httpcontext.aspx) s [ `Application` propriété](https://msdn.microsoft.com/en-us/library/system.web.httpcontext.application.aspx)et utilisés à partir d’une classe code-behind de la page s ASP.NET comme suit :


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample2.cs)]

Le cache de données fournit une interface plus riche API de mise en cache de données, en offrant des mécanismes pour temporels et dépendance des expirations dans le, les priorités des éléments du cache et ainsi de suite. Avec des membres statiques et l’état de l’application, ces fonctionnalités doivent être ajoutées manuellement par le développeur de pages. Toutefois, lors de la mise en cache des données au démarrage de l’application pour la durée de vie de l’application, les avantages de cache s de données sont hypothétiques. Dans ce didacticiel, nous allons examiner le code qui utilise ces trois techniques de mise en cache des données statiques.

## <a name="step-3-caching-thesupplierstable-data"></a>Étape 3 : Mise en cache le`Suppliers`données de Table

Le tables de base de données nous ve implémenté à la date de Northwind n’incluent pas toutes les tables de correspondance classique. Les quatre tables de données implémentée dans notre DAL toutes les tables de modèle dont les valeurs ne sont pas statiques. Plutôt que consacre du temps pour ajouter un nouveau DataTable à la couche DAL et ensuite une nouvelle classe et les méthodes à la couche BLL, pour ce didacticiel permettre s simplement prétendre qui le `Suppliers` données de la table s sont statiques. Par conséquent, nous pouvons mettre en cache ces données au démarrage de l’application.

Pour commencer, créez une classe nommée `StaticCache.cs` dans le `CL` dossier.


![Créer la classe StaticCache.cs dans le dossier CL](caching-data-at-application-startup-cs/_static/image2.png)

**Figure 2**: créer le `StaticCache.cs` classe dans le `CL` dossier


Nous devons ajouter une méthode qui charge les données au démarrage dans le magasin de cache appropriée, ainsi que les méthodes qui retournent des données à partir de ce cache.


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample3.cs)]

Le code ci-dessus utilise une variable membre static, `suppliers`, pour stocker les résultats de la `SuppliersBLL` classe s `GetSuppliers()` (méthode), qui est appelé à partir de la `LoadStaticCache()` (méthode). Le `LoadStaticCache()` méthode est destinée à être appelée pendant le démarrage de l’application s. Une fois que ces données ont été chargées au démarrage de l’application, n’importe quelle page qui doit fonctionner avec les données des fournisseurs peut appeler le `StaticCache` classe s `GetSuppliers()` (méthode). Par conséquent, l’appel à la base de données pour obtenir les fournisseurs ne se produit une seule fois, au démarrage de l’application.

Au lieu d’utiliser une variable membre statique en tant que le stockage de cache, nous avons également utiliser état de l’application ou le cache de données. Le code suivant illustre la classe remaniée pour utiliser l’état de l’application :


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample4.cs)]

Dans `LoadStaticCache()`, les informations de fournisseur sont stockées dans la variable d’application *clé*. Il s retourné en tant que le type approprié (`Northwind.SuppliersDataTable`) à partir de `GetSuppliers()`. Alors que l’état de l’application sont accessibles dans les classes de code-behind des pages ASP.NET à l’aide de `Application["key"]`, de l’architecture, nous devons utiliser `HttpContext.Current.Application["key"]` afin d’obtenir en cours `HttpContext`.

De même, le cache de données peut servir comme magasin de cache, comme le montre le code suivant :


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample5.cs)]

Pour ajouter un élément au cache de données sans expiration basée sur le temps, utilisez le `System.Web.Caching.Cache.NoAbsoluteExpiration` et `System.Web.Caching.Cache.NoSlidingExpiration` valeurs en tant que paramètres d’entrée. Cette surcharge particulière du cache de données s `Insert` méthode a été sélectionnée afin que nous pouvons spécifier le *priorité* de l’élément de cache. La priorité est utilisée pour déterminer les éléments à récupérer à partir du cache lorsque la mémoire disponible est faible. Nous utilisons ici la priorité `NotRemovable`, ce qui garantit que cet élément de cache a gagné t être nettoyés.

> [!NOTE]
> Ce téléchargement didacticiel s implémente la `StaticCache` classe à l’aide de l’approche de variable membre statique. Le code pour les techniques de cache état et les données d’application est disponible dans les commentaires dans le fichier de classe.


## <a name="step-4-executing-code-at-application-startup"></a>Étape 4 : Exécution de Code au démarrage de l’Application

Pour exécuter le code de démarrage d’une application web, nous devons créer un fichier spécial nommé `Global.asax`. Ce fichier peut contenir des gestionnaires d’événements pour l’application-session-, et événements au niveau de la demande et il est là où nous pouvons ajouter le code qui sera exécuté chaque fois que l’application démarre.

Ajouter le `Global.asax` fichier dans votre répertoire racine de web application s en cliquant sur le nom de projet de site Web dans Visual Studio s l’Explorateur de solutions et en choisissant Ajouter un nouvel élément. À partir de la boîte de dialogue Ajouter un nouvel élément, sélectionnez le type d’élément de classe d’Application globale, puis sur le bouton Ajouter.

> [!NOTE]
> Si vous avez déjà un `Global.asax` fichier dans votre projet, la classe d’Application globale type d’élément ne sera pas être répertorié dans la boîte de dialogue Ajouter un nouvel élément.


[![Ajoutez le fichier Global.asax pour le répertoire racine s de votre Application Web](caching-data-at-application-startup-cs/_static/image4.png)](caching-data-at-application-startup-cs/_static/image3.png)

**Figure 3**: ajouter la `Global.asax` fichier à votre Application Web s répertoire racine ([cliquez pour afficher l’image en taille réelle](caching-data-at-application-startup-cs/_static/image5.png))


La valeur par défaut `Global.asax` modèle de fichier inclut cinq méthodes au sein d’un côté serveur `<script>` balise :

- **`Application_Start`**s’exécute au premier démarrage de l’application web
- **`Application_End`**s’exécute lorsque l’application est en cours d’arrêt
- **`Application_Error`**s’exécute chaque fois qu’une exception non gérée atteint l’application
- **`Session_Start`**s’exécute lorsqu’une nouvelle session est créée.
- **`Session_End`**s’exécute lorsqu’une session a expiré ou a abandonné

Le `Application_Start` Gestionnaire d’événements est appelé une seule fois pendant un cycle de vie de l’application s. Démarrage de l’application la première fois une ressource ASP.NET est demandée par l’application et continue de s’exécuter jusqu'à ce que l’application est redémarrée, ce qui peut se produire en modifiant le contenu de la `/Bin` dossier, modification `Global.asax`, modification le contenu dans le `App_Code` dossier, ou en modifiant le `Web.config` fichier, entre autres. Reportez-vous à [vue d’ensemble du Cycle de vie ASP.NET Application](https://msdn.microsoft.com/en-us/library/ms178473.aspx) pour plus d’informations sur le cycle de vie d’application.

Pour ces didacticiels, nous avons besoin uniquement ajouter du code pour le `Application_Start` d’une méthode, c’est le cas hésitez pas à supprimer les autres. Dans `Application_Start`, appelez simplement le `StaticCache` classe s `LoadStaticCache()` (méthode), qui charge et mettre en cache les informations de fournisseur :


[!code-aspx[Main](caching-data-at-application-startup-cs/samples/sample6.aspx)]

Il est tout s ! Au démarrage de l’application, le `LoadStaticCache()` méthode Saisissez les informations de fournisseur à partir de la couche BLL et stockez-le dans une variable membre statique (ou le cache stocker vous trouvez à l’aide de la `StaticCache` classe). Pour vérifier ce comportement, définissez un point d’arrêt le `Application_Start` (méthode) et exécuter votre application. Notez que le point d’arrêt est atteint au démarrage de l’application. Les demandes suivantes, toutefois, n’entraînent pas la `Application_Start` méthode à exécuter.


[![Utiliser un point d’arrêt pour vérifier que le Gestionnaire d’événements Application_Start est en cours d’exécution](caching-data-at-application-startup-cs/_static/image7.png)](caching-data-at-application-startup-cs/_static/image6.png)

**Figure 4**: utiliser un point d’arrêt pour vérifier que le `Application_Start` Gestionnaire d’événements est en cours d’exécution ([cliquez pour afficher l’image en taille réelle](caching-data-at-application-startup-cs/_static/image8.png))


> [!NOTE]
> Si vous ne cliquez pas sur le `Application_Start` point d’arrêt lorsque vous commencez le débogage, il est fait que votre application a déjà commencé. Forcer l’application à redémarrer en modifiant votre `Global.asax` ou `Web.config` de fichiers, puis réessayez. Vous pouvez simplement ajouter (ou supprimer) une ligne vide à la fin de l’un de ces fichiers rapidement redémarrer l’application.


## <a name="step-5-displaying-the-cached-data"></a>Étape 5 : Afficher les données mises en cache

À ce stade le `StaticCache` classe a une version des données de fournisseur mis en cache au démarrage de l’application qui sont accessibles via son `GetSuppliers()` (méthode). Pour travailler avec ces données à partir de la couche de présentation, nous pouvons utiliser un ObjectDataSource ou appeler par programme la `StaticCache` classe s `GetSuppliers()` méthode d’une classe code-behind s de page ASP.NET. Permettent d’examiner l’utilisation des contrôles ObjectDataSource et GridView pour afficher les informations de mise en cache de fournisseur s.

Commencez par ouvrir le `AtApplicationStartup.aspx` page dans le `Caching` dossier. Faites glisser un contrôle GridView à partir de la boîte à outils vers le concepteur, en définissant ses `ID` propriété `Suppliers`. Ensuite, dans le contrôle GridView balise s Choisissez Créer un nouveau ObjectDataSource nommé `SuppliersCachedDataSource`. Configurer l’ObjectDataSource à utiliser le `StaticCache` classe s `GetSuppliers()` (méthode).


[![Configurer pour utiliser la classe StaticCache ObjectDataSource](caching-data-at-application-startup-cs/_static/image10.png)](caching-data-at-application-startup-cs/_static/image9.png)

**Figure 5**: configurer l’ObjectDataSource à utiliser le `StaticCache` classe ([cliquez pour afficher l’image en taille réelle](caching-data-at-application-startup-cs/_static/image11.png))


[![Utilisez la méthode GetSuppliers() pour récupérer les données des fournisseurs de mise en cache](caching-data-at-application-startup-cs/_static/image13.png)](caching-data-at-application-startup-cs/_static/image12.png)

**Figure 6**: utilisez le `GetSuppliers()` pour récupérer les données mises en cache des fournisseurs ([cliquez pour afficher l’image en taille réelle](caching-data-at-application-startup-cs/_static/image14.png))


Après la fin de l’Assistant, Visual Studio ajoute automatiquement BoundFields pour chacun des champs de données `SuppliersDataTable`. Votre balisage déclaratif s GridView et ObjectDataSource doit ressembler à ce qui suit :


[!code-aspx[Main](caching-data-at-application-startup-cs/samples/sample7.aspx)]

La figure 7 illustre la page via un navigateur. La sortie est le même avions nous extraites les données à partir de la couche BLL s `SuppliersBLL` classe, mais à l’aide de la `StaticCache` classe retourne les données des fournisseurs en tant que mises en cache au démarrage de l’application. Vous pouvez définir des points d’arrêt dans le `StaticCache` classe s `GetSuppliers()` méthode pour vérifier ce comportement.


[![Les données mises en cache des fournisseurs s’affiche dans un contrôle GridView](caching-data-at-application-startup-cs/_static/image16.png)](caching-data-at-application-startup-cs/_static/image15.png)

**Figure 7**: la mise en cache les données des fournisseurs s’affiche dans un contrôle GridView ([cliquez pour afficher l’image en taille réelle](caching-data-at-application-startup-cs/_static/image17.png))


## <a name="summary"></a>Résumé

La plupart des chaque modèle de données contient une grande quantité de données statiques, généralement implémentées sous la forme de tables de recherche. Étant donné que ces informations sont statiques, là s aucune raison de cette information doit être affichée en permanence pour accéder à la base de données chaque heure. En outre, en raison de sa nature statique, lors de la mise en cache les données là s pas nécessaire pour un délai d’expiration. Dans ce didacticiel, nous avons vu comment ces données en mémoire cache dans le cache de données, état de l’application et via une variable membre statique. Ces informations sont mises en cache au démarrage de l’application et restent dans le cache pendant la durée de vie application s.

Dans ce didacticiel et les deux derniers, nous ve effectue la recherche de données de mise en cache pour la durée de la durée de vie d’application s ainsi que d’à l’aide de la durée des expirations dans le. Lors de la mise en cache de la base de données, cependant, une expiration basée sur le temps est peut-être pas idéale. Plutôt que régulièrement vider le cache, il serait optimale pour supprimer uniquement l’élément mis en cache lorsque la base de données sous-jacente est modifié. Cette valeur est possible grâce à l’utilisation des dépendances de cache SQL, lequel nous allons examiner dans notre didacticiel suivant.

Bonne programmation !

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Teresa Murphy et Zack Jones. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](caching-data-in-the-architecture-cs.md)
[Suivant](using-sql-cache-dependencies-cs.md)
