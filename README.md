# 🛡️ Projet : Mon Labo de Cybersécurité (pfSense & Kali Linux)

L'objectif de ce projet est simple : **savoir mettre en place un pare-feu (firewall) de A à Z et comprendre concrètement comment ça marche sous le capot.** Plutôt que de suivre un tutoriel aveuglément, j'ai voulu créer mon propre "bac à sable" pour expérimenter, segmenter un réseau, router des flux et surtout : apprendre à diagnostiquer les problèmes quand la théorie se heurte à la pratique.

---

## 🏗️ 1. L'Architecture du Laboratoire

Pour expérimenter sans casser mon propre réseau physique, j'ai tout virtualisé sur VirtualBox en créant une vraie zone de confiance :

* **Le Pare-feu (pfSense) :** C'est le cœur du projet. Il fait office de routeur et de garde-frontière avec deux cartes réseau :
  * **WAN :** La patte connectée à Internet (via le NAT de VirtualBox, IP `10.0.2.15`).
  * **LAN :** Mon réseau privé et isolé, que j'ai nommé `monLab` (IP `192.168.1.1`).
* **Le Client (Kali Linux) :** C'est ma machine de test. Elle est enfermée dans le réseau LAN et dépend entièrement du pare-feu pfSense pour communiquer avec l'extérieur.

![Console pfSense avec adresses IP](images/etape1.png)

---

## 🛠️ 2. Le Journal de Dépannage (Troubleshooting)

Mettre en place un pare-feu, c'est aussi résoudre les blocages qui vont avec. Voici comment j'ai construit et debuggé mon labo étape par étape.

### Étape A : Le problème de branchement (vSwitch)
Au début, ma Kali ne communiquait pas du tout avec le pare-feu. 
* **Mon diagnostic :** En regardant la configuration de la VM, j'ai vu qu'elle était en mode NAT par défaut. Elle cherchait à sortir directement sur Internet au lieu de passer par mon routeur pfSense.
* **Ma solution :** J'ai modifié les paramètres réseau de Kali dans VirtualBox pour la forcer sur le "Réseau interne" (`intnet`).

![Configuration Réseau VirtualBox](images/etape2.png)

### Étape B : Le serveur DHCP fait la grève
Une fois branchée sur le bon réseau, ma machine aurait dû recevoir une adresse IP automatiquement.
* **Le problème :** En lançant la commande `dhclient`, les requêtes `DHCPDISCOVER` tournaient en boucle sans jamais recevoir d'offre (`No DHCPOFFERS received`).
* **Ma solution (Le plan B) :** Au lieu de rester bloquée, j'ai pris le contrôle en configurant une **IP statique**. C'est une étape clé pour comprendre comment on force l'identification d'une machine sur un réseau.

![Erreur DHCP](images/pb1.png)

sudo ip addr add 192.168.1.50/24 dev eth0
# J'active l'interface
sudo ip link set eth0 up
