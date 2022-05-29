# Pràctica 2 AP2 - MetroNyam

**NomDelBot** et permetrà anar a cualsevol restaurant en el menor temps possible! 🍽

## Introducció

Aquest projecte tracta en implementar un bot de Telegram que ajudi a l'usuari a anar fins al restaurant de Barcelona que desitji en el menor temps possible ja sigui a peu o en metro.

**Inserir Imatge Conversa**
<!-- ![conversa.png](conversa.png) -->

Per tal de fer-ho, s'han creat diversos mòduls connectats entre ells, cadascún d'ells encarregat d'algunes de les funcionalitats que inclou el projecte. Els mòduls implementats han estat el `restaurants.py`, el `metro.py`, el `city.py` i el `boy.py`. Tots ells d'explicaran en detall més endevant.

## Indicacions i funcionament del projecte

Per tal de poder utilitzar el bot de Telegram desenvolupat cal que l'usuari tingui descarregada l'aplicació Telegram en el dispositiu que vagi a utilitzar. Un cop descarregada, l'usuari haurà de crear-se un compte i cercar al buscador el nom del bot, `Escriure_nom_del_bot`.

Un cop es tingui el bot a l'apartat de xats, ja es podrà començar a utilitzar correctament seguint les següents indicacions.

En primer lloc, cal remarcar que per tal de poder iniciar la conversa amb el bot, aquest s'haurà d'executar. Únicament quan el bot estigui en actiu es podra utilitzar. Un cop en actiu, es pot iniciar la conversa amb el bot amb la comanda `/start`. El bot fa una sèrie d'inicialitzacions en executar-se, pel que es possible que, sobretot el primer cop, trigui una estona en rebre la comanda `/start`.

A continuació, amb la conversa ja iniciada, es podrà seguir utilitzant el bot amb alguna de les comandes disponibles.

### Comandes disponibles

- `/help` Mostra enviarà un missatge a l'usuari amb totes les comandes disponibles i informació sobre el que fan i com utilitzar-les.
- `/author` Mostra el nom de les dues persones que han dut a terme el projecte.
- `find <query>` Mostra una llista amb els restaurants que satisfacin la cerca donada (Fins a un màxim de 12).
- `/info <number>` Mostra informació extra sobre el restaurant especificat. El nombre que s'ha de passar com a paràmetre és el que es mostra amb la comanda `/find <query>`.
- `/guide <number>` Mostra, en una imatge, el camí més curt per anar al restaurant especificat a peu i/o en metro. El nombre que s'ha de passar com a paràmetre és el que es mostra amb la comanda `/find <query>`.

Algunes d'aquestes comandes necessiten que l'usuari comparteixi la seva **localització**. És molt important seguir aquesta indicació ja que sinó no s'executarà la comanda. De totes formes, el bot ja indica el motiu de l'error amb un missatge i l'informa de com continuar.

## Requeriments

Per tal de poder utilitzar el bot i totes les seves funcionalitats de forma correcta cal, abans de res, instal·lar un seguit de llibreries. Totes aquestes llibreries es troben al fitxer `requeriments.txt`, que es troba juntament amb la resta de fitxers del projecte.

Per tal d'instal·lar les llibreries del document, cal executar la següent comanda a la terminal:

`pip3 install nom_llibreria` o bé `pip install nom_llibreria`

## Funcionalitat dels mòduls
### Mòdul `restaurants`

El mòdul restaurants és l'encarregat de llegit el fitxer `restaurants.csv` i passar el seu conttingut a un `Data Frame` utilitzant `pandas` amb el qual podrem treballar correctament. També s'encarrega de realitzar la cerca que l'usuari demani.

La informació de cada restaurant que llegim ho guardarem en una variable de tipus `Restaurant` que s'ha definit de la següent forma.

```python3
@dataclass
class Restaurant: ...
```
De la mateixa forma hem definit una variable de tipus `Restaurants` que denota una llista de `Restaurant` de la següent forma:

