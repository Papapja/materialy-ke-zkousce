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

Následující ukázky jsou převzaté pouze z částí přílohy, u kterých je uveden konkrétní řádek **Zdroj:** nebo jsou výslovně označené jako pokračování stejného souboru. Obecné nedoložené šablony zde nejsou.

+### 1. `.h` a `.cpp`

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
Vybaveni(const std::string& kod, double hmotnost);
virtual ~Vybaveni();
static int getPocetKusu();
virtual void pripravKAkci() = 0;
```

**Zdroj:** `Vybaveni.cpp`

```cpp
Vybaveni::Vybaveni(const std::string& kod, double hmotnost)
    : kodOznaceni(kod), hmotnost(hmotnost) {
    pocetKusu++;
}
```

**Zdroj:** `Vybaveni.cpp`

```cpp
Vybaveni::~Vybaveni() {
    pocetKusu--;
}
```

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

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
virtual void udelejZvuk() = 0;
```

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
std::vector<int> cisla;
cisla.push_back(10);
cisla.push_back(5);
cisla.push_back(20);
```

### 3. Tři různé návraty vektoru

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
std::vector<int> loadDepthData(std::string filename) {
    std::vector<int> data;
    // ...
    return data;
}
```

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
void vypisVektor(const std::vector<int>& vec) {
    for (int hodnota : vec) {
        std::cout << hodnota << " ";
    }
}
```

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
std::vector<double>& getData();
```

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
const std::vector<double>& getData() const;
```

### 4. Statický čítač

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
static int pocetKusu;
static int getPocetKusu();
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
int Vybaveni::pocetKusu = 0;
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
Vybaveni::Vybaveni(const std::string& kod, double hmotnost)
    : kodOznaceni(kod), hmotnost(hmotnost) {
    pocetKusu++;
}
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
Vybaveni::~Vybaveni() {
    pocetKusu--;
}
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
int Vybaveni::getPocetKusu() {
    return pocetKusu;
}
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
cout << "Pocatocni pocet kusu vybaveni: "
     << Vybaveni::getPocetKusu() << endl << endl;
```

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

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.cpp`

```cpp
PalnaZbran::PalnaZbran(const std::string& kod, double hmotnost, int kadence)
    : Vybaveni(kod, hmotnost), kadence(kadence) {}
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.cpp`

```cpp
void PalnaZbran::pripravKAkci() {
    std::cout << "* Nabijeni zbrane " << getKodOznaceni()
              << ", nastaveni kadence na " << kadence << " ran/min. *" << std::endl;
}
```

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

### Součet a průměr

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST2/main.cpp`

```cpp
double soucet = 0.0;
for (int i = 0; i < POCET_DNI; i++) {
    soucet += tyden[i].teplota;
}
cout << "Prumerna teplota: " << (soucet / POCET_DNI) << " stupnu." << endl;
```

### Největší hodnota

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST2/main.cpp`

```cpp
Mereni maxMereni = tyden[0];
if (tyden[i].teplota > maxMereni.teplota) {
    maxMereni = tyden[i];
}
```

### Počet hodnot pomocí `count_if`

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int count = std::count_if(c.begin(), c.end(), [limit](int x) -> bool
                          { return x > limit; });
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int pocet = 0;
for (double x : historie)
    if (x > 0) pocet++;
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int pocet = 0;
for (double x : historie)
    if (x < 0) pocet++;
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
double soucet = 0;
for (double x : historie)
    if (x < 0) soucet += -x;
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
double minimum = historie[0];
for (double x : historie)
    if (x < minimum) minimum = x;
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int pocet = 0;
for (double x : historie)
    if (x > minimum && x < maximum) pocet++;
```

### 6. Polymorfismus a mazání paměti

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
std::vector<Zvire *> zvirata;

zvirata.push_back(new Pes("Baryk"));
zvirata.push_back(new Kocka("Minda"));
zvirata.push_back(new Pes("Rex"));
```

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
for (Zvire *z : zvirata)
{
    z->udelejZvuk();
    z->spi();
}
```

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
for (Zvire *z : zvirata)
{
    delete z;
}
zvirata.clear();
```

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
virtual ~Zvire()
{
    std::cout << "  ~Zvire destruktor pro: " << this->jmeno << std::endl;
}
```

### `==`

**Zdroj:** `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
bool operator==(const Vektor2D& other) const
{
    return (x == other.x) && (y == other.y);
}
```

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

