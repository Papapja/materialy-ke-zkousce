# Moje poznámky k C++ z materiálů

U ukázek převzatých ze souboru mám napsaný zdroj. 

## 1. `.h` a `.cpp`

Do `.h` si píšu hlavně názvy atributů a deklarace metod, tedy co moje třída bude mít a umět. Do `.cpp` potom dopíšu, co mají jednotlivé metody opravdu dělat.

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
Vybaveni(const std::string& kod, double hmotnost);
virtual ~Vybaveni();
static int getPocetKusu();
virtual void pripravKAkci() = 0;
```

**Co kód dělá:** Deklaruje konstruktor, virtuální destruktor, statický getter a čistě virtuální metodu. Kvůli `= 0` je `Vybaveni` abstraktní a každý potomek musí vytvořit vlastní `pripravKAkci()`.

Stejné metody v `Vybaveni.cpp`:

```cpp
Vybaveni::Vybaveni(const std::string& kod, double hmotnost)
    : kodOznaceni(kod), hmotnost(hmotnost) {
    pocetKusu++;
}
```

**Co kód dělá:** Konstruktor uloží přijatý kód a hmotnost do atributů a zvýší společný počet existujících kusů.

```cpp
Vybaveni::~Vybaveni() {
    pocetKusu--;
}
```

**Co kód dělá:** Při zániku objektu sníží statický čítač o jedna.

---

## 2. Obecná kostra abstraktní třídy

Abstraktní třída je společný základ pro potomky a sama se většinou nevytváří. Když má metoda na konci `= 0`, každý potomek si musí udělat její vlastní verzi.

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

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

**Co kód dělá:** Definuje abstraktní rodičovskou třídu. Uchovává společné údaje, statický čítač, gettery, virtuální metodu a povoluje výpis přes `operator<<`.

Další čistě virtuální metoda ze souboru:

Zdroj: `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
virtual void udelejZvuk() = 0;
```

**Co kód dělá:** Nařizuje každému potomkovi, aby vytvořil vlastní verzi metody `udelejZvuk()`.

Přidávání do vektoru ze souboru:

Zdroj: `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
std::vector<int> cisla;
cisla.push_back(10);
cisla.push_back(5);
cisla.push_back(20);
```

**Co kód dělá:** Vytvoří prázdný vektor celých čísel a postupně na jeho konec vloží tři hodnoty.

---

## 3. Tři různé návraty vektoru

Vektor můžu vrátit jako kopii, měnitelnou referenci nebo konstantní referenci. Nejvíc si hlídám znak `&` a `const`, protože podle nich poznám, jestli pracuji s původním vektorem a jestli ho smím změnit.

Vrácení kopie vektoru.

Zdroj: `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
std::vector<int> loadDepthData(std::string filename) {
    std::vector<int> data;
    // ...
    return data;
}
```

**Co kód dělá:** Funkce vytvoří vektor a vrátí ho hodnotou. Volající tedy dostane výsledný vektor.

Použití konstantní reference na vektor je také v souborech, ale jako parametr funkce.

Zdroj: `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
void vypisVektor(const std::vector<int>& vec) {
    for (int hodnota : vec) {
        std::cout << hodnota << " ";
    }
}
```

**Co kód dělá:** Přijme původní vektor bez kopírování a pouze ho přečte a vypíše. `const` zakazuje jeho změnu.

**Getter vracející měnitelnou referenci:**

```cpp
std::vector<double>& getData();
```

**Co kód dělá:** Měl by vrátit referenci na původní vektor, takže volající může jeho obsah měnit.

**Getter vracející konstantní referenci:**

```cpp
const std::vector<double>& getData() const;
```

**Co kód dělá:** Měl by vrátit původní vektor bez kopírování, ale pouze ke čtení.

---

## 4. Statický čítač

Statická proměnná patří celé třídě, takže ji mají všechny objekty společnou. V konstruktoru ji většinou zvýším a v destruktoru zase snížím, abych věděla, kolik objektů právě existuje.

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
static int pocetKusu;
static int getPocetKusu();
```

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
int Vybaveni::pocetKusu = 0;
```

**Co kód dělá:** Vytvoří jedinou společnou statickou proměnnou a nastaví její počáteční hodnotu na nulu.

```cpp
Vybaveni::Vybaveni(const std::string& kod, double hmotnost)
    : kodOznaceni(kod), hmotnost(hmotnost) {
    pocetKusu++;
}
```

```cpp
Vybaveni::~Vybaveni() {
    pocetKusu--;
}
```

```cpp
int Vybaveni::getPocetKusu() {
    return pocetKusu;
}
```

**Co kód dělá:** Vrátí aktuální hodnotu společného čítače.

Použití v `main()`:

```cpp
cout << "Pocatocni pocet kusu vybaveni: "
     << Vybaveni::getPocetKusu() << endl << endl;
