# Napoved rasti obnovljivih virov energije v Evropi


Filip Mally, Žiga Novak, Matevž Skvarč, Mark Šincek, Simon Korošec

# 1. Opis problema
Glavni cilj raziskave je analizirati rast deleža obnovljivih virov energije v državah EU v obdobju zadnjega desetletja ter oceniti, kako uspešne so države pri uresničevanju cilja 42.5% do leta 2030.

Problem, ki ga preučujemo, je neenakomerna hitrost energetske rasti med državami, kar otežuje enotno načrtovanje evropske podnebne politike.

Z uporabo podatkovne analize in napovednih modelov želimo identificirati ključne razlike med državami ter oceniti prihodnje trende. Dodatno preučujemo, kateri dejavniki (gospodarska razvitost, naravni viri, izvoz fosilnih goriv) so povezani z uspešnostjo prehoda.


# 2. podatki
Uporabljamo uradne podatke statističnega urada Eurostat.

Glavni uporabljeni podatkovni viri so:
 - `estat_nrg_ind_ren`: delež OVE v končni porabi energije
 - `estat_nrg_bal_s`: energetske bilance držav
 - `estat_nrg_cb_rw`: podatki o posameznih vrstah obnovljivih virov
 - `sdg_08_10_tabular`: bruto domači proizvod na prebivalca (€ na prebivalca)

 Analiza se osredotočna na obdobje od leta 2014 do najnovejših razpoložljivih podatkov, kar omogoča vpogled v trende zadnjega desetletja.

 Podatki so bili v surovi obliki nepregledni in kompleksni, zato smo zgradili pomožno knjižnico `data_utils.py`, ki omogoča:
 - filtriranje relevantnih kazalnikov,
 - pretvorbo podatkov v numerične vrednosti,
 - preslikavo kratic držav v razumljiva imena,
 - čiščenje manjkajočih ali neveljavnih vrednosti

 Pri podatkih o GDP smo dodatno zapise filtrirali tako, da smo upoštevali le vrednosti v stalnih cenah CLV in na prebivalca (EUR_HAB). Odstranili smo prenizke vrednosti(manjše od 1000€), saj te najverjetneje predstavljajo manjkajoče ali nepravilno zabeležene podatke.

 Za potrebe analize smo podatke transformirali v pivotno obliko, kjer vrstice predstavljajo države, stolpci leta, vrednosti pa delež OVE. Manjkajoče vrednosti smo zapolnili z linearno interpolacijo.

# 3. Izvedene analize in rezultati

 ## 3.1 Razvoj deleža OVE skozi čas
Za pregled razvoja smo uporabili toplotni zemljevid (Slika 1), ki omogoča hitro vizualno primerjavo med državami. Razlike so izrazite, sploh med severom in jugom oz. zahodom Evrope. Skandinavske države so skozi celotno desetletje konsistentno v zelenem območju (Islandija 79,3 %, Norveška 77,9 %, Švedska 62,8 % v letu 2024), medtem ko Malta, Luksemburg in Belgija ostajajo okoli 14 %. Slovenija v celotnem obdobju stagnira pri približno 25 %.

 ![razvojdeleza](./image.png)
 
 ## 3.2 Napoved za leto 2030
Za vsako državo posebej smo zgradili model linearne regresije na podatkih 2014–2024 in napovedali delež OVE v letu 2030 (Slika 2). Po tem modelu cilj 42,5 % doseže le 10 od 35 držav: Islandija, Norveška, Švedska, Finska, Bosna in Hercegovina, Albanija, Danska, Latvija, Avstrija in Estonija. Velika večina držav, vključno s Slovenijo (napoved ≈ 27,6 %), Nemčijo, Francijo in Hrvaško, cilja po trenutnih trendih ne bo dosegla.

![napoved](./image-1.png)

Linearni model predpostavlja konstantno rast in lahko podcenjuje države s pospeševanjem prehoda. Zato smo zgradili tudi polinomski model 2. stopnje z Ridge regularizacijo (α = 1). Ta je bolj optimističen, saj cilj dosega 12 držav, dodatno še Litva, Črna gora in Portugalska. Razlika med modeloma poudarja negotovost napovedi: pri državah s spreminjajočo se dinamiko rasti izbira modela bistveno vpliva na rezultat.

 ## 3.3 EU kot celota
Linearno regresijo smo izvedli tudi na povprečnem deležu OVE vseh obravnavanih držav. Napoved za leto 2030 znaša 36,1 %, kar je pod ciljem 42,5 %. To nakazuje, da bo brez pospešitve obstoječih trendov skupno povprečje zaostalo za cilji, čeprav posamezne vodilne države cilj že presegajo.

 ## 3.4 Hitrost rasti (CAGR) in razvrščanje držav
