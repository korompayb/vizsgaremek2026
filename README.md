# GENTEL Élet Biztosító – Hálózatkiépítési dokumentáció
**Készítette:** Szabó Erik, Korompay Bertalan

## Tartalomjegyzék
1. [Bevezető](#bevezető)
   - [Előkészületek](#előkészületek)
   - [Projektmunka kivitelezése](#projektmunka-kivitelezése)
   - [Topológia](#topológia)
   - [Címzési tábla](#címzési-tábla)
2. [A második rétegbeli redundancia megvalósítása](#a-második-rétegbeli-redundancia-megvalósítása)
3. [OSPF](#ospf)
4. [NAT és PAT](#nat-és-pat)

---

## Bevezető

### Előkészületek

Ebben a projektben egy biztosító cég belső rendszerét szerettük volna megvalósítani. Első lépésként szükségünk volt információra arról, hogy hogy is néz ki egy biztosítócég felépítése. Szerencsére volt egy ismerősöm, aki az egyik nagy biztosítócégnél dolgozott, ezért el tudta mondani, hogyan épül fel.

A cégünk négy telephelyre van bontva:
- **Iroda** (Főhadiszállás)
- **Adatközpont**
- **Belvárosi ügyfélszolgálat**
- **Vidéki ügyfélszolgálat**

Cégünk rendszerét igyekeztünk úgy kialakítani, hogy gondoljunk a jövőbeli bővítésekre, esetleges új telephelyekre.

Első lépésként a feladatleírás alapján elkezdtük megcsinálni a topológiát Packet Tracerben. A topológia elkészítésénél figyelembe vettük, hogy milyen eszközök állnak rendelkezésünkre:
- Cisco Catalyst 2960-as kapcsoló
- ISR 1941-es router
- 3. rétegbeli kapcsolók

A szerverekhez a Windows Server 2022 csomagját választottuk, ami a Microsoft Evaluation Centerből ingyenesen letölthető. A Linuxhoz a Debian 12-t választottuk.

### Projektmunka kivitelezése

A feladatok megosztását tűztük ki első célnak. Erikkel megállapodtunk, hogy ő csinálja a hálózat felépítését, én pedig előkészítem a projekt management rendszert, amin a közös munkát meg tudjuk valósítani. Mivel „pkt" fájlról beszélünk, a Git használata korlátolt volt. Ezért nem találtuk értelmét a Git repónkat ágakra bontani. Alapvetően egységesen dolgoztunk a teljes Packet Tracer projekten – amint kész volt valami, azt feltöltöttük.

### Topológia

A központi iroda és az adatközpont között közvetlen, nagy sávszélességű kapcsolat került kialakításra. Ezen a linken EtherChannel biztosítja a redundanciát és a terheléselosztást, így egy kábel vagy interfész meghibásodása esetén a kommunikáció zavartalanul folytatódik. Míg a szerverközpontban a layer 2 switchek csak trunkként szolgálnak.

A multilayer switchek végzik a VLAN-ok közötti Layer 3 routingot, míg az adatközpontban található Layer 2 switchek kizárólag trunk portként működnek. A szerverek látják el a DHCP, DNS, ActiveDirectory és adatbázis szolgáltatásokat, amelyek a dolgozók és ügyfelek hitelesítését, valamint az üzleti adatok tárolását biztosítják. Az elhelyezett management PC segítségével könnyen kezelhető. A nyilvános weboldal egy DMZ szegmensben kapott helyet. A DMZ-ben található webszerver kapcsolatban áll az adatbázissal, így az ügyfelek online intézhetik ügyeiket, miközben a belső hálózat védett marad.

A DMZ és a belső hálózat közötti forgalmat CISCO ASA tűzfalak szabályozzák, amelyek gondoskodnak a megfelelő szűrésről és biztonsági szabályokról.

A hálózatban található hozzáférési listák (ACL-ek) kialakítása még folyamatban van. A telephelyeken elhelyezett vezeték nélküli routerek DHCP-vel C osztályú IP címeket osztanak a mobil eszközök számára.

### Címzési tábla

A vállalat hálózata több logikai és fizikai alhálózatra van bontva. Az alhálózatok elkülönítése VLAN-okkal történik, így biztosítva a forgalom szegmentálását, a biztonságot és a hatékonyabb hálózati működést.

| Eszköz | Interfész | Eszköz típusa | IP Cím | Alhálózati maszk | Alap átjáró |
|--------|-----------|---------------|--------|------------------|-------------|
| Multilayer Switch 5 | F0/4 | Multilayer Switch | 10.2.10.5 | 255.255.255.248 | - |
| ActiveDirectory | F0/1 | Szerver PC | 10.2.10.2 | 255.255.255.248 | 10.2.10.5 |
| SQL SRK | F0/1 | Szerver PC | 10.2.10.3 | 255.255.255.248 | 10.2.10.5 |
| BACKUP | F0/1 | Szerver PC | 10.2.10.4 | 255.255.255.248 | 10.2.10.5 |
| DHCP | F0/1 | Szerver PC | 10.2.10.6 | 255.255.255.248 | 10.2.10.5 |
| DATACENTER | G0/0/0 | Router | 10.2.10.1 | 255.255.255.248 | - |
| ASA-DATA | G1/2 | Tűzfal (CISCO ASA) | 10.2.10.2 | 255.255.255.248 | - |
| ASA-OFFICE | G1/1 | Tűzfal (CISCO ASA) | 10.1.10.125 | 255.255.255.128 | - |
| interNet1 | S0/1/1 | Gateway (cluster) Router | 100.0.0.10 | 255.255.255.252 | - |
| interNet1 | S0/2/0 | Gateway (cluster) Router | 100.0.0.1 | 255.255.255.252 | - |
| interNet2 | S0/1/0 | Gateway (cluster) Router | 100.0.0.5 | 255.255.255.252 | - |
| interNet2 | S0/2/0 | Gateway (cluster) Router | 100.0.0.2 | 255.255.255.252 | - |
| interNet3 | S0/1/0 | Gateway (cluster) Router | 100.0.0.6 | 255.255.255.252 | - |
| interNet3 | S0/1/1 | Gateway (cluster) Router | 100.0.0.9 | 255.255.255.252 | - |
| NYOMTATO1 | 0 | Printer-PT | 10.1.30.8 | 255.255.255.0 | - |
| NYOMTATO2 | 0 | Printer-PT | 10.1.40.8 | 255.255.255.0 | - |
| NYOMTATO3 | 0 | Printer-PT | 10.1.50.8 | 255.255.255.0 | - |
| NYOMTATO4 | 0 | Printer-PT | 10.3.10.8 | 255.255.255.0 | - |
| NYOMTATO5 | 0 | Printer-PT | 10.4.10.5 | 255.255.255.0 | - |
| NYOMTATO6 | 0 | Printer-PT | 10.4.10.4 | 255.255.255.0 | - |
| NYOMTATO7 | 0 | Printer-PT | 10.4.10.3 | 255.255.255.0 | - |

Ezek az alhálózatok a címzési táblában szereplő eszközökkel összhangban vannak. A hálózati VLAN-ok a következőképp alakultak ki:

| ID | Név | Alhálózat |
|----|------|-----------|
| 10 | CALL CENTER | 10.1.10.0/24 |
| 20 | BOSS | 10.1.20.0/24 |
| 30 | SALES | 10.1.30.0/24 |
| 40 | ACCOUNTING | 10.1.40.0/24 |
| 50 | HR | 10.1.50.0/24 |
| 60 | CONFERENCE | 10.1.60.0/24 |

A gateway-ek minden VLAN esetében a multilayer switch virtuális interfészei (SVI-k).

---

## A második rétegbeli redundancia megvalósítása

A hálózat tervezése során az egyik legfontosabb szempont az volt, hogy a rendszer akkor is működőképes maradjon, ha valamelyik eszköz vagy kapcsolat meghibásodik. Egy biztosítótársaságnál ez különösen fontos, hiszen az ügyfelek adataihoz és a belső rendszerekhez folyamatos hozzáférésre van szükség.

A hálózatban több helyen is találhatók párhuzamos fizikai kapcsolatok, ezért elengedhetetlen volt az STP használata. Az STP feladata, hogy megakadályozza a hurkok kialakulását, amelyek könnyen teljes hálózati összeomláshoz vezethetnek. Mi a gyorsabb helyreállás miatt a Rapid STP-t választottuk, mert ez sokkal gyorsabban reagál egy linkhibára, mint a hagyományos STP. Így, ha valahol megszakad egy kapcsolat, a hálózat néhány másodpercen belül új útvonalat talál.

A stabil működés érdekében kijelöltük a root bridge-et is. Ezt a szerepet a központi multilayer switch kapta, mivel ez az eszköz rendelkezik a legjobb teljesítménnyel, és a hálózat logikai középpontjában helyezkedik el. Így biztosítható, hogy a forgalom mindig a lehető leghatékonyabb útvonalon haladjon.

A másik fontos elem az **EtherChannel** használata volt. Azokon a pontokon, ahol elengedhetetlen az adatforgalom – például az iroda és az adatközpont között – nem egyetlen kábelt használunk, hanem több fizikai kapcsolatot fogunk össze egy logikai csatornába. Ez két szempontból is előnyös: egyrészt jelentősen megnöveli a rendelkezésre álló sávszélességet, másrészt, ha az egyik kábel meghibásodik, a forgalom automatikusan a többi kábelen megy tovább. Mi LACP-t használtunk, mert ez dinamikusan kezeli a csatornába tartozó portokat, és megbízhatóbb működést biztosít.

### Miért EtherChannel?
- Növeli a rendelkezésre álló sávszélességet.
- Ha egy kábel meghibásodik, a kapcsolat továbbra is működik a többi linken.
- STP egyetlen logikai linkként kezeli, így nem blokkolja feleslegesen.
- Több fizikai portot logikai csatornaként tud kezelni.

Ez különösen fontos egy olyan környezetben, ahol nagy mennyiségű adat mozog a telephelyek között, és ahol a szolgáltatáskimaradás komoly problémát jelentene.

**Összességében:** a második rétegbeli redundancia kialakítása jelentősen növeli a hálózat megbízhatóságát. A rendszer akkor is működőképes marad, ha egy kábel vagy eszköz meghibásodik.

---

## OSPF

Az internet modellezéséhez kialakítottunk egy három routerből álló cluster-t. A routerek mind statikus publikus címet kaptak és az OSPF area 0-ba tartoznak.

---

## NAT és PAT

A telephelyek közötti kommunikáció nagy része az interneten keresztül zajlik, ezért szükség volt valamilyen címfordítási megoldásra. A belső hálózat privát IP-tartományokat használ, amelyek közvetlenül nem jelenhetnek meg az interneten, így NAT-ot és PAT-ot alkalmaztunk.

A **NAT** segítségével a belső, privát IP-címeket publikus címekre fordítjuk át, amikor a forgalom kilép a hálózatból. A **PAT** pedig lehetővé teszi, hogy akár több száz belső eszköz is ugyanazt a publikus IP-címet használja, csak eltérő portokkal. Ez különösen hasznos, mert nem kell minden telephelynek nagy mennyiségű publikus címet vásárolnia.

A címfordítást a telephelyek határán lévő routerek végzik. Így a belső hálózat védve marad, az internet felé pedig csak a szükséges információk kerülnek ki. A megoldás egyszerű, megbízható, és jól illeszkedik a biztosítótársaság hálózati felépítéséhez. PAT-ot otthoni és munkahelyi hálózatok is előszeretettel választják.

---

**Projekt elkészítésének dátuma:** 2026-04-20