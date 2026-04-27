━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IPv4 — VLSM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TROUVER @RÉSEAU depuis IP/masque:
  Taille bloc = 2^(32-masque)
  @réseau = début du bloc où tombe l'IP

BLOCS PAR MASQUE:
  /25 → 128: 0, 128
  /26 → 64:  0, 64, 128, 192
  /27 → 32:  0, 32, 64, 96, 128, 160, 192, 224
  /28 → 16:  0, 16, 32, 48, 64, 80, 96...
  /30 → 4    (lien P-P = 2 hôtes)

HÔTES UTILISABLES:
  /25=126  /26=62  /27=30
  /28=14   /29=6   /30=2

TROUVER MASQUE depuis nb hôtes H:
  n = ceil(log2(H+2)) → masque = /32-n
  Ex: 100h → n=7 → /25

TYPE Q1 — Trouver @réseau:
  IP donnée → trouver dans quel bloc elle tombe
  209.56.10.150/25 → bloc 128 → @réseau=209.56.10.128/25
  209.56.10.50/26  → bloc 0   → @réseau=209.56.10.0/26
  209.56.10.70/26  → bloc 64  → @réseau=209.56.10.64/26

TYPE Q2 — VLSM complet:
  Trier besoins GRAND→PETIT
  broadcast = @réseau + taille_bloc - 1
  @réseau suivant = broadcast + 1
  Ex: 192.168.1.0/24 → 100h, 50h, 25h
  SR1(100h): 192.168.1.0/25   BC=.127
  SR2(50h):  192.168.1.128/26 BC=.191
  SR3(25h):  192.168.1.192/27 BC=.223

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
IPv6 — HIÉRARCHIE & CONSTRUCTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
/44    /48    /52    /60      /64
Org → Site → Empl → Succ → Dept
      4bits  4bits  8bits   4bits
      16SR   16SR   256SR   16SR

Nb SR = 2^(masque_suivant - masque_actuel)
Ex: /48→/52 = 4 bits = 2^4 = 16 emplacements

3e GROUPE = encode le SITE:
  Base ex: 1200 → DERNIER chiffre varie
  Site N → remplacer dernier chiffre par (N-1) hex
  ⚠️ /44 = 12 bits fixes → dernier chiffre G3 varie
  Site1=1200 Site2=1201 Site10=1209
  Site11=120A Site12=120B Site16=120F

4e GROUPE = [EMPL][SUCC][SUCC][DEPT]:
  Empl N → (N-1) hex → position 1
  Succ N → (N-1) sur 2 chiffres hex → positions 2+3
  Dept N → (N-1) hex → position 4

CONVERSION DEC → HEX:
  10=A  11=B  12=C  13=D  14=E  15=F
  16=10  17=11  18=12  19=13  20=14
  21=15  22=16  23=17  24=18  25=19
  26=1A  27=1B  28=1C  29=1D  30=1E
  31=1F  32=20

TYPE Q1 — Construire adresse:
  Préfixe: 2001:abcd:1200::/44
  → 2e dept, 3e succ, 3e empl, 2e site
  1. Site 2  → 1201              /48
  2. Empl 3  → [2]___            /52
  3. Succ 3  → 3-1=2 → [2][02]_ /60
  4. Dept 2  → 2-1=1 → [2][02][1] /64
  ✅ 2001:abcd:1201:2021::/64

TYPE Q2 — LIRE adresse existante → trouver préfixe:
  ⚠️ AJOUT CRITIQUE (examen Q6 2021)
  /64 → garder G1:G2:G3:G4 → mettre ::/64
  /48 → garder G1:G2:G3     → mettre ::/48
  /52 → garder G1:G2:G3 + 1er nibble G4 → ::/52

  EXEMPLES RÉELS EXAMEN 2021:
  2002:a45b:3c78:c931:ba1a:48fd:feaa:2222
  → /64 = 2002:a45b:3c78:c931::/64
  → /48 = 2002:a45b:3c78::/48

  2002:a45b:3c78:c900:b1ba:48fd:fe01:aaaa
  → /64 = 2002:a45b:3c78:c900::/64
  → /48 = 2002:a45b:3c78::/48

  SR entre 2 routeurs → préfixe commun des 2 interfaces → ::/64
  ⚠️ Même G1:G2:G3 = même site (/48)
  ⚠️ Même G1:G2:G3:G4 = même sous-réseau (/64)

