---
title: "Héberger ASP.NET Core sur Linux avec Nginx"
description: "Décrit comment configurer Nginx comme proxy inverse sur Ubuntu 16.04 pour transférer le trafic HTTP vers une application web ASP.NET Core s’exécutant sur Kestrel."
keywords: ASP.NET Core, Linux, nginx, Ubuntu, Proxy inverse
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 08/21/2017
ms.topic: article
ms.assetid: 1c33e576-33de-481a-8ad3-896b94fde0e3
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/linuxproduction
ms.openlocfilehash: 1f2b5fc6d769c63110f832a31cd0d0aa8c3298e9
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2017
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-nginx-and-deploy-to-it"></a>Configurer un environnement d’hébergement pour ASP.NET Core sur Linux avec Nginx et déployer dessus

De [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Ce guide explique comment configurer un environnement ASP.NET Core prêt pour la production sur un serveur Ubuntu 16.04.

**Remarque :** Pour Ubuntu 14.04, nous vous recommandons d’utiliser supervisord comme solution pour l’analyse du processus Kestrel. systemd n’est pas disponible sur Ubuntu 14.04. [Voir la version précédente de ce document](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)

Ce guide montre comment effectuer les opérations suivantes :

* Placer une application ASP.NET Core existante derrière un serveur proxy inverse
* Configurer le serveur proxy inverse pour transférer les requêtes au serveur web Kestrel
* S’assurer que l’application web s’exécute au démarrage en tant que démon
* Configurer un outil de gestion des processus pour aider à redémarrer l’application web

## <a name="prerequisites"></a>Prérequis

1. Accès à un serveur Ubuntu 16.04 avec un compte d’utilisateur standard disposant de privilèges sudo
2. Application ASP.NET Core existante

## <a name="copy-over-your-app"></a>Copier votre application

Exécutez `dotnet publish` à partir de l’environnement de développement pour empaqueter une application dans un répertoire autonome pouvant s’exécuter sur le serveur.

Copiez l’application ASP.NET Core sur le serveur à l’aide de n’importe quel outil (SCP, FTP, etc.) qui s’intègre dans votre flux de travail. Testez l’application, par exemple :
 - À partir de la ligne de commande, exécutez `dotnet yourapp.dll`
 - Dans un navigateur, accédez à `http://<serveraddress>:<port>` pour vérifier que l’application fonctionne sur Linux. 
 
**Remarque :** Utilisez [Yeoman](xref:client-side/yeoman) pour créer une application ASP.NET Core pour un nouveau projet.

## <a name="configure-a-reverse-proxy-server"></a>Configurer un serveur proxy inverse

Un proxy inverse est une configuration courante pour traiter les applications web dynamiques. Un proxy inverse met fin à la requête HTTP et la transfère à l’application ASP.NET Core.

### <a name="why-use-a-reverse-proxy-server"></a>Pourquoi utiliser un serveur proxy inverse ?

Kestrel est une bonne solution pour héberger du contenu dynamique à partir d’ASP.NET ; toutefois, les composants du service web ne sont pas aussi enrichis que ceux offerts par des serveurs comme IIS, Apache ou Nginx. Un serveur proxy inverse peut décharger du travail comme le traitement du contenu statique, la mise en cache des requêtes, la compression des requêtes et l’arrêt SSL à partir du serveur HTTP. Un serveur proxy inverse peut résider sur un ordinateur dédié ou peut être déployé à côté d’un serveur HTTP.

Pour les besoins de ce guide, nous utilisons une seule instance de Nginx. Elle s’exécute sur le même serveur, en plus du serveur HTTP. En fonction de vos besoins, vous pouvez choisir une configuration différente.

Les requêtes étant transférées par le proxy inverse, vous devez utiliser l’intergiciel (middleware) `ForwardedHeaders` à partir du package `Microsoft.AspNetCore.HttpOverrides`. Cet intergiciel met à jour `Request.Scheme`, à l’aide de l’en-tête `X-Forwarded-Proto`, afin que les URI de redirection et d’autres stratégies de sécurité fonctionnent correctement.

Quand vous configurez un serveur proxy inverse, l’intergiciel d’authentification a besoin que `UseForwardedHeaders` s’exécute en premier. Cet ordre permet d’être sûr que l’intergiciel d’authentification peut consommer les valeurs affectées et générer l’URI de redirection correct.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Appelez la méthode `UseForwardedHeaders` (dans la méthode `Configure` de *Startup.cs*) avant d’appeler `UseAuthentication` ou un intergiciel de schéma d’authentification similaire :

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Appelez la méthode `UseForwardedHeaders` (dans la méthode `Configure` de *Startup.cs*) avant d’appeler `UseIdentity` et `UseFacebookAuthentication` ou un intergiciel de schéma d’authentification similaire :

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

### <a name="install-nginx"></a>Installer Nginx

```bash
sudo apt-get install nginx
```

> [!NOTE]
> Si vous envisagez d’installer des modules Nginx facultatif, vous devrez peut-être générer Nginx à partir de la source.

Utilisez `apt-get` pour installer Nginx. Le programme d’installation crée un script d’initialisation System V qui exécute Nginx en tant que démon au démarrage du système. Comme il s’agit de l’installation initiale de Nginx, vous devez le démarrer explicitement en exécutant :

```bash
sudo service nginx start
```

Vérifiez qu’un navigateur affiche la page d’accueil par défaut de Nginx.

### <a name="configure-nginx"></a>Configurer Nginx

Pour configurer Nginx en tant que proxy inverse pour transférer les requêtes à notre application ASP.NET Core, modifiez le fichier `/etc/nginx/sites-available/default`. Ouvrez-le dans un éditeur de texte et remplacez le contenu par ce qui suit :

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Ce fichier de configuration Nginx transfère le trafic public entrant du port `80` au port `5000`.

Une fois que vous avez fini de modifier votre configuration Nginx, vous pouvez exécuter `sudo nginx -t` pour vérifier la syntaxe de vos fichiers de configuration. Si le test de fichier de configuration réussit, vous pouvez demander à Nginx d’appliquer les modifications en exécutant `sudo nginx -s reload`.

## <a name="monitoring-our-application"></a>Surveillance de notre application

Nginx est désormais configuré pour transférer les requêtes faites à `http://yourhost:80` à l’application ASP.NET Core s’exécutant sur Kestrel à l’adresse `http://127.0.0.1:5000`. Toutefois, Nginx n’est pas configuré pour gérer le processus Kestrel. Vous pouvez utiliser *systemd* et créer un fichier de service pour démarrer et surveiller l’application web sous-jacente. *systemd* est un système d’initialisation qui fournit de nombreuses et puissantes fonctionnalités pour le démarrage, l’arrêt et la gestion des processus. 

### <a name="create-the-service-file"></a>Créer le fichier de service

Créez le fichier de définition de service :

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

Voici un exemple de fichier de service pour notre application :

```ini
[Unit]
Description=Example .NET Web API Application running on Ubuntu

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

**Remarque :** Si l’utilisateur *www-data* n’est pas utilisé par votre configuration, vous devez d’abord créer l’utilisateur défini ici et l’affecter en tant que propriétaire des fichiers.

Enregistrez le fichier et activez le service.

```bash
systemctl enable kestrel-hellomvc.service
```

Démarrez le service et vérifiez qu’il s’exécute.

```
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API Application running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

Le proxy inverse étant configuré et Kestrel étant géré par le biais de systemd, l’application web est maintenant entièrement configurée et accessible à partir d’un navigateur sur l’ordinateur local à l’adresse `http://localhost`. Elle est également accessible à partir d’un ordinateur distant, sauf en cas de blocage par un pare-feu. Si l’on inspecte les en-têtes de réponse, on constate que l’en-tête `Server` indique que l’application ASP.NET Core est traitée par Kestrel.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>Affichage des journaux

Puisque l’application web utilisant Kestrel est gérée à l’aide de `systemd`, tous les processus et les événements sont enregistrés dans un journal centralisé. Toutefois, ce journal inclut toutes les entrées pour tous les services et les processus gérés par `systemd`. Pour afficher les éléments propres à `kestrel-hellomvc.service`, utilisez la commande suivante :

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

Si vous souhaitez appliquer un filtrage supplémentaire, des options temporelles telles que `--since today`, `--until 1 hour ago` ou une combinaison de ces options peut réduire la quantité d’entrées retournées.

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a>Sécurisation de votre application

### <a name="enable-apparmor"></a>Activer AppArmor

Linux Security Modules (LSM) est un framework qui fait partie du noyau Linux depuis Linux 2.6. LSM prend en charge différentes implémentations de modules de sécurité. [AppArmor](https://wiki.ubuntu.com/AppArmor) est un LSM qui implémente un système de contrôle d’accès obligatoire permettant de confiner le programme à un ensemble limité de ressources. Vérifiez qu’AppArmor est activé et configuré correctement.

### <a name="configuring-our-firewall"></a>Configuration de notre pare-feu

Fermez tous les ports externes qui ne sont pas en cours d’utilisation. Uncomplicated firewall (ufw) fournit une interface de ligne de commande pour `iptables` afin de configurer le pare-feu. Vérifiez que `ufw` est configuré pour autoriser le trafic sur les ports dont vous avez besoin.

```bash
sudo apt-get install ufw
sudo ufw enable

sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### <a name="securing-nginx"></a>Sécurisation de Nginx

La distribution par défaut de Nginx n’active pas le protocole SSL. Pour activer des fonctionnalités de sécurité supplémentaires, générez à partir de la source.

#### <a name="download-the-source-and-install-the-build-dependencies"></a>Télécharger la source et installer les dépendances de build

```bash
# Install the build dependencies
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3-dev libssl-dev libxslt1-dev libxml2-dev libgd2-xpm-dev libgeoip-dev libgoogle-perftools-dev libperl-dev

# Download nginx 1.10.0 or latest
wget http://www.nginx.org/download/nginx-1.10.0.tar.gz
tar zxf nginx-1.10.0.tar.gz
```

#### <a name="change-the-nginx-response-name"></a>Changer le nom de la réponse Nginx

Modifiez *src/http/ngx_http_header_filter_module.c* :

```c
static char ngx_http_server_string[] = "Server: Your Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Your Web Server" CRLF;
```

#### <a name="configure-the-options-and-build"></a>Configurer les options et générer

La bibliothèque PCRE est obligatoire pour les expressions régulières. Les expressions régulières sont utilisées dans la directive d’emplacement pour ngx_http_rewrite_module. http_ssl_module ajoute la prise en charge du protocole HTTPS.

Pour renforcer votre application, vous pouvez utiliser un pare-feu d’application web tel que *ModSecurity*.

```bash
./configure
--with-pcre=../pcre-8.38
--with-zlib=../zlib-1.2.8
--with-http_ssl_module
--with-stream
--with-mail=dynamic
```

#### <a name="configure-ssl"></a>Configurer le protocole SSL

* Configurez votre serveur pour qu’il écoute le trafic HTTPS sur le port `443` en spécifiant un certificat valide émis par une autorité de certificat approuvée.

* Renforcez la sécurité en appliquant certaines des pratiques mentionnées dans le fichier */etc/nginx/nginx.conf* suivant. Vous pouvez par exemple choisir un chiffrement plus fort et rediriger tout le trafic sur HTTP vers HTTPS.

* L’ajout d’un en-tête `HTTP Strict-Transport-Security` (HSTS) garantit que toutes les requêtes ultérieures du client s’effectuent uniquement par le biais du protocole HTTPS.

* N’ajoutez pas l’en-tête Strict-Transport-Security ou choisissez une valeur `max-age` appropriée si vous prévoyez de désactiver le protocole SSL ultérieurement.

Ajoutez le fichier de configuration */etc/nginx/proxy.conf* :

[!code-nginx[Main](linuxproduction/proxy.conf)]

Modifiez le fichier de configuration */etc/nginx/nginx.conf*. Dans l’exemple, les sections `http` et `server` figurent dans un même fichier de configuration.

[!code-nginx[Main](../publishing/linuxproduction/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Sécuriser Nginx contre le détournement de clic
Le détournement de clic, ou clickjacking, est une technique malveillante visant à recueillir les clics d’un utilisateur infecté. Elle pousse la victime (visiteur) à accéder par des clics de souris à un site infecté. Utilisez X-FRAME-OPTIONS pour sécuriser votre site.

Modifiez le fichier *nginx.conf* :

```bash
sudo nano /etc/nginx/nginx.conf
```

Ajoutez la ligne `add_header X-Frame-Options "SAMEORIGIN";` et enregistrez le fichier, puis redémarrez Nginx.

#### <a name="mime-type-sniffing"></a>Détection de type MIME

Cet en-tête empêche la plupart des navigateurs de détourner le type MIME d’une réponse et de remplacer le type de contenu déclaré, car l’en-tête indique au navigateur qu’il ne doit pas substituer le type de contenu de la réponse. Avec l’option `nosniff`, si le serveur indique que le contenu est « text/html », le navigateur le restitue en tant que « text/html ».

Modifiez le fichier *nginx.conf* :

```bash
sudo nano /etc/nginx/nginx.conf
```

Ajoutez la ligne `add_header X-Content-Type-Options "nosniff";` et enregistrez le fichier, puis redémarrez Nginx.
