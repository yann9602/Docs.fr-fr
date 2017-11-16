---
title: "Héberger ASP.NET Core sur Linux avec Apache"
description: "Découvrez comment configurer Apache comme serveur proxy inverse sur CentOS pour rediriger le trafic HTTP vers une application web ASP.NET Core s’exécutant sur Kestrel."
keywords: "ASP.NET Core, Apache, CentOS, Proxy inverse, Linux, mod_proxy, httpd, hébergement"
author: spboyer
ms.author: spboyer
manager: wpickett
ms.date: 10/19/2016
ms.topic: article
ms.assetid: fa9b0cb7-afb3-4361-9e7e-33afffeaca0c
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/apache-proxy
ms.openlocfilehash: 34ede2fdebe0e9516f62e39618e7adba3c8c89db
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-apache-and-deploy-to-it"></a>Configurer un environnement d’hébergement pour ASP.NET Core sur Linux avec Apache et déployer sur celui-ci

Par [Shayne Boyer](https://github.com/spboyer)

Apache est un serveur HTTP très répandu et qui peut être configuré comme proxy pour rediriger le trafic HTTP, de façon similaire à nginx. Dans ce guide, vous découvrez comment configurer Apache sur CentOS 7, et comment l’utiliser comme proxy inverse pour accueillir les connexions entrantes et les rediriger vers l’application ASP.NET Core s’exécutant sur Kestrel. Pour cela, nous utiliserons l’extension *mod_proxy* et d’autres modules Apache associés.

## <a name="prerequisites"></a>Conditions préalables

1. Un serveur exécutant CentOS 7 avec un compte d’utilisateur standard disposant du privilège sudo.
2. Une application ASP.NET Core existante. 

## <a name="publish-your-application"></a>Publier votre application

Exécutez `dotnet publish -c Release` à partir de votre environnement de développement pour empaqueter votre application dans un répertoire autonome qui peut s’exécuter sur votre serveur. L’application publiée doit ensuite être copiée sur le serveur en utilisant SCP, FTP ou une autre méthode de transfert de fichiers. 

> [!NOTE]
> Dans un scénario de déploiement en production, un workflow d’intégration continue effectue le travail de publication de l’application et de copie des composants sur le serveur. 

## <a name="configure-a-proxy-server"></a>Configurer un serveur proxy

Un proxy inverse est une configuration courante pour traiter les applications web dynamiques. Le proxy inverse met fin à la requête HTTP et la transfère à l’application ASP.NET.

Un serveur proxy est un serveur qui transfère les demandes des clients à un autre serveur, au lieu de les traiter lui-même. Un proxy inverse transfère à une destination fixe, en général pour le compte de clients arbitraires. Dans ce guide, Apache est configuré comme proxy inverse s’exécutant sur le même serveur où Kestrel traite l’application ASP.NET Core. 

Chaque composant de l’application peut exister sur des machines physiques distinctes, sur des conteneurs Docker ou sur une combinaison de configurations en fonction de vos besoins en termes d’architecture ou des restrictions.

### <a name="install-apache"></a>Installer Apache

L’installation du serveur web Apache sur CentOS se fait en une seule commande, mais pour commencer, nous mettons d’abord à jour nos packages.

```bash
    sudo yum update -y
```

Ceci garantit que tous les packages installés sont mis à jour vers leur dernière version. Installer Apache en utilisant `yum`

```bash
    sudo yum -y install httpd mod_ssl
```

La sortie doit être similaire à ceci.

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
> Dans cet exemple, la sortie correspond à httpd.86_64, car la version de CentOS 7 est en 64 bits. La sortie peut être différente pour votre serveur. Pour vérifier où Apache est installé, exécutez `whereis httpd` à partir d’une invite de commandes. 

### <a name="configure-apache-for-reverse-proxy"></a>Configurer Apache pour le proxy inverse

Les fichiers de configuration pour Apache se trouvent dans le répertoire `/etc/httpd/conf.d/`. Les fichiers avec l’extension **.conf** sont traités par ordre alphabétique en plus des fichiers de configuration des modules dans `/etc/httpd/conf.modules.d/`, qui contient tous les fichiers de configuration nécessaires au chargement des modules.

Créez un fichier de configuration pour votre application. Pour cet exemple, nous l’appellerons `hellomvc.conf`.

```text
    <VirtualHost *:80>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ErrorLog /var/log/httpd/hellomvc-error.log
        CustomLog /var/log/httpd/hellomvc-access.log common
    </VirtualHost>
```

Le nœud *VirtualHost*, qui peut exister en plusieurs exemplaires dans un fichier ou sur un serveur dans de nombreux fichiers, est défini pour écouter sur n’importe quelle adresse IP en utilisant le port 80. Les deux lignes sont définies pour passer toutes les demandes reçues au niveau de la racine à la machine 127.0.0.1 sur le port 5000 et inversement. Pour permettre à cet endroit une communication bidirectionnelle, les deux paramètres *ProxyPass* et *ProxyPassReverse* sont obligatoires.

La journalisation peut être configurée par VirtualHost à l’aide des directives *ErrorLog* et *CustomLog*. *ErrorLog* est l’emplacement où le serveur consigne les erreurs, et *CustomLog* définit le nom de fichier et le format du fichier journal. Dans notre cas, c’est l’endroit où les informations des demandes sont enregistrées. Il y aura une ligne pour chaque demande.

Enregistrez le fichier et testez la configuration. Si tout réussit, la réponse doit être `Syntax [OK]`.

```bash
    sudo service httpd configtest
```

Redémarrez Apache.

```text
    sudo systemctl restart httpd
    sudo systemctl enable httpd
```

## <a name="monitoring-our-application"></a>Surveillance de notre application

Apache est désormais configuré pour transférer les demandes faites à `http://localhost:80` vers l’application ASP.NET Core s’exécutant sur Kestrel à l’adresse `http://127.0.0.1:5000`.  Cependant, Apache n’est pas configuré pour gérer le processus Kestrel. Nous utilisons *systemd* pour créer un fichier de service pour démarrer et surveiller l’application web sous-jacente. *systemd* est un système d’initialisation qui offre de nombreuses fonctionnalités puissantes pour le démarrage, l’arrêt et la gestion des processus. 


### <a name="create-the-service-file"></a>Créer le fichier de service

Créez le fichier de définition de service 

```bash
    sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

Un exemple de fichier de service pour notre application.

```text
[Unit]
    Description=Example .NET Web API Application running on CentOS 7

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
> **Remarque :** Si l’utilisateur *apache* n’est pas utilisé par votre configuration, vous devez d’abord créer l’utilisateur défini ici et l’affecter en tant que propriétaire des fichiers.

Enregistrez le fichier et activez le service.

```bash
    systemctl enable kestrel-hellomvc.service
```

Démarrez le service et vérifiez qu’il s’exécute.

```
    systemctl start kestrel-hellomvc.service
    systemctl status kestrel-hellomvc.service

    ● kestrel-hellomvc.service - Example .NET Web API Application running on CentOS 7
        Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
        Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
    Main PID: 9021 (dotnet)
        CGroup: /system.slice/kestrel-hellomvc.service
                └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

Le proxy inverse étant configuré et Kestrel étant géré via systemd, l’application web est maintenant entièrement configurée et accessible à partir d’un navigateur sur la machine locale à l’adresse `http://localhost`. L’examen des en-têtes de réponse indique que le **Serveur** montre que l’application ASP.NET Core est traitée par Kestrel.

```text
    HTTP/1.1 200 OK
    Date: Tue, 11 Oct 2016 16:22:23 GMT
    Server: Kestrel
    Keep-Alive: timeout=5, max=98
    Connection: Keep-Alive
    Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>Affichage des journaux

Comme l’application web utilisant Kestrel est gérée via systemd, tous les événements et processus sont enregistrés dans un journal centralisé. Cependant, ce journal comprend toutes les entrées pour tous les services et processus gérés par systemd. Pour afficher les éléments spécifiques à `kestrel-hellomvc.service`, utilisez la commande suivante.

```bash
    sudo journalctl -fu kestrel-hellomvc.service
```

Si vous voulez appliquer un filtrage supplémentaire, des options chronologiques, comme `--since today`, `--until 1 hour ago` ou une combinaison de ces options, peuvent réduire la quantité d’entrées retournées.

```bash
    sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a>Sécurisation de votre application

### <a name="configure-firewall"></a>Configurer le pare-feu

*Firewalld* est un démon dynamique pour gérer le pare-feu avec prise en charge des zones du réseau. Vous pouvez néanmoins utiliser iptables pour gérer les ports et le filtrage des paquets. Firewalld doit être installé par défaut ; `yum` peut être utilisé pour installer ou pour vérifier le package.

```bash
    sudo yum install firewalld -y
```

Avec `firewalld`, vous pouvez ouvrir seulement les ports nécessaires à l’application. Dans ce cas, les ports 80 et 443 sont utilisés. Les commandes suivantes définissent l’ouverture de ces ports de façon permanente.

```bash
    sudo firewall-cmd --add-port=80/tcp --permanent
    sudo firewall-cmd --add-port=443/tcp --permanent
```

Rechargez les paramètres du pare-feu, et vérifiez les services et les ports disponibles dans la zone par défaut. Vous voyez les options disponibles en examinant le résultat de `firewall-cmd -h`

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

Pour configurer Apache pour SSL, le module mod_ssl est utilisé.  Ceci a été installé à l’origine quand nous avons installé le module `httpd`. S’il a été omis ou s’il n’a pas été installé, utilisez yum pour l’ajouter à votre configuration.

```bash
    sudo yum install mod_ssl
```
Pour appliquer SSL, installez `mod_rewrite`

```bash
    sudo yum install mod_rewrite
```

Le fichier `hellomvc.conf` qui a été créé pour cet exemple doit être modifié pour permettre la réécriture ainsi que l’ajout de la nouvelle section **VirtualHost** pour HTTPS.

```text
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
> Cet exemple utilise un certificat généré localement. **SSLCertificateFile** doit être votre fichier de certificat principal pour votre nom de domaine. **SSLCertificateKeyFile** doit être le fichier de clé généré quand vous avez créé la demande de signature de certificat. **SSLCertificateChainFile** doit être le fichier de certificat intermédiaire (le cas échéant) qui a été fourni par votre autorité de certification

Enregistrez le fichier et testez la configuration.

```bash
    sudo service httpd configtest
```

Redémarrez Apache.

```bash
    sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Autres suggestions pour Apache

### <a name="additional-headers"></a>En-têtes facultatifs 
Afin d’assurer une protection contre les attaques malveillantes, vous devez modifier ou ajouter quelques en-têtes. Vérifiez que le module `mod_headers` est installé.

```bash
    sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking"></a>Sécuriser Apache contre le détournement de clic
Le détournement de clic, ou clickjacking, est une technique malveillante visant à recueillir les clics d’un utilisateur infecté. Elle pousse la victime (le visiteur) à cliquer sur un site infecté. Utilisez X-FRAME-OPTIONS pour sécuriser votre site.

Modifiez le fichier httpd.conf.

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

Ajoutez la ligne `Header append X-FRAME-OPTIONS "SAMEORIGIN"` et enregistrez le fichier, puis redémarrez Apache.

#### <a name="mime-type-sniffing"></a>Détection de type MIME

Cet en-tête empêche Internet Explorer de détecter le type MIME d’une réponse et de remplacer le type de contenu déclaré, en indiquant au navigateur de ne pas remplacer le type de contenu de la réponse. Avec l’option nosniff, si le serveur indique que le contenu est « text/html », le navigateur le restitue en tant que « text/html ».

Modifiez le fichier httpd.conf.

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

Ajoutez la ligne `Header set X-Content-Type-Options "nosniff"` et enregistrez le fichier, puis redémarrez Apache.

### <a name="load-balancing"></a>Équilibrage de charge 

Cet exemple montre comment installer et configurer Apache sur CentOS 7 et Kestrel sur la même machine de l’instance.  Cependant, afin de multiplier les instances en cas de défaillance, l’utilisation de *mod_proxy_balancer* et la modification du VirtualHost permet de gérer plusieurs instances des applications web derrière le serveur proxy Apache.

```bash
    sudo yum install mod_proxy_balancer
```

Dans le fichier de configuration, une autre instance de l’application `hellomvc` a été configurée pour s’exécuter sur le port 5001, et la section *Proxy* a été définie avec une configuration d’équilibrage comprenant deux membres pour équilibrer la charge *byrequests*.

```text
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
Avec `mod_ratelimit`, qui est inclus dans le module `htttpd`, vous pouvez limiter la quantité de bande passante des clients. 

```bash
    sudo nano /etc/httpd/conf.d/ratelimit.conf
```
L’exemple de fichier limite la bande passante à 600 Ko/s sous l’emplacement racine.

```text
    <IfModule mod_ratelimit.c>
        <Location />
            SetOutputFilter RATE_LIMIT
            SetEnv rate-limit 600
        </Location>
    </IfModule>
```
