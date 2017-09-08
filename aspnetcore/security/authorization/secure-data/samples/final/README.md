# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="73521-101">Comment faire pour générer/exécuter l’exemple de données utilisateur sécurisé</span><span class="sxs-lookup"><span data-stu-id="73521-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="73521-102">Définir le mot de passe avec l’outil Gestionnaire de Secret :</span><span class="sxs-lookup"><span data-stu-id="73521-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="73521-103">Mettre à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="73521-103">Update the database:</span></span>

    `dotnet ef database update`
