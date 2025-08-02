# 🔋 Qilowatt & Sagedusturg – Kasutusjuhend

## 1. Sagedusturuga seotud info

> **NB!** Alljärgnev info põhineb kasutajate kogemustel ning sellel puudub ametlik kinnitus.

### 1.1 Käsud

Qilowatt sagedusturul kasutatakse kahte tüüpi käske:

- **BUY** – Arvesse läheb energia, mis läheb **akusse**. Energia päritolu ei ole oluline.
- **SELL** – Arvesse läheb energia, mis tuleb **akust**. Energia sihtkoht (enda tarbimine või võrku müük) ei ole oluline.  
  Kõrge Nord Pool Spot (NPS) hinnaga on **võrku müük** sageli kasulikum.

---

### 1.2 Tasustamise loogika

Käsu täitmisel arvestatakse **aku käitumise muutust** käsu andmise hetkel võrreldes eelneva olukorraga.

- **Baseline** on viimase 15 minuti keskmine võimsus.
- **Baseline lukustub käsu saamise hetkeks** ja jääb lukku kuni 15 minutit pärast viimast käsku.
- Tasu saadakse ainult **muutuse** eest võrreldes baseline'iga.

---

### 1.3 Näited

#### BUY käsk

| Enne käsku        | Käsk     | Reageerimine | Tasu |
|-------------------|----------|---------------|------|
| Laadimine 5 kW    | BUY 10 kW| +5 kW         | Jah  |
| Laadimine 10 kW   | BUY 10 kW| 0 kW          | Ei   |

#### SELL käsk

| Enne käsku                        | Käsk  | Reageerimine   | Tasu |
|----------------------------------|-------|----------------|------|
| PV toodab võrku 10 kW (aku täis) | SELL  | Akust 10 kW     | Jah  |
| Akust 10 kW müük juba käib       | SELL  | 0 kW            | Ei   |

#### Ristreaktsioonid

| Enne käsku                     | Käsk  | Reageerimine | Tasu |
|--------------------------------|-------|---------------|------|
| Müük -10 kW → Ostad +10 kW    | BUY   | +20 kW        | Jah  |
| Laadimine +10 kW → Müük -10 kW| SELL  | +20 kW        | Jah  |

---

## 2. Tuluarvestus Qilowatti veebis

### 2.1 Fuseboxi kasti tulu

Qilowatti veebis olev *"kast"* kuvab **ainult SELL käskudest teenitud tulu**.  
Andmed uuenevad **korra ööpäevas**, tavaliselt öösel.

---

### 2.2 Tulu kalkulaator

QW entusiastide poolt on loodud kalkulaator, mis aitab **Qilowatti raporti põhjal tulu hinnata**.

**Kalkulaatori eeldused:**

- Enne käsku on baseline = 0
- Käsku täidetakse 100% täpsusega
- Erisusi (nagu tegelik baseline või osaline täitmine) ei arvestata

👉 Proovi kalkulaatorit:  
**[https://tihend.energy/profit](https://tihend.energy/profit)**
