# Développement ASM ARM sur x86

Ce projet a été fait avec amour pour les élèves de l'université de Sherbooke qui ne veulent pas faire leurs devoirs d'IFT209 sur les ordinateurs de l'école ou d'installer l'ÉNORME image de machine virtuelle de 15gb qui est littéralement impossible à télécharger (j'ai essayé 2 fois et j'ai abandonné).

Je vous prend donc par la main pour vous aider à configurer un environnement de développement assembly beaucoup plus légé et moderne que la solution proposée en cours.

## Prérequis

> **Utilisateurs de Linux**  
La configuration de votre machine est beaucoup plus facile. Installez d'abord [Docker Engine](https://docs.docker.com/engine/install) et passez directement à l'étape [Configurer la couche d'émulation](#configurer-la-couche-démulation) # 2.

> **Docker Desktop**  
(À vérifier) Si vous utilisez Docker Desktop pour Windows, vous pourrez directement passer à l'étape [Utiliser le Dev Container](#utiliser-le-dev-container).

Ce projet dépend des DevContainers. Vous aurez besoin des composantes suivantes pour l'exécuter correctement (sur Windows):

- *Visual Studio Code*
- WSL
- Virtualisation activée (dans le bios)

## Configurer *Visual Studio Code*
1. Installez les extensions suivantes:
    - [Container Tools](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-containers).
    - [Remote Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
    - [WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)

## Installer WSL
1. Ouvrez la fenêtre de modification des features de Windows en cliquant sur `win` + `r` pour ouvrir la fenêtre de lancement rapide, et en y entrant `optionalfeatures`.

2. Sélectionnez `Windows Subsystem For Linux` et cliquez sur **ok**. Vous serez invités à redémarer l'ordinateur. Faites-le!

3. Une console intitulée **wsl.exe** peut s'ouvrir après l'ouverture de votre session pour compléter l'installation. Appuyer sur `enter` et attendez que Windows télécharge et installe les composantes manquantes. Redémarrez l'ordinateur une dernière fois.

4. Ouvrez un nouveau terminal en cliquant sur `win` + `r` pour ouvrir la fenêtre de lancement rapide, et en y entrant `optionalfeatures`.

5. Entrez la commande suivante pour installer la distribution par défaut (Ubuntu)
```bash
wsl --install
```

6. Configurez votre utilisateur Unix par défaut (Si le terminal ne vous le propose pas automatiquement, rouvrez wsl en entrant la commande `wsl` dans le terminal).

## Installer Docker dans WSL
1. Dans *Visual Studio Code*, appuyez sur `f1` et sélectionnez `Dev Containers: Install Docker in WSL`.  
*Docker devrait être installé et correctement configuré dans votre distribution de WSL.*

## Configurer la couche d'émulation
1. Dans *Visual Studio Code*, Appuyez sur `f1` et entrez `WSL: Connect to WSL`. L'IDE devrait se recharger et vous verrez en bas à gauche de l'application un ruban bleu libellé avec le nom de votre distribution.

2. Copiez le service [qemu-binfmt.service](qemu-binfmt.service) sous le dossier suivant: `/etc/systemd/system/qemu-binfmt.service`.
```bash
# En Ayant cd la racine du projet
sudo cp ./qemu-binfmt.service /etc/systemd/system/
```

3. Activez le service de façon permanente:
```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable qemu-binfmt
sudo systemctl start qemu-binfmt
```

- **Alternativement,**  si vous ne voulez pas bisouiller avec les services, ne faites qu'exécuter la ligne suivante à chaque lancement:
    ```bash
    docker run --privileged --rm tonistiigi/binfmt --install arm64
    ```

## Utiliser le Dev Container
1. Copiez le dossier intitulé [`.devcontainer`](.devcontainer) (à partir de ce dépôt) dans le même dossier que le devoir que vous utilisez.

2. Ouvrez votre dossier de travail avec *Visual Studio Code*. Celui-ci devrait vous proposer de rouvrir le dossier dans le DevContainer. Faites-le! Si vous ne voyez pas le prompt, assurez vous que le dossier [`.devcontainer`](.devcontainer) se situe bien à la racine de votre dossier de travail. Vous pouvez forcer Visual Studio Code à lancer le Dev Container en appuyant sur `f1` et en sélectionnant `Dev Containers: Reopen in Container`.

La page va se recharger et vous verrez plusieurs lignes défiler sur un terminal inférieur.

Au premier lancement, Docker téléchargera les fichiers nécéssaires pour l'exécution de l'environnement de développement ARM. Ça prendra quelques minutes. Vous verrez apparaître vos fichiers dans l'explorateur à gauche lorsque le processus sera fini. Le libellé du ruban bleu en bas à gauche devrait aussi indiquer `armdevenv`.

3. Ouvrez un nouveau terminal, compilez votre programme avec les commandes vues en classe et exécutez le comme si vous utilisiez une vraie machine ARM !

L'image de base du DevContainer vient préinstallée avec les outils nécéssaires à la compilation de code asm:
- `gdb`
- `gcc`
- `make` 
- `ld`
- `as`

## Erreurs potentielles
### Aucune distribution de linux n'a été installée dans WSL / `wsl --install` ne fonctionne pas:
1. Redémarrez l'ordinateur.  


### Failed with error: Command failed: wsl --install -d Ubuntu
1. Vous devez activer la virtualisation dans le bios.

### Erreurs de lancement du dev container.
```log
WARNING: The requested image's platform (linux/arm64) does not match the detected host platform (linux/amd64/v3) and no specific platform was requested
exec /bin/sh: exec format error
```
1. Assurez-vous d'avoir bien suivi les instructions sous [Configurer la couche d'émulation](#configurer-la-couche-démulation).

### Je n'utilise pas Visual Studio Code
Ougabouga  
![Singe qui frappe une planche avec un marteau.](https://media1.tenor.com/m/NazUtPacRpIAAAAd/monke-orangutan.gif)
