# PRACT1-WebScraping: Articles sobre recerca neurocientífica de Neuroscience News

**Assignatura:** Tipologia i cicle de vida de les dades
**Titulació:** Màster Universitari en Ciència de Dades, Universitat Oberta de Catalunya (UOC)
**Semestre:** Tardor 2026

## Integrants
        
- Pau Ramos
- Judit Bonachí

## 1. Descripció

Aquest repositori conté el codi de la Pràctica 1: un *web scraper* que extreu les metadades dels articles de recerca neurocientífica publicats a [Neuroscience News](https://neurosciencenews.com/), a partir del llistat de l'àrea de neurociència (<https://neurosciencenews.com/neuroscience-topics/neuroscience/>) i de la pàgina de detall de cada article.

El resultat és un dataset CSV de prop de 2000 registres i 13 camps, pensat per a anàlisi de text i de temes (àrees, etiquetes, institucions, editorials a partir del DOI, evolució temporal).

Neuroscience News no és una revista científica, sinó un mitjà que difon recerca ja publicada. La fiabilitat que s'espera del dataset és la de les metadades (dates, fonts, categories, DOI), no la dels resultats científics.

## 2. Arxius del repositori

```text
PRACT1-WebScraping/
├── dataset/
│   └── output.csv        # Dataset generat (CSV, UTF-8, separador ;)
├── source/
│   ├── scraper.py        # Classe UOCScraper: lògica de recollida i extracció
│   └── main.py           # Punt d'entrada que crida UOCScraper
├── requirements.txt      # Dependències i versions (pip freeze)
├── .gitignore
└── README.md
```

## 3. Com utilitzar el codi

Cal tenir Python 3 instal·lat (es recomana una versió recent, 3.10 o superior).

1. Clonar el repositori i entrar a la carpeta:

   ```bash
   git clone https://github.com/PauRF03/PRACT1-WebScraping.git
   cd PRACT1-WebScraping
   ```

2. (Opcional) Crear i activar un entorn virtual:

   ```bash
   python -m venv venv
   source venv/bin/activate      # Windows: venv\Scripts\activate
   ```

3. Instal·lar les dependències:

   ```bash
   pip install -r requirements.txt
   ```

4. Executar el scraper:

   ```bash
   python source/main.py
   ```

El CSV resultant es desa a la carpeta `dataset/`.

> Amb un interval de 10 segons entre peticions, una recollida completa triga força hores. El procés escriu cada fila al CSV a mesura que l'obté i es pot reprendre sense repetir els articles ja desats.

## 4. Funcionament i bones pràctiques

El procés té dues fases:

1. **Llistat:** el programa recorre les pàgines de la secció de neurociència, extreu els enllaços, conserva només els que corresponen a una notícia (URL d'un sol segment que acaba en un identificador numèric, filtrada amb una expressió regular) i localitza l'enllaç a la pàgina següent, fins al final del llistat o fins al límit indicat.
2. **Detall:** cada URL trobada es visita i el seu HTML s'analitza amb BeautifulSoup per extreure'n els camps.

Criteris ètics i tècnics aplicats:

- **Identificació transparent:** una sola sessió HTTP amb un User-Agent propi, `UOC-TCVD-Practica1`, sense simular cap navegador.
- **Respecte pel servidor:** s'ha consultat el `robots.txt` del lloc i s'espera 10 segons entre peticions, amb una petita variació aleatòria (`time.sleep(self.delay * random.uniform(0.8, 1.2))`).
- **Tolerància a fallades:** davant d'un error temporal es fan fins a 3 reintents amb una espera creixent. Si un article continua fallant, es registra i es salta sense aturar la recollida.
- **Persistència:** cada fila s'escriu al CSV i es fa `flush()` immediatament, de manera que no es perden dades si el procés s'interromp.
- **Només dades públiques:** no cal autenticació. No es recullen dades personals dels autors dels estudis ni contactes de premsa. Només es desen les metadades i el resum de cada notícia (no el text complet), i es conserven la URL i el DOI perquè es pugui consultar l'original i atribuir-ne l'autoria.

## 5. Descripció del dataset

- **Format:** CSV, codificació UTF-8, separador de columnes `;`.
- **Llistes:** `categories` i `tags` es guarden en una sola cel·la separades per `|`.
- **Valors absents:** si una dada no existeix a l'article (per exemple, el DOI o la institució), la cel·la queda buida.

| Camp | Tipus | Descripció |
| :--- | :--- | :--- |
| `article_id` | Numèric | Identificador numèric de la notícia |
| `url` | Text | Adreça de l'article |
| `title` | Text | Títol de l'article |
| `date_published` | Data i hora | Data de publicació (format ISO 8601) |
| `section` | Categòric | Àrea temàtica principal (s'exclou «Featured», que és una marca editorial i no un tema) |
| `categories` | Categòric | Totes les categories de l'article, separades per `\|` |
| `tags` | Categòric | Etiquetes temàtiques, separades per `\|` |
| `n_tags` | Numèric | Nombre d'etiquetes |
| `read_time_min` | Numèric | Temps de lectura estimat, en minuts (extret d'una etiqueta `<meta>`) |
| `n_related_links` | Numèric | Nombre d'enllaços a altres articles dins de la pàgina |
| `summary` | Text | Resum de la notícia |
| `source_org` | Categòric | Institució que ha generat la recerca |
| `doi` | Text | DOI de l'estudi científic original |

Dificultat tècnica destacable: el web marca tant les categories com les etiquetes amb `rel="tag"`. Les categories es llegeixen de la línia de signatura de l'article i les etiquetes s'obtenen restant-les.

## 6. Propietat intel·lectual i llicència

Els drets dels continguts originals pertanyen a Neuroscience News LLC i a les institucions i revistes que originen els estudis. El dataset només recull metadades i resums, amb enllaç a l'origen de cada registre.

El dataset es publica sota la llicència **Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)**, que manté la reutilització dins l'àmbit acadèmic i no lucratiu en què s'ha fet la recollida.

## 7. DOI del dataset a Zenodo

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23953997.svg)](https://doi.org/10.5281/zenodo.23953997)

<https://doi.org/10.5281/zenodo.23953997>