```

**Co kód dělá:** Zavolá statickou metodu přes název třídy a vypíše počet právě existujících objektů.

---

## 5. Odvozená třída a `override`

Potomek zdědí společné věci z rodiče a přidá si jen to, co je pro něj navíc. Pomocí `override` si kontroluji, že opravdu přepisuji virtuální metodu z rodičovské třídy.

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.h`

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

**Co kód dělá:** Vytvoří potomka třídy `Vybaveni`, přidá atribut `kadence`, přepíše virtuální metodu a deklaruje operátor `+`.

Konstruktor potomka.

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.cpp`

```cpp
PalnaZbran::PalnaZbran(const std::string& kod, double hmotnost, int kadence)
    : Vybaveni(kod, hmotnost), kadence(kadence) {}
```

**Co kód dělá:** Pošle společné hodnoty `kod` a `hmotnost` konstruktoru rodiče a vlastní hodnotu uloží do `kadence`.

Přepsaná metoda:

```cpp
void PalnaZbran::pripravKAkci() {
    std::cout << "* Nabijeni zbrane " << getKodOznaceni()
              << ", nastaveni kadence na " << kadence << " ran/min. *" << std::endl;
}
```

**Co kód dělá:** Přepisuje čistě virtuální metodu a vypíše údaje konkrétní palné zbraně.

### Co můžu napsat do `analyzuj()`

#### Počítání podle podmínky

Zdroj: `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
int count = 0;
for (int i = 1; i < data.size(); i++) {
    if (data[i] > data[i - 1]) {
        count++;
    }
}
```

**Co kód dělá:** Projde hodnoty od druhého prvku, porovná každou s předchozí a spočítá, kolikrát došlo ke zvýšení.

#### Součet a průměr

Zdroj: `priklady-z-hodin/2025-2026LS/TEST2/main.cpp`

```cpp
double soucet = 0.0;
for (int i = 0; i < POCET_DNI; i++) {
    soucet += tyden[i].teplota;
}
cout << "Prumerna teplota: " << (soucet / POCET_DNI) << " stupnu." << endl;
```

**Co kód dělá:** Sečte teploty a vydělí součet počtem dnů, čímž získá průměr.

#### Největší hodnota

Zdroj: `priklady-z-hodin/2025-2026LS/TEST2/main.cpp`

```cpp
Mereni maxMereni = tyden[0];
if (tyden[i].teplota > maxMereni.teplota) {
    maxMereni = tyden[i];
}
```

**Co kód dělá:** Začne první hodnotou jako dosavadním maximem a při nalezení větší hodnoty maximum nahradí.

#### Počet hodnot pomocí `count_if`

Zdroj: `priklady-z-hodin/2025-2026LS/stl/main.cpp`

```cpp
int count = std::count_if(c.begin(), c.end(), [limit](int x) -> bool
                          { return x > limit; });
```

**Co kód dělá:** Spočítá prvky větší než hodnota uložená v proměnné `limit`.

**Ruční počet kladných hodnot v `vector<double>`:**

```cpp
int pocet = 0;
for (double x : historie)
    if (x > 0) pocet++;
```

**Ruční počet záporných hodnot:**

```cpp
int pocet = 0;
for (double x : historie)
    if (x < 0) pocet++;
```

**Součet záporných hodnot převedený na kladné číslo:**

```cpp
double soucet = 0;
for (double x : historie)
    if (x < 0) soucet += -x;
