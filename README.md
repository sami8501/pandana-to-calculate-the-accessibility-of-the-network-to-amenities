# Pandana - Analyse de l'accessibilité aux aménités urbaines

Pandana est une bibliothèque Python puissante pour l'analyse de l'accessibilité du réseau urbain. Ce README se concentre sur l'utilisation de Pandana pour étudier l'accessibilité aux aménités urbaines, en utilisant les données d'OpenStreetMap (OSM).

## Installation

```bash
pip install pandana matplotlib
```

## Fonctionnalités principales

### 1. Importation des bibliothèques nécessaires

```python
import pandana
import matplotlib.pyplot as plt
from pandana.loaders import osm
```

### 2. Définition de la zone d'étude et des paramètres

```python
bbox = [47.118292, -1.694756, 47.292525, -1.410485]  # Nantes, France
amenity = 'restaurant'
distance = 1500  # distance maximale en mètres
```

### 3. Récupération des données OSM

```python
pois = osm.node_query(bbox[0], bbox[1], bbox[2], bbox[3], tags='"amenity"="{}"'.format(amenity))
network = osm.pdna_network_from_bbox(bbox[0], bbox[1], bbox[2], bbox[3])
```

### 4. Préparation du réseau pour l'analyse

```python
network.precompute(distance + 1)
network.init_pois(num_categories=1, max_dist=distance, max_pois=7)
network.set_pois(category='my_amenity', x_col=pois['lon'], y_col=pois['lat'])
```

### 5. Calcul de l'accessibilité

```python
access = network.nearest_pois(distance=distance, category='my_amenity', num_pois=7)
```

### 6. Visualisation des résultats

```python
bmap, fig, ax = network.plot(access[1], bbox=bbox, plot_kwargs=plot_kwargs, fig_kwargs=fig_kwargs)
plt.show()
```

## Analyses avancées

### 1. Agrégation de réseau

```python
pois['node_ids'] = network.get_node_ids(pois['lon'], pois['lat'])
network.set(pois['node_ids'], variable=pois['capacity'], name='capacity')
capacity_sum = network.aggregate(distance=distance, type='sum', decay='linear', name='capacity')
```

### 2. Visualisation de l'agrégation

```python
bmap, fig, ax = network.plot(capacity_sum, bbox=bbox, plot_type='scatter', plot_kwargs=agg_plot_kwargs, 
                             fig_kwargs=fig_kwargs, cbar_kwargs=cbar_kwargs)
plt.show()
```

## Exemples d'utilisation

Le notebook fourni illustre :
- La récupération des données de restaurants à Nantes, France
- Le calcul de l'accessibilité aux restaurants dans un rayon de 1500 mètres
- La visualisation de la distance au restaurant le plus proche
- L'agrégation de la capacité des restaurants dans la zone d'étude

## Personnalisation

Le code peut être facilement adapté pour étudier d'autres types d'aménités ou d'autres zones géographiques en modifiant les paramètres `amenity` et `bbox`.

## Licence

Pandana est distribué sous licence BSD 3-Clause.

## Contact

Pour plus d'informations, visitez la [documentation officielle de Pandana](https://udst.github.io/pandana/).
