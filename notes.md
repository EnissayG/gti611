━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IPv6 — STRUCTURE DE BASE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
128 bits = 8 groupes de 16 bits en HEX

0000:0000:0000:0000:0000:0000:0000:0000
 G1   G2   G3   G4   G5   G6   G7   G8
|______ 64 bits NetID ______|__ 64 bits Interface __|

1 groupe = 4 chiffres hex = 16 bits
1 chiffre hex = 4 bits

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IPv6 — LIRE UNE ADRESSE EXISTANTE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ Q6 EXAMEN — TYPE DE QUESTION:
On te donne l'IP d'un hôte → trouver @réseau

RÈGLE SELON LE MASQUE:
  /64 → garder G1:G2:G3:G4 → mettre ::/64
  /60 → garder G1:G2:G3 + 12 premiers bits G4
  /52 → garder G1:G2:G3 + 4 premiers bits G4

MÉTHODE /64 (le plus fréquent):
  IP: 2002:a45b:3c78:c900:b1ba:48fd:fe01:aaaa
  → Couper après le 4e groupe
  → @réseau = 2002:a45b:3c78:c900::/64 ✅

MÉTHODE masque non-multiple de 16:
  IP: 2002:a45b:3c78:c931:550a:48fd:feaa:1111
  Masque /? → regarder le contexte
  → garder les N bits → mettre reste à 0

SOUS-RÉSEAU ENTRE 2 ROUTEURS:
  R1 interface: 2002:a45b:3c78:c930:120a:48fd:feaa:4444
  R2 interface: 2002:a45b:3c78:c930:990a:48fd:feaa:3333
  → Même préfixe /64 = 2002:a45b:3c78:c930::/64 ✅
  (les 4 premiers groupes sont identiques)

PRÉFIXE /48 (1 seul site):
  Prendre les 3 premiers groupes + ::/48
  Ex: 2002:a45b:3c78:c900::/64
  → /48 = 2002:a45b:3c78::/48

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IPv6 — HIÉRARCHIE & CONSTRUCTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
/44    /48    /52    /60      /64
Org → Site → Empl → Succ → Dept
      4bits  4bits  8bits   4bits
      16SR   16SR   256SR   16SR

Nb SR = 2^(masque_suivant - masque_actuel)
Ex: /48→/52 = 4 bits = 2^4 = 16 emplacements

3e GROUPE = encode le SITE:
  Base ex: 1200 → dernier chiffre = site
  Site N → (N-1) hex → dernier chiffre
  Site 1=1200  Site 2=1201  Site 12=120B

4e GROUPE = [EMPL][SUCC][SUCC][DEPT]:
  Empl N → (N-1) → pos 1
  Succ N → (N-1) sur 2 chiffres → pos 2+3
  Dept N → (N-1) → pos 4

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CONVERSION DEC → HEX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
10=A  11=B  12=C  13=D  14=E  15=F
16=10  17=11  18=12  19=13  20=14
21=15  22=16  23=17  24=18  25=19
26=1A  27=1B  28=1C  29=1D  30=1E
31=1F  32=20

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
EXEMPLE CONSTRUCTION (examen révision)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Préfixe: 2001:abcd:1200::/44
→ 2e dept, 3e succ, 3e empl, 2e site

1. Site 2  → 1200+(2-1)=1201      /48
2. Empl 3  → 3-1=2 → [2]___      /52
3. Succ 3  → 3-1=2 → 02 → [2][02]_ /60
4. Dept 2  → 2-1=1 → [2][02][1]  /64
✅ 2001:abcd:1201:2021::/64

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IPv4 — VLSM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TROUVER @RÉSEAU depuis IP/masque:
  Taille bloc = 2^(32-masque)
  @réseau = début du bloc où tombe l'IP

BLOCS PAR MASQUE:
  /25 → 128: 0, 128
  /26 → 64:  0, 64, 128, 192
  /27 → 32:  0, 32, 64, 96, 128...
  /28 → 16:  0, 16, 32, 48...
  /30 → 4    (lien P-P = 2 hôtes)

HÔTES UTILISABLES:
  /25=126  /26=62  /27=30
  /28=14   /29=6   /30=2

TROUVER MASQUE depuis nb hôtes H:
  n = ceil(log2(H+2)) → masque = /32-n
  Ex: 100h → n=7 → /25