```

**Hledání minima:**

```cpp
double minimum = historie[0];
for (double x : historie)
    if (x < minimum) minimum = x;
```

**Počet hodnot v intervalu:**

```cpp
int pocet = 0;
for (double x : historie)
    if (x > minimum && x < maximum) pocet++;
```

---

## 6. Polymorfismus a mazání paměti

Do jednoho vektoru ukazatelů na rodiče můžu dát různé potomky a přes `virtual` se vždy zavolá správná metoda. Co vytvořím pomocí `new`, musím nakonec smazat pomocí `delete`, jinak zůstane zabraná paměť.

Zdroj: `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
std::vector<Zvire *> zvirata;

zvirata.push_back(new Pes("Baryk"));
zvirata.push_back(new Kocka("Minda"));
zvirata.push_back(new Pes("Rex"));
```

**Co kód dělá:** Vytvoří společný vektor ukazatelů na rodiče a vloží do něj dynamicky vytvořené objekty různých potomků.

Polymorfní volání:

```cpp
for (Zvire *z : zvirata)
{
    z->udelejZvuk();
    z->spi();
}
```

**Co kód dělá:** Projde všechny ukazatele. Díky `virtual` se u každého zvířete zavolá správná verze přepsané metody.

Mazání paměti:

```cpp
for (Zvire *z : zvirata)
{
    delete z;
}
zvirata.clear();
```

**Co kód dělá:** Nejdřív uvolní každý objekt vytvořený pomocí `new` a potom odstraní už neplatné ukazatele z vektoru.

Virtuální destruktor ze stejného souboru:

```cpp
virtual ~Zvire()
{
    std::cout << "  ~Zvire destruktor pro: " << this->jmeno << std::endl;
}
```

**Co kód dělá:** Zajistí správné volání destruktoru potomka při mazání přes ukazatel typu `Zvire*`.

---

## 7. Operátory nalezené v souboru

U operátoru si hlídám zápis v `.h` a potom celé tělo v `.cpp`. Když operátor mění přímo můj objekt, většinou vracím `*this`; když jen porovnává nebo čte, bývá na konci `const`.

### `==`

Zdroj: `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
bool operator==(const Vektor2D& other) const
{
    return (x == other.x) && (y == other.y);
}
```

**Co kód dělá:** Vrátí `true`, pouze pokud se shodují obě souřadnice porovnávaných objektů.

### `+`

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/PalnaZbran.cpp`

```cpp
PalnaZbran PalnaZbran::operator+(const PalnaZbran& other) const {
    return PalnaZbran(
        getKodOznaceni() + " a " + other.getKodOznaceni(),
        getHmotnost() + other.getHmotnost(),
        kadence + other.kadence
    );
}
```

**Co kód dělá:** Sečte údaje dvou zbraní a vrátí nový objekt `PalnaZbran`. Původní objekty nemění.

### `<<`

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.h`

```cpp
friend std::ostream& operator<<(std::ostream& os, const Vybaveni& vybaveni);
```

**Co kód dělá:** Povolí funkci `operator<<` přístup k atributům objektu a umožní zápis `cout << objekt`.

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/Vybaveni.cpp`

```cpp
std::ostream& operator<<(std::ostream& os, const Vybaveni& vybaveni) {
    os << vybaveni.kodOznaceni << " (hmotnost: " << vybaveni.hmotnost << " kg)";
    return os;
}
```

**Co kód dělá:** Vloží údaje objektu do výstupního proudu a proud vrátí, aby šlo pokračovat dalším `<<`.

### `<` a `>`

Zdroj: `priklady-z-hodin/2025-2026/linked-list-templates-dedicnost/Student.cpp`

```cpp
bool Student::operator>(const Student& other) const {
    return this->prumer > other.prumer;
}

bool Student::operator<(const Student& other) const {
    return this->prumer < other.prumer;
}
```

**Co kód dělá:** Porovná dva studenty podle průměru. Tyto operátory lze použít například při řazení.

### `[]`

Hranaté závorky slouží k přístupu podle indexu. V konkrétním zadání nemusí vracet právě `Node*`; mohou vracet například `double&`, pokud se má pomocí `objekt[index]` číst nebo měnit hodnota ve vektoru. Návrat reference poznám podle znaku `&`.