Za primerjavo dinamike, neodvisne od začetnega stanja, smo izračunali sestavljeno letno stopnjo rasti (CAGR). Najvišjo rast dosegajo države z nizkim začetnim deležem: Nizozemska (14,1 %), Malta (13,8 %) in Luksemburg (12,7 %). Vodilne skandinavske države imajo nizek CAGR (≈ 1–2 %), saj se približujejo zgornji meji nasičenja. Nekatere države (Moldavija, Kosovo, Črna gora) beležijo celo negativno rast.
To dinamiko smo formalizirali z razvrščanjem K-means (3 skupine) glede na delež OVE in CAGR:

Vodilne države (povp. OVE 68 %, CAGR 1,8 %): Islandija, Norveška, Švedska, Finska – visok delež, počasna rast (nasičenost).

Države v prehodu (povp. OVE 17 %, CAGR 10,3 %): Ciper, Nizozemska, Malta, Irska, Luksemburg, Belgija – nizek delež, a najhitrejši napredek.

Zaostajajoče države (povp. OVE 29 %, CAGR 2,5 %): največja skupina, vključno s Slovenijo – zmeren delež in počasna rast, kar je najbolj zaskrbljujoče za doseganje ciljev.

## 3.5 Povezava med BDP in deležem OVE
Preverili smo, ali so bogatejše države uspešnejše pri prehodu. Pearsonov korelacijski koeficient med BDP na prebivalca in deležem OVE znaša 0,21 (Slika 3), kar pomeni le šibko povezavo, torej gospodarska razvitost torej ni glavni dejavnik.

![povezavabdpinove](./image-2.png)

Analiza odstopanj (ostankov regresije) je razkrila, da imajo države z največjim pozitivnim odklonom (Islandija, Norveška, Švedska) visok delež OVE kljub temu, da niso najbogatejše, njihovo prednost pojasnjujejo naravni viri (hidro- in geotermalna energija). Nasprotno Luksemburg (101 000 € BDP, le 14,7 % OVE), Irska in Belgija močno zaostajajo kljub visoki gospodarski moči. To potrjuje, da je za prehod pomembnejša geografija in politika kot zgolj bogastvo.

 ## 3.6 Izvoz fosilnih goriv (dodatna hipoteza)
Preverili smo hipotezo, da lahko prihodki od izvoza fosilnih goriv financirajo zeleni prehod. Korelacija med (logaritmiranim) izvozom fosilnih goriv in CAGR obnovljivih virov znaša 0,52, kar nakazuje zmerno pozitivno povezavo. Države z večjimi prihodki od izvoza fosilnih goriv torej pogosteje hitreje povečujejo delež OVE, čeprav povezava ni prepričljiva.

 ## 3.7 Analiza odstopanj
Na podlagi regresijskega modela smo našli države, ki odstopajo od pričakovanih vrednosti glede na njihov GDP.
Našli smo države katere imajo visok delež OVE glede na BDP in države ki imajo nizek delež OVE glede na BDP

 # 4. Zaključek
 Analiza kaže, da je Evropa pri prehodu na OVE močno razdeljena. Po trenutnih trendih bo cilj 42,5 % do leta 2030 dosegla manjšina držav, skupno povprečje pa naj bi znašalo le okoli 36 %. Najbolj uspešne države se opirajo na bogate naravne vire, gospodarska razvitost pa se izkaže za šibek napovednik. Najbolj zanimiva ugotovitev je razvrstitev v tri skupine: vodilne države so dosegle nasičenost, skupina »v prehodu« hitro dohiteva z izjemnimi stopnjami rasti, največja skupina pa stagnira pri zmernih deležih in predstavlja glavno tveganje za skupne cilje EU. Slovenija sodi v to zadnjo skupino in bi za doseganje cilja potrebovala bistveno pospešitev.

 # 5. Streamlit aplikacija
Interaktivni vpogled smo implementirali v aplikaciji app.py (Streamlit), ki uporabniku omogoča: (1) primerjavo izbranih držav skozi čas, (2) pregled držav z največjim napredkom, (3) interaktivno uporabo napovednega modela za 2030 z izbiro med linearno in polinomsko regresijo ter (4) raziskovanje korelacije med BDP in OVE z izstopajočimi državami. Zagon: streamlit run app.py (potrebne knjižnice v requirements.txt).

 # Priloge

Celotna analiza in koda: [analysis.ipynb](./analysis.ipynb) – vsebuje tudi dodatne vizualizacije, ki niso ključne za glavni del: graf CAGR po državah, napoved za EU kot celoto, razsevni diagram razvrščanja K-means ter analizo izvoza fosilnih goriv.

Pomožna knjižnica: [data_utils.py](./data_utils.py) – nalaganje in čiščenje podatkov Eurostat.

Streamlit aplikacija: [app.py](./app.py)

Seznam odvisnosti: [requirements.txt](./requirements.txt)