TYPE Q3 — Compter sous-réseaux:
  Nb sites  /44 → 2^(48-44) = 16
  Nb empl   /48 → 2^(52-48) = 16
  Nb succ   /52 → 2^(60-52) = 256
  Nb dept   /60 → 2^(64-60) = 16

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VLANs — PRINCIPES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VLAN = bulle isolée
Même switch ≠ comm entre VLANs SANS routeur

ACCESS = 1 VLAN, connecte PC
TRUNK  = N VLANs, connecte switches

Même VLAN, switches différents → trunk suffit ✅
VLANs DIFFÉRENTS → ROUTEUR OBLIGATOIRE
  PC→access→SW→trunk→ROUTEUR→trunk→SW→access→PC

PRUNING = limiter VLANs sur trunk
  switchport trunk allowed vlan remove X

LIEN SDN:
  Push_vlan = ajouter tag (access→trunk)
  Pop_vlan  = retirer tag (trunk→access)

TYPE Q1 — Communication possible? (examen Q4.1 et Q4.2)
  Même VLAN sur switches différents → OUI si trunk entre switches ✅
  VLANs différents (ex: VLAN20 ↔ VLAN40) → NON sans routeur ❌
  VLANs différents (ex: VLAN30 ↔ VLAN40) → NON sans routeur ❌
  ⚠️ Même si les switches sont reliés par trunk, 2 VLANs différents
     NE PEUVENT PAS communiquer sans routeur ou couche 3

TYPE Q2 — Commande pruning? (examen Q4.3)
  "limiter propagation VLAN X vers SW Y"
  → switchport trunk allowed vlan remove X
  ⚠️ Réponse = 2 mots: "remove vlan" ou commande complète

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SDN / OPENFLOW — RÉFÉRENCE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
| EF (priorité) | CC (conditions) | Instruction |
EF1 = plus haute | 1er match → exécute
Aucun match → Table-miss (drop ou →contrôleur)

CHAMPS CC:
  dl_type=0x800  → IPv4
  dl_type=0x806  → ARP
  nw_src=        → IP source
  nw_dst=        → IP destination
  in_port=       → port physique entrée
  dl_src=        → MAC source
  dl_dst=        → MAC destination ⚠️ SOUVENT OUBLIÉ
  nw_proto=6     → TCP
  nw_proto=17    → UDP
  tp_src=        → port source TCP/UDP
  tp_dst=        → port destination TCP/UDP
  VLAN-ID=X      → tag VLAN
  priority=0     → catch-all (tout le reste)

⚠️ 3 types ports — NE PAS CONFONDRE:
  in_port=X    → port physique ENTRÉE switch
  output:X     → port physique SORTIE switch
  tp_src/dst=X → port applicatif TCP/UDP
  80=HTTP  443=HTTPS  22=SSH  3333=custom

INSTRUCTIONS:
  output:X        → sortir par port X
  drop            → jeter le paquet
  resubmit(,X)    → aller à table X
  group:X         → table de groupe X
  dec_TTL         → décrémenter TTL (routage L3)
  Push_vlan=X     → ajouter tag VLAN X
  Pop_vlan        → retirer tag VLAN
  mod_nw_src=IP   → changer IP src (NAT sortant)
  mod_nw_dst=IP   → changer IP dst (NAT entrant)
  mod_dl_dst=MAC  → changer MAC dst
  mod_dl_src=MAC  → changer MAC src
  mod_tp_src=X    → changer port src TCP/UDP
  mod_tp_dst=X    → changer port dst TCP/UDP

PIPELINE:
  Table 0 → Table 1 → ... → Table N
  Toujours croissant. Jamais reculer.