Zdroj: `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

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

**Co kód dělá:** Postupuje spojovým seznamem na požadovaný index. Vrátí nalezený uzel, nebo `nullptr`, pokud cesta skončí.

### `*`

Zdroj: `priklady-z-hodin/2025-2026/pretezovani/main.cpp`

```cpp
std::string operator*(char a, A b){
    std::string test = "";
    for (int i = 0; i<b.value;i++){
        test+=a;
    }
    return test;
}
```

**Co kód dělá:** Zopakuje znak `a` tolikrát, kolik určuje `b.value`, a vrátí vytvořený text.

### Další operátory

**Operátor `+=`:**

```cpp
Potomek& Potomek::operator+=(double hodnota)
{
    /* změna objektu */
    return *this;
}
```

**Co kód dělá:** Upraví současný objekt a pomocí `return *this` vrátí odkaz na právě upravený objekt.

**Operátor `*=`.** Má stejný obecný princip jako `+=`.

**Prefixové `++`:**

```cpp
Potomek& Potomek::operator++()
{
    parametr++;
    return *this;
}
```

**Co kód dělá:** Prefixová verze nejdřív zvýší atribut a potom vrátí už změněný objekt.

**Postfixové `++`:**

```cpp
Potomek Potomek::operator++(int)
{
    Potomek puvodni = *this;
    parametr++;
    return puvodni;
}
```

**Co kód dělá:** Postfixová verze si uloží původní stav, zvýší objekt a vrátí kopii původního stavu. Parametr `int` pouze rozlišuje postfixový zápis.

**Přetížené `--`.**

**Operátor `()`:**

```cpp
double Potomek::operator()() const
{
    return parametr;
}
```

**Co kód dělá:** Umožní použít objekt jako funkci, například `objekt()`, a vrátí požadovanou hodnotu.

Kulaté závorky mohou mít uvnitř parametr. Potom zápis jako `objekt(hodnota)` zavolá `operator()` a může například přidat změnu do historie. Jestli metoda nic nevrací, její návratový typ je `void`; takové volání nevkládám přímo do `cout`.

---

## 8. Algoritmy

Algoritmus si můžu napsat jako samostatnou funkci nad `main()` a předat mu objekt nebo vektor, se kterým má pracovat. Nejdřív si řeknu, jestli chci počet, součet, průměr, maximum, mazání nebo nový vektor, a podle toho si připravím proměnné.

### Porovnávání sousedních hodnot

Zdroj: `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 1; i < data.size(); i++) {
    if (data[i] > data[i - 1]) {
        count++;
    }
}
```

**Co kód dělá:** Porovná každý prvek s předchozím a spočítá zvýšení.

### Porovnávání sousedních oken

Zdroj: `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 3; i < data.size(); i = i + 1) {
    int windowA = data[i - 3] + data[i - 2] + data[i - 1];
    int windowB = data[i - 2] + data[i - 1] + data[i];
    if (windowB > windowA) count = count + 1;
}
```

**Co kód dělá:** Porovnává součty dvou sousedních tříprvkových oken a počítá, kolikrát je nové okno větší.

### Lineární hledání

Zdroj: `06-algoritmizace/01-zakladni-algoritmy/02-vyhledavaci-algoritmy/01-linearni-vyhledavani/main.cpp`

```cpp
for (int i = 0; i < velikost; i++) {
    if (pole[i] == cil) {
        return i;
    }
}
return -1;
```

**Co kód dělá:** Prochází pole zleva doprava. Vrátí index první shody, nebo `-1`, když hodnotu nenajde.

### Mazání celého spojového seznamu

Zdroj: `priklady-z-hodin/2025-2026/sprava-studentu/main.cpp`

```cpp
while (current != nullptr)
{
    Node *next = current->next;
    delete current;
    current = next;
}
```

**Co kód dělá:** Postupně si uloží následující uzel, smaže aktuální a pokračuje, dokud nesmaže celý spojový seznam.

**Nejdelší souvislá řada:**

```cpp
int aktualni = 0, maximum = 0;
for (double x : data)
    if (/* podmínka */) {
        aktualni++;
        if (aktualni > maximum) maximum = aktualni;
    } else aktualni = 0;
