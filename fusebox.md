# 🔋 Qilowatt & Sagedusturg (mFRR) – Kasutusjuhend

## Sissejuhatus

See dokument koondab Qilowatti kogukonna teadmised ja parimad praktikad sagedusturul (mFRR) osalemiseks läbi Fuseboxi platvormi. Sagedusturg on dünaamiline, selle reeglid on keerulised ja sageli puudulikult dokumenteeritud.

> **NB!** Alljärgnev info põhineb kasutajate kogemustel ning sellel puudub ametlik kinnitus. Suhtu sagedusturule kui „kasiinosse" – pikas plaanis on võimalik teenida head tulu, kuid üksikud käsud võivad olla kahjumlikud ning käskude saamine on ettearvamatu. Qilowatt on siin vahendaja ja sõltub suuresti oma partnerist Fuseboxist.

---

## 1. Sagedusturuga seotud info

> **NB!** Alljärgnev info põhineb kasutajate kogemustel ning sellel puudub ametlik kinnitus.

### 1.1 Käsud ja nende tähendus

Sagedusturul (mFRR) on kahte tüüpi reguleerimist. Qilowatti süsteemis vastavad neile järgmised käsud:

-   **DOWN (alla reguleerimine):** Võrgus on elektrit üle, sagedus on liiga kõrge. Sinu ülesanne on energiat **tarbida**.
    -   Qilowatti käsk: **`BUY`**
    -   Arvesse läheb energia, mis liigub **akusse sisse**. Energia päritolu (võrk või oma PV-toodang) ei ole oluline.

-   **UP (üles reguleerimine):** Võrgus on elektrist puudus, sagedus on liiga madal. Sinu ülesanne on energiat **toota**.
    -   Qilowatti käsk: **`SELL`** või **`frrup`**
    -   Arvesse läheb energia, mis liigub **akust välja**. Energia sihtkoht (enda tarbimine või võrku müük) ei ole oluline. Kõrge Nord Pool Spot (NPS) hinnaga on **võrku müük** sageli kasulikum, kuna teenid lisaks sagedusturu tulule ka NPS müügitulu.

#### Erikäsud
-   **`frrup`:** Spetsiaalne `SELL` käsk **ainult Fuseboxilt**. Selle käsu ajal peab sinu süsteem **piirama päikesepaneelide (PV) tootmist**, et tagada maksimaalne energiavoog akust. Qilowatti uuema tarkvaraga seadmed teevad seda automaatselt. Erinevalt tavalisest `SELL` käsust, mis võib tulla ka timerist.
-   **`0W` käsk / `CANCEL`:** Varem tekitasid `0W` käsud kahjumit. Nüüd on Qilowatt need oma süsteemis defineerinud kui **tühistamise (`CANCEL`) käsu**. See tähendab, et aktiivne mFRR käsk lõppeb ja su süsteem naaseb eelmisesse olekusse (nt `Optimizer` või `Timer`). **Fuseboxi vaatenurgast** on `0W` käsud kasulikud, kuna klient peab energia oma raha eest ostma, aga Fusebox ei pea midagi maksma.

---

### 1.2 Tasustamise loogika: Baseline'i reegel

See on kõige olulisem ja keerulisem osa. Sinu teenistus ei põhine aku absoluutsel võimsusel, vaid **muutusel (deltal)** võrreldes sinu varasema tegevusega.

-   **Baseline** on sinu aku keskmine võimsus **15 minuti jooksul ENNE esimese mFRR käsu algust**.
-   **Baseline lukustub** esimese käsu hetkega ja jääb kehtima kõikidele järjestikustele käskudele.
-   **Uus baseline** arvutatakse alles siis, kui käskude vahel on olnud vähemalt **üks täis 15-minutiline periood ilma mFRR käsuta**.
-   Tasu saadakse ainult **muutuse** eest võrreldes `baseline`'iga.

> **HOIATUS:** Kui müüd kõrge NPS hinnaga ja sellele tuleb peale pikk `UP` käsk, siis sinu `baseline` on negatiivne ja sa ei teeni sagedusturult tulu. Kuna sa ei saa käsust keelduda, oled sunnitud edasi müüma ka siis, kui NPS hind on juba langenud. See on hetkel suurim risk ja võib tekitada kahjumit.

---

### 1.3 Näited

#### BUY käsk (Osta akusse)

