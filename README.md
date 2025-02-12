Az Országos Mentőszolgálat kiérkezési statisztikáinak vizsgálata: a 90.
percentilis becslése
================
Ferenci Tamás (<https://www.medstat.hu/>)
<br>2025. február 11.

- [Összefoglaló](#összefoglaló)
- [Problémafelvetés](#problémafelvetés)
- [Eredmények](#eredmények)
- [Módszertani részletek](#módszertani-részletek)
  - [Felhasznált eloszlások](#felhasznált-eloszlások)
  - [Számítástechnikai részletek](#számítástechnikai-részletek)
  - [Az eloszlás illesztése](#az-eloszlás-illesztése)
- [Nyitott kérdések, továbbfejlesztési
  lehetőségek](#nyitott-kérdések-továbbfejlesztési-lehetőségek)
- [Irodalomjegyzék](#irodalomjegyzék)

## Összefoglaló

- Egy blog a minap
  [közölte](https://rontgen.444.hu/2025/02/10/bozotharc-aran-sikerult-megszerezni-az-orszagos-mentoszolgalat-kierkezesi-statisztikajat-a-szamoktol-nem-lesz-boldog-senki)
  az Országos Mentőszolgálat (OMSZ) kiérkezési időkre vonatkozó
  statisztikáit. Jellemző, hogy olyan adatokat, amiket mondjuk Angliában
  bárki letölthet, folyamatosan frissülően, magyarázatokkal ellátva a
  mentőszolgálat [saját
  honlapjáról](https://www.england.nhs.uk/statistics/statistical-work-areas/ambulance-quality-indicators/),
  azt Magyarországon egy blognak (!) kell, adatkéréssel megszereznie,
  majd ha sikerül, akkor a mentőszolgálat úgy adja oda a táblázatot,
  hogy kinyomtatják, majd rossz minőségben beszkennelik és *képként*
  átküldik… (Tehát az OMSZ *plusz munkát* fektetett abba, hogy az
  eredmény *kevésbé* legyen felhasználható.) Hogy ennek vajon mi az oka,
  annak eldöntését az olvasóra bízom, a blog azonban szerencsére tudta
  digitalizálni ezeket az adatokat. Az adatközlés 2024 októberig tart.
- Van azonban a történetben egy csavar: az OMSZ csak az átlagot és
  mediánt (tehát azt az időt, amin belül az esetek feléhez érnek ki)
  adta meg. A mentőellátás minőségi vizsgálatában tipikus 90.
  percentilist (tehát azt az időt, amin belül az esetek 90%-ához
  kiérnek) nem közölte. A dolog azért érdekes, mert a blog ezt is
  explicite kérte, de annak ellenére sem adták ki, hogy az átlagot és a
  mediánt igen (tehát mind a nyers adatok, mind a számítási módszer
  biztosan az OMSZ rendelkezésére áll). Hogy mi ennek az oka, annak
  eldöntését ismét csak az olvasóra bízom.
- A dolog azonban így egy érdekes statisztikai probléma lett, *ha*
  ugyanis ismerjük a kiérkezési idők eloszlását, *és* az olyan eloszlás,
  amit az átlaga és a mediánja meghatároz, akkor ebből a két értékből ki
  tudjuk számolni a nem közölt 90. percentilist is! Azt persze nem
  tudhatjuk, hogy ez Magyarországon milyen eloszlás, de irodalmi adatok
  és statisztikai megfontolások alapján tehetünk észszerű feltevéseket.
- Írásomban bemutatom e kérdés hátterét, kitérve mind a használt
  mutatószámokra, mind az előbb említett statisztikai módszerre. Ezután
  végigszámolom ezt, a robusztusság kedvéért többféle eloszlással is, és
  közlöm az eredményeket. A transzparencia és a nyílt tudomány jegyében
  minden számítást nyilvánosságra hozok.
- A legmagasabb, Kiemelt P1 prioritási kategóriában (pl. újraélesztés)
  az utolsó közölt időpontokban a 90. percentilis becslésem szerint
  országosan 19-23 perc körül van, Budapesten némileg rosszabb, 17-25
  perc körül is lehet. Tehát ennyi idő alatt érnek ki az esetek 90%-hoz,
  vagyis a betegek 10%-a ennél is többet kénytelen várni a Kiemelt P1
  kategóriában. P1-nél (közvetlenül életveszélyes, azonnali beavatkozást
  igénylő állapotok, pl. többszörös traumás sérülés, zajló görcsroham,
  végtagamputáció, súlyos égés, a legmagasabb időfaktorú esetek
  kivételével) a becsült 90. percentilis országosan 22-27 perc körüli,
  Budapesten 25-30 perc körüli. Végül P2 kategóriában (potenciálisan
  életveszélyes, gyors beavatkozást igénylő állapotok, pl. légúti
  idegentest nehézlégzés nélkül, közepesfokú égés, vérhányás, hirtelen
  látás- vagy hallásvesztés) a becsült 90. percentilis országosan 40-45
  perc körüli, Budapesten 60-70 perc körüli.
- Újra hangsúlyozom, hogy ez egy becslés, de szerencsére nincsen semmi
  gond, mert ha a becslésem rossz lenne, akkor ezt a kérdést az OMSZ egy
  pillanat alatt tisztázhatja azzal, hogy közli a tényadatokat.

## Problémafelvetés

Bármely mentőszolgálat működésének egy nagyon fontos, közvélemény
számára pedig talán legfontosabb minőségi mutatója a **kiérkezési idő**,
tehát a segélyhívástól a mentőegység helyszínre érkezéséig eltelő idő.

Kitérő, de a mai magyar helyzet fényében nem felesleges kitérő: mint
általában is, de egy ilyen kérdésben pláne, a precíz definíciók
fontosak. A helyszínre érkezés, tehát az óra megállítása elég egyértelmű
(bár itt is pontosnak kell lenni: az [angol
eljárásrend](https://www.england.nhs.uk/statistics/wp-content/uploads/sites/2/2025/01/20250117-AmbSYS-specification.pdf#%5B%7B%22num%22%3A65%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C69%2C398%2C0%5D)
másfél oldalon keresztül specifikálja, hogy pontosan mikor kell az órát
megállítani, odáig, hogy a betegtől hány méterre kell lenni), de az
elindítás izgalmasabb kérdés. A magyar Mentőszolgálat esetében a
probléma ott kezdődik, hogy ennek az egésznek eleve nincsen specifikált,
nyilvánosan elérhető eljárásrendje, ezért esetenkénti nyilatkozatokra,
sajtóhírekre vagyunk utalva. [Ezek
szerint](https://www.hazipatika.com/eletmod/veszelyben/cikkek/a_mentok_cafolnak_nem_kozmetikazzak_az_adatokat)
az OMSZ nem a hívás beérkezésétől számítja a kiérkezési időt, mert „a
hívás beérkezésének pillanatától addig, amíg a mentő indítása
megtörténhet, az információk kinyerése (ami legtöbbször a hívó fél
lelkiállapotától, válaszadási képességétől függ) eltérő időhosszúságú
beszélgetéseket eredményez”. (Mondjuk azt nem írják, hogy akkor
mikortól; ebből úgy tűnik, hogy a mentő elindításától, ami elég meredek,
mert így ha nem áll rendelkezésre rögtön hozzárendelhető mentőegység, az
nem fogja növelni a kiérkezési időt…) Sőt, azt írják, hogy a hívás
beérkezésétől való számítás – szó szerint idézem az OMSZ közleményét –
„teljesen szakmaiatlan” és az erre való rákérdezés „hozzá nem értésről
tanúskodik”. Én ebben a kérdésben nem mernék igazságot tenni, de az
mindenesetre izgalmas, hogy az angolok valamiért mégis pontosan így
csinálják. Azzal a különbséggel, hogy ők ezt [pontosan le is
írják](https://www.england.nhs.uk/statistics/wp-content/uploads/sites/2/2025/01/20250117-AmbSYS-specification.pdf#%5B%7B%22num%22%3A65%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C69%2C727%2C0%5D)
az eljárásrendjükben: a legmagasabb prioritási kategóriában az óra
indítása a hívás klinikai kódolása, vagy az első mentőegység
hozzárendelése (amelyik korábban megtörténik), *de legkésőbb* a hívás
kapcsolásától számított 30 másodperc, a többi prioritási kategóriában a
hívás klinikai kódolása, vagy az első mentőegység hozzárendelése
(amelyik korábban megtörténik), *de legkésőbb* a hívás kapcsolásától
számított 240 másodperc. Mivel az OMSZ esetében még csak leírva sincs
nyilvánosan az eljárásrend, nemhogy indokolva, így nehezen megítélhető,
hogy amit csinálnak az helyénvaló-e; azt pedig csak remélni lehet, hogy
a módszerük nem torzítja a statisztikát (mert ha igen, akkor azon
semmilyen utólagos elemzése a számoknak nem fog segíteni).

Ha már egyszer definiálva van az óra elindítása és megállítása, és
innentől ne törődjünk azzal, hogy hogyan, akkor a kettőből megkapjuk a
kiérkezési időt. A következő kérdés az, hogy ezt hogyan jellemezzük.

A probléma magva az, hogy ez nem egyetlen szám: lesz aki a mentőállomás
előtt esik össze és 3 perc alatt ott vannak, lesz, aki a világ végén,
pont hóviharban és két óra alatt. (Fontos, hogy most arról beszélek, ha
leszűkítjük magunkat egy adott prioritásra, tehát nem arról van szó,
hogy a szóródást az okozza, hogy alacsonyabb prioritásoknál hosszabbak a
kiérkezési idők. Szóródás még adott prioritáson *belül* is van.) Szóval
ez nem egyetlen érték, hanem – szép szóval élve – eloszlása van.
Kinézhet például így:

<img src="README_files/figure-gfm/unnamed-chunk-1-1.png" width="100%" />

Ez egy képzeletbeli példa (az adatokat én szimuláltam), ami azt mutatja,
hogy 10 ezer mentőállátásból hány történt adott időablakon belül (5
perces felbontásban). Volt 416 eset, ahol 5 percen belül kiért a mentő,
3085 esetben 5 és 10 perc között, 3155 esetben 10 és 15 perc között, és
így tovább. A legszerencsésebb embernek 1.9 percet kellett várnia, a
legpechesebbnek 81.9 percet.

Hogyan jellemezzük a helyzetet egy, vagy legfeljebb néhány számba
sűrítve? Hiszen, ahogy volt róla szó, nekünk valamilyen indikátor,
mérőszám kellene a mentőellátás minőségének jellemzésére. A két
legismertebb mutató erre a célra az átlag és a medián.

Az **átlag** a kiérkezési idők összege osztva azzal, hogy hány
kiérkezésről beszélünk; lényegében tehát megadja, hogy ha *tényleg*
mindenkihez ugyanannyi idő alatt ért volna ki a mentő, akkor az hány
perc volna (ha közben a kiérkezési idők összege állandó maradna). Az
átlag problémáit részletesen tárgyalja az
[irodalom](https://tamas-ferenci.github.io/FerenciTamas_BevezetesABiostatisztikaba/deskriptiv.html#deskriptivmennyegyvaltanalitikusmutatoszamokcentralistendencia),
itt most csak annyit említek meg, hogy érzékeny a kilógó értékekre: ha
van – akár csak kis számú – de a többitől lényegesen eltérő érték, akkor
azok „elhúzzák” az átlagot maguk felé, ami miatt az többé már nem igazán
„tipikus” érték lesz. Ez a probléma itt is jelentkezik, mert a fenti
eloszlás nem szimmetrikus, a statisztikusok úgy mondják: ferde az
eloszlás, mert az egyik irányban van egy szigorú korlátja (0 perc), a
másik irányban viszont szabadon tud szóródni. A dolog végeredménye, hogy
felfelé előfordulnak, ha nem is gyakran, de előfordulnak a többitől
nagyobb, akár lényegesen nagyobb értékek, amit nem tudnak ellensúlyozni
a másik irányban kilógó értékek, hiszen a kiérkezési idő 0 alá nem
mehet. A fenti példában az átlag 13.8 perc. Tipikus érték ez? Ez nem
igazán jó kérdés, mert hát mi az, hogy „tipikus”, de az mindenesetre
elmondható, hogy az esetek 60.3%-a kisebb ennél! Sokan vitatkoznának,
hogy mennyire „tipikus” ez a kiérkezési idő, ha egyszer az esetek
majdnem kétharmadában jobb a helyzet! (Egyébként pontosan ugyanez a
helyzet a jövedelmekkel, azok eloszlása is ferde, ezért van az, hogy az
átlag-jövedelem sem feltétlenül szerencsés mutató önmagában. Nem tudom
megállni, hogy e ponton ne idézzem a legjobb magyar nyelvű
[biostatisztika könyv](http://biostatkonyv.hu/) példáját: a magyar
lakosság 99%-ának az átlagosnál több lába van! És tényleg, gondoljuk
végig: 1 vagy 0 lába néhányaknak sajnos van – erről történetesen [pont
én is](https://hunvascdata.hu/) bőven tudnék adatokat mondani –, 3 vagy
több azonban nem igazán, így a magyar lakosság átlagos lábszáma 1,99.
Tehát a lakosság 99%-ának valóban az átlagosnál több lába van! Ez
ugyanaz, ferde eloszlás, csak, ahogy a statisztikusok mondják, egy
diszkrét eloszlással.)

Ez a gondolat vezet el minket a következő mutatóhoz, ami nem más, mint a
**medián**. A medián az az idő, amin belül a betegek feléhez értek ki
(ebből fakadóan a feléhez ennél lassabban értek ki). A medián kiérkezési
idő tehát egyszerűen a nagyság szerint sorbarendezett kiérkezési idők
közül a középső. A fenti példában a medián kiérkezési idő 12.1 perc volt
– ennyi időn belül értek ki az esetek feléhez, ebből fakadóan a betegek
fele ennél több időt volt kénytelen várni. A mediánt sokkal kevésbé
sújtja a kilógó értékek fent említett problémája, úgy szokták mondani,
hogy robusztus mutató. (Gondoljuk végig: ha a fenti példában fogunk egy
80 perces kiérkezést és lecseréljük 80 évre, akkor az átlag teljesen
értelmetlenné válik – a medián viszont meg sem moccan, még ebben a
teljesen extrém esetben sem!) Vannak bizonyos hátrányai is, de ezen
előnye miatt ferde eloszlásoknál nagyon gyakori a medián használata.

A kérdést lehetne még hosszan boncolgatni, azért nem teszem, mert a
problémakör, amit most vizsgálni szeretnék, más. Nincs semmi baj sem az
átlaggal, sem a mediánnal, de mindkettő ún. **középérték**, tehát a
közepes („tipikus”, „jellemző”, „gyakori”, pláne idézőjelben „átlagos”…
ilyeneket szoktak mondani, egyik rosszabb mint a másik, nincs nagy
jelentősége, igazából ez inkább már szómágia) értékét igyekeznek
jellemezni az eloszlásnak. Csakhogy: a kiérkezési időknél, pláne a magas
prioritásoknál, életveszélyes helyzetekben, nem csak ez fontos! Nem csak
az számít, hogy mi a *közepes* kiérkezési idő, hanem az is, hogy hogyan
néznek ki a *rossz* kiérkezési idők. Mi történik akkor, ha közepesnél
rosszabbul alakul a helyzet – mert ilyen is van, és vajon akkor
*mennyire* szokott rosszul alakulni? Vagyis: mi a helyzet az eloszlás
felső szélénél? Mert az remek, ha a medián idő mondjuk 12 perc, de
gondoljunk bele, ez összesen annyit mond, hogy a betegek feléhez kiérnek
12 percen belül – ami szuper hír, félreértés ne essék, de mi a helyzet a
betegek másik 50%-ával? Hozzájuk 12,1 perc alatt érnek oda? Vagy 20 perc
alatt? Vagy 20 óra alatt? A medián erről *semmit nem mond*!

A mentőellátás tipikusan az a terület, ahol a közepes értékek mellett
rendkívül fontos az eloszlás *szélét* is jellemezni; az kevés, ha
tudjuk, hogy a közepes idő jó, ez nagyon tipikusan az a műfaj, ahol
rosszabb forgatókönyvekre is méretezni kell – ebben a műfajban, pláne
mondjuk egy újraélesztésnél, a rossz időknek is elfogadhatóan jónak kell
lennie!

Itt érdemes egy pillanatra megállni a különféle standardok, elvárások
kapcsán. Gyakran szokták emlegetni a „15 perces szabályt”, tehát, hogy a
legsürgősebb esetekhez ennyi idő alatt érjen ki a mentő, de azt talán
már kevesebben gondolják végig, hogy ez *pontosan* mit is jelent. Azt,
hogy *mindenkihez* érjen ki a mentő 15 perc alatt ilyen sürgősségnél?
(Tehát a mérőszámunk a maximális kiérkezési idő legyen?) Ez jól hangzik,
és tényleg nagyon megnyugtató, ha így van, csak sajnos valószínűleg a
világ egyetlen országában sem teljesül, hiszen *egyszer* bárhol
előfordulhat valamilyen vis maior. Ezt tehát aligha lehet elvárni, és
nem is lenne túl fair mutató, ha egymilliószor kiérünk egy perc alatt,
egyszer pedig egy óra alatt, azt mondani jellemzésként, hogy „1 óra volt
a maximális kiérkezési idő”. (A statisztikusok úgy mondanák: a maximum
nagyon érzékeny mutató, mert egyetlen érték is el tudja húzni, ezért nem
jellemzi jól az eloszlás szélét.) Másik oldalról viszont a medián sem
jó, mert noha nem érzékeny, de nem az eloszlás szélét jellemzi, ahogy
arról az imént szó volt. Akkor mi a megoldás?

A legjobb mutató erre a célra a **percentilis**. A percentilisek a
medián általánosításai, ezért megőrzik a robusztusságát, de már szélt is
tudnak jellemezni, nem csak közepet. A medián az az időtartam, amin
belül a betegek 50%-ához érnek ki, 50%-ához nem – a percentilis ezt
általánosítja: ugyanez a mutató, csak nem 50%-kal. Hanem például
75%-kal: a 75. percentilis az az időtartam, amin belül a betegek
75%-ához kiérnek – tehát a betegek 25%-a ennél többet kénytelen várni.
(A statisztikusok egyébként inkább a **kvantilis** kifejezést
használják, és ezt 0,75-kvantilisnek hívják.) A mentőellátásban
tipikusan a 90. percentilist használják: mennyi az az időtartam, amin
belül az esetek 90%-ához kiérnek? (Azaz, a betegek 10%-a kénytelen ennél
több várni.) Ez lesz a jó mutató: szemben a maximummal, robusztus, de
szemben a mediánnal az eloszlás szélét jellemzi. A 90. percentilis jól
jellemzi a rosszul alakuló helyzeteket is tekintve a teljesítményt!

Ezt szokták tehát mérőszámként használni, és most már azt is
elárulhatom, hogy például az angol mentőszolgálat is *erre* értelmezi a
15 perces limitet! A 90. percentilis legyen 15 perc alatt, tehát az
esetek legalább 90%-ához érjenek ki 15 perc alatt. Amint az az
eljárásrendjük [megfelelő
pontjából](https://www.england.nhs.uk/statistics/wp-content/uploads/sites/2/2025/01/20250117-AmbSYS-specification.pdf#%5B%7B%22num%22%3A21%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C69%2C570%2C0%5D)
kiderül; most megint megjegyezhetném, hogy ilyet hol találunk a magyar
mentőszolgálat honlapján…

Ezen iszonyatosan hosszúra nyúlt bevezető után elérünk a
problémafelvetéshez.

Egy magyar blog ugyanis a minap
[közölte](https://rontgen.444.hu/2025/02/10/bozotharc-aran-sikerult-megszerezni-az-orszagos-mentoszolgalat-kierkezesi-statisztikajat-a-szamoktol-nem-lesz-boldog-senki)
az OMSZ kiérkezési statisztikáit. Jellemző adalék, hogy olyan adatokat,
amiket mondjuk Angliában bárki letölthet, folyamatosan frissülően,
magyarázatokkal, gépi úton feldolgozható idősoros adatokkal,
definíciókkal, adatközlési naptárakkal ellátva a mentőszolgálat [saját
honlapjáról](https://www.england.nhs.uk/statistics/statistical-work-areas/ambulance-quality-indicators/),
azt Magyarországon egy blognak (!) kell, adatkéréssel megszereznie, majd
ha sikerül, akkor a mentőszolgálat úgy adja oda a táblázatot, hogy
kinyomtatják, majd rossz minőségben beszkennelik és *képként* átküldik…
(Tehát az OMSZ *plusz munkát* fektetett abba, hogy az eredmény *kevésbé*
legyen felhasználható.) Hogy ennek vajon mi az oka, annak eldöntését az
olvasóra bízom, a blog azonban szerencsére tudta digitalizálni ezeket az
adatokat. Az adatközlés 2024 októberig tart.

Van azonban a történetben egy csavar: az OMSZ csak az átlagot és mediánt
adta meg. A fent tárgyalt, és mint láttuk, a mentőellátás minőségi
vizsgálatában kritikusan fontos 90. percentilist nem közölték. A dolog
azért érdekes, mert a blog ezt is explicite kérte, de annak ellenére sem
adták ki, hogy az átlagot és a mediánt igen (tehát mind a nyers adatok,
mind a számítási módszer biztosan az OMSZ rendelkezésére áll). Hogy mi
ennek az oka, annak eldöntését ismét csak az olvasóra bízom, akárhogy
is, mediánunk és átlagunk van, de 90. percentilisünk nincs.

Tudunk ezzel valamit kezdeni? A legtisztább megoldás természetesen az
lenne, ha egész egyszerűen az OMSZ közölné a 90. percentilist is (még
egyszer: olyan adatról beszélünk, ami biztosan a rendelkezésükre áll, és
ami más nyugat-európai országban a mentőszolgálat saját honlapjáról
bárki számára, magyarázattal ellátva letölthető), de addig is, van még
egy lehetőség.

A valószínűségszámításban és statisztikában [szoktunk
beszélni](https://tamas-ferenci.github.io/FerenciTamas_AValoszinusegszamitasEsAStatisztikaAlapvonalai/)
eloszlásokról; ez lényegében a fenti ábrán is szereplő viselkedést
(„hogyan oszlik el a kiérkezési idő?”) igyekszik matematikai
formalizmussal megragadni és leírni. A dolgot úgy kell elképzelni, hogy
az eloszlásnak van egy általános kinézete, hogy nagyjából milyen alakot
követ, ez az eloszláscsalád, ennek általában saját neve van, de ezen túl
van egy vagy több ún. paramétere, amivel az általános kinézeten belül
„finomhangolható”: kicsit eltolható balra-jobbra, összenyomható vagy
széthúzható, ilyenekre kell gondolni. Példának okáért, van egy nevezetes
eloszlás, a **lognormális eloszlás**, aminek kinézetét a következő ábra
mutatja néhány különböző paraméter mellett:

<img src="README_files/figure-gfm/unnamed-chunk-2-1.png" width="100%" />

Az ötlet a következő: mi van akkor, ha valaki megsúgja nekünk, hogy a
magyar kiérkezési idők lognormális eloszlást követnek? Első ránézésre
ezzel nem vagyunk sokkal beljebb: mint láttuk, nagyon sokféle konkrét
lognormális eloszlás van attól függően, hogy milyen paramétert
választunk – még ha tudjuk is (a súgásnak köszönhetően), hogy
*valamilyen* lognormális, honnan tudjuk, hogy pontosan melyik?! Itt jön
a kulcsgondolat: tudjuk az átlagot és a mediánt! Hiszen azokat megadta
az OMSZ. Igen, végtelen sokféle konkrét lognormális eloszlás van,
csakhogy ezek közül kevés lesz olyan, aminek *pont* az az átlaga, amit
megadott az OMSZ, és *pont* az a mediánja, amit megadott az OMSZ. A
helyzet valójában ennél is sokkal jobb: nem „kevés” olyan lesz, hanem
*konkrétan egy*! Se több, se kevesebb. (Ennek belátásához kell némi
statisztika, ezt a következő szakaszban mutatom be.) Azaz valami nagyon
fontosra jutottunk: teljes joggal mondhatjuk, hogy ha ismerjük az
átlagot és a mediánt, az meghatározza, hogy *konkrétan* melyik
lognormális eloszlást követik a kiérkezési idők! (De ne feledjük, a
súgás, tehát, hogy *valamilyen* lognormálist követ, kellett!) Nem túl
bonyolult a konkrét kiszámolása sem, ezt szintén megmutatom a következő
szakaszban.

És ezzel: meg is vagyunk. Ha ugyanis tudjuk teljesen pontosan a konkrét
eloszlást, akkor onnantól *bármit* könnyedén kiszámolhatunk arra
vonatkozóan – például a 90. percentilist is! És ezzel végeztünk is,
ezzel a módszerrel megvan a nem közölt 90. percentilis. Elsőként (a
súgás alapján) tekintünk egy eloszláscsaládot, utána a közölt átlag és
medián alapján meghatározzuk a konkrét eloszlást az eloszláscsaládon
belül, végül pedig, ha már egyszer megvan a konkrét eloszlás, lekérjük
annak a 90. percentilisét.

Az egyetlen bökkenő a fenti okfejtésben a súgás. A valóságban ugyanis
sajnos senki nem súgja meg nekünk, hogy mi az eloszláscsalád, azaz ez
innentől egy *feltevés* lesz – de szerencsére elég észszerű feltevés.
Egyrészt azért, mert statisztikai megfontolások alapján az ilyen jellegű
adatoknak ez általában jó modellje. (Például, és ezt a fenti ábra is
szemlélteti, a lognormális eloszlásnak 0 alsó korlátja, és az eloszlás
ferde – mindkettő pont olyan, mint a kiérkezési időknél várjuk!
Egyébként a lognormálist általában is használják bármiféle utazási idő
eloszlásának leírására.) A másik ok, hogy bár magyar adataink nincsenek,
de az irodalomban fellelhetőek erre vonatkozó eredmények: kutatók,
akinek volt hozzáférésük egyesével a kiérkezési időkhöz, megnézték, hogy
milyen eloszlás passzol rájuk, és van rá közlésünk, ahol azt találták,
hogy a lognormális. Ez is megnöveli a bizalmunkat ebben.

Még egy dolgot teszek, hogy növeljem az eredmények robusztusságát: nem
csak egyféle eloszláscsaládot használok. A lognormális mellett
végigszámolom a dolgot még néhány további, szintén szóba jövő
eloszlással. Ha ugyanis azt kapjuk, hogy az eredmények nagyjából
hasonlóak, az szintén megnöveli bennük a bizalmunkat, hiszen azt
jelenti, hogy a kapott érték nem függ nagyon attól, hogy konkrétan
milyen az eloszlás – ami jó hír, hiszen ezt végső soron nem tudhatjuk.
Az eredmények között ezért fog nem csak a lognormális, hanem több egyéb
név is megjelenni. Ezek tartománya mutatja azt a bizonytalanságot, ami
abból fakad, hogy nem tudjuk pontosan melyik eloszlás érvényes; de
szerencsére, mint majd látni fogjuk, elég közel futnak egymáshoz.
Természetesen ez azon nem segít, ha valamilyen teljesen más eloszlás
érvényes.

Szerencsére ez mind statisztikai megfontolások, mind szakirodalmi adatok
alapján nem túl valószínű, így a kapott becslések jól védhetőek, de
ettől természetesen nem téveszthetjük szem elől, hogy ezek csak
becslések! De szerencsére nincsen semmi gond, mert ha a becslésem rossz
lenne, akkor ezt a kérdést az OMSZ egy pillanat alatt tisztázhatja
azzal, hogy közli a tényadatokat.

## Eredmények

A kapott számszerű eredményeimet elérhetővé tettem bármilyen további
gépi feldolgozásra, megtekintésre jól alkalmas
[CSV](https://github.com/tamas-ferenci/omsz-kierkezesi-ido-percentilis/blob/main/OMSZ-kierkezesi-ido.csv),
illetve R nyelven feldolgozható
[RDS](https://github.com/tamas-ferenci/omsz-kierkezesi-ido-percentilis/blob/main/OMSZ-kierkezesi-ido.rds)
formátumban.

Itt néhány egyszerű vizualizációt közlök a kapott eredmények
szemléltetésére. Az eredményeket mindenhol prioritás szerint lebontva
közlöm, a dolognak csak így van értelme. A prioritásokban eligzítást
nyújt az OMSZ [egyik
útmutatója](https://oktatas.mentok.hu/pluginfile.php/258749/mod_resource/content/1/Kerdezesi_protokoll_mentesiranyitok_reszere_v2.0_2023.12.19.pdf).

A talán legfontosabb a **Kiemelt P1** kategória; ez a legmagasabb
prioritású kategória, ahol a beteg közvetlen életveszélyben van,
azonnali beavatkozást igényel, és még ezen belül is legmagasabb
időfaktorú a kórkép (tipikusan: újraélesztés). Itt így alakult a becsült
90. percentilis (a különböző színek a különböző becslési
megközelítéseket mutatják, ahogy fent is szerepelt):

<img src="README_files/figure-gfm/unnamed-chunk-3-1.png" width="100%" />

Az utóbbi időben országosan 19-23 perc körül, Budapesten 17-25 perc
körül alakult a becsült 90. percentilis a Kiemelt P1 prioritási
kategóriában.

A következő, **P1** prioritású kategória továbbra is az azonnali
beavatkozást igénylő életveszély, a korábban említett legmagasabb
időfaktorú kórképek nélkül (olyanok tartoznak ide, mint a többszörös
trauma, zajló görcsroham, végtagamputáció, súlyos égés, a legmagasabb
időfaktorú esetek kivételével). Itt így alakult a becsült 90.
percentilis:

<img src="README_files/figure-gfm/unnamed-chunk-4-1.png" width="100%" />

Az utóbbi időben országosan 22-27 perc körül, Budapesten 25-30 perc
körül alakult a becsült 90. percentilis a P1 prioritási kategóriában.

Végül még ábrázolom a **P2** prioritási kategóriát; ide tartoznak a
potenciálisan életveszélyes, gyors beavatkozást igénylő állapotok (pl.
légúti idegentest nehézlégzés nélkül, közepesfokú égés, vérhányás,
hirtelen látás- vagy hallásvesztés). Itt így alakult a becsült 90.
percentilis:

<img src="README_files/figure-gfm/unnamed-chunk-5-1.png" width="100%" />

Az utóbbi időben országosan 40-45 perc körül, Budapesten 60-70 perc
körül alakult a becsült 90. percentilis a P2 prioritási kategóriában.

## Módszertani részletek

### Felhasznált eloszlások

Mint láttuk, az egész probléma kulcskérdése az, hogy milyen sokasági
eloszlást feltételezünk a kiérkezési időkre. OMSZ által szolgáltatott
adat híján ezt empirikusan (tehát a konkrét magyar adatokon) nem tudjuk
megnézni. A mostani konkrét kérdéstől függetlenül is jó lenne, ha erről
készülne hazai vizsgálat is, de addig is a legjobb amit tehetünk, hogy
általános biostatisztikai ismeretek, illetve a konkrét helyzetre
vonatkozó, más országból származó irodalmi adatok alapján megpróbáljuk
összeszedni, hogy milyen eloszlások jöhetnek reálisan szóba.

Egyáltalán nem állítom, hogy átfogó irodalomkutatást végeztem,
ellenkezőleg, ezt az egész elemzést villámgyorsan dobtam össze, így az
irodalmat is inkább csak átpörgettem, ez alapján a következő
lehetőségeket találtam (mindenhol megadok hivatkozást is
alátámasztásként):

| Eloszlás | Irodalmi hivatkozás |
|----|----|
| Lognormális | ([Ingolfsson, Budge, és Erkut 2008](#ref-ingolfsson2008)), ([Pury 2021](#ref-pury2021)), ([Rastpour, Ingolfsson, és Kolfal 2020](#ref-rastpour2020)), ([Westgate és mtsai. 2016](#ref-westgate2016)), ([Aladdini 2010](#ref-aladdini2010)) |
| Inverz normális | ([Zhang és mtsai. 2016](#ref-zhang2016)) |
| Weibull | ([Chong 2016](#ref-chong2016)) |
| Gamma | ([Nelas és Dias 2020](#ref-nelas2020)) |

Mint látható, legjobban a lognormális alátámasztott, ezért használom én
is azt elsődlegesen, a többit pedig robusztusság-vizsgálatra.

Egy matematikai jellegű megjegyzést is ide kell tűznünk. Nekünk most két
adatunk van (átlag és medián), tehát általánosságban véve arra van
reményünk, hogy olyan eloszlásokat tudjunk illeszteni, amiket két
paraméter határoz meg! Ez egyfajta limitáció, például a lognormálisnak
is van háromparaméteres kiterjesztése ([Kuk és mtsai.
2017](#ref-kuk2017)), lehet a standardizált logaritmizált adatokra
$t$-eloszlást illeszti a normális helyett ([Budge, Ingolfsson, és Zerom
2010](#ref-budge2010)), elég gyakran fordulnak elő location-scale
családok több paraméterrel ([Zhang és mtsai. 2016](#ref-zhang2016)),
stb., ezeket általánosságban nem tudjuk meghatározni. Ennek megfelelően
a fenti táblázat is csak kétparaméteres eloszláscsaládokat tartalmaz;
szerencsére, mint látjuk, ezek is jól működnek az irodalmi adatok
alapján.

### Számítástechnikai részletek

A feladat megoldásához az R statisztikai nyelvet és számítási
környezetet fogjuk használni (érdeklődőknek merem ajánlani a készülőben
lévő
[jegyzetemet](https://tamas-ferenci.github.io/ferenci-tamas-r-nyelv/),
illetve [YouTube-csatornám](https://www.youtube.com/@FerenciTamas)
idevágó videóit, ha szeretnének megismerkedni ezzel a nyelvvel). Az
adatfeldolgozás megkönnyítéséhez a `data.table`, a vizualizációhoz a
`ggplot2` csomagokat fogjuk használni:

``` r
library(data.table)
library(ggplot2)
theme_set(theme_bw())
```

Töltsük le (a blogról), aztán pedig be az adatokat:

``` r
if(!file.exists("M_23.csv"))
  download.file(
    paste0("https://drive.google.com/uc?export=download&id=",
           "1-LaabC0DwjNMx7XSsu_Fz0jJdznbfrcN"), "M_23.csv")
RawData <- fread("M_23.csv", dec = ",")
```

Nézzünk rá, hogy minden rendben ment-e:

``` r
RawData
```

    ##             DATE   CATEGORY    VALUE METRIC      GEO
    ##           <IDat>     <char>    <num> <char>   <char>
    ##    1: 2018-11-01 Kiemelt P1 12.58333 Medián Budapest
    ##    2: 2018-12-01 Kiemelt P1 15.02500 Medián Budapest
    ##    3: 2019-01-01 Kiemelt P1 13.04167 Medián Budapest
    ##    4: 2019-02-01 Kiemelt P1 12.20000 Medián Budapest
    ##    5: 2019-03-01 Kiemelt P1 11.28333 Medián Budapest
    ##   ---                                               
    ## 1212: 2024-06-01         P5 31.38512  Átlag Országos
    ## 1213: 2024-07-01         P5 38.41653  Átlag Országos
    ## 1214: 2024-08-01         P5 37.39049  Átlag Országos
    ## 1215: 2024-09-01         P5 37.51304  Átlag Országos
    ## 1216: 2024-10-01         P5 41.32629  Átlag Országos

A későbbiekhez jól fog jönni, ha a fenti (long) formátum helyett
megvannak az adataink wide formátumban is:

``` r
RawDataWide <- dcast(RawData, DATE + CATEGORY + GEO ~ METRIC,
                     value.var = "VALUE")
```

### Az eloszlás illesztése

#### A probléma és megoldási lehetőségei

A kérdés statisztikai értelemben tehát az, hogy az átlag és a medián
ismeretében határozzuk meg az eloszlást. (Az eloszlás családját
feltettük – szép szóval élve: paraméteres statisztikában gondolkozunk –
vagyis a „határozzuk meg az eloszlást” azt jelenti, hogy határozzuk meg
az eloszlás paramétereit.) Ha ez megvan, akkor már könnyű dolgunk van,
hiszen egyszerűen az (immár ismert) eloszlás adott kvantilisét, jelen
esetben a 0,9-kvantilist kell kiszámolni.

De hogyan kapjuk meg az eloszlás paramétereit? Ha a lognormális
eloszlással kezdünk, akkor nagyon könnyűnek tűnik a probléma, hiszen a
lognormális várható értéke $e^{\mu + \frac{\sigma^2}{2}}$, mediánja
$e^{\mu}$, így a feladat lényegében az, hogy a következő
egyenletrendszert oldjuk meg $\mu$-re és $\sigma^2$-re ($\overline{x}$
az átlag, $m$ a medián):

$$
\begin{align}
e^{\mu + \frac{\sigma^2}{2}} &= \overline{x} \\
e^{\mu} &= m
\end{align}
$$

A dolog nem nehéz: a második egyenletből $\mu = \log m$ (a $\log$ alatt
mindenhol természetes alapú logaritmust értek), ezt az elsőbe
helyettesítve rögtön kapjuk, hogy
$\sigma^2 = 2 \cdot \left(\log\overline{x} - \log m\right)$.

So far so good, ahogy a művelt francia mondaná. Igen ám, csakhogy. Mi a
helyzet a Weibull-eloszlással? Az egyenletrendszer:

$$
\begin{align}
\lambda \cdot \Gamma\left(1 + 1/k\right) &= \overline{x} \\
\lambda \cdot \left(\log 2\right)^{1/k} &= m
\end{align}
$$

Nem vagyok rendes elméleti matematikus, de elég erős a gyanúm, hogy
ennek az egyenletrendszernek nincs zárt alakú megoldása. (A
gamma-függvénynek eleve nincs zárt alakú inverze.)

Még durvább a helyzet a gamma-eloszlással: ott a mediánnak már magának
sincs zárt alakú kifejezése!

Egy szó mint száz: úgy tűnik, hogy a lognormális eloszlással egyszerűen
szerencsénk volt, de a tipikus az, hogy nincs zárt alakú megoldás, azaz
numerikusan kell megoldanunk az egyenletrendszert.

#### Numerikus megoldás

A kérdés innentől inkább számítástechnikai. Próbáljunk a dologra egy
univerzális megoldást adni!

Először is, kell egy célfüggvény. Ez azt kell, hogy leírja, hogy a
numerikusan kiszámolt várható érték és medián egyezik-e a megadott
értékkel. Pontosabban szólva, numerikus optimalizálásról lévén szó, nem
egyezik, hanem közel van hozzá, magyarul a célfüggvény azt kell lemérje,
hogy mennyire vagyunk közel. Nagyképűen ez többkritériumú probléma
(hiszen a várható érték is van valamennyire a valóditól, meg a medián
is), de mivel bízhatunk abban, hogy kb. tökéletes megoldást tudunk
találni, így ennek remélhetőleg nem lesz nagy jelentősége, egyszerűen
használjuk a két eltérés négyzetének sima összegét (ezzel azt is
mondtuk, hogy az eltéréseknél a négyzetet használjuk, vélhetően szintén
azt fogjuk tapasztalni, hogy ennek nincs jelentősége). Például:

``` r
errlnorm <- function(par, mean, median) {
  (exp(par[1] + par[2]^2/2) - mean)^2 +
    (qlnorm(0.5, par[1], par[2]) - median)^2
}
```

Itt a kvantilist függvényhívással számoltattuk – noha beírhattuk volna
nagyon könnyen az elméleti értéket is ez esetben, azért döntöttem mégis
e megoldás mellett, mert ez a séma később is, minden esetben működni
fog, míg a formulát esetről-esetre cserélgetni kellene (ráadásul, mint
láttuk, azt sem biztos, hogy létezik). Sajnos az átlagnál ezt nem
tehetjük meg, egyszerűen azért, mert az R-nek nincs beépített
átlag-függvénye az eloszlásokhoz, de ha ezt kipótoljuk…

``` r
mlnorm <- function(par) exp(par[1] + par[2]^2/2)
```

…akkor innentől írhatjuk a következőt:

``` r
errlnorm <- function(par, mean, median) {
  (mlnorm(par) - mean)^2 +
    (qlnorm(0.5, par[1], par[2]) - median)^2
}
```

Ebben pedig az a nagyon jó, hogy reguláris: pontosan ugyanígy fog
kinézni minden eloszlásra! Ami azért jó, mert így nem kell minden egyes
eloszlásra egy saját függvényt írnunk, elég egyetlen egyet, aminek
paraméterként adjuk át az eloszlást! A dolgot segíti, hogy a
függvénynevek is regulárisak (`m<eloszlas>` a várható érték, ez úgyis
rajtunk múlik, `q<eloszlas>` a kvantilis). Emiatt tényleg egységesen
megoldható minden; használjuk ki a `do.call`-t, hogy a sztringként
előállított függvénynevet meghívjuk:

``` r
errfun <- function(dist, par, mean, median) {
  (do.call(paste0("m", dist), list(par)) - mean)^2 +
    (do.call(paste0("q", dist),
             as.list(c(p = 0.5, par))) - median)^2
}
```

Ez majdnem tökéletes, az egyetlen probléma, ami felvethető, hogy
bizonyos eloszlások bizonyos paraméterei nem vehetnek fel minden valós
számot értékként (például lognormálisnál a szórás csak pozitív lehet).
Ez majd az optimalizálásnál lesz gond, mert így az optimalizáló majd
lehetetlen paraméterekkel is meghívhatja ezt a függvényt. Ezt
többféleképp kezelhetjük: alkalmazhatunk olyan optimalizálót, aminek meg
lehet adni korlátokat a paraméterekre, vagy módosíthatjuk ezt a
függvényt is, hogy ilyen esetekben végtelent adjon vissza a célfüggvény
értékeként. Én nem igazán értek az optimalizáláshoz, úgyhogy inkább
érzésből, de egy harmadik megoldást választottam: ilyen esetekben a
paraméter exponenciáltját adjuk át a függvénynek, így az eredeti
nyugodtan felvehet bármilyen értéket, a függvény csak pozitív értéket
fog kapni. (Ezzel persze megváltoztatjuk a keresési tér geometriáját.)
Természetesen csak azokat a paramétereket kell exponenciálni amelyek
szigorúan pozitívak. (Más típusú megkötés nem lesz az általunk most
használt függvények körében, így ez nekünk elég.) Ehhez egyrészt
módosítjuk a fenti függvényt:

``` r
errfun <- function(dist, par, mean, median) {
  par[ poss[[dist]] ] <- exp(par[ poss[[dist]] ])
  (do.call(paste0("m", dist), list(par)) - mean)^2 +
    (do.call(paste0("q", dist), as.list(c(p = 0.5, par))) - median)^2
}
```

Másrészt beveztjük a kis adatbázist, hogy melyik eloszláshoz milyen
megkötések tartoznak, amit a módosított függvény is használt:

``` r
poss <- list(lnorm = c(FALSE, TRUE), gamma = c(TRUE, TRUE),
             weibull = c(TRUE, TRUE), invgauss = c(TRUE, TRUE))
```

Meg is vagyunk! Definiáljuk most a használt eloszlások várható értékeit
(illetve ahol külső könyvtárból hívjuk a kvantilist, ott annak is adjunk
reguláris nevet):

``` r
mlnorm <- function(par) exp(par[1] + par[2]^2/2)
mgamma <- function(par) par[1]/par[2]
mweibull <- function(par) par[2] * gamma(1 + 1/par[1])
minvgauss <- function(par) par[1]
qinvgauss <- actuar::qinvgauss
```

Helyes gyakorlat ilyen esetekben legalább egy-két példán szimulációsan
is ellenőrizni magunkat, nehogy hibát vétsünk (akár matematikait, akár a
kódolásnál). Például:

``` r
t.test(rgamma(1e6, 2.3, 1.45))
```

    ## 
    ##  One Sample t-test
    ## 
    ## data:  rgamma(1e+06, 2.3, 1.45)
    ## t = 1516, df = 1e+06, p-value < 2.2e-16
    ## alternative hypothesis: true mean is not equal to 0
    ## 95 percent confidence interval:
    ##  1.583431 1.587531
    ## sample estimates:
    ## mean of x 
    ##  1.585481

``` r
mgamma(c(2.3, 1.45))
```

    ## [1] 1.586207

``` r
t.test(rweibull(1e6, 1.85, 2.12))
```

    ## 
    ##  One Sample t-test
    ## 
    ## data:  rweibull(1e+06, 1.85, 2.12)
    ## t = 1783.7, df = 1e+06, p-value < 2.2e-16
    ## alternative hypothesis: true mean is not equal to 0
    ## 95 percent confidence interval:
    ##  1.880364 1.884501
    ## sample estimates:
    ## mean of x 
    ##  1.882432

``` r
mweibull(c(1.85, 2.12))
```

    ## [1] 1.883011

Ha ez megvan, akkor elvileg már tudunk hibát számolni. A biztonság
kedvéért érdemes lehet ilyenkor lefuttatni a függvényt egy-két értékkel,
hogy lássuk nagyjából rendben van-e, amit kidob:

``` r
errfun("lnorm", c(1, 1), 12, 11)
```

    ## [1] 9545.062

``` r
errfun("lnorm", c(2, 0.5), 12, 11)
```

    ## [1] 294.0863

``` r
errfun("lnorm", c(5, 5), 12, 11)
```

    ## [1] Inf

Most jöhet az optimalizálás! Használjuk az R beépített `optim`
függvényét (egyedül arra vigyázzunk, hogy ha nem konvergál az
optimalizálás, akkor `NA`-t adjunk vissza):

``` r
solpar <- function(dist, mean, median) {
  res <- optim(c(1, 1), errfun, dist = dist, mean = mean,
               median = median)
  res$par[ poss[[dist]] ] <- exp(res$par[ poss[[dist]] ])
  if(res$convergence != 0) return(list(NA, NA)) else
    as.list(res$par)
}
```

Jól látszik, hogy a függvényben is gondoskodunk szükség esetén az
exponenciálásról.

Itt is próbáljuk ki a működést:

``` r
solpar("lnorm", 12, 11)
```

    ## [[1]]
    ## [1] 2.397368
    ## 
    ## [[2]]
    ## [1] 0.4181431

Jól néz ki! Most már csak annyi van hátra, hogy az immár azonosított
eloszlásnak kiszámítsuk a megfelelő kvantilisét; a kényelem kedvéért
definiáljunk erre is egy függvényt:

``` r
estquantile <- function(dist, mean, median, p) {
  do.call(paste0("q", dist), c(p = p, solpar(dist, mean, median)))
}
```

#### Az eredmény kiszámítása, ellenőrzése és elmentése

Ez után nincs más dolgunk hátra, mint ezt a függvényt alkalmazni a
táblára, és ilyen módon kiszámolni a becsült értékeket. Tegyük
célszerűen automatikusan minden esetre egy hívásban; ehhez vezessünk be
egy segédfüggvényt, csak hogy kevesebbet kelljen írni:

``` r
estquantileOMSZ <- function(x, dist, p = 0.9)
  estquantile(dist, as.numeric(x["Átlag"]),
              as.numeric(x["Medián"]), p = p)
```

Ezzel már tényleg könnyű a dolog az `apply` használatával:

``` r
RawDataWide$P90gamma <- apply(RawDataWide, 1, estquantileOMSZ,
                              dist = "gamma")
RawDataWide$P90lnorm <- apply(RawDataWide, 1, estquantileOMSZ,
                              dist = "lnorm")
RawDataWide$P90weibull <- apply(RawDataWide, 1, estquantileOMSZ,
                                dist = "weibull")
RawDataWide$P90invgauss <- apply(RawDataWide, 1, estquantileOMSZ,
                                 dist = "invgauss")
```

Utolsó biztonsági ellenőrzésként a lognormálist számoljuk ki
analitikusan is (ugye ez az egyetlen, ahol ezt meg tudjuk tenni):

``` r
RawDataWide$P90 <- qlnorm(0.9, log(RawDataWide$Medián),
                          sqrt((log(RawDataWide$Átlag) -
                                  log(RawDataWide$Medián)) * 2))
```

Ellenőrzésként nézzük meg, hogy a különböző módon kiszámolt becslések
mennyire mozognak együtt:

``` r
pairs(RawDataWide[, -(1:5)], panel =  function(x, y, ...) {
  points(x, y, ...)
  abline(a = 0,b = 1, col = "red", ...)
})
```

<img src="README_files/figure-gfm/unnamed-chunk-25-1.png" width="100%" />

Kiváló! (Ami természetesen nem azt jelenti, hogy maguk a becslések jók,
hiszen ez azon a problémán nem segít, hogy az eloszlást nem ismerjük, de
azt igen, hogy a fenti számolási és kódolási munka során valószínűleg
jól dolgoztunk.)

Ebből egy dolgot külön is nézzünk meg: a kétféle módon számolt
lognormálisnak elvileg is (egzaktan) egyeznie kell. Ha kiszámoljuk,
akkor a legnagyobb különbség köztük 0.51 perc (2.5%), ami az egyéb
bizonytalansági források mellett elfogadható. (Néhány perces
intervallumnál pontosabban amúgy sincs értelme megadni az eredményeket
az alapvetően közelítés jelleg miatt, noha persze pontosan nem
tudhatjuk, hogy ez mekkora bizonytalanság.)

Úgyhogy nincs más dolgunk, mint kimenteni az eredményeket, célszerűen
R-ben használható, és további gépi feldolgozásra is alkalmas formátumban
is:

``` r
fwrite(RawDataWide, "OMSZ-kierkezesi-ido.csv",
       dec = ",", sep =";", bom = TRUE)
saveRDS(RawDataWide, "OMSZ-kierkezesi-ido.rds")
```

A gépi feldolgozáshoz jól fog jönni, ha ismét long formátumúra alakítjuk
az adatokat; egyúttal adjunk normális neveket is:

``` r
RawDataProc <- melt(RawDataWide,
                    id.vars = c("DATE", "CATEGORY", "GEO"))
RawDataProc <- merge(RawDataProc, data.table(
  variable = c("Átlag", "Medián", "P90gamma", "P90lnorm",
               "P90weibull", "P90invgauss", "P90"),
  varname = c("Átlag", "Medián", "Becsült P90 (gamma)",
              "Becsült P90 (lognormális)",
              "Becsült P90 (Weibull)",
              "Becsült P90 (inv. normális)",
              "Becsült P90 (egzakt lognormális)")
), by = "variable")
RawDataProc$varname <- factor(
  RawDataProc$varname, levels =
    c("Átlag", "Medián", "Becsült P90 (gamma)",
      "Becsült P90 (lognormális)",
      "Becsült P90 (Weibull)",
      "Becsült P90 (inv. normális)",
      "Becsült P90 (egzakt lognormális)"))
saveRDS(RawDataProc, "OMSZ-kierkezesi-ido-long.rds")
```

## Nyitott kérdések, továbbfejlesztési lehetőségek

- Adható-e valamilyen elméleti abszolút korlát arra, hogy adott átlag és
  medián mellett hol lehet a 0,9-kvantilis? (A Markov-egyenlőtlenség
  például azt mondja, ha nem tévedek, hogy biztosan kisebb, mint az
  átlag 10-szerese. Ez már valami, és válasz a kérdésre, csak nagyon
  gyenge. Lehet élesíteni? Lehet adni nem csak felső, de alsó korlátot
  is…?)
- A log-$t$ eloszlást illesztő megoldásnál kipróbálni azt, hogy minden
  lehetséges szabadsági fokkal mi az eredmények tartománya (így megoldva
  a három-paraméteres problémát). Igazából lehet, hogy elég lenne
  $\nu=1$-gyel, hiszen ez van a legtávolabb a lognormálistól, amit
  amúgyis használunk.
- Kicsit jobb optimalizáló-algoritmus használata (így is jó, de azért a
  `P90` és a `P90lnorm` között illene, hogy kb. nulla legyen a
  különbség).

## Irodalomjegyzék

<div id="refs" class="references csl-bib-body hanging-indent"
entry-spacing="0">

<div id="ref-aladdini2010" class="csl-entry">

Aladdini, Kian. 2010. „EMS response time models: A case study and
analysis for the region of Waterloo”. Mathesis, University of Waterloo.

</div>

<div id="ref-budge2010" class="csl-entry">

Budge, Susan, Armann Ingolfsson, és Dawit Zerom. 2010. „Empirical
analysis of ambulance travel times: the case of Calgary emergency
medical services”. *Management Science* 56 (4): 716–23.
<https://doi.org/10.1287/mnsc.1090.1142>.

</div>

<div id="ref-chong2016" class="csl-entry">

Chong, Kenneth. 2016. „Models for decision-making and performance
evaluation in emergency medical service systems”.

</div>

<div id="ref-ingolfsson2008" class="csl-entry">

Ingolfsson, Armann, Susan Budge, és Erhan Erkut. 2008. „Optimal
ambulance location with random delays and travel times”. *Health Care
Management Science* 11: 262–74.
<https://doi.org/10.1007/s10729-007-9048-1>.

</div>

<div id="ref-kuk2017" class="csl-entry">

Kuk, Megan, Steven Butt, Tycho Fredericks, Osama Abudayyeh, és Anil
Kumar. 2017. „Statistical investigation of modeling EMS response time:
Michigan case study”. In *IIE Annual Conference. Proceedings*, 830–35.
Institute of Industrial; Systems Engineers (IISE).

</div>

<div id="ref-nelas2020" class="csl-entry">

Nelas, José, és Joana Dias. 2020. „Optimal Emergency Vehicles Location:
An approach considering the hierarchy and substitutability of
resources”. *European Journal of Operational Research* 287 (2): 583–99.
<https://doi.org/10.1016/j.ejor.2020.03.067>.

</div>

<div id="ref-pury2021" class="csl-entry">

Pury, Pedro A. 2021. „Time to Critical Condition in Emergency Services”.
*Mathematical and Computational Applications* 26 (4).
<https://doi.org/10.3390/mca26040070>.

</div>

<div id="ref-rastpour2020" class="csl-entry">

Rastpour, Amir, Armann Ingolfsson, és Bora Kolfal. 2020. „Modeling
Yellow and Red Alert Durations for Ambulance Systems”. *Production and
Operations Management* 29 (8): 1972–91.
<https://doi.org/10.1111/poms.13190>.

</div>

<div id="ref-westgate2016" class="csl-entry">

Westgate, Bradford S., Dawn B. Woodard, David S. Matteson, és Shane G.
Henderson. 2016. „Large-network travel time distribution estimation for
ambulances”. *European Journal of Operational Research* 252 (1): 322–33.
<https://doi.org/10.1016/j.ejor.2016.01.004>.

</div>

<div id="ref-zhang2016" class="csl-entry">

Zhang, Zhenhua, Qing He, Jizhan Gou, és Xiaoling Li. 2016. „Performance
measure for reliable travel time of emergency vehicles”. *Transportation
Research Part C: Emerging Technologies* 65: 97–110.
<https://doi.org/10.1016/j.trc.2016.01.015>.

</div>

</div>
