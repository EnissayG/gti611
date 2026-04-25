IPv6 — Structure
─────────────────────────────
128 bits = 8 groupes de 16 bits (hex)
64 bits NetID | 64 bits Interface
Règle demi-octet: masques multiples de 4

Hiérarchie type:
/44→org /48→site /52→empl /56→grp /60→succ /64→dept

Nb sous-réseaux = 2^(prochain_niveau - niveau_actuel)
Ex: /48→/52 = 4 bits = 16 emplacements

Numérotation (bits 48-51 pour emplacements /52):
0→0000 1→1000 2→2000 3→3000
4→4000 5→5000 ... F→F000

Préfixe site N (base /48):
2001:abcd:120+N::/48
(120 en hex, N s'additionne)



━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📌 IPv6 — STRUCTURE & HIÉRARCHIE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
128 bits = 8 groupes hex de 16 bits
[   64 bits NetID    |  64 bits Interface  ]
Règle: masques = multiples de 4 (demi-octet)

HIÉRARCHIE TYPE:
/44      /48     /52    /60    /64
 Org →  Site → Empl → Succ → Dept
         4bits  4bits  8bits  4bits
         16     16     256    16

Nb SR = 2^(masque_suivant - masque_actuel)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔢 NUMÉROTATION (position dans l'adresse)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Chaque niveau occupe les 4 bits SUIVANTS
→ le chiffre hex va dans la BONNE position

Bits 44-47 (site)    → 4e groupe, pos 1
Bits 48-51 (empl)    → 4e groupe, pos 2
Bits 52-59 (succ)    → 4e groupe, pos 3+4
Bits 60-63 (dept)    → 4e groupe, pos 4 ← 
                         (partage avec succ)

TABLEAU DE POSITION (4e groupe = XYZW):
  X = site (bits 44-47)
  Y = emplacement (bits 48-51)
  ZW = succursale (bits 52-59)
  W = département (bits 60-63) ← écrase ZW!

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚡ SHORTCUT — Lire l'adresse
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Base: 2001:abcd:120::/44
                ^^^
             3e groupe = 120 (hex fixe)

Site N  → 3e groupe devient 120+N (hex)
  Site 0 → 120  Site 1 → 121  Site 2 → 122

4e groupe = [site][empl][succ][dept] en hex
  Empl 3, Succ 0, Dept 0 → 0300 → :0300:
  Empl 2, Succ 03, Dept 0 → :2030:

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📝 EXEMPLE TYPE (examen révision)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Préfixe: 2001:abcd:120::/44
→ 2e dept, 3e succ, 3e empl, 2e site

ÉTAPE PAR ÉTAPE:
1. Site 2    → 3e grp: 120+2 = 122 → /48
              2001:abcd:122:____ ::/48

2. Empl 3   → 4e grp pos1: 2
              2001:abcd:122:2___::/52

3. Succ 3   → 4e grp pos3+4: 03
              2001:abcd:122:2030::/60
              (succ=03 car on commence à 01?)
              ⚠️ succ commence à 0: 03→ pos3=0,pos4=3
              2001:abcd:122:2030::/60 ✓
              (si commence à 1: succ 3 = index 2 = 02)

4. Dept 2   → 4e grp pos4: écrase → +1
              2001:abcd:122:2031::/64
              (dept 2 = index 1 si commence à 1)

RÉPONSE: 2001:abcd:122:2031::/64


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