```python3
Restaurants: TypeAlias = List[Restaurant]
```

A més inclou diverses funcions que altres mòduls utilitzaran per tal de dur a terme algunes funcions del projecte.

La funció `read` s'encarrega de tractar la informació del `Data Frame` mencionat abans.

```python3
def read() -> Restaurants: ...
```

Per tal de trobar els restaurants que satisfan una determinada cerca utilitzem la funció `find`.

```python3
def find(query: str, restaurants: Restaurants) -> Restaurants: ...
```

A més, s'ha creat una funció que encapçala les dues anteriors. Aquesta funció crida a la funció `read` i després fa la cerca sobre la llista que hagi retornat `read`.

```python3
def get_restaurants(query: str) -> Restaurants: ...
```

### Mòdul `metro`

El mòdul `metro` és l'encarregat de generar el graf del metro de Barcelona. Per fer-ho, llegeix dos fitxer `estacions.csv` i `accessos.csv` a partir dels quals genera el graf.

El graf del metro de Barcelona es guarda en una variable de tipus `MetroGraph` definida de la següent forma usant el paquet `NetworkX` de Python (cal notar que s'utilitza `nx` com a abreviació del paquet).

```python3
MetroGraph: TypeAlias = nx.Graph
```

La informació obtinguda a partir d'aquests fitxers es guarda en dues variables diferents de tipus `Station` i `Access` definides de la següent forma.

```python3
@dataclass
class Station: ...

#dataclass
class Access: ...
```
De la mateixa forma que s'ha fet amb els restaurants, s'han definit dues variables per denotar les llistes que continguin variables de tipus `Station` i `Access`.

```python3
Stations: TypeAlias = List[Station]
Accesses: TypeAlias = List[Access]
```

Per tal de llegir els fitxers de tipus `csv`mencionats s'utilitzen les funcions `read_stations` i `read_accesses`

```python3
def read_stations() -> Stations: ...
def read_accesses() -> Accesses: ...
```

A més, el mòdul inclou tres funcions principals `get_metro_graph`, `show` i `plot`.

```python3
def get_metro_graph() -> MetroGraph: ...
def show(g: MetroGraph) -> None: ...
def plot(g: MetroGraph, filename: str) -> None: ...
```

La funció `get_metro_graph` s'encarrega de la creació del graf del metro. Aquesta funció s'explicarà més en detall a continuació. La funció `show` mostra, en el navegador, una finestra interactiva en la que podrem observar el graf de metro. La funció `plot` genera una imatge del metro de Barcelona usant `Static Map` i la desa amb el nom indicat a la variable `filename`.

La funció `get_metro_graph` esta composada per diverses funcions que simplifiquen la lectura i estructura.

Les funcions `add_stations` i `add_accesses` afegeixen les estacions i els accessos al graf com a nodes identificats amb un `ID` únic.

```python3
def add_stations(metro: MetroGraph, stations_list: Stations) -> None: ...
def add_accesses(metro: MetroGraph, accesses_list: Accesses, stations_list: Stations) -> None: ...
```
A més, a l'hora, es van creant les arestes del graf amb les funcions següents.

```python3
def add_edge_tram(metro: MetroGraph, act_station: Station,
                  prev_station: Station) -> None: ...
def add_edge_link(metro: MetroGraph, node: Station,
                  act_station: Station) -> None: ...
def add_edge_access(metro: MetroGraph, station: Station,
                    act_access: Access) -> None: ...
```
Cadascuna de les tres funcions afegeix un tipus d'aresta diferent. Les de tram corresponen a la primer funció. Les d'enllaç corresponen a la segona funció. Per últim, les de tipus accès corresponen a la tercera funció.

A l'hora d'afegir qualsevol aresta, se li passen uns determinats atributs que seràn necessaris per altres funcions d'altres mòduls.

## Autors

Aïda Santacreu Perez i Ignacio Gris Martín

Universitat Politècnica de Catalunya, 2022