EXEMPLES Q6 EXAMEN 2021:
  209.56.10.150/25 → bloc 128 → @réseau=209.56.10.128/25
  209.56.10.50/26  → bloc 0   → @réseau=209.56.10.0/26
  209.56.10.70/26  → bloc 64  → @réseau=209.56.10.64/26
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VLANs — PRINCIPES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VLAN = bulle isolée. Même switch = pas de comm
entre VLANs différents JAMAIS sans routeur.

PORTS:
  ACCESS = 1 VLAN, connecte PC
           switch ajoute/retire tag automatiquement
  TRUNK  = N VLANs, connecte switches
           paquet garde son tag VLAN ID

COMMUNICATION:
  Même VLAN, switches différents:
  PC→access→SW1═trunk═SW2→access→PC ✅

  VLANs DIFFÉRENTS (ex: VLAN20↔VLAN40):
  VLAN20→access→SW1→trunk→ROUTEUR→trunk→SW2→access→VLAN40
  ⚠️ ROUTEUR OBLIGATOIRE

PRUNING:
  Par défaut trunk = tous les VLANs passent
  Pruning = limiter VLANs autorisés sur trunk
  Commande: pruning vlan
  (Cisco: switchport trunk allowed vlan remove X)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LIEN AVEC SDN:
  Push_vlan = ajouter tag VLAN (access→trunk)
  Pop_vlan  = retirer tag VLAN (trunk→access)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SDN — CONCEPTS CLÉS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Idée: Contrôleur (cerveau) programme
      Commutateurs (exécutants) via Tables de flux

Plans séparés:
  Contrôle  → Contrôleur SDN
  Données   → Switches OpenFlow
  Orchest.  → API (Northbound/Southbound)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SDN / OPENFLOW — RÉFÉRENCE COMPLÈTE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STRUCTURE TABLE DE FLUX:
| EF (priorité) | CC (conditions) | Instruction |
↑ EF1 = plus haute priorité
↑ On descend jusqu'au 1er match → exécute
↑ Aucun match → Table-miss (drop ou →contrôleur)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CHAMPS CC:
  dl_type=0x800  → IPv4
  dl_type=0x806  → ARP
  nw_src=        → IP source
  nw_dst=        → IP destination
  in_port=       → port d'entrée physique
  dl_src/dst=    → MAC src/dst
  nw_proto=6     → TCP
  nw_proto=17    → UDP
  tp_dst=80      → port 80 (HTTP)
  tp_dst=3333    → port 3333
  VLAN-ID=X      → tag VLAN X
  priority=0     → catch-all (tout le reste)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
INSTRUCTIONS:
  output:X        → sortir par port X
  drop            → jeter le paquet
  resubmit(,X)    → aller à table X
  group:X         → table de groupe X
  mod_nw_src=IP   → changer IP src (NAT)
  mod_nw_dst=IP   → changer IP dst (NAT)
  mod_dl_dst=MAC  → changer MAC dst
  mod_dl_src=MAC  → changer MAC src
  dec_TTL         → décrémenter TTL (routage L3)
  Push_vlan=X     → ajouter tag VLAN X
  Pop_vlan        → retirer tag VLAN

  ⚠️ 3 types de ports — NE PAS CONFONDRE:
in_port=X  → port physique ENTRÉE du switch
output:X   → port physique SORTIE du switch
tp_dst=X   → port applicatif TCP/UDP (service)
  80=HTTP, 443=HTTPS, 22=SSH, 3333=custom

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PIPELINE:
Table 0 → Table 1 → ... → Table N
Toujours croissant. Jamais reculer.
resubmit(,1) = aller à table 1

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FONCTIONS OFFICIELLES (termes examen):

Contrôle d'accès (ACL)
→ autoriser/refuser selon type paquet
→ indice: dl_type seul + resubmit ou drop

Firewall
→ filtrage sécurité selon port/protocole
→ indice: in_port + tp_dst + drop/resubmit

Commutation L2 (Switching)
→ acheminement selon MAC
→ indice: dl_dst= + output:X

Routage L3 (Routing)
→ acheminement selon IP
→ indice: nw_dst= + dec_TTL + output:X

Marquage VLAN
→ assigner tag VLAN selon port+MAC
→ indice: Push_vlan= + resubmit

Commutation VLAN
→ livraison finale avec retrait tag
→ indice: VLAN-ID= + Pop_vlan + output:X

NAT
→ changer adresse IP src/dst
→ indice: mod_nw_src= ou mod_nw_dst=

Load Balancing
→ répartir trafic entre serveurs
→ indice: group:X type=select

