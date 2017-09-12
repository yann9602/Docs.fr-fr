Pour générer des certificats SSL auto-signés sur Windows, vous pouvez utiliser l’applet de commande PowerShell [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pki/new-selfsignedcertificate). Il existe également des outils tiers qui le rendent plus facile de générer des certificats auto-signés :

* [SelfCert](https://www.pluralsight.com/blog/software-development/selfcert-create-a-self-signed-certificate-interactively-gui-or-programmatically-in-net)
* [Interface utilisateur de Makecert](http://makecertui.codeplex.com/)

Sur Mac OS et Linux, vous pouvez créer un certificat auto-signé à l’aide de [OpenSSL](https://www.openssl.org/).

Pour plus d’informations, consultez [configuration de HTTPS pour le développement](xref:security/https).