| Enne käsku (Baseline) | Käsk      | Reageerimine         | Reaalne arvestatav maht | Kasumlikkus                                  |
| --------------------- | --------- | -------------------- | ----------------------- | -------------------------------------------- |
| Laadimine +5 kW       | BUY 10 kW | Laed 10 kW           | 5 kW                    | Tasu saad 5 kW eest.                         |
| Laadimine +10 kW      | BUY 10 kW | Laed 10 kW           | 0 kW                    | Tasu ei saa.                                 |
| **Müük -8 kW**        | BUY 10 kW | Hakkad laadima 10 kW | **18 kW**               | **Suurim kasum!** Saad tasu 18 kW mahu eest. |

#### SELL käsk (Müü akust)

| Enne käsku (Baseline) | Käsk       | Reageerimine     | Reaalne arvestatav maht | Kasumlikkus                                  |
| --------------------- | ---------- | ---------------- | ----------------------- | -------------------------------------------- |
| Müük -5 kW            | SELL 10 kW | Müüd 10 kW       | 5 kW                    | Tasu saad 5 kW eest.                         |
| Müük -10 kW           | SELL 10 kW | Müüd 10 kW       | 0 kW                    | Tasu ei saa. **See on ohtlik stsenaarium!**  |
| **Laadimine +8 kW**   | SELL 10 kW | Hakkad müüma 10 kW | **18 kW**               | **Suurim kasum!** Saad tasu 18 kW mahu eest. |

#### Ristreaktsioonid

| Enne käsku                     | Käsk  | Reageerimine | Tasu |
|--------------------------------|-------|---------------|------|
| Müük -10 kW → Ostad +10 kW    | BUY   | +20 kW        | Jah  |
| Laadimine +10 kW → Müük -10 kW| SELL  | +20 kW        | Jah  |

---

## 2. Strateegiad ja Parimad Praktikad

-   **`DOWN` käsu ajal piira tarbimist:** Kogu sinu kodune tarbimine tuleb sel ajal osta võrgust kalli NPS hinnaga. Lülita suured tarbijad välja.
-   **`DOWN` käsk päikesega on jackpot:** `DOWN` käsk on eriti kasulik, kui päike paistab ja enne seda oled PV võrku müünud. Saad tasu selle eest, et laed omaenda päikeseenergiat akusse.
-   **Aku SOC piirangud:** Määra Qilowatti veebis Fuseboxi seadetes oma akule mõistlikud miinimum- ja maksimumpiirid (nt min 15%, max 98%). See aitab vältida kahjumlikke käske tühja/täis aku korral. **NB!** Need seaded ei jõustu automaatselt! Saada e-mail aadressile `support@qilowatt.it` ja palu neil sinu seadistatud piirangud Fuseboxi süsteemi kanda.
-   **Käskude prioriteetide järjekord:** Qilowatt süsteemis kehtib järgmine prioriteetide järjekord: **Fusebox > Manual > Timer/Optimizer > No Timer**. See tähendab, et mFRR käsud alati üle kirjutavad kõik muud seadistused.

---

## 3. Tuluarvestus ja Väljamaksed

### 3.1 Tulu arvutamine
-   **`UP` tulu** = `(mFRR hind - NPS hind) * maht * 0.8` (Fuseboxi/QW vahendustasu 20%)
-   **`DOWN` "tulu" (kulu vähendus)** = `(NPS hind - mFRR hind) * maht * 0.8`
-   **Maksud ja tasud:** Eraisikuna maksad `DOWN` käsu ajal ostetud elektri NPS hinna osalt käibemaksu, mida ei kompenseerita. Samuti lisanduvad alati võrgutasud.

-   Qilowatti veebis olev **Fuseboxi kast** kuvab **ainult `UP` käskudest teenitud tulu**.
-   **`DOWN` käskude tulu ei ole QW süsteemis näha.** See arvutatakse Fuseboxi poolt käsitsi ja lisatakse perioodi lõpus kogusummale.
-   Andmed uuenevad viitega, tavaliselt järgmiseks päevaks.

### 3.3 Väljamaksed
-   Alates aprillist 2025 toimub arveldamine **kuupõhiselt**.
-   Protsess on aeglane, kuna sõltub Fuseboxi manuaalsest andmetöötlusest. Väljamaksed toimuvad tavaliselt järgneva kuu lõpupoole.

### 3.4 Tulu kalkulaator

QW entusiastide poolt on loodud kalkulaator, mis aitab **Qilowatti raporti põhjal tulu hinnata**.

**Kalkulaatori eeldused:**

- Enne käsku on baseline = 0
- Käsku täidetakse 100% täpsusega
- Kõigil CSV ridadel on hinnad olemas
- Erisusi (nagu tegelik baseline või osaline täitmine) ei arvestata


👉 Proovi kalkulaatorit:  
**[https://tihend.energy/profit](https://tihend.energy/profit)**