Multicast/Broadcast
→ copier vers plusieurs ports
→ indice: group:X type=all

Failover / Haute disponibilité
→ basculer vers backup si panne
→ indice: group:X type=ff + watch_port=

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TABLES DE GROUPE:
all      → TOUS buckets (multicast/copie)
select   → UN bucket (load balancing)
indirect → UN seul (indirection)
ff       → 1er bucket ACTIF (failover) ⚠️

Ex all:
  group:1, type=all
  bucket=output:2, bucket=output:3
  → cloné → port 2 ET port 3

Ex ff (failover):
  group:1, type=ff
  bucket=watch_port:1, output:1 (primaire)
  bucket=watch_port:6, output:6 (backup)
  → port 1 tombe → bascule port 6 auto

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
EXEMPLE Q3 EXAMEN 2021:
T0: Contrôle accès — IPv4 → resubmit(,1)
T1: Firewall — Internet(p5)→S1:80 ✅
              Internet(p5)→S1:autre ❌ drop
              reste → resubmit(,2)
T2: Marquage VLAN — port+MAC → Push_vlan
T3: Commutation VLAN — Pop_vlan + output
T4: Routage L3 — dec_TTL + output/nw_dst
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CELLULAIRES — CALCULS TDMA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RÈGLE DE BASE:
  1 porteuse (200kHz) = 8 canaux (TOUJOURS)
  Arrondir AU-DESSUS (ceil) si pas entier

CALCUL NORMAL (k connu):
  1. Porteuses totales = bande/2 ÷ 0.2 MHz
  2. Porteuses/cellule = N_total ÷ k
  3. Canaux totaux     = porteuses/cellule × 8
  4. Canaux trafic     = canaux totaux − canaux ctrl
     (ctrl = 1 diffusion + X signalisation)
  5. Mobiles actifs    = canaux_signal ÷ % par usager
  6. Users simultanés  = (actifs × durée_moy) ÷ 60
  7. Efficacité        = users_simult ÷ canaux_trafic

CIBLES efficacité:
  <50% mal | 50-80% ok | 80-95% optimal | >95% saturé
  → AMÉLIORER: ajouter canaux signalisation

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CALCUL INVERSE (k inconnu):
  1. Canaux signal = ceil(actifs × % par usager)
  2. Porteuses totales = bande/2 ÷ 0.2 MHz
  3. Tester k valides du + grand au + petit:
     k valides: 1, 3, 4, 7, 9, 12
     → choisir le + grand k où N/k est entier
  4. Porteuses/cellule = N ÷ k choisi
  5. Vérifier canaux trafic = (porteuses×8) - ctrl > 0

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
EXEMPLE NORMAL:
N=45, k=9, ctrl=3 (1 diff + 2 signal), 1.5%/user

1. 45/9   = 5 porteuses/cellule
2. 5×8    = 40 canaux totaux
3. 40−3   = 37 canaux trafic
4. 2/0.015= 133 actifs
5. pas demandé ici
→ k=9 est le maximum car 45/9=5 (entier) ✅

EXEMPLE INVERSE (Q5 examen):
70 actifs, 1.5%/user, 1 diff, bande=18MHz

1. ceil(70×0.015) = ceil(1.05) = 2 canaux signal
2. 18/2 ÷ 0.2    = 45 porteuses totales
3. Tester k=12 → 45/12=3.75 ❌ pas entier
   Tester k=9  → 45/9=5 ✅ entier → k=9
4. 5 porteuses/cellule
5. (5×8)−3 = 37 canaux trafic ✅
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ASSOCIATIONS — DÉFINITIONS CLÉS
(type Q7 examen)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SDN
→ contrôle CENTRALISÉ des équipements réseau
→ sépare plan contrôle (contrôleur) et plan données (switch)

NFV (Network Function Virtualization)
→ migration fonctions réseau (routeur, firewall...)
  vers des VMs/conteneurs

VTEP
→ passerelle encapsulation/restitution trames VXLAN
  (virtualisation réseau L2 sur L3)

Micro IP (µIP)
→ implémente pile TCP/IP dans réseaux à
  ressources ET énergie limitées (IoT/capteurs)

IEEE 802.15.4
→ fournit couches physique + liaison
  pour applis faible débit, autonomie énergétique limitée
  (base de ZigBee et 6LoWPAN)

6LoWPAN
→ compression IPv6 pour trames 802.15.4
  (IPv6 over Low power Wireless PAN)
  → intègre capteurs dans Internet IPv6

