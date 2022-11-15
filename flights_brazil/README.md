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
```



## Assortativity
In a nutshell, assortivity is a metric that seeks to quantify how much the network components are related based on their similar attributes. That is, whether elements with similar characteristics tend to relate more.
The assortative coefficient is between [-1, 1] networks with values below between [-1,0 ] are non-assortative and above 0 and less than 1 assortative.
To obtain the assortative coefficient in relation to the regions, simply:
```python
nx.attribute_assortativity_coefficient(air_trafic, 'region')
```
resulted in : 0.36858192263245576
Assortativity can be seen from the graph
![CircosPlot](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/circusplot.png)

The coefficient of assortative between regions can be visualized by simply
```python
nx.attribute_mixing_matrix(air_trafic,'region')
```
```
array([[0.13652482, 0.02393617, 0.01983599, 0.00742465, 0.02870124],
       [0.02393617, 0.04986702, 0.0106383 , 0.01252216, 0.03291223],
       [0.01983599, 0.0106383 , 0.0802305 , 0.01340869, 0.05529699],
       [0.00742465, 0.01252216, 0.01340869, 0.07180851, 0.03989362],
       [0.02870124, 0.03291223, 0.05529699, 0.03989362, 0.17242908]])
````

## Bivariate analysis
Nodes in a network have the degree property, which shows how many neighbors it has . However, it is also possible to perform an analysis regarding the degrees of assotativity which is based on whether nodes with high degrees tend to connect. That said, it is possible to analyze whether the correlation between the degrees of nodes and whether it is assortative with respect to nodes
Para vizualiar isto basta:
```python
degree, av_neigh_degree = zip(*nx.average_degree_connectivity(air_trafic).items())
degree = np.array(degree)
av_neigh_degree = np.array(av_neigh_degree)

x = degree
y = av_neigh_degree

# fit a linear curve an estimate its y-values and their error.
a, b = np.polyfit(x, y, deg=1)
y_est = a * x + b
y_err = x.std() * np.sqrt(1/len(x) +
                          (x - x.mean())**2 / np.sum((x - x.mean())**2))

fig, ax = plt.subplots()
ax.set_xlabel('Node Degree')
ax.set_ylabel('Average neighbhor degree')
ax.grid()
ax.plot(x, y_est, '-')
ax.fill_between(x, y_est - y_err, y_est + y_err, alpha=0.2)
ax.plot(x, y, 'o', color='tab:blue')

````
![RLPlot](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/bivariate_analisis.png)

It is possible to obtain the degree assortativity coefficient:
```python
nx.degree_assortativity_coefficient(air_trafic)
```

From the graph, it is possible to observe that the coefficient is negative, that is, the network shows that as the degree of the nodes increase, they tend to connect with nodes of lower degree.

## Paths
Through networks it is possible to know the shortest path from one node to another.
Para isto, foi escolhido arbitrariamente uma ciadade de cada regição para vizualizar o caminho mais curto para passar por todas esla na seguinte ordem:
 - city 1 (North) to city 2 (South)
 - city 2 (South) to city 3 (Northeast)
 - city 3 (Northeast) to city 4 (Midwest)
 - city 4 (Midwest) to city 5 (Southeast)

Where,
city1 is the node 'SWBI' = 'BARREIRINHA',
city2 is the node 'SSAE' = 'ARROIO GRANDE',
city3 is node'SNMU' = 'MUCURI',
city4 is the node 'SWDE' = 'SÃO MIGUEL DO ARAGUAIA',
city5 is node 'SDDM' = 'RIO DE JANEIRO'

It is possible to know the name of the city by simply:

```python
def code_to_name(code):
  for i, data in list(air_trafic.nodes(data=True)): 
    if i == code:
      return data['name']
```

```python
if nx.has_path(air_trafic,"SWBI","SSAE"):
  path = nx.shortest_path(air_trafic,"SWBI","SSAE")
list(map(code_to_name, path))

if nx.has_path(air_trafic,city2, city3 ):
  path = nx.shortest_path(air_trafic,city2,city3)
print(list(map(code_to_name, path)))
print(path)

if nx.has_path(air_trafic,city3, city4 ):
  path = nx.shortest_path(air_trafic,city3,city4)
print(list(map(code_to_name, path)))
print(path)

if nx.has_path(air_trafic,city4, city5 ):
  path = nx.shortest_path(air_trafic,city4,city5)
print(list(map(code_to_name, path)))
print(path)
```
- Azul  'BARREIRINHA' até  'ARROIO GRANDE'
- Vermelho   'ARROIO GRANDE' até  'MUCURI'
- Laranja 'MUCURI', até  'SÃO MIGUEL DO ARAGUAIA'
- Verde  'SÃO MIGUEL DO ARAGUAIA' até   'RIO DE JANEIRO'

O plot de toda a red  abaixo mostra os caminhos:
![PlotPath](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/paths_simulation.png)
 
## Clustering Coefficient
The clustering coefficient is a metric which measures represents how much a node tends to be clustered. For example, if a reference node (or ego node) connects to N nodes and all these N are also connected, the clustering coefficient is maximum, that is, 1. This metric can be measured using the number of triangles that exist between the nos. It is also possible to calculate the global average of the coefficients.

Assim sendo, foi cálculado o  average clustering de toda rede e para cada região.
```python
nx.average_clustering(air_trafic)
```
Then, the coefficient for each node was extracted.
```python
cc = nx.clustering(air_trafic)
```
The clustering coefficient for the region can be seen below

![HISTPLOT](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/hist_of_cc.png)

![CC_NOR](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/grafh_of_nordeste.png)
![CC_nrte](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/grafh_of_norte.png)
![CC_sud](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/grafh_of_sudeste.png)
![CC_ce](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/grafh_of_centro_oeste.png)
![CC_sul](https://github.com/VictorNGomes/DCA0209_Algorithms_and_data_structure_2/blob/main/flights_brazil/images/grafh_of_sul.png)