FONCTIONS OFFICIELLES (indices examen):
  Contrôle d'accès (ACL)
    CC: dl_type | Inst: resubmit(,1) ou drop
  Firewall
    CC: in_port + nw_proto + tp_dst + nw_dst
    Inst: drop ou resubmit
    ⚠️ EXAMEN 2021 T1: in_port=5, nw_proto=6, tp_dst=80, nw_dst=S1 → resubmit(,2)
                        in_port=5, nw_dst=S1 (autre port) → drop
                        priority=0 → resubmit(,2)
  Commutation L2
    CC: dl_type + dl_dst=MAC
    Inst: output:X (SANS dec_TTL)
  Routage L3
    CC: dl_type + nw_dst=IP
    Inst: dec_TTL + output:X
  Marquage VLAN
    CC: dl_type + in_port + dl_dst
    Inst: Push_vlan=X + resubmit(,N)
    ⚠️ EXAMEN 2021 T2: in_port=2, dl_dst=MAC → Push_vlan=1, resubmit(,3)
                        in_port=3, dl_dst=MAC → Push_vlan=2, resubmit(,3)
  Commutation VLAN
    CC: dl_type + VLAN-ID + dl_dst
    Inst: Pop_vlan + output:X
  NAT sortant
    CC: dl_type + tp_src + nw_src + nw_dst
    Inst: mod_nw_src + mod_tp_src + dec_TTL + output:X
  NAT retour ⚠️ ne pas oublier!
    CC: dl_type + tp_src=port_public + nw_dst=IP_publique
    Inst: mod_nw_dst=IP_privée + mod_tp_src=port_privé
          + dec_TTL + output:X
  Load Balancing
    CC: nw_dst | Inst: group:X type=select
  Multicast/Broadcast
    CC: dl_type | Inst: group:X type=all
  Failover/Haute dispo
    CC: nw_dst
    Inst: group:X type=ff + watch_port=

INDICES RAPIDES — identifier fonction:
  dl_type seul → ACL
  in_port+tp_dst+drop → Firewall
  dl_dst+output (SANS dec_TTL) → Commutation L2
  nw_dst+dec_TTL → Routage L3
  Push_vlan → Marquage VLAN
  Pop_vlan+output → Commutation VLAN
  mod_nw_src+mod_tp_src → NAT
  group:all → Multicast | group:ff → Failover

TABLES DE GROUPE:
  all      → TOUS buckets (multicast/copie)
  select   → UN bucket (load balancing)
  indirect → UN seul (next-hop partagé)
  ff       → 1er bucket ACTIF (failover auto)

Ex all:
  group:1|all|bucket=output:2, bucket=output:3
Ex ff:
  group:1|ff
  bucket=watch_port:1, output:1 (primaire)
  bucket=watch_port:6, output:6 (backup)
Ex indirect:
  group:2|indirect|bucket=output:3

TYPE Q1 — ANALYSER tableau existant:
  → Regarder CC → identifier type de champ
  → Regarder instruction → identifier action
  → Nommer avec termes officiels ci-dessus

TYPE Q2 — TRACER chemin d'un paquet:
  → Chaque table: 1ère règle qui match
  → resubmit(,X) → aller table X
  → output:X → fin | drop → fin
  ⚠️ Aucun match → priority=0 s'applique

  EXEMPLE EXAMEN 2021 — Requête HTTP depuis Internet vers S1:
  Paquet: in_port=5, IPv4, TCP, tp_dst=80, nw_dst=10.162.8.10
  T0: EF1 match (dl_type=0x800) → resubmit(,1)
  T1: EF1 match (in_port=5, nw_proto=6, tp_dst=80, nw_dst=S1) → resubmit(,2)
  T2: cherche dl_dst MAC de S1 → Push_vlan ou routage → output:1 (vers S1)
  ✅ Paquet autorisé car HTTP (port 80) vers S1 depuis Internet

TYPE Q3 — CRÉER tables from scratch:
  1. T0 = TOUJOURS ACL (dl_type=0x800 → resubmit, sinon drop)
  2. 1 table par fonction
  3. Toujours finir par priority=0
  4. Commutation L2 → dl_dst (SANS dec_TTL)
  5. Routage L3 → nw_dst + dec_TTL
  6. MAC inconnue → resubmit vers table routage
  7. NAT → créer ALLER et RETOUR

TYPE Q4 — FAILOVER:
  → Table qui route vers S1 → modifier
  → group:ff
    bucket=watch_port:port_S1, output:port_S1
    bucket=watch_port:port_S2, output:port_S2

