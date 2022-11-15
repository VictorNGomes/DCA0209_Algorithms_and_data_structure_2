# Network analysis on the dataset of flights from Brazil

In this repository, a study of the Brazilian air network is carried out through network analysis. Network science seeks to understand complex systems by inspecting the relationships and iterations between their components <sub>[1]</sub>. With the help of the networkX package, the properties of these networks are extracted in order to understand how airports relate to each other and from different regions of Brazil.

## Data Pre-Processing 
The data was obtained through the repository below:
```bash
!git clone https://github.com/alvarofpp/dataset-flights-brazil.git

```
The attribute to be analyzed in the network are regions of Brazil, for this it was necessary to make a modification in the code so that each node of the network has the region attribute
````python
for index, row in df_airports.iterrows():
    G.add_node(row['code'],
               name=row['name'],
               country=row['country'],
               latitude=row['lat_geo_point'],
               longitude=row['lon_geo_point'],
               region=row['region'] # Added line
               )
````

For the extraction, creation of the dataset and the .graphml file, the step by step given in the repository was followed.
Loading graph
```python
air_trafic = nx.read_graphml('/content/drive/MyDrive/airport_traffic_analisis/dataset-flights-brazil/data/air_traffic.graphml')

````
em seguida foram removidos os nós que represetam o aeroportos fora do brasil

```python
regions= ['NORTE', 'NORDESTE', 'CENTRO-OESTE', 'SUDESTE', 'SUL']

for i, data in list(air_trafic.nodes(data=True)): 
  if data['region'] not in regions:
    air_trafic.remove_node(i)
  print(i,data)
````



## Assortativity
In a nutshell, assortivity is a metric that seeks to quantify how much the network components are related based on their similar attributes. That is, whether elements with similar characteristics tend to relate more.


## Bivariate analysis

## Paths

## Clustering Coefficient
