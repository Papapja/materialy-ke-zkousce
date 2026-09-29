# C++ – stručné poznámky ke zkoušce

Tento soubor obsahuje jen nejdůležitější principy a krátké vzory zápisu. Nejsou zde celé hotové třídy ani kompletní řešení jednoho zadání. Názvy tříd, atributů a metod je vždy nutné upravit podle konkrétního zadání.

## Rychlé odkazy

- [Jak číst zadání](#jak-cist-zadani)
- [Co patří do `.h` a `.cpp`](#h-a-cpp)
- [Dědičnost, `virtual` a `override`](#dedicnost)
- [Co můžu napsat do `analyzuj()`](#analyzuj)
- [Polymorfismus a paměť](#polymorfismus)
- [Všechny operátory](#operatory)
- [Algoritmy](#algoritmy)
- [Kódové ukázky se zdrojem](#zdrojove-ukazky)
- [Kontrola programu](#kontrola)

## Odkazy na jednotlivé operátory

- [`operator==`](#operator-rovna-se)
- [`operator+`](#operator-plus)
- [`operator<<`](#operator-vystup)
- [`operator<` a `operator>`](#operator-porovnani)
- [`operator[]`](#operator-index)
- [`operator*`](#operator-nasobeni)
- [`operator+=`](#operator-plus-rovna-se)
- [`operator*=`](#operator-krat-rovna-se)
- [Prefixový `operator++`](#operator-prefix-plus-plus)
- [Postfixový `operator++`](#operator-postfix-plus-plus)
- [`operator--`](#operator-minus-minus)
- [`operator()`](#operator-zavorky)

---

<a id="jak-cist-zadani"></a>
## Jak číst zadání

Nejdřív si v textu označím podstatná slova:

- **atribut** – proměnná uvnitř třídy,
- **statický** – jedna společná hodnota pro všechny objekty,
- **abstraktní třída** – obsahuje alespoň jednu čistě virtuální metodu,
- **odvozená třída** – dědí z rodiče,
- **překryjte metodu** – použiji `override`,
- **bez změny objektu** – metoda má na konci `const`,
- **vraťte referenci** – v návratovém typu musí být `&`,
- **dynamicky vytvořte** – použiji `new` a později `delete`,
- **pro všechny prvky** – potřebuji cyklus,
- **vyfiltrujte** – vytvořím nový výsledek a vložím jen vyhovující prvky,
- **odstraňte** – měním původní kolekci,
- **seřaďte sestupně** – větší hodnoty musí být před menšími.

Nejdřív si sepíšu názvy tříd, jejich atributy a požadované metody. Teprve potom začnu psát implementaci.

<a id="h-a-cpp"></a>
## Co patří do `.h` a `.cpp`

V `.h` je přehled toho, co třída obsahuje a umí:

- název třídy a případný rodič,
- atributy,
- deklarace konstruktoru a destruktoru,
- deklarace metod,
- deklarace operátorů.

V `.cpp` je skutečné chování:

- připojení vlastní hlavičky,
- definice statické proměnné,
- těla konstruktorů a metod,
- těla operátorů.

Krátký vzor deklarace metody:

```cpp
double vypocitej() const;
```

Odpovídající začátek implementace:

```cpp
double Trida::vypocitej() const
```

Návratový typ, parametry a `const` se musí v `.h` a `.cpp` shodovat.

Konstruktor potomka předává společné údaje rodiči v inicializačním seznamu:

```cpp
Potomek::Potomek(/* parametry */) : Rodic(/* společné údaje */)
```

### Tři důležité návraty vektoru

```cpp
vector<double> getData();
```

Vrátí kopii. Změna výsledku nezmění původní vektor.

```cpp
vector<double>& getData();
```

Vrátí původní vektor a dovolí jeho změnu.

```cpp
const vector<double>& getData() const;
```

Vrátí původní vektor bez kopírování, ale pouze ke čtení.

<a id="dedicnost"></a>
## Dědičnost, `virtual` a `override`

Zápis dědičnosti:

```cpp
class Potomek : public Rodic
```

`public` znamená veřejnou dědičnost. Potomek získá dostupné vlastnosti rodiče a přidá svoje.

Čistě virtuální metoda končí `= 0`:

```cpp
virtual void proved() = 0;
```

Kvůli ní je rodič abstraktní. Každý konkrétní potomek musí dodat vlastní verzi:

```cpp
void proved() override;
```

Pokud metoda objekt pouze čte, hlídám `const`:

```cpp
virtual double hodnota() const = 0;
double hodnota() const override;
```

Rodič určený pro polymorfní použití má mít virtuální destruktor:

```cpp
virtual ~Rodic();
```

### Statický čítač

Statická proměnná patří celé třídě, ne každému objektu zvlášť. Obvyklý princip:

- na začátku je `0`,
- konstruktor ji zvýší,
- destruktor ji sníží,
- statický getter ji vrátí.

Definice společné proměnné patří do `.cpp`:

```cpp
int Rodic::pocet = 0;
```

Volání statické metody používá název třídy:

```cpp
Rodic::getPocet()
```

<a id="analyzuj"></a>
## Co můžu napsat do `analyzuj()`

Nejdřív zjistím, jaký má být výsledek. Podle toho připravím pomocné proměnné.

### Počet hodnot splňujících podmínku

Potřebuji počítadlo začínající nulou. V cyklu ho zvýším, pouze když podmínka platí:

```cpp
int pocet = 0;
for (double x : data)
    if (/* podmínka */) pocet++;
```

Příklady podmínek:

```cpp
x > 0                 // kladná hodnota
x < 0                 // záporná hodnota
x > minimum && x < maximum
```

### Součet a průměr

Pro průměr potřebuji součet i počet vybraných hodnot. Před dělením kontroluji, že počet není nula.

```cpp
if (pocet > 0)
    prumer = soucet / pocet;
```

### Minimum nebo maximum

Výchozí hodnotu vezmu z prvního prvku, ne náhodně zvolenou nulu:

```cpp
double maximum = data[0];
```

Tento zápis smím použít až po ověření, že vektor není prázdný.

### Nejdelší souvislá řada

Použiji dvě počítadla:

- `aktualni` – délka právě běžící řady,
- `nejdelsi` – nejlepší dosavadní výsledek.

Když podmínka přestane platit, vynuluji jen `aktualni`. Hodnotu `nejdelsi` zachovám.

### Rozdíl mezi `&&` a `||`

```cpp
x > minimum && x < maximum
```

Hodnota musí splnit obě podmínky – je uvnitř intervalu.

```cpp
x < minimum || x > maximum
```

Stačí jedna podmínka – hodnota je mimo interval.

<a id="polymorfismus"></a>
## Polymorfismus a paměť

Do jednoho vektoru ukazatelů na rodiče lze vložit různé potomky:

```cpp
vector<Rodic*> objekty;
```

Při průchodu volám společnou virtuální metodu. Podle skutečného typu objektu se vybere správná verze.

```cpp
for (Rodic* objekt : objekty)
    objekt->vypisInfo();
```

Tečka je pro obyčejný objekt, šipka pro ukazatel:

```cpp
objekt.metoda();
ukazatel->metoda();
```

Co vzniklo pomocí `new`, musí být uvolněno pomocí `delete`:

```cpp
for (Rodic* objekt : objekty)
    delete objekt;
```

Po smazání objektů lze vektor vyprázdnit. Správné pořadí je nejdřív `delete`, potom `clear()`.

---

<a id="operatory"></a>
## Všechny operátory

U operátoru kontroluji:

1. co přijímá,
2. zda mění aktuální objekt,
3. co musí vrátit,
4. zda má být na konci `const`,
5. zda jde o členskou, nebo `friend` funkci.

<a id="operator-rovna-se"></a>
### `operator==`

Porovnává dva objekty a vrací `bool`. Objekt nemění, proto bývá metoda `const`.

```cpp
bool operator==(const Trida& druhy) const;
```

Uvnitř porovnám atribut nebo vypočítanou hodnotu určenou zadáním.

<a id="operator-plus"></a>
### `operator+`

Obvykle vytvoří a vrátí nový objekt. Původní objekty nemění.

```cpp
Trida operator+(const Trida& druhy) const;
```

Pozor: `+` většinou není totéž jako `+=`. Operátor `+` vytváří výsledek, zatímco `+=` mění levý objekt.

<a id="operator-vystup"></a>
### `operator<<`

Umožní zápis `cout << objekt`. Často jde o `friend` funkci:

```cpp
friend ostream& operator<<(ostream& os, const Trida& objekt);
```

Na konci vrací stejný proud:

```cpp
return os;
```

Díky tomu funguje řetězení `cout << objekt << endl`.

<a id="operator-porovnani"></a>
### `operator<` a `operator>`

Vrací `bool` a porovnávají hodnotu určenou zadáním:

```cpp
bool operator<(const Trida& druhy) const;
bool operator>(const Trida& druhy) const;
```

Mohou se používat při řazení objektů.

<a id="operator-index"></a>
### `operator[]`

Umožní přístup zápisem `objekt[index]`.

```cpp
double& operator[](int index);
```

Reference dovoluje hodnotu nejen přečíst, ale také změnit. Návratový typ se řídí tím, co kolekce obsahuje.

<a id="operator-nasobeni"></a>
### `operator*`

Význam není pevně daný. Může násobit hodnoty, spojovat význam objektů nebo například opakovat text. Rozhoduje zadání.

```cpp
Trida operator*(double hodnota) const;
```

Pokud má být vlevo jiný typ než objekt, může být potřeba samostatná `friend` funkce.

<a id="operator-plus-rovna-se"></a>
### `operator+=`

Mění aktuální objekt a obvykle vrací `*this` jako referenci:

```cpp
Trida& operator+=(double hodnota);
```

Typický konec implementace:

```cpp
return *this;
```

<a id="operator-krat-rovna-se"></a>
### `operator*=`

Má stejný princip jako `+=`, ale obvykle mění objekt pomocí koeficientu.

```cpp
Trida& operator*=(double koeficient);
```

Po změně atributů může být nutné přepočítat výsledek nebo uložit novou hodnotu do historie.

<a id="operator-prefix-plus-plus"></a>
### Prefixový `operator++`

Zápis `++objekt` nejdřív objekt změní a potom vrátí změněný objekt.

```cpp
Trida& operator++();
```

Vrací referenci na `*this`.

<a id="operator-postfix-plus-plus"></a>
### Postfixový `operator++`

Zápis `objekt++` vrací původní stav. Parametr `int` pouze rozlišuje postfixovou verzi.

```cpp
Trida operator++(int);
```

Princip: uložit kopii původního objektu, změnit aktuální objekt a vrátit původní kopii.

<a id="operator-minus-minus"></a>
### `operator--`

Má stejný rozdíl mezi prefixovou a postfixovou verzí jako `++`:

```cpp
Trida& operator--();
Trida operator--(int);
```

Je potřeba hlídat případné omezení, například aby hodnota neklesla pod povolené minimum.

<a id="operator-zavorky"></a>
### `operator()`

Umožní použít objekt jako funkci:

```cpp
objekt(hodnota)
```

Deklarace závisí na požadovaném výsledku:

```cpp
bool operator()(int hodnota) const;
```

Může například testovat podmínku, vracet počet výskytů nebo přidávat hodnotu. Přesný význam vždy určuje zadání.

---

<a id="algoritmy"></a>
## Algoritmy

### Porovnávání sousedních hodnot

Začínám na indexu `1`, protože používám také prvek `i - 1`:

```cpp
for (size_t i = 1; i < data.size(); i++)
```

Uvnitř mohu počítat zvýšení, rozdíl nebo poměr. Před dělením kontroluji, že předchozí hodnota není nula.

### Lineární hledání

Procházím prvky od začátku. Při první shodě vrátím index. Pokud nic nenajdu, vrátím hodnotu určenou zadáním, často `-1`.

### Filtrování do nového vektoru

Původní data neměním. Vytvořím prázdný výsledek a přidám jen vyhovující prvky:

```cpp
if (/* podmínka */)
    vysledek.push_back(x);
```

### Mazání z vektoru

Při mazání přes iterátor použiji výsledek `erase`. Starý iterátor už po smazání nemusí být platný.

```cpp
if (/* smazat */) it = data.erase(it);
else ++it;
```

### Řazení

`sort` standardně řadí vzestupně. Pro sestupné řazení musí porovnání vracet `true`, když má být první prvek před druhým kvůli větší hodnotě.

U ukazatelů používám uvnitř porovnání šipku:

```cpp
a->hodnota() > b->hodnota()
```

### Práce s prázdným vektorem

Před použitím `data[0]`, výpočtem průměru nebo dělením kontroluji:

```cpp
if (data.empty())
```

Tím zabráním přístupu k neexistujícímu prvku a dělení nulou.

<a id="kontrola"></a>
## Kontrola programu

Před odevzdáním kontroluji:

- mají deklarace a implementace stejné parametry a stejné `const`,
- má každá definice metody v `.cpp` správné `Trida::`,
- je za definicí třídy středník,
- má abstraktní rodič virtuální destruktor,
- mají přepsané metody `override`,
- vracejí změnové operátory `*this`,
- vrací `operator<<` výstupní proud,
- nepoužívám `data[0]` u prázdného vektoru,
- nedělím nulou,
- mažu každý objekt vytvořený pomocí `new`,
- volám algoritmy ještě před `delete`,
- používám tečku pro objekt a šipku pro ukazatel,
- odpovídají podmínky přesně slovům „větší“, „menší“, „alespoň“ a „v intervalu“.

Nejbezpečnější pořadí práce je: nejdřív základní třída, potom potomci, jednoduchý test, polymorfní vektor, operátory, algoritmy a nakonec kontrola paměti.

---

<a id="zdrojove-ukazky"></a>
## Kódové ukázky doložené v přiloženém souboru

Následující ukázky jsou převzaté pouze z částí přílohy, u kterých je uveden konkrétní řádek **Zdroj:** nebo jsou výslovně označené jako pokračování stejného souboru. U každé ukázky je doplněný stručný popis jejího účelu. Obecné nedoložené šablony zde nejsou.

### 1. `.h` a `.cpp`

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
Vybaveni(const std::string& kod, double hmotnost);
virtual ~Vybaveni();
static int getPocetKusu();
virtual void pripravKAkci() = 0;
```

**Popis:** Deklaruje konstruktor, virtuální destruktor, statický getter a čistě virtuální metodu. Kvůli `= 0` je `Vybaveni` abstraktní a každý potomek musí vytvořit vlastní `pripravKAkci()`.

**Zdroj:** `Vybaveni.cpp`

```cpp
Vybaveni::Vybaveni(const std::string& kod, double hmotnost)
    : kodOznaceni(kod), hmotnost(hmotnost) {
    pocetKusu++;
}
```

**Popis:** Konstruktor uloží přijatý kód a hmotnost do atributů a zvýší společný počet existujících kusů.

**Zdroj:** `Vybaveni.cpp`

```cpp
Vybaveni::~Vybaveni() {
    pocetKusu--;
}
```

**Popis:** Při zániku objektu sníží statický čítač o jedna.

### 2. Obecná kostra abstraktní třídy

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
class Vybaveni {
private:
    std::string kodOznaceni;
    double hmotnost;
    static int pocetKusu;

public:
    Vybaveni(const std::string& kod, double hmotnost);
    virtual ~Vybaveni();

    static int getPocetKusu();
    virtual void pripravKAkci() = 0;

    std::string getKodOznaceni() const;
    double getHmotnost() const;

    friend std::ostream& operator<<(std::ostream& os, const Vybaveni& vybaveni);
};
```

**Popis:** Definuje abstraktní rodičovskou třídu. Uchovává společné údaje, statický čítač, gettery, virtuální metodu a povoluje výpis přes `operator<<`.

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
virtual void udelejZvuk() = 0;
```

**Popis:** Nařizuje každému potomkovi, aby vytvořil vlastní verzi metody `udelejZvuk()`.

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
std::vector<int> cisla;
cisla.push_back(10);
cisla.push_back(5);
cisla.push_back(20);
```

**Popis:** Vytvoří prázdný vektor celých čísel a postupně na jeho konec vloží tři hodnoty.

### 3. Tři různé návraty vektoru

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
std::vector<int> loadDepthData(std::string filename) {
    std::vector<int> data;
    // ...
    return data;
}
```

**Popis:** Funkce vytvoří vektor a vrátí ho hodnotou. Volající tedy dostane výsledný vektor.

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
void vypisVektor(const std::vector<int>& vec) {
    for (int hodnota : vec) {
        std::cout << hodnota << " ";
    }
}
```

**Popis:** Přijme původní vektor bez kopírování a pouze ho přečte a vypíše. `const` zakazuje jeho změnu.

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
std::vector<double>& getData();
```

**Popis:** Měl by vrátit referenci na původní vektor, takže volající může jeho obsah měnit.

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
const std::vector<double>& getData() const;
```

**Popis:** Měl by vrátit původní vektor bez kopírování, ale pouze ke čtení.

### 4. Statický čítač

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
static int pocetKusu;
static int getPocetKusu();
```

**Popis:** Ukázka předvádí důležitý zápis k tomuto tématu; názvy je potřeba přizpůsobit konkrétnímu zadání.

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
int Vybaveni::pocetKusu = 0;
```

**Popis:** Vytvoří jedinou společnou statickou proměnnou a nastaví její počáteční hodnotu na nulu.

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
Vybaveni::Vybaveni(const std::string& kod, double hmotnost)
    : kodOznaceni(kod), hmotnost(hmotnost) {
    pocetKusu++;
}
```

**Popis:** Ukázka předvádí důležitý zápis k tomuto tématu; názvy je potřeba přizpůsobit konkrétnímu zadání.

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
Vybaveni::~Vybaveni() {
    pocetKusu--;
}
```

**Popis:** Ukázka předvádí důležitý zápis k tomuto tématu; názvy je potřeba přizpůsobit konkrétnímu zadání.

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
int Vybaveni::getPocetKusu() {
    return pocetKusu;
}
```

**Popis:** Vrátí aktuální hodnotu společného čítače.

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
cout << "Pocatocni pocet kusu vybaveni: "
     << Vybaveni::getPocetKusu() << endl << endl;
```

**Popis:** Zavolá statickou metodu přes název třídy a vypíše počet právě existujících objektů.

### 5. Odvozená třída a `override`

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.h`

```cpp
class PalnaZbran : public Vybaveni {
private:
    int kadence;

public:
    PalnaZbran(const std::string& kod, double hmotnost, int kadence);
    void pripravKAkci() override;
    PalnaZbran operator+(const PalnaZbran& other) const;
};
```

**Popis:** Vytvoří potomka třídy `Vybaveni`, přidá atribut `kadence`, přepíše virtuální metodu a deklaruje operátor `+`.

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.cpp`

```cpp
PalnaZbran::PalnaZbran(const std::string& kod, double hmotnost, int kadence)
    : Vybaveni(kod, hmotnost), kadence(kadence) {}
```

**Popis:** Pošle společné hodnoty `kod` a `hmotnost` konstruktoru rodiče a vlastní hodnotu uloží do `kadence`.

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.cpp`

```cpp
void PalnaZbran::pripravKAkci() {
    std::cout << "* Nabijeni zbrane " << getKodOznaceni()
              << ", nastaveni kadence na " << kadence << " ran/min. *" << std::endl;
}
```

**Popis:** Přepisuje čistě virtuální metodu a vypíše údaje konkrétní palné zbraně.

### Počítání podle podmínky

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
int count = 0;
for (int i = 1; i < data.size(); i++) {
    if (data[i] > data[i - 1]) {
        count++;
    }
}
```

**Popis:** Projde hodnoty od druhého prvku, porovná každou s předchozí a spočítá, kolikrát došlo ke zvýšení.

### Součet a průměr

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST2/main.cpp`

```cpp
double soucet = 0.0;
for (int i = 0; i < POCET_DNI; i++) {
    soucet += tyden[i].teplota;
}
cout << "Prumerna teplota: " << (soucet / POCET_DNI) << " stupnu." << endl;
```

**Popis:** Sečte teploty a vydělí součet počtem dnů, čímž získá průměr.

### Největší hodnota

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST2/main.cpp`

```cpp
Mereni maxMereni = tyden[0];
if (tyden[i].teplota > maxMereni.teplota) {
    maxMereni = tyden[i];
}
```

**Popis:** Začne první hodnotou jako dosavadním maximem a při nalezení větší hodnoty maximum nahradí.

### Počet hodnot pomocí `count_if`

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int count = std::count_if(c.begin(), c.end(), [limit](int x) -> bool
                          { return x > limit; });
```

**Popis:** Spočítá prvky větší než hodnota uložená v proměnné `limit`.

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int pocet = 0;
for (double x : historie)
    if (x > 0) pocet++;
```

**Popis:** Ukázka prochází prvky cyklem a provádí nad nimi podmínku, výpočet nebo výpis.

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int pocet = 0;
for (double x : historie)
    if (x < 0) pocet++;
```

**Popis:** Ukázka prochází prvky cyklem a provádí nad nimi podmínku, výpočet nebo výpis.

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
double soucet = 0;
for (double x : historie)
    if (x < 0) soucet += -x;
```

**Popis:** Ukázka prochází prvky cyklem a provádí nad nimi podmínku, výpočet nebo výpis.

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
double minimum = historie[0];
for (double x : historie)
    if (x < minimum) minimum = x;
```

**Popis:** Ukázka prochází prvky cyklem a provádí nad nimi podmínku, výpočet nebo výpis.

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int pocet = 0;
for (double x : historie)
    if (x > minimum && x < maximum) pocet++;
```

**Popis:** Ukázka prochází prvky cyklem a provádí nad nimi podmínku, výpočet nebo výpis.

### 6. Polymorfismus a mazání paměti

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
std::vector<Zvire *> zvirata;

zvirata.push_back(new Pes("Baryk"));
zvirata.push_back(new Kocka("Minda"));
zvirata.push_back(new Pes("Rex"));
```

**Popis:** Vytvoří společný vektor ukazatelů na rodiče a vloží do něj dynamicky vytvořené objekty různých potomků.

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
for (Zvire *z : zvirata)
{
    z->udelejZvuk();
    z->spi();
}
```

**Popis:** Projde všechny ukazatele. Díky `virtual` se u každého zvířete zavolá správná verze přepsané metody.

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
for (Zvire *z : zvirata)
{
    delete z;
}
zvirata.clear();
```

**Popis:** Nejdřív uvolní každý objekt vytvořený pomocí `new` a potom odstraní už neplatné ukazatele z vektoru.

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
virtual ~Zvire()
{
    std::cout << "  ~Zvire destruktor pro: " << this->jmeno << std::endl;
}
```

**Popis:** Zajistí správné volání destruktoru potomka při mazání přes ukazatel typu `Zvire*`.

### `==`

**Zdroj:** `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
bool operator==(const Vektor2D& other) const
{
    return (x == other.x) && (y == other.y);
}
```

**Popis:** Vrátí `true`, pouze pokud se shodují obě souřadnice porovnávaných objektů.

### `+`

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.cpp`

```cpp
PalnaZbran PalnaZbran::operator+(const PalnaZbran& other) const {
    return PalnaZbran(
        getKodOznaceni() + " a " + other.getKodOznaceni(),
        getHmotnost() + other.getHmotnost(),
        kadence + other.kadence
    );
}
```

**Popis:** Sečte údaje dvou zbraní a vrátí nový objekt `PalnaZbran`. Původní objekty nemění.

### `<<`

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
friend std::ostream& operator<<(std::ostream& os, const Vybaveni& vybaveni);
```

**Popis:** Povolí funkci `operator<<` přístup k atributům objektu a umožní zápis `cout << objekt`.

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
std::ostream& operator<<(std::ostream& os, const Vybaveni& vybaveni) {
    os << vybaveni.kodOznaceni << " (hmotnost: " << vybaveni.hmotnost << " kg)";
    return os;
}
```

**Popis:** Vloží údaje objektu do výstupního proudu a proud vrátí, aby šlo pokračovat dalším `<<`.

### `<` a `>`

**Zdroj:** `priklady-z-hodin/2025-2026/linked-list-templates-dedicnost/Student.cpp`

```cpp
bool Student::operator>(const Student& other) const {
    return this->prumer > other.prumer;
}

bool Student::operator<(const Student& other) const {
    return this->prumer < other.prumer;
}
```

**Popis:** Porovná dva studenty podle průměru. Tyto operátory lze použít například při řazení.

### `[]`

**Zdroj:** `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
Node *operator[](int index)
{
    Node *current = this;
    for (int i = 0; i < index; i++)
    {
        if (current == nullptr)
            return nullptr;
        current = current->next;
    }
    return current;
}
```

**Popis:** Postupuje spojovým seznamem na požadovaný index. Vrátí nalezený uzel, nebo `nullptr`, pokud cesta skončí.

### `*`

**Zdroj:** `priklady-z-hodin/2025-2026/pretezovani/main.cpp`

```cpp
std::string operator*(char a, A b){
    std::string test = "";
    for (int i = 0; i<b.value;i++){
        test+=a;
    }
    return test;
}
```

**Popis:** Zopakuje znak `a` tolikrát, kolik určuje `b.value`, a vrátí vytvořený text.

### Porovnávání sousedních hodnot

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 1; i < data.size(); i++) {
    if (data[i] > data[i - 1]) {
        count++;
    }
}
```

**Popis:** Porovná každý prvek s předchozím a spočítá zvýšení.

### Porovnávání sousedních oken

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 3; i < data.size(); i = i + 1) {
    int windowA = data[i - 3] + data[i - 2] + data[i - 1];
    int windowB = data[i - 2] + data[i - 1] + data[i];
    if (windowB > windowA) count = count + 1;
}
```

**Popis:** Porovnává součty dvou sousedních tříprvkových oken a počítá, kolikrát je nové okno větší.

### Lineární hledání

**Zdroj:** `06-algoritmizace/01-zakladni-algoritmy/02-vyhledavaci-algoritmy/01-linearni-vyhledavani/main.cpp`

```cpp
for (int i = 0; i < velikost; i++) {
    if (pole[i] == cil) {
        return i;
    }
}
return -1;
```

**Popis:** Prochází pole zleva doprava. Vrátí index první shody, nebo `-1`, když hodnotu nenajde.

### Mazání celého spojového seznamu

**Zdroj:** `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
while (current != nullptr)
{
    Node *next = current->next;
    delete current;
    current = next;
}
```

**Popis:** Postupně si uloží následující uzel, smaže aktuální a pokračuje, dokud nesmaže celý spojový seznam.

**Zdroj:** `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
int aktualni = 0, maximum = 0;
for (double x : data)
    if (/* podmínka */) {
        aktualni++;
        if (aktualni > maximum) maximum = aktualni;
    } else aktualni = 0;
```

**Popis:** Počítá délku právě probíhající řady a zvlášť si pamatuje nejdelší nalezenou řadu.

**Zdroj:** `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
for (auto it = data.begin(); it != data.end(); )
    if (/* smazat */) it = data.erase(it);
    else ++it;
```

**Popis:** Bezpečně maže vybrané prvky. Po smazání použije iterátor vrácený metodou `erase`.

**Zdroj:** `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
std::vector<double> vysledek;
for (double x : data)
    if (/* vybrat */) vysledek.push_back(x);
return vysledek;
```

**Popis:** Vytvoří nový vektor pouze z prvků splňujících podmínku. Původní vektor nemění.

### Volání přes objekt a přes ukazatel

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
Pes p("Alik");
p.udelejZvuk();
```

**Popis:** Ukázka předvádí důležitý zápis k tomuto tématu; názvy je potřeba přizpůsobit konkrétnímu zadání.

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
for (Zvire *z : zvirata)
{
    z->udelejZvuk();
}
```

**Popis:** Ukázka prochází prvky cyklem a provádí nad nimi podmínku, výpočet nebo výpis.

### Lokální blok a automatický zánik objektu

**Zdroj:** `03-pokrocile-cpp/13-staticke-cleny/main.cpp`

```cpp
{
    Hrac hrac3("Charlie");
    hrac3.predstavSe();
    std::cout << Hrac::getHracuOnline() << std::endl;
}
```

**Popis:** Ukázka vypisuje hodnoty nebo výsledek programu do konzole.

### Použití operátoru na objektech za ukazateli

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/main.cpp`

```cpp
{
    PalnaZbran komplet = *zbran1 + *zbran2;
    cout << "Slouceny zbranovy system: " << komplet << endl;
    komplet.pripravKAkci();
}
```

**Popis:** Ukázka vypisuje hodnoty nebo výsledek programu do konzole.

### `operator==`

**Zdroj:** `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
bool operator==(const Vektor2D& other) const
{
    return (x == other.x) && (y == other.y);
}
```

**Popis:** Ukázka předvádí deklaraci nebo implementaci přetíženého operátoru a způsob jeho návratové hodnoty.

**Zdroj:** `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
PotomekA& PotomekA::operator+=(double hodnota)
{
    pridejHodnotu(hodnota);
    return *this;
}
```

**Popis:** Ukázka předvádí deklaraci nebo implementaci přetíženého operátoru a způsob jeho návratové hodnoty.

**Zdroj:** `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
objekt += 500;
```

**Popis:** Ukázka předvádí důležitý zápis k tomuto tématu; názvy je potřeba přizpůsobit konkrétnímu zadání.

### Vektor celočíselných hodnot

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
std::vector<int> cisla;
cisla.push_back(10);
```

**Popis:** Ukázka pracuje s vektorem, tedy dynamickou kolekcí prvků stejného typu.

### Kontrola platného rozsahu

**Zdroj:** `priklady-z-hodin/2025-2026LS/uvodni_test/main.cpp`

```cpp
if (rok < 1 || mesic < 1 || mesic > 12 || den < 1) {
    return false;
}
```

**Popis:** Ukázka vypočítá nebo vybere hodnotu a vrátí ji volajícímu.

**Zdroj:** `priklady-z-hodin/2025-2026LS/uvodni_test/main.cpp`

```cpp
if (hodnota < minimum || hodnota > maximum)
```

**Popis:** Ukázka předvádí důležitý zápis k tomuto tématu; názvy je potřeba přizpůsobit konkrétnímu zadání.

### Výpočet výsledné hodnoty ze dvou atributů

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
int vypocet()
{
    return parametrA * parametrB;
}
```

**Popis:** Ukázka vypočítá nebo vybere hodnotu a vrátí ji volajícímu.

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
std::cout << "Vysledek: " << objekt1.vypocet() << std::endl;
```

**Popis:** Ukázka vypisuje hodnoty nebo výsledek programu do konzole.

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
virtual double vypocitejHodnotu() const = 0;
```

**Popis:** Ukázka používá virtuální metodu, aby potomci mohli dodat vlastní chování.

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
double PotomekB::vypocitejHodnotu() const
{
    return konstanta * parametr * parametr;
}
```

**Popis:** Ukázka vypočítá nebo vybere hodnotu a vrátí ji volajícímu.

### Změna číselných atributů

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
objekt1.setParametrA(10);
std::cout << "Nova hodnota: " << objekt1.getParametrA() << std::endl;
std::cout << "Novy vysledek: " << objekt1.vypocet() << std::endl;
```

**Popis:** Ukázka vypisuje hodnoty nebo výsledek programu do konzole.

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
virtual void upravHodnoty(double koeficient) = 0;
```

**Popis:** Ukázka používá virtuální metodu, aby potomci mohli dodat vlastní chování.

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
parametrA *= koeficient;
parametrB *= koeficient;
```

**Popis:** Ukázka předvádí důležitý zápis k tomuto tématu; názvy je potřeba přizpůsobit konkrétnímu zadání.

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
parametr *= koeficient;
```

**Popis:** Ukázka předvádí důležitý zápis k tomuto tématu; názvy je potřeba přizpůsobit konkrétnímu zadání.

### Největší poměr sousedních hodnot

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 1; i < data.size(); i++) {
    if (data[i] > data[i - 1]) {
        count++;
    }
}
```

**Popis:** Ukázka prochází prvky cyklem a provádí nad nimi podmínku, výpočet nebo výpis.

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (std::size_t i = 1; i < data.size(); i++) {
    if (data[i - 1] != 0) {
        double pomer = data[i] / data[i - 1];
        if (pomer > maximum) maximum = pomer;
    }
}
```

**Popis:** Ukázka prochází prvky cyklem a provádí nad nimi podmínku, výpočet nebo výpis.

### Výběr objektů nad průměrem

**Zdroj:** `03-pokrocile-cpp/16-lambda-a-algoritmy/main.cpp`

```cpp
std::sort(objekty.begin(), objekty.end(), [](const Polozka& a, const Polozka& b) {
    return a.hodnota > b.hodnota;
});
```

**Popis:** Ukázka vypočítá nebo vybere hodnotu a vrátí ji volajícímu.