EXEMPLE EXAMEN 2021 (5 tables complètes):
  T0: dl_type=0x800 → resubmit(,1) | priority=0 → drop
  T1 (Firewall):
    in_port=5,nw_proto=6,tp_dst=80,nw_dst=10.162.8.10 → resubmit(,2)
    in_port=5,nw_dst=10.162.8.10 → drop
    priority=0 → resubmit(,2)
  T2 (Marquage VLAN):
    dl_type=0x800,in_port=2,dl_dst=74E5.F987.1022 → Push_vlan=1,resubmit(,3)
    dl_type=0x800,in_port=2,dl_dst=74E5.F987.1044 → Push_vlan=1,resubmit(,3)
    dl_type=0x800,in_port=3,dl_dst=74E5.F987.1022 → Push_vlan=2,resubmit(,3)
    priority=0 → resubmit(,3)
  T3 (Commutation VLAN):
    VLAN-ID=1,dl_dst=MAC → Pop_vlan,output:X
    VLAN-ID=2,dl_dst=MAC → Pop_vlan,output:X
    priority=0 → resubmit(,4)
  T4 (Routage L3):
    nw_dst=10.162.8.10 → dec_TTL,output:1 (S1)
    nw_dst=10.195.10.x → dec_TTL,output:2
    nw_dst=10.0.0.1    → dec_TTL,output:5 (Internet)
    priority=0 → drop
  Failover S1→S2:
    T4: nw_dst=S1 → group:ff
    group:ff|watch_port:1,output:1|watch_port:6,output:6

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CELLULAIRES — CALCULS TDMA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RÈGLE: 1 porteuse (200kHz) = 8 canaux (TOUJOURS)
       Arrondir AU-DESSUS (ceil) sauf users simultanés

CALCUL NORMAL (k connu) — EXAMEN Q2:
  1. Porteuses totales = bande ÷ 0.2 MHz
     ⚠️ bande/2 SEULEMENT si uplink+downlink égaux
     Ex Q2: 50MHz total, égal → 50/2=25MHz downlink → 25÷0.2=125 porteuses
  2. Porteuses/cellule = N_total ÷ k
  3. Canaux totaux = porteuses/cellule × 8
  4. Canaux trafic = totaux − ctrl
     ctrl = 1 diffusion + X signalisation
  5. Mobiles actifs = canaux_signal ÷ % usager
     ⚠️ 2%=0.02  3%=0.03  1.5%=0.015  5%=0.05
  6. Users simultanés = (actifs × durée_moy) ÷ 60
     ⚠️ Arrondir EN-DESSOUS (pas de demi-user)
  7. Efficacité = users_simult ÷ canaux_trafic

CIBLES efficacité:
  <50% mal | 50-80% ok | 80-95% optimal | >95% saturé

CALCUL INVERSE — TROUVER k (examen Q5):
  ⚠️ NOUVEAU TYPE — examen Q5 2021:
  Données: 18MHz uplink+downlink, 70 abonnés actifs,
           1 canal diffusion, 1.5%/user, maximiser k

  ÉTAPES:
  1. Canaux signal = ceil(actifs × % usager)
     → ceil(70 × 0.015) = ceil(1.05) = 2 canaux signal
  2. Porteuses totales downlink = (bande/2) ÷ 0.2
     → (18/2) ÷ 0.2 = 45 porteuses totales
  3. Tester k valides du PLUS GRAND au plus petit
     k valides: 1, 3, 4, 7, 9, 12
     k=12 → 45/12=3.75 ❌ (pas entier)
     k=9  → 45/9=5 ✅ → GARDER k=9 (le plus grand valide)
  4. Porteuses/cellule = 45 ÷ 9 = 5

  ⚠️ MAXIMISER k = meilleure réutilisation fréquences
     → prendre le PLUS GRAND k où N/k est entier

CALCUL INVERSE — TROUVER canaux signal pour efficacité cible (examen Q2.4):
  Données: 125 porteuses totales, k=8, 1 diff, 5%/user, cible 90%
  1. Porteuses/cellule = 125 ÷ 8 ≈ 15 (arrondir au bas si non entier)
     ⚠️ Si N/k pas entier → prendre la partie entière
  2. Canaux totaux = 15 × 8 = 120
  3. Pour chaque valeur X (canaux signal):
     Canaux trafic = 120 - 1(diff) - X
     Actifs = X ÷ 0.05 = 20X
     → Tester X=1: actifs=20, trafic=118 → pas assez
     → Tester X=2: actifs=40, trafic=117 → pas assez
     → Continuer jusqu'à efficacité ≥ 90%
  4. Efficacité = users_simult ÷ canaux_trafic ≥ 90%