### `<<`

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
friend std::ostream& operator<<(std::ostream& os, const Vybaveni& vybaveni);
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
std::ostream& operator<<(std::ostream& os, const Vybaveni& vybaveni) {
    os << vybaveni.kodOznaceni << " (hmotnost: " << vybaveni.hmotnost << " kg)";
    return os;
}
```

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

### Porovnávání sousedních hodnot

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 1; i < data.size(); i++) {
    if (data[i] > data[i - 1]) {
        count++;
    }
}
```

### Porovnávání sousedních oken

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 3; i < data.size(); i = i + 1) {
    int windowA = data[i - 3] + data[i - 2] + data[i - 1];
    int windowB = data[i - 2] + data[i - 1] + data[i];
    if (windowB > windowA) count = count + 1;
}
```

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

**Zdroj:** `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
int aktualni = 0, maximum = 0;
for (double x : data)
    if (/* podmínka */) {
        aktualni++;
        if (aktualni > maximum) maximum = aktualni;
    } else aktualni = 0;
```

**Zdroj:** `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
for (auto it = data.begin(); it != data.end(); )
    if (/* smazat */) it = data.erase(it);
    else ++it;
```

**Zdroj:** `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
std::vector<double> vysledek;
for (double x : data)
    if (/* vybrat */) vysledek.push_back(x);
return vysledek;
```

### Volání přes objekt a přes ukazatel

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
Pes p("Alik");
p.udelejZvuk();
```

**Zdroj:** `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
for (Zvire *z : zvirata)
{
    z->udelejZvuk();
}
```

### Lokální blok a automatický zánik objektu

**Zdroj:** `03-pokrocile-cpp/13-staticke-cleny/main.cpp`

```cpp
{
    Hrac hrac3("Charlie");
    hrac3.predstavSe();
    std::cout << Hrac::getHracuOnline() << std::endl;
}
```

### Použití operátoru na objektech za ukazateli

**Zdroj:** `priklady-z-hodin/2025-2026LS/TEST5/main.cpp`

```cpp
{
    PalnaZbran komplet = *zbran1 + *zbran2;
    cout << "Slouceny zbranovy system: " << komplet << endl;
    komplet.pripravKAkci();
}
```

### `operator==`

**Zdroj:** `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
bool operator==(const Vektor2D& other) const
{
    return (x == other.x) && (y == other.y);
}
```

**Zdroj:** `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
PotomekA& PotomekA::operator+=(double hodnota)
{
    pridejHodnotu(hodnota);
    return *this;
}
```

**Zdroj:** `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
objekt += 500;
```

### Vektor celočíselných hodnot

**Zdroj:** `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
std::vector<int> cisla;
cisla.push_back(10);
```

### Kontrola platného rozsahu

**Zdroj:** `priklady-z-hodin/2025-2026LS/uvodni_test/main.cpp`

```cpp
if (rok < 1 || mesic < 1 || mesic > 12 || den < 1) {
    return false;
}
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/uvodni_test/main.cpp`

```cpp
if (hodnota < minimum || hodnota > maximum)
```

### Výpočet výsledné hodnoty ze dvou atributů

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
int vypocet()
{
    return parametrA * parametrB;
}
```

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
std::cout << "Vysledek: " << objekt1.vypocet() << std::endl;
```

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
virtual double vypocitejHodnotu() const = 0;
```

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
double PotomekB::vypocitejHodnotu() const
{
    return konstanta * parametr * parametr;
}
```

### Změna číselných atributů

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
objekt1.setParametrA(10);
std::cout << "Nova hodnota: " << objekt1.getParametrA() << std::endl;
std::cout << "Novy vysledek: " << objekt1.vypocet() << std::endl;
```

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
virtual void upravHodnoty(double koeficient) = 0;
```

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
parametrA *= koeficient;
parametrB *= koeficient;
```

**Zdroj:** `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
parametr *= koeficient;
```

### Největší poměr sousedních hodnot

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 1; i < data.size(); i++) {
    if (data[i] > data[i - 1]) {
        count++;
    }
}
```

**Zdroj:** `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (std::size_t i = 1; i < data.size(); i++) {
    if (data[i - 1] != 0) {
        double pomer = data[i] / data[i - 1];
        if (pomer > maximum) maximum = pomer;
    }
}
```

### Výběr objektů nad průměrem

**Zdroj:** `03-pokrocile-cpp/16-lambda-a-algoritmy/main.cpp`

```cpp
std::sort(objekty.begin(), objekty.end(), [](const Polozka& a, const Polozka& b) {
    return a.hodnota > b.hodnota;
});
```

