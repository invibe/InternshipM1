# 2018-04-30 - B_theta human fix
Le code pour tester les orientatations et voir les perfs comparés au réseau de neurones marche : l'erreur était dans matplotlib. Pour montrer une image :

    plt.imshow(Image.read('path'))
    plt.show()

*A priori* on retrouve la même chute de détection mais plus vers 50° que 25° (à tester sur plus qu'une personne).

J'ai installé CHAMP et dépendances, il demande trop de RAM pour être utilisé sur le laptop.

# 2018-05-01 - GPU at home
J'ai installé Ubuntu 18.04 sur le GPU chez moi pour pouvoir faire tourner PyTorch le WE. Pour démarrer :

    F11 > Ubuntu > hugo

Pour faire tourner CUDA sur U-18.04, il faut prendre la 9.0 et refuser les install de drivers. Ensuite c'est classique.

Si jamais les drivers empêchent de démarrer, il faut modifier GRUB (e en selectionnant la distribution) et modifier

    ro quiet slash
    en
    ro nomodeset quiet slash

200 épochs du convo de CHAMP prennent
    ssh hugo@10.164.7.21a peu près 4 minutes à train.

# 2018-05-03 - MC to CHAMP
### CHAMP
On peut modifier les packages installés avec pip directement dans le folder d'installation, donc ici avec

    pip install -e .

on modifie directement les fichiers dans le folder InVibe Internship/W2/CHAMP. Il faut restart le kernel jupyter et bien sur re-importer les modules.

J'ai rajouté une fonction pour ouvrir et générer les motionsclouds dans Champ. Un exemple de call : il faut spécifier folder ET difficulté :

    LoadData('Clouds','clouds_medium',
    download=True, clouds_diff = 'medium')

Dans le cas des MC, l'option download appelle un module qui génère les folders de motionclouds.

### SSH
J'ai installé les dépendances de CHAMP sur babbage

Pour [ouvrir des notebooks](https://coderwall.com/p/ohk6cg/remote-access-to-ipython-notebooks-via-ssh) sur babbage :

    ssh hugo@10.164.7.21
    ipython notebook --no-browser --port=8889

    ssh -N -f -L localhost:8888:localhost:8889 username@babbage

puis

    firefox http://localhost:8888

pour terminer le serv, ctrl+C sur la machine locale

Pour envoyer des fichiers sur babbage (comme les modifs de test de CHAMP) :

    scp nomdufichier hugo@10.164.7.21:/path/to/whereyouwant/thefile

## PYTORCH ERREUR BATCH SIZE
STRUCTURER LA BOUCLE COMME CECI !!!

    for epoch in range(num_epochs):
    for i, data in enumerate(train_loader):
        images, labels = data
        images, labels = Variable(images), Variable(labels)

        images = images.view(-1,32*32)

        print(images.size())
        print(labels.size())

        # Forward + Backward + Optimize
        optimizer.zero_grad()
        outputs = model(images)
        print(outputs.size())
        print(labels.size())
        print(outputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        if (i+1) % 100 == 0:
            print ('Epoch: [%d/%d], Step: [%d/%d], Loss: %.4f'
                   % (epoch+1, num_epochs, i+1, len(train_dataset)//batch_size, loss.data[0]))

# 2018-05-04 CHAMP Logistic Regression
Pour sauvegarder les datas, pickle.dump(var, file)

# A lire
* [Pytorch Logistic Regression](https://github.com/vinhkhuc/PyTorch-Mini-Tutorials) - Pour la suite
* [Optimal eye movement strategies in visual search](https://liberalarts.utexas.edu/files/1516227) - Intégration what+where, discuter avec Pierre.

# Extras
* [Markdown cheatsheet](https://support.zendesk.com/hc/fr/articles/203691016-Formatage-de-texte-avec-Markdown) - Pour faire des jolis rapports
* [LogGabor sur Wikipedia](https://www.wikiwand.com/en/Log_Gabor_filter) - En complément de la doc
* [Générer des graphs de réseau PyTorch](https://github.com/szagoruyko/pytorchviz) - Pour faire un joli rapport