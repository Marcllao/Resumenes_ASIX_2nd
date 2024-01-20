# UF2 Seguretat en Serveis

Fecha: 24 de enero de 2024
Hecho: No
Asignatura: MP17 Seguretat en sistemes, xarxes i serveis (https://www.notion.so/MP17-Seguretat-en-sistemes-xarxes-i-serveis-a6a6996f62f94764936f4a9aea58316f?pvs=21)
Propietario: Examen

# Introducció a la seguretat en serveis

Els **atacs informàtics** solen constar de cinc fases:

1. **Reconeixement** → Recopolació per part de l’atacant, de la informació a comprometre → Recerques a internet i sniffing de la xarxa
2. **Escaneig →** Ús de la informació obtinguda per tal de buscar vulnerabilitats → Nmap i tracerroute
3. **Accés al sistema →** Fase efectiva de l’atac aprofitant les vulnerabilitats → Crackeig de contrasenyes
4. **Manteniment accés** → Intentar preservar la possibilitat d’accedir un altre cop en el futur → Malware per monitoritzar accions i codi maliciós
5. **Esborrat empremtes** → Esborrar les empremptes del que s’ha fet al sistema → Fitxers de registre i eborrar pistes de les possibles eines utilitzades

# Vulnerabilitats

Es necessari conèixer, emmagatzemar i gestionar les vulnerabilitats que es descobreixen per a:

- Comprovar ràpidament si es coneix alguna vulnerabilitat per alguna plataforma
- Identificar exploits a partir de les vulnerabilitats
    
    ## Gestió CPE
    
    - Common Platform Enumeration
    - Nom “estàndard” que s’assigna a un producte software, o sistema
    - Permet identificar-lo de manera única
    
    ## Gestió CVE
    
    - Common vulnerabilities and Exposures
    - Identificador que s’assigna a una vulnerabilitat
    - Llista d’entrades, on cadascun conté un nombre identificador, una descripció i almenys una referència pública
    
    ## Gestió CVSS
    
    - Pretén identificar i avaluar les vulnerabilitats
    - Intenta assignar puntuacions de severitat a vulnerabilitats, permetent prioritzar respostes i recursos segons l’amenaça
    - La puntuació oscil·la de 0 a 10
    - Es composa de tres grups principals: Base, Temporal i d’Entorn
    
    ## OpenVAS
    
    - Eina que permet escanejar una infraestructura, detectar els serveis que ofereix i automatitzar la seva explotació
    - Open source sita llicència GNU General Public License (GNU GPL)
        
        ### GVM
        
        - Gestor de vulnerabilitat de Greenbone
        - Servei central que consolida l’exploració de les vulnerabilitats en una solució completa de gestió de la vulnerabilitat
        - El servei té un sistema d’execució intern per a tasques programades i altres esdeveniments
        
        ### Scanner
        
        - És un motor d’escaneig de funcions completes que executa un feed actualitzat i ampliat contínuament
        
        ### GVM-Tools
        
        - Complementari a la interfície web
        - Format per clients interactius i no interactius
        - Una col·lecció de scripts ofereix exemples per a casos típics d’ús
    
    ## Gestió NVD
    
    - Disposa d’una base de dades amb totes les vulnerabilitats que han aparegut desde 2002
    - Permet la consulta directa o bé descarregar totes les dades en format XML
    - S’actualitza diàriament
    

---

# El servei DNS

## Introducció

- Domain Name System (DNS)
- Conté una base de dades dinàmica que associa direccions IP de hosts, serveis…
- Soporta ipv4 i ipv6
- Resource Records (RR), la informació es guarda en format de registres
- La informació s’agrupa en zones
- DNS tradueix direccions IP de recursos de xarxa a noms, i a l’inversa
- **Components principals que integren el DNS**
    - **Espai de dominis de noms**
        
        Estructura jeràrquica d’arbre on cada node té 0 o més registres (RR) amb la informació del domini
        
    - **Servidor de noms**
        
        Hi ha els servidor autoritzats de zona, i altres servidors que guarden conjunts de registres de diferents zones/dominis que obtenen consultant als servidors autoritatius
        
    - **Resolvers**
        
        Servidors caché o programes client que generen les consultes necessàries i obtenen la infomracion sol·licitada per oferir-la a qui la necessite
        

**Format genèric** d’un missatge de DNS:

| SECCIÓN  | DESCRIPCIÓN |
| --- | --- |
| HEADER | Contiene información sobre el tipo de mensaje |
| QUESTION | Contiene una o más solicitudes de información que se envían al servidor DNS |
| ANSWER | Contiene uno o más registros que responden a las solicitudes |
| AUTHORITY | Contiene uno o más registros que apuntan al servidor autoritativo del dominio en cuestión |
| ADDITIONAL | Registro con información adicional no necesaria para responder a la query |

El **header d’un missatge DNS** consta de **16 bytes** que es divideixen en diversos camps

Les **transaccions DNS** més comunes son:

- **Consultes i respostes:** DNS (Queries)
- **Transferències de Zona:** Replicació de fitxers de zona entre servidors
- **Actualitzacions Dinàmiques:** Per actualitzar els fitxers de zona d’un servidor DNS
- **Notificacions:** Per part d’un servidor autoritatiu per notificar canvis de dades de zones

## Seguretat en el servei DNS. Anàlisi de vulnerabilitats

| Tipus d’atac | Amenaça | Contramesura |
| --- | --- | --- |
| Locals | Mesures i polítiques de seguretat en la xarxa interna | Mecanismes anti-spoofing, IDS/IPS. Protecció de l’accés als servidors
i arxius |
| Servidor-Servidor | Es centralitza
l’administració de les dades a través de DDNS | Utilitzar un canal
restringit de comunicació o implementar TSIG. |
| Servidor Master-Esclau | Transferències de zona | Xifratge amb una clau. Es pot utilitzar
també SSL/TLS. |
| Servidor Master-Client Caché/Resolver | Aleatorietat en el port
origen de la consulta, i en els identificadors de missatge | DNSSEC |
| Servidor-Client | Atacs locals per interceptar dades i spoofing per
suplantar al servidor DNS | DNSSEC |

Què fa que el servei DNS sigui insegur

- Utilitza **UDP** com a **protocol de transport →** Vulnerable a **IP Spoffing**
- Ús del servei DNS per executar atacs de **DoS,** utilitzant peticions DNS en les que la direccio IP origen s’ha substituït per la del servidor víctima per a que les respostes vagin dirigides a ell
- Atacs de **Man-in-the-Middle**
- La majoria d’atacs es basen en la **suplantació de les direccions IP**
- No utilitza **mecanismes bàsics com autentiticació, xifrat i/o integritat**

## Explotació de vulnerabilitats. Atacs a DNS.

### DNS cache poisoning

L'enverinament de la memòria DNS és una tècnica d'atac cibernètic que implica corrompre o inserir informació falsa a la memòria cache del DNS

En un atac de cache poisoning l'atacant busca manipular la informació emmagatzemada al cau del resolutor de DNS. L'objectiu és redirigir el trànsit legítim a llocs web o servidors maliciosos, permetent a l'atacant realitzar diverses activitats malicioses, que poden ser:

- **Enviant sol·licituds malicioses**
- **Actualitzant la memòria cau**
- **Redirigint el trànsit**

Es pot mitigar aquestos atacs amb aquestes contramesures:

- **ID de transacció aleatori**
- **DNSSEC**
- **Aleatorització del port d'origen**
- **Limitació de taxa**

### DNS spoofing

Consisteix en la suplantació del servidor DNS

Permet generar **respostes DNS des d’un servidor maliciós** per consultes DNS que anaven dirigides al servidor DNS legítim, de forma que es manipula el seu contingut

Usa **tècniques** de **Man-in-the-middle** o manipulació dels protocols d’enrutament dinàmic

Es pot mitigar aquestos atacs amb aquestes contramesures:

- **Verificació de Registre:** Han de tenir un mètoda de verificació per garantir l’autenticitat del sol·licitant
- **Fortificació del sistema d’autentificació:** Implementar polítiques i mecanismes de fiabilitat o per mitja de VPN o SSL
- **Multiplicitat de punts de contacte:** Tenir diversos contactes per proporcionar major seguretat sobre les accions a realitzar
- **Política de renovació:**

### DNS tunneling

Tunelització del tràfic dintre del protocol DNS

El objectiu és establir un canal de comunicacions per filtrar informació o per disposar del control remot dels recursos involucrats

### DoS DNS amplification attack

Atacs de denegació de serveis mitjançant amplificació DNS

S’utilitza el servei DNS per **atacar amb contínues respostes a un servidor víctima remot**

Es **suplanta** la direcció **IP origen del servidor víctima** (IP spoofing) des d’un resolver (client DNS) per **enviar moltes consultes o peticions a servidors DNS intermitjos públics**

Els resolvers envien les respostes al servidor víctima saturant l’ample de banda