GÉNÉRATIONS CELLULAIRES:
  1G → FDMA | analogique | voix seulement
  2G → GSM TDMA+FDMA | numérique | voix+SMS
       2.5G=GPRS(données) 2.75G=EDGE(+ rapide)
  3G → WCDMA | paquet sauf voix | 2Mbps
  4G → LTE/LTE-A | tout IP | 100Mbps-1Gbps
  5G → multi-fréq | 20Gbps | IoT massif
  Évolution: Analogique→Numérique→Paquet→Tout IP

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ITINÉRANCE GSM — ENTITÉS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MS   = téléphone (ME+SIM)
BTS  = antenne locale (couverture radio)
BSC  = contrôleur BTS (handover intra-BSC)
BSS  = BTS + BSC ensemble
MSC  = commutateur (handover inter-BSC)
GMSC = passerelle réseau public (PSTN)
HLR  = BD centrale — sait TOUJOURS où est l'abonné
       1 seul par opérateur DOMICILIAIRE
VLR  = BD locale zone MSC (visiteurs)
AuC  = authentification (Ki, Kc) — intégré HLR
EIR  = vérifie IMEI (white/gray/black)

IDENTIFIER:
  HLR/AuC = ville opérateur DOMICILIAIRE de L'APPELÉ
  MSC/VLR = ville où la personne EST physiquement
  BSS     = même ville que MSC/VLR

TYPE Q1 — Placer entités dans schéma (examen Q1 2021):
  ⚠️ HLR = domicile de L'APPELÉ (pas de l'appelant!)
  ⚠️ GMSC n'apparaît PAS dans le diagramme examen 2021
  Ordre TOUJOURS:
  MS appelant → BSS appelant → MSC/VLR appelant
  → HLR/AuC domicile APPELÉ
  → MSC/VLR appelé → BSS appelé → MS appelé

EXEMPLE EXACT EXAMEN 2021:
  Alice (opérateur Montréal, physiquement à Halifax)
  appelle Bob (opérateur Toronto, physiquement à Winnipeg)

  Réponse:
  MS Alice → BSS Halifax → MSC/VLR Halifax
  → HLR/AuC Toronto (domicile BOB, pas Alice)
  → MSC/VLR Winnipeg → BSS Winnipeg → MS Bob

  ⚠️ Pièges:
  - HLR/AuC Montréal = domicile Alice → NE PAS METTRE ici
  - MSC/VLR Toronto = là où Bob est enregistré, pas là où il EST
  - BSS Winnipeg (pas BSS Toronto)

SÉQUENCE MESSAGES COMPLÈTE:
  1. MS Alice → BSS Halifax → MSC/VLR Halifax
  2. MSC/VLR Halifax → HLR/AuC Toronto (Location Request)
  3. HLR/AuC Toronto → MSC/VLR Winnipeg (Location Request)
  4. MSC/VLR Winnipeg → HLR/AuC Toronto (Location Reply)
  5. HLR/AuC Toronto → MSC/VLR Halifax (Location Reply)
  6. MSC/VLR Halifax → MSC/VLR Winnipeg (Call Setup Request)
  7. MSC/VLR Winnipeg → BSS Winnipeg (Call Setup Request)
  8. BSS Winnipeg → MS Bob (Call Setup + sonnerie)
  9. Call Setup Confirmation remonte jusqu'à MS Alice
  10. Media Exchange bout en bout ✅

HANDOVER:
  Intra-BSC → géré par BSC
  Inter-BSC → géré par MSC
  Inter-MSC → géré par MSC + GMSC

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NDN — EXERCICE TYPE EXAMEN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NDN vs IP:
  IP  → OÙ est le contenu (adresse IP)
  NDN → QUOI est le contenu (nom)

