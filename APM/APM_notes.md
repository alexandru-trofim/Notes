
- [Sistemul de memorie](#sistemul-de-memorie)
- [Memoria Cache(1)](#memoria-cache1)
    - [CPU-Memory Bottleneck](#cpu-memory-bottleneck)
    - [Organizarea ierarhica a memoriei](#organizarea-ierarhica-a-memoriei)
      - [Analogie pentru a intelege Cache-ul mai bine](#analogie-pentru-a-intelege-cache-ul-mai-bine)
      - [Cache Hit/Miss rate formula](#cache-hitmiss-rate-formula)
      - [Performantele unui sistem cache pe doua niveluri](#performantele-unui-sistem-cache-pe-doua-niveluri)
      - [Parametrii memoriei Cache](#parametrii-memoriei-cache)
      - [Tipuri de miss din cache](#tipuri-de-miss-din-cache)
      - [Cum plasam liniile? Care e faza cu complet asociativ, set asociativ, mapat direct](#cum-plasam-liniile-care-e-faza-cu-complet-asociativ-set-asociativ-mapat-direct)
      - [Politica de inlocuire](#politica-de-inlocuire)
      - [Imbunatatirea performantelor memoriei cache](#imbunatatirea-performantelor-memoriei-cache)
- [Memoria Cache(2)](#memoria-cache2)
      - [Dimensiunile liniilor si localitatea spatiala](#dimensiunile-liniilor-si-localitatea-spatiala)
      - [Imbunatatirea performantei memoriei cache](#imbunatatirea-performantei-memoriei-cache)
      - [Prefetching](#prefetching)
- [Memoria virtuala](#memoria-virtuala)
- [Dispozitive periferice](#dispozitive-periferice)


# Sistemul de memorie

**Memoria principala** :
//TODO Search about DRAM SRAM
- SRAM(cache) 
- DRAM(main)
- HDD, SSD(nonvolatile)

SRAM: 
- stocare volatila 
- Structura interna de bistabil (flip-flop)
- Viteza mai mare ca la DRAM
- Costa mai mult

Memoria nu tine pasul cu CPU: 
![alt text](image.png)
___

# Memoria Cache(1)

### CPU-Memory Bottleneck
Performanta calculatoarelor sete de obicei limitata de latimea 
de banda a memoriei si de latenta.
- Latenta - timpul necesar pentru un singur acces
- Latime de banda(numarul de accese pe unitatea de timp)

Nu este destul pur si simplu sa adaugam o memorie mai mare.
![alt text](image-1.png)
Trebuie gasit un echilibru.

### Organizarea ierarhica a memoriei
CPU <\==> Memorie mica si rapida(RF, SRAM) <==> Memorie mare si lenta

#### Analogie pentru a intelege Cache-ul mai bine
![alt text](image-2.png)
#### Cache Hit\/Miss rate formula
![alt text](image-3.png)

#### Performantele unui sistem cache pe doua niveluri
![alt text](image-4.png)
Se rezolva cu formula: 
$$C_{eff} = C_{fast} + p*(1 - h_{1})[C_{medium} + (1 - h_{h2})*C_{slow}]$$

#### Parametrii memoriei Cache
- Cache size
- Block or cache-line size (cate date se aduc la un transfer din mem principala in cache)
- Placement policy (Determinarea unde o line cache e plasata)
- Replacement policy (Determinarea blocului din cache ales pentru suprascriere)
- Write policy - Determina data actualizarile in cache suntr transmise imediat memoriei principare (*write-through*) sau blocurile modificate sunt copiate inapoi in memoria principala atunci cand trebuie copiate (*write-back* sau *copy-back*)

*Comentariu*: Din ce am inteles daca avem mai multe valori dintr-un vector in cache, la modificare acestea sunt modificate in cache si daca avem write-through modificarile se propaga instant in RAM si atunci cant trebuie inlocuite aceste date in cache atunci are loc scrierea in RAM (write-back sau copy-back).

**Vizualizare interesanta a sabloanelor de acces la memorie** 
![alt text](image-6.png)

**Localitate temporala:** - Daca o valoare este accesata acum cel mai probabil o sa fie accesata si in viitorul apropiat.
**Localitate spatiala:** - Daca o adresa din memorie este accesata foarte probabil ca programul sa acceseze si adresele alaturate din aceasta zona de memorie.


#### Tipuri de miss din cache

- **Compuldosry misses:** Daca avem fectching on demand, primul acces la memorie intotdeauna va fi un miss. Insa daca avem o politica de pre-fetching unele din aceste miss-uri pot fi evitate.
- **Capacity misses:** Trebuie sa ne debarasam de date pentru a face loc altora(nu avem *capacity*)
- **Conflict misses:** Ocazional, exista spatiu ocuapte de date care sunt inutile, dar strategia de alocare/mapare ne forrteaza sa invalidam intrari utile pentru a aduce date noi.(**Aici si not so clear**)

Primele doua tipuri de miss sunt mai mult sau mai putin previzibile. Al treilea timp este influentat de strategia de mapare. Tipuri de mapare: **Directa** si **Set-Asociativa**

#### Cum plasam liniile? Care e faza cu complet asociativ, set asociativ, mapat direct
![alt text](image-7.png)

#### Politica de inlocuire
- Random 
- Least-Recently-Used (LRU)
- First in First-Out (FIFO) Round-Robin
  - La cache urile complet-asociative
- Not-Most-Recently-Used (NMRU)
  - FIFO, cu exceptia blockurilor cel mai recent folosite

**Ce este un efect de ordin secundar. De ce?**
//TODO

**Block** - unitatea de transfer dintre cache si memoria principala(a.k.a. *Line*).

#### Imbunatatirea performantelor memoriei cache
![alt text](image-8.png)
___

# Memoria Cache(2)
==!! **Reminder**:SRAM este folosit pentru memoria cache==

#### Dimensiunile liniilor si localitatea spatiala

**O linie in cache:**
TAG | Word0 | Word1 | Word2 | Word3 | - 4 word line (need 2 bit for offset in line)

Care sunt avantajele la o linie de dimensiuni mai mari? 
- overhead mai mic la tag-uri
- exploateaza transferurile in rafala din DRAM
- Exploateaza transferurile in rafala pe magistralele de date
  
Care sunt dezavantajele? 
Mai putine linii => mai multe conflicte. Poate sa iroseasca memoria de banda

#### Imbunatatirea performantei memoriei cache

AMAT(Average memory access time) = Hit time + Miss rate x Miss penalty
Pentru a imbunatati performanta avem nevoie de: 
- Reducrearea hit time (timpul de verificare daca data e in cache sau nu)
- Reducerea miss rate
- Reducerea miss penalty (overhead-ul cand avem un cache miss)

- Cache de dimensiuni mari: 
  - +Reduce miss-urile de capacitate si de conflict
  - -Creste hit time
- Asociativitate marita:
  - +Reduce conflict misses
  - -Poate sa mareasca hit time
- Linii de dimensiuni mai mari:
  - +Reduce miss-urile obligatorii si de capacitate(la reload) 
  - -Creste miss-urile de conflict si penalty pentru un miss

#### Prefetching
Presupune care vor fi urmatoarele accese la memorie si incarca acele date
in cache. (Accesele la instructiuni sunt mai usor de prezis decat instructiunile la date)

Variante de prefetching: 
- Hardware 
- Software 
- Mixed

Challenge-uri care apar cand ne dorim sa facem prefetching: 
- Ar trebui sa mareasca hit rate-ul
- nu trebuie sa ajung prea tarziu(dar nici prea devereme)
- Poluarea cache-ului si a latimii de banda
  
___
# Memoria virtuala

Mai demult aveam doar adrese fizice pe un sistem de calcul, iar adresele dintr-un 
program depindeau de locatia unde programul era incarcat. Pentru programatori era 
mai ok sa scrie programe independente de locatie. Aceasta poate fi obtinut prin linker
si/dau loader care modifica adresele atunci cand creeaza imaginea in memorie a programului
respectiv.

**Adrese Virtuale:** 
- Programele folosesc adresele virtuale
- CPU translateaza adresele virtuale in adrese fizice
- Swapping

**Protectia memoriei:** 
- Fiecare program are propria mapare de la adrese vrituale la adrese fizice.
- Doua programe pot folosi aceleasi adrese virtuale. (Nu trebuie sa ma gandesc
  daca vreo aplicatie de pe calculator mai foloseste adresa de care vreau eu sa ma
  folosesc)
- Programele nu trebuie sa stie de faptul ca mai exista alte programe care ruleaza 
  in acelasi timp

**TLB** - Tabela de pagini se afla si ea in memorie si la un acces de memorie ar trebui sa se 
parcurga tabela de pagini pentru translatare, si doar dupa asta sa se acceseze pagina fizica.
De aceea se foloseste TLB, un cache care tine minte mapari dintre adrese fizice in adrese vrituale.

**Problemele pe care le rezolva adaugarea Spatiului Virtual de Adrese**
- Unde incape totul? Avem adrese de 64 de biti adica putem accesa un miliard de giga
  cu atatea adrese, dar memoria fizica este de doar cativa GB. Respectiv ce ne facem
  daca un proces vrea sa foloseasca 0xDEADBEEFCAFEBABE?
- Ce merge unde? unde plasam in memorie toate zonele de memorie a tuturor proceselor
- Protectie. Doua procese sa nu poata accesa aceeasi zona de memorie.
- Partajarea memoriei. Trebuie sa putem sa partajam memoria intre procese

==Note: MMU verifica cache-ul==

**Page Hit steps**
1. Procesorul trimite adresa virtuala la MMU
2. MMU face fetch la PTE din tabela de pagini in memorie
3. MMU trimite adresa fizica la cache/memorie
4. Cache/memoria trimite cuvantul de date la procesor

**Page Fault**
1. Procesorul trimite adresa virtuala la MMU
2. MMU face fetch la PTE din tabela de pagini in memorie
3. Valid bit este 0, MMU declanseaza page fault exception
4. Hndler-ul identifica vicctima (si, dac ae "murdara", o pagineaza pe disk)
5. Handler-ul aduce o noua pagina si actualizeaza PTE in memorie
6. Handler-ul face return la procesul, original restartand instructiunea care a generat exceptia.

**Interactiunea MMU, TLB, Cache/Memory**
![alt text](image-9.png)
Aici avem un TLB hit.

**Concluzii memoria virtuala**
- Memoria virtuala mareste capacitatae
- Avem un subset de pagini virtuale in memoria fizica
- **Tabela de pagini** mapeaza paginile virtuale pe pagini fizice - translatarea de adrese 
- **TLB** mareste viteza cu care se fac translatiile adreselor
- Tabele de pagini diferite pentru programe diferite asigura **protectia memoriei**


___ 

# Dispozitive periferice