```

**Co kód dělá:** Počítá délku právě probíhající řady a zvlášť si pamatuje nejdelší nalezenou řadu.

**Mazání prvků z vektoru pomocí návratu `erase`:**

U mazání nesmím po `erase` použít starý iterátor. `erase` proto vrátí následující platnou pozici. Když prvek nemažu, posunu iterátor ručně pomocí `++it`; proto je třetí část cyklu `for` prázdná.

```cpp
for (auto it = data.begin(); it != data.end(); )
    if (/* smazat */) it = data.erase(it);
    else ++it;
```

**Co kód dělá:** Bezpečně maže vybrané prvky. Po smazání použije iterátor vrácený metodou `erase`.

**Filtrování do nového vektoru:**

Filtrování je jiné než mazání. Původní data nechám být, vytvořím prázdný výsledek a přidám do něj jen vyhovující hodnoty. Funkce proto vrací celý nový vektor.

```cpp
std::vector<double> vysledek;
for (double x : data)
    if (/* vybrat */) vysledek.push_back(x);
return vysledek;
```

**Co kód dělá:** Vytvoří nový vektor pouze z prvků splňujících podmínku. Původní vektor nemění.

Další algoritmy skutečně obsažené v souboru, ale zde nerozepsané: `sort`, `find`, `find_if`, `count_if`, `transform`, `for_each`, `accumulate`, binární vyhledávání, selection sort, bubble sort, insertion sort, merge sort, quick sort a grafové algoritmy.

---

## 9. Další užitečné části programu

### Volání přes objekt a přes ukazatel

Přímé volání na objektu ze souboru:

Zdroj: `03-pokrocile-cpp/09-polymorfismus/main.cpp`

```cpp
Pes p("Alik");
p.udelejZvuk();
```

Volání přes ukazatel ze stejného souboru:

```cpp
for (Zvire *z : zvirata)
{
    z->udelejZvuk();
}
```

Tečka je pro objekt, šipka pro ukazatel.

### Lokální blok a automatický zánik objektu

Zdroj: `03-pokrocile-cpp/13-staticke-cleny/main.cpp`

```cpp
{
    Hrac hrac3("Charlie");
    hrac3.predstavSe();
    std::cout << Hrac::getHracuOnline() << std::endl;
}
```

Po uzavření `}` lokální objekt automaticky zanikne a zavolá se jeho destruktor.

### Použití operátoru na objektech za ukazateli

Zdroj: `priklady-z-hodin/2025-2026LS/TEST5/main.cpp`

```cpp
{
    PalnaZbran komplet = *zbran1 + *zbran2;
    cout << "Slouceny zbranovy system: " << komplet << endl;
    komplet.pripravKAkci();
}
```

`zbran1` je ukazatel. Zápis `*zbran1` znamená samotný objekt, nad kterým se použije `operator+`.

---

### Další příklady změny hodnot a práce s historií

### `operator==`

Nejbližší skutečný vzor je porovnání dvou vektorů.

Zdroj: `03-pokrocile-cpp/10-pretezovani-operatoru/main.cpp`

```cpp
bool operator==(const Vektor2D& other) const
{
    return (x == other.x) && (y == other.y);
}
```

U jiného typu objektu se změní pouze porovnávané atributy.

**Operátor `+=` přidávající hodnotu do historie:**

```cpp
PotomekA& PotomekA::operator+=(double hodnota)
{
    pridejHodnotu(hodnota);
    return *this;
}
```

Použití:

```cpp
objekt += 500;
```

### Nejdelší řada hodnot splňujících podmínku

**Nejdelší řada hodnot splňujících podmínku:**

```cpp
int aktualni = 0, nejdelsi = 0;
for (double x : data) {
    if (x > 0) {
        aktualni++;
        if (aktualni > nejdelsi) nejdelsi = aktualni;
    } else aktualni = 0;
}
```

`aktualni` počítá právě probíhající řadu. `nejdelsi` uchovává nejlepší výsledek. Nevyhovující hodnota vynuluje pouze `aktualni`.

### Mazání hodnot z určitého intervalu

**Mazání hodnot z intervalu přes iterátor:**

```cpp
for (auto it = data.begin(); it != data.end(); ) {
    if (*it < 0 && *it > -50)
        it = data.erase(it);
    else
        ++it;
}
```

Po `erase` se nepíše další `++it`, protože `erase` vrátí novou platnou pozici.

---

### Další příklady kontroly celočíselných hodnot

### Vektor celočíselných hodnot

V souboru jsou běžné vektory celých čísel.

Zdroj: `03-pokrocile-cpp/14-uvod-do-stl/main.cpp`

```cpp
std::vector<int> cisla;
cisla.push_back(10);
```

Podle zadání se změní název vektoru a přidávaná hodnota.

### Kontrola platného rozsahu

Nejbližší skutečná kontrola rozsahu v souboru:

Zdroj: `priklady-z-hodin/2025-2026LS/uvodni_test/main.cpp`

```cpp
if (rok < 1 || mesic < 1 || mesic > 12 || den < 1) {
    return false;
}
```

**Podmínka pro hodnotu mimo povolený rozsah:**

```cpp
if (hodnota < minimum || hodnota > maximum)
```

### Odstranění hodnot mimo povolený rozsah

**Odstranění hodnot mimo rozsah:**

```cpp
for (auto it = hodnoty.begin(); it != hodnoty.end(); ) {
    if (*it < minimum || *it > maximum)
        it = hodnoty.erase(it);
    else
        ++it;
}
```

### Několik stejných hodnot bezprostředně za sebou

**Hledání souvislé řady stejné hodnoty:**

```cpp
int rada = 0;
for (int hodnota : hodnoty) {
    if (hodnota == hledana) rada++;
    else rada = 0;
    if (rada >= pozadovanyPocet) return true;
}
return false;
```

Hledaná hodnota i požadovaná délka řady mohou přijít jako parametry funkce.

### Prefixový `operator++`

**Zvýšení číselného atributu o pevnou hodnotu:**

```cpp
PotomekB& PotomekB::operator++()
{
    dobaPlatnosti += krok;
    return *this;
}
```

Deklarace a použití:

```cpp
PotomekB& operator++();
++polozka;
```

Prefixová varianta nemá uvnitř závorek `int`.

### `operator()`

Přesný význam tohoto operátoru v původním zadání není jistý.

**Možnost „má alespoň tolik hodnot“:**

```cpp
bool Polozka::operator()(int pocet) const
{
    return hodnoty.size() >= pocet;
}
```

Použití: `bool vysledek = polozka(5);`

**Možnost „kolikrát se hodnota vyskytuje“:**

```cpp
int Polozka::operator()(int hledana) const
{
    int pocet = 0;
    for (int hodnota : hodnoty)
        if (hodnota == hledana) pocet++;
    return pocet;
}
```

O správné variantě rozhoduje návratový typ a přesný text zadání.

---

### Další příklady výpočtů a změny číselných atributů

### Výpočet výsledné hodnoty ze dvou atributů

Zdroj: `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
int vypocet()
{
    return parametrA * parametrB;
}
```

Použití ze stejného souboru:

```cpp
std::cout << "Vysledek: " << objekt1.vypocet() << std::endl;
```

**Čistě virtuální výpočet hodnoty:**

```cpp
virtual double vypocitejHodnotu() const = 0;
```

**Výpočet z jednoho číselného atributu:**

```cpp
double PotomekB::vypocitejHodnotu() const
{
    return konstanta * parametr * parametr;
}
```

### Změna číselných atributů

V souboru je ukázka změny rozměru objektu pomocí setteru.

Zdroj: `03-pokrocile-cpp/06-uvod-do-oop/main.cpp`

```cpp
objekt1.setParametrA(10);
std::cout << "Nova hodnota: " << objekt1.getParametrA() << std::endl;
std::cout << "Novy vysledek: " << objekt1.vypocet() << std::endl;
```

**Virtuální změna hodnot:**

```cpp
virtual void upravHodnoty(double koeficient) = 0;
```

**Změna dvou atributů:**

```cpp
parametrA *= koeficient;
parametrB *= koeficient;
```

**Změna jednoho atributu:**

```cpp
parametr *= koeficient;
```

### `operator*=`

**Změna hodnot operátorem:**

```cpp
PotomekA& PotomekA::operator*=(double koeficient)
{
    upravHodnoty(koeficient);
    return *this;
}
```

Použití: `objekt *= 2;`

### Porovnání vypočítaných hodnot

Přímý vzor `operator==` je v souboru u `Vektor2D`. Pro tvary se může porovnávaný výraz změnit na výsledek metody.

**Porovnání vypočítaných hodnot:**

```cpp
bool PotomekA::operator==(const PotomekA& druhy) const
{
    return vypocitejHodnotu() == druhy.vypocitejHodnotu();
}
```

### Největší poměr sousedních hodnot

V souboru je přímo indexový průchod a porovnání sousedů:

Zdroj: `priklady-z-hodin/2025-2026LS/soutez_1/task_1/main.cpp`

```cpp
for (int i = 1; i < data.size(); i++) {
    if (data[i] > data[i - 1]) {
        count++;
    }
}
```

**Výpočet největšího poměru:**

```cpp
for (std::size_t i = 1; i < data.size(); i++) {
    if (data[i - 1] != 0) {
        double pomer = data[i] / data[i - 1];
        if (pomer > maximum) maximum = pomer;
    }
}
```

### Výběr objektů nad průměrem

Výpočet součtu a průměru je přímo v `TEST2/main.cpp`. Vložení vybraných prvků přes `push_back` je také běžně v materiálech.

**Výběr ukazatelů nad průměrem:**

```cpp
std::vector<Zaklad*> vysledek;
for (Zaklad* objekt : objekty)
    if (objekt->vypocitejHodnotu() > prumer)
        vysledek.push_back(objekt);