ZigBee
→ complète 802.15.4 (ajoute couches réseau+app)
→ routage mesh via algorithme AODV

AODV
→ protocole routage RÉACTIF (à la demande)
→ établit routes uniquement quand nécessaire

DNS64
→ convertit enregistrements IPv4 → IPv6
  (transition IPv4 vers IPv6)

KPIs (Key Performance Indicators)
→ permettent d'évaluer l'efficacité d'un service

La migration (VMs)
→ consolidation ressources + équilibrage de charge
→ déplacer VMs entre hôtes sans interruption

NDN / ICN
→ réseau centré sur le CONTENU (pas l'adresse)
→ routage par NOM, cache distribué natif

CS / PIT / FIB (routeur NDN)
→ CS  = cache local (Content Store)
→ PIT = requêtes en attente (Pending Interest Table)
→ FIB = table routage par préfixe de nom

SIP
→ protocole de SIGNALISATION session multimédia
→ établit/modifie/termine session (pas transport)
→ messages: INVITE, 200OK, ACK, BYE

RTP
→ transport MÉDIA en temps réel (audio/vidéo)
→ actif PENDANT la session (SIP se désengage)

CoAP / MQTT
→ protocoles application légers pour IoT
→ CoAP = REST pour capteurs
→ MQTT = publish/subscribe léger

IMS (IP Multimedia Subsystem)
→ architecture téléphonie IP virtualisée
→ fonctions S-CSCF, I-CSCF, P-CSCF

CSMA/CA
→ gestion accès canal radio (802.15.4)
→ évite collisions dans réseaux sans fil

FFD vs RFD (802.15.4/ZigBee)
→ FFD = full function (peut router/coordonner)
→ RFD = reduced function (capteur simple, veille)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ITINÉRANCE GSM — ENTITÉS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MS   = téléphone (ME+SIM)
BTS  = antenne locale (couverture radio)
BSC  = contrôleur BTS (handover intra)
BSS  = BTS + BSC ensemble
MSC  = commutateur (handover inter-BSC)
GMSC = passerelle réseau public (PSTN)
HLR  = BD centrale abonnés + localisation
       → sait TOUJOURS où est l'abonné
       → 1 seul HLR par opérateur domiciliaire
VLR  = BD locale zone MSC (visiteurs)
       → change selon où est l'abonné
AuC  = authentification (clés Ki, Kc)
       → intégré au HLR
EIR  = vérifie IMEI (white/gray/black)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
COMMENT IDENTIFIER LES ENTITÉS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Pour chaque personne (ex: Alice et Bob):

ALICE (appelante, Montréal, opérateur Montréal):
  Position actuelle = Halifax
  → BSS Halifax (antenne locale)
  → MSC/VLR Halifax (réseau VISITÉ)
  → HLR/AuC Montréal (domicile ALICE)

BOB (appelé, Toronto, opérateur Toronto):
  Position actuelle = Winnipeg
  → BSS Winnipeg (antenne locale)
  → MSC/VLR Winnipeg (réseau VISITÉ)
  → HLR/AuC Toronto (domicile BOB)

RÈGLE:
  HLR/AuC = ville opérateur DOMICILIAIRE
  MSC/VLR = ville où la personne EST physiquement
  BSS     = même ville que MSC/VLR

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SÉQUENCE APPEL EN ITINÉRANCE
(Alice Halifax appelle Bob Winnipeg)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 1 — LOCALISER BOB:
1. MS Alice → BSS Halifax
2. BSS Halifax → MSC/VLR Halifax
3. MSC/VLR Halifax → HLR/AuC Toronto
   "Où est Bob ?" (Location Request)
4. HLR/AuC Toronto → MSC/VLR Winnipeg
   "Bob est là ?" (Location Request)
5. MSC/VLR Winnipeg → HLR/AuC Toronto
   "Oui Bob est ici" (Location Reply)
6. HLR/AuC Toronto → MSC/VLR Halifax
   "Bob est à Winnipeg" (Location Reply)

PHASE 2 — ÉTABLIR L'APPEL:
7. MSC/VLR Halifax → MSC/VLR Winnipeg
   Call Setup Request
8. MSC/VLR Winnipeg → BSS Winnipeg
   Call Setup Request
9. BSS Winnipeg → MS Bob
   Sonnerie ✅

PHASE 3 — COMMUNICATION:
10. MS Alice ←————————→ MS Bob
    Media Exchange (bout en bout)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RÈGLES CLÉS:
HLR domicile = TOUJOURS consulté 1er
VLR visité   = gère abonné localement
BSS          = 1er contact radio du mobile

⚠️ L'ordre dans le schéma examen:
MS appelant → BSS appelant → MSC/VLR appelant
→ HLR domicile appelé → MSC/VLR appelé
→ BSS appelé → MS appelé

HANDOVER:
Intra-BSC → géré par BSC
Inter-BSC → géré par MSC
Inter-MSC → géré par MSC + GMSC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NDN — EXERCICE TYPE EXAMEN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RÈGLE CACHE (la plus importante):
  1ère demande d'un paquet
  → va jusqu'au Producteur
  → mis en cache sur TOUT le chemin retour

  Demande suivante du MÊME paquet
  → servi par le 1er nœud sur le chemin
    qui l'a en cache (le plus proche)

  ⚠️ Un nœud cache seulement les paquets
     qui PASSENT par lui

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MÉTHODE POUR RÉSOUDRE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Dessiner le réseau + chemins FIB
2. Traiter les demandes dans l'ORDRE chronologique
3. Pour chaque paquet:
   → Vérifier CS de chaque nœud sur le chemin
   → 1er nœud avec le paquet = celui qui répond
   → Si aucun → Producteur répond
   → Mettre à jour les CS après chaque échange

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
EXEMPLE (Exercice 5 révision)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Réseau:
  C1 → R1 → R2 → Producteur
  C2 → R3 → R4 → R2 → Producteur
  (R1 et R2 sont sur les 2 chemins)

Demandes:
  C1: p00@1s | p01,p02@3s | p03,p04@4s
  C2: p00,p01@2s | p02,p03@4s

Résolution chronologique:
  1s: C1 demande p00
      → cache vide partout → Producteur répond
      → p00 mis en cache: R1, R2

  2s: C2 demande p00
      → R3,R4 vides → R2 a p00 → R2 répond ✅
      C2 demande p01
      → cache vide partout → Producteur répond
      → p01 mis en cache: R4, R3 (chemin C2)
      → p01 mis en cache: R2 aussi

  3s: C1 demande p01
      → R1 vide → R2 a p01 → R2 répond ✅
      C1 demande p02
      → cache vide partout → Producteur répond
      → p02 mis en cache: R2, R1

  4s: C1 demande p03,p04
      → cache vide → Producteur répond (x2)
      C2 demande p02
      → R3,R4 vides → R2 a p02 → R2 répond ✅
      C2 demande p03
      → cache vide → Producteur répond

TABLEAU RÉSUMÉ:
C1|p00|Producteur → 1ère demande
C2|p00|R2         → R2 l'avait (chemin commun)
C2|p01|Producteur → 1ère demande
C1|p01|R2         → R2 l'avait (chemin commun)
C1|p02|Producteur → 1ère demande
C1|p03|Producteur → 1ère demande
C1|p04|Producteur → 1ère demande
C2|p02|R2         → R2 l'avait (C1 l'avait demandé)
C2|p03|Producteur → 1ère demande
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CHAPITRE 4 — RÉSEAUX VIRTUELS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VIRTUALISATION:
  Divise ressources physiques → ressources virtuelles
  Outils: KVM, Xen, OpenStack, Docker (conteneurs)

