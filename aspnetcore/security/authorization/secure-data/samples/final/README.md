# <a name="how-to-buildrun-secure-user-data-sample"></a>Comment faire pour générer/exécuter l’exemple de données utilisateur sécurisé

* Définir le mot de passe avec l’outil Gestionnaire de Secret :

  `dotnet user-secrets set SeedUserPW <pw>`

* Mettre à jour la base de données :

    `dotnet ef database update`
