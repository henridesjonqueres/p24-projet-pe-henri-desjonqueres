---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.16.4
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<h1 style="text-align: center;">
    <strong>UE12 p24 Projet PE.</strong>
</h1>


```python
## Importation des bibliothèques
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

Pour étudier ces fichiers, la première étape est de les importer comme fichier pandas :

```python
cities = pd.read_csv('cities.csv')
countries = pd.read_csv('countries.csv')
weather = pd.read_csv('daily-weather-cities.csv')
```

Avant de proposer une analyse des données, il est également nécessaire de vérifier les informations que contiennent ces tableaux :

```python
print("Aperçu des données des villes :")
print(cities.head(2), cities.info())

print("\nAperçu des données des pays :")
print(countries.head(2), countries.info())

print("\nAperçu des données météorologiques :")
print(weather.head(2), weather.info())
```

Ces 3 tableaux sont donc une collection de donnés géographiques, démographiques et météorologiques de différentes villes et pays du monde entier. On peut notamment représenter l'ensemble des villes présentes grâce au tableau cities :

```python
def tracer_carte_villes():
    plt.figure(figsize=(10, 8))
    plt.scatter(cities['longitude'], cities['latitude'], alpha=0.6, c='red')
    plt.title('Localisation des villes')
    plt.xlabel('Longitude')
    plt.ylabel('Latitude')
    plt.show()

tracer_carte_villes()
```

Mon **idée** est donc de proposer des cartes interactives : une représentant toutes les villes du monde et l'autre les pays, sur lesquels il serait possible de cliquer pour obtenir les différentes informations disponibles concernant la zone choisie. (si cela n'est pas très clair, un exemple est donné à la fin.)

Malheureusement, la réalisation d'une carte interactive me semble un peu trop ambitieuse au vu de mon niveau. Par ailleurs, les données n'étant pas complètes (par exemple, les relevés de températures ne sont disponibles que pour quelques villes.).

Je me contenterai donc de proposer quelques fonctions utiles à ma carte interactive afin de mettre en forme les données fournies, à savoir :
- une fonction pour tracer les cartes
- une fonction pour tracer la température et calculer la température moyenne


**Première étape** : les cartes
Mon but est de représenter les villes et les pays proportionnellement à leur population. 
- Pour les villes, je n'ai pas la population, nous devrons donc nous contenter de la fonction tracer_carte_villes
- Pour les pays, je vais les représenter par un cercle donc le centre sera leur capitale et le rayon proportionnel à leur population. 

```python
def tracer_carte_pays():
    capitals = countries[['country','capital', 'capital_lat', 'capital_lng', 'population']].dropna()  #on ne sélectionne que ce qui nous intéresse

    # Tracer les cercles
    plt.figure(figsize=(12, 10))
    plt.scatter(capitals['capital_lng'], capitals['capital_lat'], s=capitals['population'] / 100000, alpha=0.7, c='blue', edgecolor='k')
    plt.title('Carte du monde suivant la populaion')
    plt.xlabel('Longitude')
    plt.ylabel('Latitude')

    # mettre le nom des pays 
    for i in range(len(capitals)):
        plt.text(capitals.iloc[i]['capital_lng'] + 0.5,capitals.iloc[i]['capital_lat'] + 0.5, capitals.iloc[i]['country'], fontsize=8)
 
    plt.show()  

tracer_carte_pays()
```

**Deuxième étape** : Les informations
Maintenant, que nous avons nos cartes, nous devons nous intéresser à ce qu'elles vont afficher lorsque nous cliquerons dessus. Il y a deux cas :
- Les informations ont simplement besoin d'être extraite des fichiers (comme la taille de la population, la surface du pays, le nom de la capitale, ...). Nous ne traiterons pas ce cas ici, car il s'agit seulement d'aller chercher l'information dans un tableau.
- Les informations ont besoin d'être traité (comme les températures, le niveau de la mer...).


**Les températures** : Le but est de pouvoir tracer la courbe de température correspondant à la ville concernée et sa température moyenne. Pour cela, nous allons travailler avec le fichier weather :

```python
def moyenne(Tmax, Tmin):
    return ( Tmax + Tmin ) / 2


def courbe_temperature(ville):
    if ville not in weather['city_name'].values:
        print(f"Aucune donnée trouvée pour la ville : {ville}")
        return

    else:
        weather_ville = weather[weather['city_name'] == ville]  # sélection de la partie de tableau intéréssante
        
        Date_numpy = weather_ville['date'].to_numpy()  # conversion de la colone date en tableau numpy
        Tmax_numpy = weather_ville['max_temp_c'].to_numpy()  # conversion de la colone max_temp_c en tableau numpy
        Tmin_numpy = weather_ville['min_temp_c'].to_numpy()  # conversion de la colone min_temp_c en tableau numpy

        Tmoy_numpy = moyenne(Tmax_numpy,Tmin_numpy)  # Calcule de la température moyenne

        # Tracer la courbe de température pour la ville choisie
        plt.figure(figsize=(12, 6))
        plt.plot(Date_numpy, Tmoy_numpy, color='blue')
        plt.title(f"Courbe de température moyenne pour {ville} du {Date_numpy[0]} au {Date_numpy[-1]}")
        plt.xlabel("Date")
        plt.ylabel("Température moyenne (°C)")
        plt.xticks(np.arange(0, len(Date_numpy), 365*5))  # Tous les 365 indices
        plt.xticks(rotation=45)
        plt.grid(True)
        plt.tight_layout()
        plt.show()
        return np.nanmean(Tmoy_numpy)

```

```python
courbe_temperature("Vienna")
```

**Conclusion** : nous avons donc nos cartes et nos fonctions pour traiter les données (ici la température). L'idée serait alors d'utiliser ces différents programmes pour pouvoir créer une carte interactive affichant les différentes données disponibles. Malheureusement, cela va bien au-delà de mes connaissances. L'image suivante illustre le rendu final.
Bien évidemment, il est possible de créer d'autres programmes afin de traiter d'autres données et avoir plus d'information.

```python
image=plt.imread("Image_finale.png")
plt.axis('off')
plt.imshow(image)
```
