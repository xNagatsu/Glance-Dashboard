# 🚀 Glance-Dashboard
Configuration de mon dashboard Glance

## Copie du Repo

Dans un premier temps, il faut ce se rendre dans le repertoire répertoire où les fichiers de configuration de Glance se trouvent.


<details>
<summary>🥷 Installation avec docker</summary>
<details>
    <summary>Tu ne sais plus comment s'appelle ton container Docker ?</summary>
Fais la commande :

```
docker ps -a --filter ancestor=glanceapp/glance --format '{{.Names}}'
```

Cela va t'afficher le nom de ton container utilisant l'image officiel de Glance, dans mon cas ```glance```

Pour savoir où se trouve le volume de ton container docker, tu peut utiliser la commande suivante

```
#Remplace glance par le nom de ton container
docker inspect glance | grep -E '"Source"|"Destination"' | paste - - 
```

Tu regardes la "Source" correspondant au volume ```/app/config```.

</details>
<details open>
<summary>Rends toi au niveau des données persistantes de ton container</summary>

```
cd /ton/repetoire/app/config
```
Puis tu peux enfin cloner le repo ⚠️ Bien mettre le ```.``` après ```https://github.com/xNagatsu/Glance-Dashboard.git``` afin de ne pas créer le répertoire Glance-Dashboard !

```
git clone https://github.com/xNagatsu/Glance-Dashboard.git .
```

Ajoute à ton docker compose : 
```
    environment:
      - PROXMOX=${PROXMOX}
      - TECHNITIUM_1=${TECHNITIUM_1}
      - TECHNITIUM_2=${TECHNITIUM_2} 
      - GITHUB=${GITHUB}
      - CHANGE_DETECTION=${CHANGE_DETECTION}
```

Puis ajoute simplement les lignes suivantes dans l'interface Portainer (Variables d'environnement) :

```
TECHNITIUM_1=xxxxxxxxxxxxxxxxxxxxxxxxxxx
TECHNITIUM_2=xxxxxxxxxxxxxxxxxxxxxxxxxx
GITHUB=github_pat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
CHANGE_DETECTION=xxxxxxxxxxxxxxxxx
```
</details>
</details>
<details>

<summary>🧓🏼 Installation sans Docker </summary>

Rends toi au répetoire où se trouve le glance.yml par défaut c'est dans
``` /opt/glance ``` :

```
cd /opt/glance
```


Supprimes le glance.yml de base ou bien renomme le : 

```
rm glance.yml
```

ou si tu veut une back-up

```
mv glance.yml glance.yml.bkp
```

Puis tu peux enfin cloner le repo ⚠️ Bien mettre le ```.``` après ```https://github.com/xNagatsu/Glance-Dashboard.git``` afin de ne pas créer le répertoire Glance-Dashboard !

```
git clone https://github.com/xNagatsu/Glance-Dashboard.git .
```

Faite une copie du fichier ```token_api.env``` et renommer le en ```prod.env```
```
cp token_api.env prod.env
```
Puis édite-le avec tes clés API ⚠️Il ne faut pas d'espace avant ni après les = ⚠️
```
nano prod.env
```

Afin de charger les tokens API il faut éditer le service glance

```
nano  /etc/systemd/system/glance.service
```

Et on lui dit de charger le fichier ```prod.env``` AVANT de lancer le service glance.
Pour cela on ajoute la ligne dans [Service] :
```
EnvironmentFile=/opt/glance/prod.env
```
AVANT la ligne :
```
ExecStart=/opt/glance/glance --config /opt/glance/glance.yml
```
Ce qui nous donne :
```
[Unit]
Description=Glance Daemon
After=network.target

[Service]
Type=simple
WorkingDirectory=/opt/glance
EnvironmentFile=/opt/glance/prod.env
ExecStart=/opt/glance/glance --config /opt/glance/glance.yml
TimeoutStopSec=20
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Enfin, il faut recharger le daemon et le service :

```
systemctl daemon-reexec
systemctl daemon-reload
systemctl restart glance
```
</details>

<img width="1168" alt="{07947CA6-D86B-4427-852D-711551917B28}" src="https://github.com/user-attachments/assets/185af1e5-6415-482d-a332-011f235905d0" />

