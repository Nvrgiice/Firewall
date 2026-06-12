# 🛡️ Mon Labo de Cybersécurité : pfSense & Kali Linux

## 🎯 Pourquoi ce projet ?
J'ai voulu créer un petit "bac à sable" sécurisé sur mon ordi pour apprendre comment fonctionne la sécurité réseau. Le but, c'était de créer un environnement où je contrôle tout : qui rentre, qui sort, et surtout, comment on protège une machine (ma Kali) derrière un pare-feu (pfSense).

## 🏗️ Comment j'ai installé mon labo
J'ai utilisé VirtualBox pour simuler un petit réseau privé, totalement isolé de mon vrai Internet.

* **Le Pare-feu (pfSense) :** C'est le chef de la sécurité. Il a deux "pattes" :
  * **WAN :** Il se connecte à Internet pour aller chercher les infos.
  * **LAN :** Il gère mon réseau privé (que j'ai appelé `monLab`). C'est lui qui distribue les accès aux autres machines.
* **La machine cliente (Kali Linux) :** C'est ma machine d'attaque. Elle est "enfermée" dans mon réseau `monLab`. Elle ne peut pas sortir sur Internet si pfSense ne lui donne pas la permission

![Architecture de mon labo](images/nom_de_ta_capture_pfsense_console.png)

---

## 🛠️ Mes galères et comment je les ai résolues
Le réseau, c'est parfois capricieux ! Voici les problèmes que j'ai rencontrés et ce que j'ai appris en les réparant.

### 1. Le problème de "branchement" (vSwitch)
Au début, ma Kali ne trouvait pas pfSense. Elle cherchait partout, mais personne ne lui répondait.
* **Le diagnostic :** En tapant `ip a`, j'ai vu que ma Kali n'avait pas d'adresse IP dans mon réseau. Elle n'était pas branchée sur le bon "switch virtuel".
* **La solution :** J'ai dû modifier les paramètres réseau dans VirtualBox pour brancher ma Kali sur le réseau `monLab` au lieu du réseau par défaut.

![Erreur de connexion](images/nom_de_ta_capture_kali_erreur.png)

### 2. Le "Plan B" : Forcer la connexion
Le serveur automatique (DHCP) de pfSense faisait un peu la tête. Alors, au lieu de rester bloquée, j'ai configuré l'adresse IP de ma Kali à la main (en "statique"). C'est comme si j'avais donné un numéro de bureau fixe à ma machine pour qu'elle soit sûre d'être trouvée.

```bash
# Je donne manuellement une adresse IP à ma carte réseau
sudo ip addr add 192.168.1.50/24 dev eth0

# J'allume la carte pour qu'elle commence à travailler
sudo ip link set eth0 up