3 STRUCTURES DU ROUTEUR:
  CS  = cache local — vérifié EN PREMIER
  PIT = requêtes en attente (interfaces src+dst)
  FIB = table routage par préfixe de nom

RÈGLE CACHE:
  1ère demande → Producteur répond
  → mis en cache sur TOUT le chemin retour
  Demande suivante → 1er nœud qui l'a répond
  ⚠️ Cache SEULEMENT si paquet PASSE par ce nœud

MÉTHODE:
  1. Dessiner réseau + chemins FIB
  2. Identifier nœuds COMMUNS aux 2 chemins
  3. Traiter dans l'ORDRE chronologique
  4. C1 avant C2 si même seconde (convention)
  5. Lister TOUS les paquets — ne rien sauter
  6. Mettre à jour CS après chaque échange

TYPE Q — Qui répond?
  Pour chaque paquet vérifier CS sur le chemin
  Explication à donner:
  → "1ère demande, cache vide → Producteur"
  → "X l'avait en cache (nœud commun)"
  → "C1 traité avant C2 à la même seconde"

EXEMPLE (Exercice 5 révision):
  Chemins: C1→R1→R2→Prod | C2→R3→R1→R2→Prod
  R1 et R2 = nœuds COMMUNS aux 2 chemins

  C1|p00@1s|Producteur → p00 en cache R2,R1
  C2|p00@2s|R1 ✅ → R1 l'avait (commun)
  C2|p01@2s|Producteur → p01 en cache R2,R1,R3
  C1|p01@3s|R1 ✅ → R1 l'avait (commun)
  C1|p02@3s|Producteur → p02 en cache R2,R1
  C1|p03@4s|Producteur → p03 en cache R2,R1
  C1|p04@4s|Producteur → cache vide
  C2|p02@4s|R1 ✅ → C1 traité avant C2 @ 4s
  C2|p03@4s|R1 ✅ → C1 traité avant C2 @ 4s

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CLOUDLETS / VMs — CALCULS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VOCABULAIRE:
  Hôte = serveur physique
  VM   = machine virtuelle sur hôte
  CL   = cloudlet (application sur VM)
  MI   = Million Instructions (longueur CL)
  MIPS = Million Instructions/s (vitesse VM)
  ⚠️ Ne pas confondre MI et MIPS!

PLACEMENT VMs — round-robin (cycle rotation):
  Distribuer 1 par 1, repart au début
  VM1→H1, VM2→H2, VM3→H3, VM4→H1, VM5→H2
  Max VMs/hôte (espace partagé) = coeurs_hôte ÷ coeurs_VM
  Ex: 4 coeurs ÷ 2 = 2 VMs max/hôte

PLACEMENT cloudlets — round-robin sur VMs:
  CL1→VM1, CL2→VM2... CL6→VM1, CL7→VM2...
  ⚠️ PAS 2 CL consécutifs sur même VM

TEMPS 1 cloudlet:
  T = longueur(MI) ÷ vitesse(MIPS)
  Ex: 2500÷500 = 5s

TEMPS TOTAL:
  coeurs_VM ≥ CL/VM → PARALLÈLE = T_1_CL
  coeurs_VM < CL/VM → T × (CL/coeurs)

ESPACE vs TEMPS PARTAGÉ:
  Espace → VMs NE partagent PAS CPU → max limité
  Temps  → VMs partagent CPU → plus de VMs possible
  ⚠️ Différence SEULEMENT si ressources insuffisantes
  Ex: 8 VMs, 3H×2max=6 → espace: 2 ÉCHOUENT
                         → temps: 8 PASSENT ✅

IaaS/PaaS/SaaS:
         Classique IaaS PaaS SaaS
  App      ✅      ✅   ✅   ❌
  OS       ✅      ✅   ❌   ❌
  Virtua.  ✅      ❌   ❌   ❌
  Matériel ✅      ❌   ❌   ❌
  IaaS=EC2/Azure | PaaS=Heroku | SaaS=Gmail
  Scale Up/Down = +/- ressources 1 instance
  Scale Out/In  = +/- nb instances VMs
  Auto-scaling: CPU>70%→ajouter | CPU<30%→retirer

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CHAPITRE 4 — RÉSEAUX VIRTUELS / NFV
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NFV = fonctions réseau → VMs/conteneurs hardware générique
  VNF  = la fonction virtualisée (routeur, firewall...)
  NFVI = Infrastructure (calcul+stockage+réseau virtuel)
  MANO = Management & Orchestration
  SFC  = enchaînement VNFs dans ordre précis
         Ex: SIP→pare-feu→LB→S-CSCF

