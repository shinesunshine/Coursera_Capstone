# Applied Data Science Capstone

## Table of contents
1. [Introduction](#intro)
2. [Chosen Approach](#approach)
3. [Data selected](#data)<br>
3.1. [Energy Classification data for buildings](#data_enpot)<br>
3.2. [Solar rooftop potential data](#data_solar)<br>
4. [Data preparation](#data_prep)<br>
4.1 [Data preparation: Energy Level Classification](#data_prep_enpot)<br>
4.2 [Data preparation: Solar rooftop potential data](#data_prep_solar)<br>
4.3. [Data preparation: Combining the two sources](#data_prep_combine)<br>
5. [Clustering the data](#clustering)<br>
6. [Conclusions and future work](#conclusion)<br>


## Capstone Project Decription

<a name="intro"></a>
### 1. Introduction

The learning objective for this project was to leverage a public API (Foursquare was suggested) in order to get some data on which to apply a data science approach in order to address a business problem. <br>

My favourite way of addressing these types of assigments is to try and link them to a real business problem, or a topic/area of interest I am currently interested in. <br>

Since sustainability and renewable enery has been in my personal top 10 for some time now, I have decided to see if I can mold this assigment such that it embraces my personal interests. Also, since I am lucky enough to live in the Danish society which has a remarably high focus on sharing data of public interest, I decided to try and give this assigment an even more personal touch by adding this extra dimension of locality. <br>

<a name="approach"></a>
### 2. Chosen Approach

I decided to see if I could find some data on solar rooftop energy potential for the buildings in the city I currently live (Copenhagen) and complement it with information regarding the buildings' energy classification in order to create an overview of the current state of afairs and perhaps dig out some interesting patterns. <br>

One use case that I envisioned would try to assist the municipality with insight on where their budget for energy refurbishment of buildings could best be spent.

<a name="data"></a>
### 3. Data selected

<a name="data_enpot"></a>
### 3.1. Energy Classification data for buildings

I managed to found one public company in Denmark called [https://sparenergi.dk/](https://sparenergi.dk/) which tries to guide people make more sustainable choices on the energy sources they use at home.

As a first step in this process, they provide [a page](https://sparenergi.dk/forbruger/vaerktoejer/find-dit-energimaerke) where you can insert an address and get the respective energy classification.

After some more digging around, I found out that they also have [a map](https://sparenergi.dk/demo/addresses/map) where such a overview is accessible so I decided to try and see if I can scrape it.

After a lot of digging, I found out what the parameters for the HTTP requests were. The most important one was a geographical polygon that described the area for which the information is to be fetched. 

So I defined a large enough polygon that should encapsulate the Municipality of Copenhagen and, in order to parallelize scrapring, I broke it down into a 200x200 grid. I then made a request for each of the individual polygons in this grid and combined the information into a single file.

Here is the large polygon I used for this process: 
![Polygon roughly enclosing Copenhagen](/Week_4/data/img/ibm_cph_map.PNG)


#### Data Format

The requests responses were collected into JSON formatted text files that these types of entries:

```json
{
  "status": "success",
  "insert_tms": "datetime.datetime(2019, 7, 15, 11, 1, 46, 421675)",
  "data": {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "id": "100268531",
        "geometry": {
          "type": "Point",
          "coordinates": [
            12.485214279217,
            55.66133371957
          ]
        },
        "properties": {
          "EnergyLabelClassification": {
            "value": "D"
          },
          "StreetName": {
            "value": "Vigerslevstræde"
          },
          "HouseNumber": {
            "value": "27C"
          },
          "ZipCode": {
            "value": "2500"
          },
          "CityName": {
            "value": "Valby"
          },
          "BBRUseCode": {
            "label": "Type",
            "value": "Række-, kæde-, eller dobbelthus"
          },
          "YearOfConstruction": {
            "label": "Opført",
            "value": "1972"
          },
          "HeatSupply": {
            "label": "Varme",
            "value": "Elektricitet"
          },
          "DEMOLink": {
            "value": "http://sparenergi.dk/forbruger/vaerktoejer/det-digitale-energimaerke#Adresse/Vigerslevstr%C3%A6de%2027C,%202500%20Valby%20(100268531)"
          }
        }
      }
    ]
  }
}    
```

We can see that there are some very useful pieces of information:
- coordinates
- EnergyLabelClassification
- StreetName
- HouseNumber
- ZipCode
- CityName
- YearOfConstruction
- HeatSupply

<a name="data_solar"></a>
#### 3.2. Solar rooftop potential data

The Municipality of Copenhagen shares a lot of interesting datasets (covering a wide array of topics) with the public. <br>

Among them there is one with [solar rooftop potential for buildings in Copenhagen](https://data.kk.dk/dataset/soldata-3d-kobenhavn).

The author also made a very good note on the data, pointing out that it should basically be used 'with a grain of salt'. Below is the Google Translate version of why this is so:
    
"Data is indicative only and gives a clue as to whether it can be a good idea with solar cells. The map does not take into account all shadows. For example, shadows from trees, chimneys, antennae, smaller twigs and bay windows are not included. If the roof of your property is flat, the roof may also be more suitable than the map shows. At the same time, does the map not show how the solar cells fit the architecture of the building and the area? Therefore, it is usually a good idea to get help from a counselor to make a more detailed assessment of the possibilities."

Still, it is way better than having nothing and easier to process than the alternative of trying to extract similar data from [this live map with rooftop solar potential](http://kbhkort.kk.dk/spatialmap?&selectorgroups=themecontainer%20bygninger%20detaljer&mapext=702689.6%206165734.4%20747310.4%206186265.6&layers=theme-startkort%20theme-disclaimer%20theme-bymodel_bygning_solgrupper_40&mapheight=807&mapwidth=1748&profile=solanalyser). I would have rather worked with this more updated data, but time was simply too limited.

##### Data Format

The file is basically a 7GB archived JSON file with the following format:

```json
{
  "name": "bymodel.SOLDATA_3D_KØBENHAVN",
  "type": "FeatureCollection",
  "crs": {
    "type": "name",
    "properties": {
      "name": "EPSG:25832"
    }
  },
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [
            [
              728157.043,
              6176689.234,
              26.181
            ],
            [
              728157.321,
              6176689.84,
              26.217
            ],
            [
              728157.527,
              6176689.586,
              26.181
            ],
            [
              728157.043,
              6176689.234,
              26.181
            ]
          ]
        ]
      },
      "properties": {
        "id": 1,
        "old_id": 1,
        "dgn_id": 76,
        "elementid": 1739045,
        "exposedseconds": 13398300,
        "shadowedseconds": 3824100,
        "directinsolation": 405282.860341,
        "diffuseinsolation": 571313.153412,
        "byg_id": 655982500039710,
        "horizontal": 143.972626614887,
        "vertical": 6,
        "area": 0.0983159397859777,
        "samletsol": 976596.013753,
        "solgruppe1": 2,
        "solgruppe2": 2,
        "solgruppe3": 3
      }
    }
  ]
}
```
Basically we need to extract the features from the features list, grab the coordinates and (to keep it simpler) the 3 pieces of information which make up the 'summary' of the photovoltaic (PV) potential: 
- "solgruppe1"
- "solgruppe2"
- "solgruppe3"

Before deciding on this approach, I actually did cross check to see if this was in line with what I could see in the other source of information (the live map) and indeed, they seem to agree more or less. This way I also found out that the higher the grading on the various "solgruppe" parameter, the higher the actual PV rating is (marks of 4 correspond to red polygons on the map -- and these are rated as "Meget god" meaning very good. The opposite is true for marks in the lower end of the scale.) 

<a name="data_prep"></a>
### 4. Data preparation

<a name="data_prep_enpot"></a>
#### 4.1. Data preparation: Energy Level Classification

There are three separate notebooks that deal with scraping the energy level classifications and cleaning the scraped results:
- [1.1.EnergyClasses_Scraping.ipynb](1.1.EnergyClasses_Scraping.ipynb): scrapes a website for energy classes of addresses found within a geographical polygon. Does so in a parallelized way by breaking the larger polygon in a grid of smaller polygon and then creating a multiprocessing pool in which the spawned processes fire of requests individually until the entire grid is processed. The results are saved to JSON formatted files.
- [1.2.EnergyClasses_Consolidate.ipynb](1.2.EnergyClasses_Consolidate.ipynb): picks up the JSON formatted files, cleans them up and creates CSV files based on them.
- [1.3.EDA_energy_classes.ipynb](1.3.EDA_energy_classes.ipynb): does some more cleaning and extracts the geo-location data (representing the coordinates of the individual addresses) in a separate column.

<a name="data_prep_solar"></a>
#### 4.2. Data preparation: Solar rooftop potential data
There are two separate notebooks that deal with processing this data :
- [2.1.EDA_solar.ipynb](2.1.EDA_solar.ipynb): Splits the 7GB JSON formatted file in 400K line files such that we are better able to process them in paralell (also would have been impossible to load that into the memory of my machine using python). Does some cleaning and then saves the results into CSV files. 
- [2.2.EDA_solar.ipynb](2.2.EDA_solar.ipynb): Consolidates the previously created files.

<a name="data_prep_combine"></a>
#### 4.3. Data preparation: Combining the two sources
[3.Combine_energy_class_solar_potential.ipynb](3.Combine_energy_class_solar_potential.ipynb): attempts to match the two datasets together based on the smallest distance between the geographical point representing the coordinates of the address for which we have the energy level classification and the centroid of the polygon for which we have the solar rooftop information. There are several steps involved in this which are oulined in the notebook. 

<a name="clustering"></a>
### 5. Clustering the data
[4.Clustering.ipynb](4.Clustering.ipynb): uses KMeans to find clusters of addresses wchich are similar in terms of the Energy Level Classification information and the solar profile of their respective rooftops. For determining a reasonable number of clusters to form, the elbow method is used. There are several steps involved in this which are oulined in the notebook.

<a name="conclusions"></a>
### 6. Conclusions and future work
In my particular case, the most important conclusion after carrying out the work for this project is that if one wishes to apply their skills in a real-life context that they actually care about, the largest amount of time can be easily spent defining your use-case, finding the data which can help you in your quest and then on top of that apply the '80% of time is spent on pre-processing the data' saying (which unfortunatelly holds truth in this context as well).

Looking into my articular use case, I was not surprised to see that the majority of buildings were in the mid range in terms of energy efficiency and there was a slight 'oh, nice!' moment when it appeared that there was one group of buildings which were in the second-half of the energy-efficiency spectrum and showed prommising solar potential. If one would have a bugdet to use for thermal rehabilitation of buildings, probably focusing on those buildings first would make sense. 

Of course this was just a very rough approach to things. There are several things that can be improved: starting from understanding why we have so few addresses in the final dataset (did we miss something while we were scraping for them, or is due to the attempt to match them against the geo-points of the solar info dataset? Or something else?), making a more thorough assesment of the solar potential of a building/address (the dataset we had was composed of polygons that combined described the potential of the entire rooftop of a building, but we just selected the polygon that was closest to our energy class geo-point) to the clustering approach selected (we just used KMeans with the elbow menthod, but perhaps something else would have been more appropriate).