TYPES D'ACCÈS RÉSEAU VM:
  Pont (Bridge) → VM accède directement au réseau
  NAT simulé    → trafic passe par l'OS hôte via NAT
  Host-only     → VM communique seulement avec hôte

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NFV (Network Function Virtualization)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Idée: déplacer fonctions réseau physiques → VMs
  Routeur, firewall, load balancer, NAT, IDS/IPS
  → tournent sur hardware générique

Composantes:
  VNF  = Virtual Network Function (la fonction)
  NFVI = Infrastructure (calcul+stockage+réseau virtuel)
  MANO = Management & Orchestration

Avantages:
  → Pas de matériel dédié
  → Déploiement rapide
  → Échelonnement dynamique
  → Réduction coûts

SFC (Service Function Chain):
  → Enchaînement de VNFs dans un ordre précis
  Ex: message SIP → pare-feu → load balancer → S-CSCF

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VTEP (Q7 associations)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VXLAN = protocole tunnel L2 sur L3
VTEP  = Virtual Tunnel End Point
      = passerelle qui encapsule/décapsule
        les trames VXLAN
      → permet étendre VLANs entre datacenters

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CLOUDLETS / VMs — CALCULS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PLACEMENT VMs — round-robin (cycle rotation):
  On distribue 1 par 1, on repart au début
  VM1→H1, VM2→H2, VM3→H3, VM4→H1, VM5→H2

  Max VMs/hôte (espace partagé):
  = coeurs_hôte ÷ coeurs_VM
  Ex: 4 coeurs ÷ 2 = 2 VMs max/hôte