```

Pokud zadání požaduje sestupné řazení výsledku, v souboru je tento tvar `sort` s lambdou:

Zdroj: `03-pokrocile-cpp/16-lambda-a-algoritmy/main.cpp`

```cpp
std::sort(objekty.begin(), objekty.end(), [](const Polozka& a, const Polozka& b) {
    return a.hodnota > b.hodnota;
});
```

U ukazatelů by se uvnitř lambdy používala šipka, například `a->vypocitejHodnotu()`.

---

## Co si zkontrolovat v `main()`

`main()` si skládám chronologicky, aby se mi jednotlivé testy nemíchaly. Nejdřív ověřím čítač, potom vytvořím objekty a naplním jejich data. Následuje polymorfní výpis a analýza, test samostatných algoritmů a operátorů. Úplně nakonec smažu vše vytvořené pomocí `new` a znovu ověřím čítač.

Pro polymorfismus používám ukazatele na rodiče, protože v jednom seznamu mohou být různí potomci. Na test operátorů jsou naopak přehlednější obyčejné lokální objekty bez `new`. Lokální objekt zanikne sám na konci bloku, ale každý objekt vytvořený pomocí `new` musím uvolnit příkazem `delete`.

Přesný test z `TEST5/main.cpp` ukazuje doporučené pořadí:

1. vypsat počáteční statický čítač,
2. vytvořit objekty pomocí `new`,
3. vypsat čítač po vytvoření,
4. udělat polymorfní průchod,
5. otestovat operátory,
6. spustit algoritmy ještě před mazáním,
7. zavolat `delete`,
8. vypsat konečný čítač.

Vytvoření objektů ze souboru:

```cpp
PalnaZbran* zbran1 = new PalnaZbran("M4A1", 3.5, 800);
PalnaZbran* zbran2 = new PalnaZbran("M203", 1.5, 100);
BalistickaOchrana* ochrana = new BalistickaOchrana("Vesta-NIJ4", 5.0, 4);
```

Polymorfní pole ze souboru:

```cpp
Vybaveni* pole[3] = { zbran1, zbran2, ochrana };
for (int i = 0; i < 3; i++) {
    pole[i]->pripravKAkci();
}
```

Úklid ze souboru:

```cpp
delete zbran1;
delete zbran2;
delete ochrana;
```