VXLAN = protocole tunnel L2 sur réseau L3
VTEP  = passerelle encapsulation/restitution trames VXLAN
        (permet étendre VLANs entre datacenters)

Accès réseau VM:
  Pont (Bridge) → VM accède directement au réseau
  NAT simulé    → trafic via OS hôte + NAT
  Host-only     → VM ↔ hôte seulement

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SIP / RTP — MULTIMÉDIA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SIP = SIGNALISATION (pas transport)
  Messages:
    INVITE → demande session (3-way: INVITE+200OK+ACK)
    BYE    → terminer (2-way: BYE+200OK)
    CANCEL → annuler avant établissement
    REGISTER → s'enregistrer (URI↔IP)
    180 Ringing → destination sonne
    302 Moved → redirection vers nouvelle adresse
  Entités:
    UAC      = client (envoie requêtes)
    UAS      = serveur (répond)
    Proxy    = achemine messages SIP
    Registrar= BD URI↔IP (crucial pour mobilité)
    Redirect = redirige client (réponse 3xx)

RTP  = TRANSPORT MÉDIA (audio/vidéo pendant session)
RTCP = contrôle qualité RTP (stats, sync)

Flux complet:
  INVITE → 180Ringing → 200OK → ACK
  → [RTP direct peer-to-peer] → BYE → 200OK
  ⚠️ SIP se DÉSENGAGE après ACK
  ⚠️ RTP = direct entre clients (pas via Proxy)
  ⚠️ ACK et Media = DIRECT (bypass Proxy)

Flux avec Proxy:
  Alice→Proxy: INVITE
  Proxy→LocationServer: Query
  Proxy→Bob: INVITE
  Bob→Proxy→Alice: 180 Ringing
  Bob→Proxy→Alice: 200 OK
  Alice→Bob: ACK (DIRECT, sans Proxy)
  Alice↔Bob: RTP/RTCP (DIRECT)
  Alice→Bob: BYE (DIRECT)
  Bob→Alice: 200 OK

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ASSOCIATIONS Q7 — DÉFINITIONS EXACTES EXAMEN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ MÉMORISER CES ASSOCIATIONS EXACTES (examen Q7 2021):

Micro IP (µIP) → "permet d'implémenter la pile TCP/IP dans
                  des réseaux ayant des ressources et énergie limitées"

VTEP → "est une passerelle qui s'occupe de
         l'encapsulation/restitution des trames VXLAN"

IEEE 802.15.4 → "fournit les couches physique et liaison de
                  données adaptées aux applications à faible débit
                  avec une autonomie énergétique limitée"

Migration (VMs) → "permet, entre autres, la consolidation des
                    ressources et l'équilibrage de charge"

DNS64 → "permet de convertir les enregistrements de type
          IPv4 en enregistrements de type IPv6"

KPIs → "permettent d'évaluer l'efficacité d'un service"

SDN → "permet un contrôle centralisé des équipements réseaux"

AUTRES DÉFINITIONS UTILES:
  NFV → fonctions réseau → VMs hardware générique
  6LoWPAN → compression IPv6 pour 802.15.4 (IoT→Internet)
  ZigBee → complète 802.15.4 (réseau+app) + routage AODV
  AODV → routage RÉACTIF (établit routes à la demande)
  NDN/ICN → routage par NOM du contenu, cache distribué natif
  CS/PIT/FIB → cache|requêtes en attente|table routage NDN
  SIP → signalisation session multimédia
  RTP → transport média temps réel (audio/vidéo)
  CoAP → REST léger pour capteurs IoT (sur UDP)
  MQTT → publish/subscribe léger pour IoT
  IMS → téléphonie IP virtualisée (S/I/P-CSCF)
  CSMA/CA → gestion accès canal radio 802.15.4
  FFD → full function (peut router/coordonner ZigBee)
  RFD → reduced function (capteur simple, veille)