PLACEMENT cloudlets — round-robin sur VMs:
  On distribue 1 par 1, on repart au début
  CL1→VM1, CL2→VM2, CL3→VM3, CL4→VM4, CL5→VM5
  CL6→VM1, CL7→VM2... (repart du début)
  ⚠️ PAS 2 cloudlets consécutifs sur même VM

TEMPS EXÉCUTION:
  T = longueur (MI) ÷ vitesse VM (MIPS)
  Ex: 2500MI ÷ 500MIPS = 5s

TEMPS TOTAL:
  Si coeurs_VM ≥ cloudlets/VM → parallèle
  → Temps total = temps 1 seule cloudlet = 5s

ESPACE vs TEMPS PARTAGÉ:
  Espace → VMs NE partagent PAS CPU
           → max VMs limité
           → Si assez ressources → même résultat
  Temps  → VMs PARTAGENT CPU
           → plus de VMs possible
  ⚠️ Différence visible seulement si
     ressources INSUFFISANTES pour espace partagé
     Ex: 8 VMs, 3 hôtes × 2 max = 6 → 2 échouent
     Temps partagé → les 8 passent ✅

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
EXEMPLE COMPLET (exercice révision)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
3 hôtes (4 coeurs 1000MIPS) | 5 VMs (2 coeurs 500MIPS)
10 cloudlets (2500MI, 1 coeur) | espace partagé

1. VMs: VM1,VM4→H1 | VM2,VM5→H2 | VM3→H3
2. Cloudlets: CL1,CL6→VM1 | CL2,CL7→VM2
              CL3,CL8→VM3 | CL4,CL9→VM4
              CL5,CL10→VM5
3. T = 2500/500 = 5s
4. Temps total = 5s (parallèle, 2 coeurs/VM)
5. Changer ordonnancement → RIEN NE CHANGE
   (assez de ressources pour 5 VMs)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IaaS / PaaS / SaaS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IaaS → CPU/réseau/stockage (EC2, Azure VMs)
PaaS → environnement dev (Heroku)
SaaS → appli via navigateur (Gmail, Office365)

         Classique  IaaS  PaaS  SaaS
App          ✅      ✅    ✅    ❌
OS           ✅      ✅    ❌    ❌
Virtualisation✅     ❌    ❌    ❌
Matériel     ✅      ❌    ❌    ❌
↑ Plus on monte → moins l'entreprise gère

Élasticité:
  Scale Up/Down  → + ou - ressources 1 instance
  Scale Out/In   → + ou - nb d'instances (VMs)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LIRE UN TABLEAU CLOUDSIM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Colonnes: CL_ID | STATUS | DC | VM | Time | Start | Finish

EXEMPLE (exercice révision):
CL_ID | STATUS  | DC | VM | Time | Start | Finish
  1   | SUCCESS |  2 |  1 |  5   |  0.1  |  5.1
  6   | SUCCESS |  2 |  1 |  5   |  0.1  |  5.1
  2   | SUCCESS |  2 |  2 |  5   |  0.1  |  5.1
  7   | SUCCESS |  2 |  2 |  5   |  0.1  |  5.1

Lecture:
→ CL1 et CL6 sur VM1 → round-robin ✅
→ Start identique (0.1) → parallèle ✅
→ Time=5s = 2500MI/500MIPS → espace partagé ✅

IDENTIFIER LA STRATÉGIE:
  Espace partagé:
  → Même VM, même Start/Finish
  → Time = MI/MIPS (normal)
  → Cloudlets EN PARALLÈLE si coeurs suffisants

  Temps partagé:
  → Time PLUS LONG que MI/MIPS normal
  → Ex: 1 coeur, 2 cloudlets → chacune prend 2×
  → Start différents ou temps allongé

VÉRIFIER PLACEMENT:
  VM ID dans tableau = round-robin attendu?
  CL1→VM1, CL2→VM2... CL6→VM1... ✅
  Si non → ressources insuffisantes detectées
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━