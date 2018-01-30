---
title: "Héberger ASP.NET Core sur Linux avec Apache"
description: "Découvrez comment configurer Apache comme un serveur de proxy inverse sur CentOS pour rediriger le trafic HTTP pour une application web ASP.NET Core sur Kestrel."
author: spboyer
manager: wpickett
ms.author: spboyer
ms.custom: mvc
ms.date: 10/19/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/linux-apache
ms.openlocfilehash: aa55ecd6dc8169e0e77b3899389ec924b1e1ae4a
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Héberger ASP.NET Core sur Linux avec Apache

Par [Shayne Boyer](https://github.com/spboyer)

À l’aide de ce guide, découvrez comment configurer [Apache](https://httpd.apache.org/) comme un serveur proxy inverse sur [CentOS 7](https://www.centos.org/) pour rediriger le trafic HTTP pour une application web ASP.NET Core [Kestrel](xref:fundamentals/servers/kestrel). Le [mod_proxy extension](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) et créer des modules associés proxy inverse de celui du serveur.

## <a name="prerequisites"></a>Prérequis

1. Serveur exécutant CentOS 7 avec un compte d’utilisateur standard avec les privilèges sudo
2. Applications ASP.NET Core

## <a name="publish-the-app"></a>Publier l'application

Publier l’application en tant qu’un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) dans la configuration de mise en production pour le runtime CentOS 7 (`centos.7-x64`). Copiez le contenu de la *bin/Release/netcoreapp2.0/centos.7-x64/publish* dossier au serveur à l’aide de SCP, FTP ou autre méthode de transfert de fichier.

> [!NOTE]
> Dans un scénario de déploiement de production, un flux de travail d’intégration continue effectue le travail de l’application de publication et copie les éléments multimédias sur le serveur. 

## <a name="configure-a-proxy-server"></a>Configurer un serveur proxy

Un proxy inverse est une installation commune pour traiter les applications web dynamiques. Le proxy inverse met fin à la requête HTTP et le transmet à l’application ASP.NET.

Un serveur proxy est un serveur qui transfère les demandes des clients à un autre serveur, au lieu de les traiter lui-même. Un proxy inverse transfère à une destination fixe, en général pour le compte de clients arbitraires. Dans ce guide, Apache est configuré en tant que le proxy inverse en cours d’exécution sur le même serveur que Kestrel sert à l’application ASP.NET Core.

### <a name="install-apache"></a>Installer Apache

Mettre à jour les packages CentOS vers leur dernière version stable :

```bash
sudo yum update -y
```

Installer le serveur web Apache sur CentOS avec une seule `yum` commande :

```bash
sudo yum -y install httpd mod_ssl
```

Exemple de sortie après l’exécution de la commande :

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> Dans cet exemple, la sortie reflète httpd.86_64 depuis la version CentOS 7 est de 64 bits. Pour vérifier où Apache est installé, exécutez `whereis httpd` à partir d’une invite de commandes. 

### <a name="configure-apache-for-reverse-proxy"></a>Configurer Apache pour le proxy inverse

Les fichiers de configuration pour Apache se trouvent dans le répertoire `/etc/httpd/conf.d/`. Tout fichier portant le *.conf* extension est traitée dans l’ordre alphabétique en plus des fichiers de configuration de module dans `/etc/httpd/conf.modules.d/`, qui contient toutes les configurations de fichiers nécessaire au chargement des modules.

Créer un fichier de configuration pour l’application nommée `hellomvc.conf`:

```
<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
</VirtualHost>
```

Le **VirtualHost** nœud peut apparaître plusieurs fois dans un ou plusieurs fichiers sur un serveur. **VirtualHost** est définie pour écouter sur n’importe quelle adresse IP à l’aide du port 80. Les deux lignes sont définies pour les demandes à la racine à 127.0.0.1 dans le serveur proxy sur le port 5000. Pour la communication bidirectionnelle, *ProxyPass* et *ProxyPassReverse* sont requis.

La journalisation peut être configurée par **VirtualHost** à l’aide de **ErrorLog** et **CustomLog** directives. **Journal des erreurs** est l’emplacement où le serveur enregistre les erreurs, et **CustomLog** définit le nom de fichier et le format du fichier journal. Il s’agit dans ce cas, où les informations de demande sont consignées. Il existe une ligne pour chaque demande.

Enregistrez le fichier et la configuration de test. Si tout réussit, la réponse doit être `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Redémarrez Apache :

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitoring-the-app"></a>Surveillance de l’application

Apache est désormais configuré pour transférer les demandes faites à `http://localhost:80` à l’application ASP.NET Core s’exécutant sur Kestrel à `http://127.0.0.1:5000`.  Toutefois, Apache n’est pas configuré pour gérer le processus Kestrel. Utilisez *systemd* et créer un fichier de service pour démarrer et surveiller l’application web sous-jacente. *systemd* est un système d’initialisation qui fournit de nombreuses et puissantes fonctionnalités pour le démarrage, l’arrêt et la gestion des processus. 


### <a name="create-the-service-file"></a>Créer le fichier de service

Créez le fichier de définition de service :

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

Un exemple de fichier service pour l’application :

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
# Restart service after 10 seconds if dotnet service crashes
RestartSec=10
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

> [!NOTE]
> **Utilisateur** &mdash; si l’utilisateur *apache* n’est pas utilisé par la configuration, l’utilisateur doit être créé en premier et étant donné la propriété appropriée pour les fichiers.

Enregistrez le fichier et activer le service :

```bash
systemctl enable kestrel-hellomvc.service
```

Démarrez le service et vérifiez qu’il s’exécute :

```bash
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

Avec la configuration de proxy inverse et Kestrel gérés via *systemd*, l’application web est entièrement configurée et qu’il est accessible à partir d’un navigateur sur l’ordinateur local à `http://localhost`. Inspecter les en-têtes de réponse, le **Server** en-tête indique que l’application ASP.NET Core est pris en charge par Kestrel :

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>Affichage des journaux

Depuis l’application web à l’aide de Kestrel est géré à l’aide de *systemd*, processus et les événements sont enregistrés dans un journal centralisé. Toutefois, ce journal inclut des entrées pour tous les services et les processus gérés par *systemd*. Pour afficher les éléments propres à `kestrel-hellomvc.service`, utilisez la commande suivante :

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

Pour le filtrage du temps, spécifiez les options d’exécution avec la commande. Par exemple, utilisez `--since today` pour filtrer le jour actuel ou `--until 1 hour ago` pour afficher les entrées de l’heure précédente. Pour plus d’informations, consultez la [page manuel journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-the-app"></a>Sécurisation de l’application

### <a name="configure-firewall"></a>Configurer le pare-feu

*Firewalld* est un démon dynamique pour gérer le pare-feu avec prise en charge pour les zones du réseau. Ports et le filtrage des paquets peuvent toujours être gérés par iptables. *Firewalld* doit être installé par défaut. `yum`peut être utilisé pour installer le package ou de vérifier qu’il est installé.

```bash
sudo yum install firewalld -y
```

Utilisez `firewalld` à ouvrir uniquement les ports nécessaires pour l’application. Dans ce cas, les ports 80 et 443 sont utilisés. Les commandes suivantes à définir les ports 80 et 443 pour ouvrir de manière permanente :

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Recharger les paramètres du pare-feu. Vérifiez les services disponibles et les ports de la zone par défaut. Options sont disponibles en inspectant `firewall-cmd -h`.

```bash 
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="ssl-configuration"></a>Configuration de SSL

Pour configurer Apache pour SSL, le *mod_ssl* module est utilisé. Lorsque le *httpd* module a été installé, le *mod_ssl* module a également été installé. S’il n’a pas été installé, utilisez `yum` pour l’ajouter à la configuration.

```bash
sudo yum install mod_ssl
```
Pour mettre en œuvre SSL, vous devez installer le `mod_rewrite` module pour permettre la réécriture d’URL :

```bash
sudo yum install mod_rewrite
```

Modifier la *hellomvc.conf* fichier pour permettre la réécriture d’URL et de sécuriser les communications sur le port 443 :

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> Cet exemple utilise un certificat généré localement. **SSLCertificateFile** doit être le fichier de certificat principal pour le nom de domaine. **SSLCertificateKeyFile** doit être le fichier de clé généré lors de la création de signature de certificat. **SSLCertificateChainFile** doit être le fichier de certificat intermédiaire (le cas échéant) qui a été fourni par l’autorité de certification.

Enregistrez le fichier et la configuration de test :

```bash
sudo service httpd configtest
```

Redémarrez Apache :

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Autres suggestions pour Apache

### <a name="additional-headers"></a>En-têtes supplémentaires

Afin de sécuriser contre les attaques, il existe quelques en-têtes qui doivent être modifiés ou ajoutés. Vérifiez que le `mod_headers` module est installé :

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Apache sécurisé contre les attaques de clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), également appelé un *UI recours attaque*, est une attaque malveillante où l’exécute un visiteur de site Web en cliquant sur un lien ou un bouton sur une autre page, qu’ils vous visitez actuellement. Utilisez `X-FRAME-OPTIONS` pour sécuriser le site.

Modifier la *httpd.conf* fichier :

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Ajoutez la ligne `Header append X-FRAME-OPTIONS "SAMEORIGIN"`. Enregistrez le fichier. Redémarrez Apache.

#### <a name="mime-type-sniffing"></a>Détection de type MIME

Le `X-Content-Type-Options` en-tête empêche Internet Explorer de *détection MIME sur* (détermination d’un fichier `Content-Type` dans le contenu du fichier). Si le serveur définit le `Content-Type` en-tête `text/html` avec la `nosniff` option est définie, Internet Explorer restitue le contenu en tant que `text/html` , quelle que soit le contenu du fichier.

Modifier la *httpd.conf* fichier :

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Ajoutez la ligne `Header set X-Content-Type-Options "nosniff"`. Enregistrez le fichier. Redémarrez Apache.

### <a name="load-balancing"></a>Équilibrage de charge 

Cet exemple montre comment installer et configurer Apache sur CentOS 7 et Kestrel sur la même machine de l’instance. Afin de ne pas disposer d’un seul point de défaillance ; à l’aide de *mod_proxy_balancer* et la modification de la **VirtualHost** permettrait pour gérer des instances de plusieurs applications web derrière le serveur proxy Apache.

```bash
sudo yum install mod_proxy_balancer
```

Dans le fichier de configuration illustré ci-dessous, une instance supplémentaire de la `hellomvc` application est configurée pour s’exécuter sur le port 5001. Le *Proxy* section est définie avec une configuration d’équilibrage avec deux membres pour équilibrer la charge *byrequests*.

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Limites du débit
À l’aide de *mod_ratelimit*, qui est inclus dans le *httpd* module, la bande passante de clients peut être limitée :

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```
L’exemple de fichier limite la bande passante 600 Ko/s sous l’emplacement racine :

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```
