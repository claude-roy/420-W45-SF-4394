# Utilisation de CyberQuébec

Voici les informations pour l'utilisation de l'environnement de CyberQuébec.

### Connexion au VPN
Pour vous connecter au VPN, naviguez à l'adresse [https://vpn.cyberquebec.org](https://vpn.cyberquebec.org) et entrez avec votre compte.

### Connexion au CyberRange
Une fois connectée au VPN, cliquez sur **OpenNebula** ou sur **OpenNebula - Preview** et utilisez les mêmes identifiants pour rejoindre le CyberRange.

![Connexion au VPN](img/ConnexionVPN.png)


### Connexion aux machines virtuelles avec OpenNebula  
Une fois connectée, vous arrivez à votre tableau de bord. Pour accéder à vos VMs, cliquez sur le carré du groupe.

![Accès aux VMs](img/groupeVMs.png)

Cliquez sur la VM que vous voulez accéder.  

![Accès à une VM](img/AccesVM.png)


Pour vous connecter sur une machine, cliquez sur l'icône d'écran vis-à-vis la machine souhaitée et choisissez VNC. 
 
![Connexion à une VM](img/ConnexionVM.png)


Les identifiants des machines virtuelles sont :  
- Nom d'utilisateur: env-admin  
- Mot de passe: admin-env

Les VMs serveurs ont des adresses statiques. Vous pouvez leur assigner une adresse dans le réseau 192.168.1.0/24 ou utiliser la commande :

```bash
sudo dhclient -v ens3
```

### Connexion aux machines virtuelles avec OpenNebula - Preview  
Une fois connectée, vous arrivez à votre tableau de bord. Pour accéder à vos VMs, cliquez sur le + à côté d'Instances.

![Ouvrir Instances](img/OuvrirInstances.png)

Cliquez sur VMs.  
![Ouvrir VMs](img/OuvrirVMs.png)

Pour vous connecter sur une machine, cliquez sur l'icône d'écran vis-à-vis la machine souhaitée. 
 
![Connexion à une VM](img/ConnexionVM2.png)


Les identifiants des machines virtuelles sont :  
- Nom d'utilisateur: env-admin  
- Mot de passe: admin-env

Les VMs serveurs ont des adresses statiques. Vous pouvez leur assigner une adresse dans le réseau 192.168.1.0/24 ou utiliser la commande :

```bash
sudo dhclient -v ens3
```

