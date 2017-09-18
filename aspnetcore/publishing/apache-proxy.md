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
ms.openlocfilehash: 9dc22ea20a6ae2e2477f9e6db95ddabecc038dcb
ms.sourcegitcommit: f8f6b5934bd071a349f5bc1e389365c52b1c00fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2017
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-apache-and-deploy-to-it"></a><span data-ttu-id="3228c-104">Configurer un environnement d’hébergement pour ASP.NET Core sur Linux avec Apache et déployer sur celui-ci</span><span class="sxs-lookup"><span data-stu-id="3228c-104">Set up a hosting environment for ASP.NET Core on Linux with Apache, and deploy to it</span></span>

<span data-ttu-id="3228c-105">Par [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="3228c-105">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="3228c-106">Apache est un serveur HTTP très répandu et qui peut être configuré comme proxy pour rediriger le trafic HTTP, de façon similaire à nginx.</span><span class="sxs-lookup"><span data-stu-id="3228c-106">Apache is a very popular HTTP server and can be configured as a proxy to redirect HTTP traffic similar to nginx.</span></span> <span data-ttu-id="3228c-107">Dans ce guide, vous découvrez comment configurer Apache sur CentOS 7, et comment l’utiliser comme proxy inverse pour accueillir les connexions entrantes et les rediriger vers l’application ASP.NET Core s’exécutant sur Kestrel.</span><span class="sxs-lookup"><span data-stu-id="3228c-107">In this guide, we will learn how to set up Apache on CentOS 7 and use it as a reverse proxy to welcome incoming connections and redirect them to the ASP.NET Core application running on Kestrel.</span></span> <span data-ttu-id="3228c-108">Pour cela, nous utiliserons l’extension *mod_proxy* et d’autres modules Apache associés.</span><span class="sxs-lookup"><span data-stu-id="3228c-108">For this purpose, we will use the *mod_proxy* extension and other related Apache modules.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3228c-109">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="3228c-109">Prerequisites</span></span>

1. <span data-ttu-id="3228c-110">Un serveur exécutant CentOS 7 avec un compte d’utilisateur standard disposant du privilège sudo.</span><span class="sxs-lookup"><span data-stu-id="3228c-110">A server running CentOS 7, with a standard user account with sudo privilege.</span></span>
2. <span data-ttu-id="3228c-111">Une application ASP.NET Core existante.</span><span class="sxs-lookup"><span data-stu-id="3228c-111">An existing ASP.NET Core application.</span></span> 

## <a name="publish-your-application"></a><span data-ttu-id="3228c-112">Publier votre application</span><span class="sxs-lookup"><span data-stu-id="3228c-112">Publish your application</span></span>

<span data-ttu-id="3228c-113">Exécutez `dotnet publish -c Release` à partir de votre environnement de développement pour empaqueter votre application dans un répertoire autonome qui peut s’exécuter sur votre serveur.</span><span class="sxs-lookup"><span data-stu-id="3228c-113">Run `dotnet publish -c Release` from your development environment to package your application into a self-contained directory that can run on your server.</span></span> <span data-ttu-id="3228c-114">L’application publiée doit ensuite être copiée sur le serveur en utilisant SCP, FTP ou une autre méthode de transfert de fichiers.</span><span class="sxs-lookup"><span data-stu-id="3228c-114">The published application must then be copied to the server using SCP, FTP or other file transfer method.</span></span> 

> [!NOTE]
> <span data-ttu-id="3228c-115">Dans un scénario de déploiement en production, un workflow d’intégration continue effectue le travail de publication de l’application et de copie des composants sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="3228c-115">Under a production deployment scenario, a continuous integration workflow does the work of publishing the application and copying the assets to the server.</span></span> 

## <a name="configure-a-proxy-server"></a><span data-ttu-id="3228c-116">Configurer un serveur proxy</span><span class="sxs-lookup"><span data-stu-id="3228c-116">Configure a proxy server</span></span>

<span data-ttu-id="3228c-117">Un proxy inverse est une configuration courante pour traiter les applications web dynamiques.</span><span class="sxs-lookup"><span data-stu-id="3228c-117">A reverse proxy is a common setup for serving dynamic web applications.</span></span> <span data-ttu-id="3228c-118">Le proxy inverse met fin à la requête HTTP et la transfère à l’application ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="3228c-118">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET application.</span></span>

<span data-ttu-id="3228c-119">Un serveur proxy est un serveur qui transfère les demandes des clients à un autre serveur, au lieu de les traiter lui-même.</span><span class="sxs-lookup"><span data-stu-id="3228c-119">A proxy server is one which forwards client requests to another server instead of fulfilling them itself.</span></span> <span data-ttu-id="3228c-120">Un proxy inverse transfère à une destination fixe, en général pour le compte de clients arbitraires.</span><span class="sxs-lookup"><span data-stu-id="3228c-120">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="3228c-121">Dans ce guide, Apache est configuré comme proxy inverse s’exécutant sur le même serveur où Kestrel traite l’application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3228c-121">In this guide, Apache is being configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core application.</span></span> 

<span data-ttu-id="3228c-122">Chaque composant de l’application peut exister sur des machines physiques distinctes, sur des conteneurs Docker ou sur une combinaison de configurations en fonction de vos besoins en termes d’architecture ou des restrictions.</span><span class="sxs-lookup"><span data-stu-id="3228c-122">Each piece of the application can exist on separate physical machines, Docker containers, or a combination of configurations depending on your architectural needs or restrictions.</span></span>

### <a name="install-apache"></a><span data-ttu-id="3228c-123">Installer Apache</span><span class="sxs-lookup"><span data-stu-id="3228c-123">Install Apache</span></span>

<span data-ttu-id="3228c-124">L’installation du serveur web Apache sur CentOS se fait en une seule commande, mais pour commencer, nous mettons d’abord à jour nos packages.</span><span class="sxs-lookup"><span data-stu-id="3228c-124">Installing the Apache web server on CentOS is a single command, but first let's update our packages.</span></span>

```bash
    sudo yum update -y
```

<span data-ttu-id="3228c-125">Ceci garantit que tous les packages installés sont mis à jour vers leur dernière version.</span><span class="sxs-lookup"><span data-stu-id="3228c-125">This ensures that all of the installed packages are updated to their latest version.</span></span> <span data-ttu-id="3228c-126">Installer Apache en utilisant `yum`</span><span class="sxs-lookup"><span data-stu-id="3228c-126">Install Apache using `yum`</span></span>

```bash
    sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="3228c-127">La sortie doit être similaire à ceci.</span><span class="sxs-lookup"><span data-stu-id="3228c-127">The output should reflect something similar to the following.</span></span>

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
> <span data-ttu-id="3228c-128">Dans cet exemple, la sortie correspond à httpd.86_64, car la version de CentOS 7 est en 64 bits.</span><span class="sxs-lookup"><span data-stu-id="3228c-128">In this example the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="3228c-129">La sortie peut être différente pour votre serveur.</span><span class="sxs-lookup"><span data-stu-id="3228c-129">The output may be different for your server.</span></span> <span data-ttu-id="3228c-130">Pour vérifier où Apache est installé, exécutez `whereis httpd` à partir d’une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="3228c-130">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span> 

### <a name="configure-apache-for-reverse-proxy"></a><span data-ttu-id="3228c-131">Configurer Apache pour le proxy inverse</span><span class="sxs-lookup"><span data-stu-id="3228c-131">Configure Apache for reverse proxy</span></span>

<span data-ttu-id="3228c-132">Les fichiers de configuration pour Apache se trouvent dans le répertoire `/etc/httpd/conf.d/`.</span><span class="sxs-lookup"><span data-stu-id="3228c-132">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="3228c-133">Les fichiers avec l’extension **.conf** sont traités par ordre alphabétique en plus des fichiers de configuration des modules dans `/etc/httpd/conf.modules.d/`, qui contient tous les fichiers de configuration nécessaires au chargement des modules.</span><span class="sxs-lookup"><span data-stu-id="3228c-133">Any file with the **.conf** extension will be processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="3228c-134">Créez un fichier de configuration pour votre application. Pour cet exemple, nous l’appellerons `hellomvc.conf`.</span><span class="sxs-lookup"><span data-stu-id="3228c-134">Create a configuration file for your app, for this example we'll call it `hellomvc.conf`</span></span>

```text
    <VirtualHost *:80>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ErrorLog /var/log/httpd/hellomvc-error.log
        CustomLog /var/log/httpd/hellomvc-access.log common
    </VirtualHost>
```

<span data-ttu-id="3228c-135">Le nœud *VirtualHost*, qui peut exister en plusieurs exemplaires dans un fichier ou sur un serveur dans de nombreux fichiers, est défini pour écouter sur n’importe quelle adresse IP en utilisant le port 80.</span><span class="sxs-lookup"><span data-stu-id="3228c-135">The *VirtualHost* node, of which there can be multiple in a file or on a server in many files, is set to listen on any IP address using port 80.</span></span> <span data-ttu-id="3228c-136">Les deux lignes sont définies pour passer toutes les demandes reçues au niveau de la racine à la machine 127.0.0.1 sur le port 5000 et inversement.</span><span class="sxs-lookup"><span data-stu-id="3228c-136">The next two lines are set to pass all requests received at the root to the machine 127.0.0.1 port 5000 and in reverse.</span></span> <span data-ttu-id="3228c-137">Pour permettre à cet endroit une communication bidirectionnelle, les deux paramètres *ProxyPass* et *ProxyPassReverse* sont obligatoires.</span><span class="sxs-lookup"><span data-stu-id="3228c-137">For there to be bi-directional communication, both settings *ProxyPass* and *ProxyPassReverse* are required.</span></span>

<span data-ttu-id="3228c-138">La journalisation peut être configurée par VirtualHost à l’aide des directives *ErrorLog* et *CustomLog*.</span><span class="sxs-lookup"><span data-stu-id="3228c-138">Logging can be configured per VirtualHost using *ErrorLog* and *CustomLog* directives.</span></span> <span data-ttu-id="3228c-139">*ErrorLog* est l’emplacement où le serveur consigne les erreurs, et *CustomLog* définit le nom de fichier et le format du fichier journal.</span><span class="sxs-lookup"><span data-stu-id="3228c-139">*ErrorLog* is the location where the server will log errors and *CustomLog* sets the filename and format of log file.</span></span> <span data-ttu-id="3228c-140">Dans notre cas, c’est l’endroit où les informations des demandes sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="3228c-140">In our case this is where request information will be logged.</span></span> <span data-ttu-id="3228c-141">Il y aura une ligne pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="3228c-141">There will be one line for each request.</span></span>

<span data-ttu-id="3228c-142">Enregistrez le fichier et testez la configuration.</span><span class="sxs-lookup"><span data-stu-id="3228c-142">Save the file, and test the configuration.</span></span> <span data-ttu-id="3228c-143">Si tout réussit, la réponse doit être `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="3228c-143">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
    sudo service httpd configtest
```

<span data-ttu-id="3228c-144">Redémarrez Apache.</span><span class="sxs-lookup"><span data-stu-id="3228c-144">Restart Apache.</span></span>

```text
    sudo systemctl restart httpd
    sudo systemctl enable httpd
```

## <a name="monitoring-our-application"></a><span data-ttu-id="3228c-145">Surveillance de notre application</span><span class="sxs-lookup"><span data-stu-id="3228c-145">Monitoring our application</span></span>

<span data-ttu-id="3228c-146">Apache est désormais configuré pour transférer les demandes faites à `http://localhost:80` vers l’application ASP.NET Core s’exécutant sur Kestrel à l’adresse `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="3228c-146">Apache is now setup to forward requests made to `http://localhost:80` on to the ASP.NET Core application running on Kestrel at `http://127.0.0.1:5000`.</span></span>  <span data-ttu-id="3228c-147">Cependant, Apache n’est pas configuré pour gérer le processus Kestrel.</span><span class="sxs-lookup"><span data-stu-id="3228c-147">However, Apache is not set up to manage the Kestrel process.</span></span> <span data-ttu-id="3228c-148">Nous utilisons *systemd* pour créer un fichier de service pour démarrer et surveiller l’application web sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="3228c-148">We will use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="3228c-149">*systemd* est un système d’initialisation qui offre de nombreuses fonctionnalités puissantes pour le démarrage, l’arrêt et la gestion des processus.</span><span class="sxs-lookup"><span data-stu-id="3228c-149">*systemd* is an init system that provides many powerful features for starting, stopping and managing processes.</span></span> 


### <a name="create-the-service-file"></a><span data-ttu-id="3228c-150">Créer le fichier de service</span><span class="sxs-lookup"><span data-stu-id="3228c-150">Create the service file</span></span>

<span data-ttu-id="3228c-151">Créez le fichier de définition de service</span><span class="sxs-lookup"><span data-stu-id="3228c-151">Create the service definition file</span></span> 

```bash
    sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

<span data-ttu-id="3228c-152">Un exemple de fichier de service pour notre application.</span><span class="sxs-lookup"><span data-stu-id="3228c-152">An example service file for our application.</span></span>

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
> <span data-ttu-id="3228c-153">**Remarque :** Si l’utilisateur *apache* n’est pas utilisé par votre configuration, vous devez d’abord créer l’utilisateur défini ici et l’affecter en tant que propriétaire des fichiers.</span><span class="sxs-lookup"><span data-stu-id="3228c-153">**User** -- If the user *apache* is not used by your configuration, the user defined here must be created first and given proper ownership for files</span></span>

<span data-ttu-id="3228c-154">Enregistrez le fichier et activez le service.</span><span class="sxs-lookup"><span data-stu-id="3228c-154">Save the file and enable the service.</span></span>

```bash
    systemctl enable kestrel-hellomvc.service
```

<span data-ttu-id="3228c-155">Démarrez le service et vérifiez qu’il s’exécute.</span><span class="sxs-lookup"><span data-stu-id="3228c-155">Start the service and verify that it is running.</span></span>

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

<span data-ttu-id="3228c-156">Le proxy inverse étant configuré et Kestrel étant géré via systemd, l’application web est maintenant entièrement configurée et accessible à partir d’un navigateur sur la machine locale à l’adresse `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="3228c-156">With the reverse proxy configured and Kestrel managed through systemd, the web application is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="3228c-157">L’examen des en-têtes de réponse indique que le **Serveur** montre que l’application ASP.NET Core est traitée par Kestrel.</span><span class="sxs-lookup"><span data-stu-id="3228c-157">Inspecting the response headers, the **Server** still shows the ASP.NET Core application being served by Kestrel.</span></span>

```text
    HTTP/1.1 200 OK
    Date: Tue, 11 Oct 2016 16:22:23 GMT
    Server: Kestrel
    Keep-Alive: timeout=5, max=98
    Connection: Keep-Alive
    Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a><span data-ttu-id="3228c-158">Affichage des journaux</span><span class="sxs-lookup"><span data-stu-id="3228c-158">Viewing logs</span></span>

<span data-ttu-id="3228c-159">Comme l’application web utilisant Kestrel est gérée via systemd, tous les événements et processus sont enregistrés dans un journal centralisé.</span><span class="sxs-lookup"><span data-stu-id="3228c-159">Since the web application using Kestrel is managed using systemd, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="3228c-160">Cependant, ce journal comprend toutes les entrées pour tous les services et processus gérés par systemd.</span><span class="sxs-lookup"><span data-stu-id="3228c-160">However, this journal includes all entries for all services and processes managed by systemd.</span></span> <span data-ttu-id="3228c-161">Pour afficher les éléments spécifiques à `kestrel-hellomvc.service`, utilisez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="3228c-161">To view the `kestrel-hellomvc.service` specific items, use the following command.</span></span>

```bash
    sudo journalctl -fu kestrel-hellomvc.service
```

<span data-ttu-id="3228c-162">Si vous voulez appliquer un filtrage supplémentaire, des options chronologiques, comme `--since today`, `--until 1 hour ago` ou une combinaison de ces options, peuvent réduire la quantité d’entrées retournées.</span><span class="sxs-lookup"><span data-stu-id="3228c-162">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
    sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a><span data-ttu-id="3228c-163">Sécurisation de votre application</span><span class="sxs-lookup"><span data-stu-id="3228c-163">Securing our application</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="3228c-164">Configurer le pare-feu</span><span class="sxs-lookup"><span data-stu-id="3228c-164">Configure firewall</span></span>

<span data-ttu-id="3228c-165">*Firewalld* est un démon dynamique pour gérer le pare-feu avec prise en charge des zones du réseau. Vous pouvez néanmoins utiliser iptables pour gérer les ports et le filtrage des paquets.</span><span class="sxs-lookup"><span data-stu-id="3228c-165">*Firewalld* is a dynamic daemon to manage firewall with support for network zones, although you can still use iptables to manage ports and packet filtering.</span></span> <span data-ttu-id="3228c-166">Firewalld doit être installé par défaut ; `yum` peut être utilisé pour installer ou pour vérifier le package.</span><span class="sxs-lookup"><span data-stu-id="3228c-166">Firewalld should be installed by default, `yum` can be used to install the package or verify.</span></span>

```bash
    sudo yum install firewalld -y
```

<span data-ttu-id="3228c-167">Avec `firewalld`, vous pouvez ouvrir seulement les ports nécessaires à l’application.</span><span class="sxs-lookup"><span data-stu-id="3228c-167">Using `firewalld` you can open only the ports needed for the application.</span></span> <span data-ttu-id="3228c-168">Dans ce cas, les ports 80 et 443 sont utilisés.</span><span class="sxs-lookup"><span data-stu-id="3228c-168">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="3228c-169">Les commandes suivantes définissent l’ouverture de ces ports de façon permanente.</span><span class="sxs-lookup"><span data-stu-id="3228c-169">The following commands permanently sets these to open.</span></span>

```bash
    sudo firewall-cmd --add-port=80/tcp --permanent
    sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="3228c-170">Rechargez les paramètres du pare-feu, et vérifiez les services et les ports disponibles dans la zone par défaut.</span><span class="sxs-lookup"><span data-stu-id="3228c-170">Reload the firewall settings, and check the available services and ports in the default zone.</span></span> <span data-ttu-id="3228c-171">Vous voyez les options disponibles en examinant le résultat de `firewall-cmd -h`</span><span class="sxs-lookup"><span data-stu-id="3228c-171">Options are available by inspecting `firewall-cmd -h`</span></span>

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

### <a name="ssl-configuration"></a><span data-ttu-id="3228c-172">Configuration de SSL</span><span class="sxs-lookup"><span data-stu-id="3228c-172">SSL configuration</span></span>

<span data-ttu-id="3228c-173">Pour configurer Apache pour SSL, le module mod_ssl est utilisé.</span><span class="sxs-lookup"><span data-stu-id="3228c-173">To configure Apache for SSL, the mod_ssl module is used.</span></span>  <span data-ttu-id="3228c-174">Ceci a été installé à l’origine quand nous avons installé le module `httpd`.</span><span class="sxs-lookup"><span data-stu-id="3228c-174">This was installed initially when we installed the `httpd` module.</span></span> <span data-ttu-id="3228c-175">S’il a été omis ou s’il n’a pas été installé, utilisez yum pour l’ajouter à votre configuration.</span><span class="sxs-lookup"><span data-stu-id="3228c-175">If it was missed or not installed, use yum to add it to your configuration.</span></span>

```bash
    sudo yum install mod_ssl
```
<span data-ttu-id="3228c-176">Pour appliquer SSL, installez `mod_rewrite`</span><span class="sxs-lookup"><span data-stu-id="3228c-176">To enforce SSL, install `mod_rewrite`</span></span>

```bash
    sudo yum install mod_rewrite
```

<span data-ttu-id="3228c-177">Le fichier `hellomvc.conf` qui a été créé pour cet exemple doit être modifié pour permettre la réécriture ainsi que l’ajout de la nouvelle section **VirtualHost** pour HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3228c-177">The `hellomvc.conf` file that was created for this example needs to be modified to enable the rewrite as well as adding the new **VirtualHost** section for HTTPS.</span></span>

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
> <span data-ttu-id="3228c-178">Cet exemple utilise un certificat généré localement.</span><span class="sxs-lookup"><span data-stu-id="3228c-178">This example is using a locally generated certificate.</span></span> <span data-ttu-id="3228c-179">**SSLCertificateFile** doit être votre fichier de certificat principal pour votre nom de domaine.</span><span class="sxs-lookup"><span data-stu-id="3228c-179">**SSLCertificateFile** should be your primary certificate file for your domain name.</span></span> <span data-ttu-id="3228c-180">**SSLCertificateKeyFile** doit être le fichier de clé généré quand vous avez créé la demande de signature de certificat.</span><span class="sxs-lookup"><span data-stu-id="3228c-180">**SSLCertificateKeyFile** should be the key file generated when you created the CSR.</span></span> <span data-ttu-id="3228c-181">**SSLCertificateChainFile** doit être le fichier de certificat intermédiaire (le cas échéant) qui a été fourni par votre autorité de certification</span><span class="sxs-lookup"><span data-stu-id="3228c-181">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by your certificate authority</span></span>

<span data-ttu-id="3228c-182">Enregistrez le fichier et testez la configuration.</span><span class="sxs-lookup"><span data-stu-id="3228c-182">Save the file, and test the configuration.</span></span>

```bash
    sudo service httpd configtest
```

<span data-ttu-id="3228c-183">Redémarrez Apache.</span><span class="sxs-lookup"><span data-stu-id="3228c-183">Restart Apache.</span></span>

```bash
    sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="3228c-184">Autres suggestions pour Apache</span><span class="sxs-lookup"><span data-stu-id="3228c-184">Additional Apache suggestions</span></span>

### <a name="additional-headers"></a><span data-ttu-id="3228c-185">En-têtes facultatifs</span><span class="sxs-lookup"><span data-stu-id="3228c-185">Additional Headers</span></span> 
<span data-ttu-id="3228c-186">Afin d’assurer une protection contre les attaques malveillantes, vous devez modifier ou ajouter quelques en-têtes.</span><span class="sxs-lookup"><span data-stu-id="3228c-186">In order to secure against malicious attacks there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="3228c-187">Vérifiez que le module `mod_headers` est installé.</span><span class="sxs-lookup"><span data-stu-id="3228c-187">Ensure that the `mod_headers` module is installed.</span></span>

```bash
    sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking"></a><span data-ttu-id="3228c-188">Sécuriser Apache contre le détournement de clic</span><span class="sxs-lookup"><span data-stu-id="3228c-188">Secure Apache from clickjacking</span></span>
<span data-ttu-id="3228c-189">Le détournement de clic, ou clickjacking, est une technique malveillante visant à recueillir les clics d’un utilisateur infecté.</span><span class="sxs-lookup"><span data-stu-id="3228c-189">Clickjacking is a malicious technique to collect an infected user's clicks.</span></span> <span data-ttu-id="3228c-190">Elle pousse la victime (le visiteur) à cliquer sur un site infecté.</span><span class="sxs-lookup"><span data-stu-id="3228c-190">Clickjacking tricks the victim (visitor) into clicking on an infected site.</span></span> <span data-ttu-id="3228c-191">Utilisez X-FRAME-OPTIONS pour sécuriser votre site.</span><span class="sxs-lookup"><span data-stu-id="3228c-191">Use X-FRAME-OPTIONS to secure your site.</span></span>

<span data-ttu-id="3228c-192">Modifiez le fichier httpd.conf.</span><span class="sxs-lookup"><span data-stu-id="3228c-192">Edit the httpd.conf file.</span></span>

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="3228c-193">Ajoutez la ligne `Header append X-FRAME-OPTIONS "SAMEORIGIN"` et enregistrez le fichier, puis redémarrez Apache.</span><span class="sxs-lookup"><span data-stu-id="3228c-193">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"` and save the file, then restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="3228c-194">Détection de type MIME</span><span class="sxs-lookup"><span data-stu-id="3228c-194">MIME-type sniffing</span></span>

<span data-ttu-id="3228c-195">Cet en-tête empêche Internet Explorer de détecter le type MIME d’une réponse et de remplacer le type de contenu déclaré, en indiquant au navigateur de ne pas remplacer le type de contenu de la réponse.</span><span class="sxs-lookup"><span data-stu-id="3228c-195">This header prevents Internet Explorer from MIME-sniffing a response away from the declared content-type as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="3228c-196">Avec l’option nosniff, si le serveur indique que le contenu est « text/html », le navigateur le restitue en tant que « text/html ».</span><span class="sxs-lookup"><span data-stu-id="3228c-196">With the nosniff option, if the server says the content is text/html, the browser will render it as text/html.</span></span>

<span data-ttu-id="3228c-197">Modifiez le fichier httpd.conf.</span><span class="sxs-lookup"><span data-stu-id="3228c-197">Edit the httpd.conf file.</span></span>

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="3228c-198">Ajoutez la ligne `Header set X-Content-Type-Options "nosniff"` et enregistrez le fichier, puis redémarrez Apache.</span><span class="sxs-lookup"><span data-stu-id="3228c-198">Add the line `Header set X-Content-Type-Options "nosniff"` and save the file, then restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="3228c-199">Équilibrage de charge</span><span class="sxs-lookup"><span data-stu-id="3228c-199">Load Balancing</span></span> 

<span data-ttu-id="3228c-200">Cet exemple montre comment installer et configurer Apache sur CentOS 7 et Kestrel sur la même machine de l’instance.</span><span class="sxs-lookup"><span data-stu-id="3228c-200">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span>  <span data-ttu-id="3228c-201">Cependant, afin de multiplier les instances en cas de défaillance, l’utilisation de *mod_proxy_balancer* et la modification du VirtualHost permet de gérer plusieurs instances des applications web derrière le serveur proxy Apache.</span><span class="sxs-lookup"><span data-stu-id="3228c-201">However, in order to not have a single point of failure; using *mod_proxy_balancer* and modifying the VirtualHost would allow for managing mutliple instances of the web applications behind the Apache proxy server.</span></span>

```bash
    sudo yum install mod_proxy_balancer
```

<span data-ttu-id="3228c-202">Dans le fichier de configuration, une autre instance de l’application `hellomvc` a été configurée pour s’exécuter sur le port 5001, et la section *Proxy* a été définie avec une configuration d’équilibrage comprenant deux membres pour équilibrer la charge *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="3228c-202">In the configuration file, an additional instance of the `hellomvc` app has been setup to run on port 5001 and the *Proxy* section has been set with a balancer configuration with two members to load balance *byrequests*.</span></span>

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

### <a name="rate-limits"></a><span data-ttu-id="3228c-203">Limites du débit</span><span class="sxs-lookup"><span data-stu-id="3228c-203">Rate Limits</span></span>
<span data-ttu-id="3228c-204">Avec `mod_ratelimit`, qui est inclus dans le module `htttpd`, vous pouvez limiter la quantité de bande passante des clients.</span><span class="sxs-lookup"><span data-stu-id="3228c-204">Using `mod_ratelimit`, which is included in the `htttpd` module you can limit the amount of bandwidth of clients.</span></span> 

```bash
    sudo nano /etc/httpd/conf.d/ratelimit.conf
```
<span data-ttu-id="3228c-205">L’exemple de fichier limite la bande passante à 600 Ko/s sous l’emplacement racine.</span><span class="sxs-lookup"><span data-stu-id="3228c-205">The example file limits bandwidth as 600 KB/sec under the root location.</span></span>

```text
    <IfModule mod_ratelimit.c>
        <Location />
            SetOutputFilter RATE_LIMIT
            SetEnv rate-limit 600
        </Location>
    </IfModule>
```
