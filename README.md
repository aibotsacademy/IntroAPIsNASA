# 🤖 [NASA Space Apps 2020](https://nasaspaceappscr.globalhackathons.co/) 🛰️ 
### Intro a los APIs de la NASA: Datos de Observación de la Tierra, Imágenes Satelitales, Clima, Misión Rover en Marte.
###### Organizado por Global Hackathons con apoyo de Comisión Aeroespacial y Local Leads de Space Apps en México, Costa Rica.
###### ||Fuente de Datos: [NASA API](https://api.nasa.gov/), [GIBS API](https://wiki.earthdata.nasa.gov/display/GIBS/GIBS+API+for+Developers). ||

###### ||Competencia Mundial 🌎 || Desafíos Globales 2020: Observar, Informar, Un Futuro Sostenible, Confrontar, Crear o Crea tu propio reto! ||

#### En este Taller aprenderemos sobre el uso de APIs de la NASA para visualizar datos como: Imágenes Satelitales y de Observación de la Tierra, entre muchos otros más! 

![Test Image 4](https://github.com/leoaiassistant/NASA_GIBS/blob/master/IMG/model.png)

#### Manos a la obra!

> Paso 1: Crear una cuenta en NASA Open APIs: https://api.nasa.gov/

> Paso 2: Genera los credenciales de tu API Key:

```
 https://api.nasa.gov/planetary/apod?api_key=YOUR_API_KEY
```
Prueba el API de Observación de la Tierra:
```
https://api.nasa.gov/planetary/earth/assets?lon=-98.83&lat=19.70&date=2020-01-01&&dim=0.10&api_key=DEMO_KEY
```

Prueba el API de Misión Rover en Marte:
```
https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=2020-1-1&api_key=DEMO_KEY
```

![Mars](https://github.com/leoaiassistant/NASA_APIs/blob/master/IMG/MARS.jpg) 
> Paso 3: [Bienvenid@](sM9dReXlARhfcp9ctZGxUt8wItACbqJTLMCW3YiI) al servicio de Imágenes Satelitales: El [GIBS API](https://earthdata.nasa.gov/eosdis/science-system-description/eosdis-components/gibs) se utiliza para construir modelos de Machine Learning y/o Realidad Virtual basados en Imágenes Satelitales abordo de Misiones como [Terra](https://www.nasa.gov/mission_pages/terra/spacecraft/index.html) & [Aqua](https://aqua.nasa.gov/) que se encuentran en una órbita polar alrededor de la Tierra y sincronizada con el Sol. 

-Paso 4: Descarga el Demo > [SpaceApps2020](https://github.com/leoaiassistant/NASA_APIs)


-Paso 5: Integrar el GIBS API a la Web
Este proyecto muestra cómo utilizar GIBS API para visualizar capas de datos de imágenes satelitales a bordo del 🛰️ Sátelite Terra & Aqua en la órbita terrestre baja, para su uso en análisis y comprensión del Clima, Terrenos, Agricultura, Océanos, entre muchos otros, mediante los datos generados por el instrumento [MODIS](https://wiki.earthdata.nasa.gov/display/GIBS/GIBS+Available+Imagery+Products#expand-CorrectedReflectance17Products) que rastrea una amplia gama de signos vitales de la tierra 🌎:

- temperatura de superficie (suelo y océano), detección de incendios
- color del océano (sedimentos, fitoplancton)
- cartografía de la vegetación global, detección de cambios
- características de la nubosidad
- concentraciones de aerosoles 

Este proyecto muestra cómo utilizar GIBS como fuente del mosaico  (en inglés tile source) de Imágenes Satelitales en OpenLayers. 
Usamos los servicios de Exploración de Imágenes Globales de la NASA [API de GIBS](https://wiki.earthdata.nasa.gov/display/GIBS/GIBS+API+for+Developers) como proveedor de una pirámide de mosaicos de imágenes satélitales (de la especificación OGC WMTS 1.0.0) y visualizar las capas de datos del ***Servicio de mosaicos de mapas web (WMTS)*** en base a parametros como:

```
window.onload = function () {
  // Seven day slider based off today, remember what today is
  var today = new Date();

  // Selected day to show on the map
  var day = new Date(today.getTime());

  // GIBS needs the day as a string parameter in the form of YYYY-MM-DD.
  // Date.toISOString returns YYYY-MM-DDTHH:MM:SSZ. Split at the 'T' and
  // take the date which is the first part.
  var dayParameter = function () {
    return day.toISOString().split('T')[0];
  };

  var map = new ol.Map({
    view: new ol.View({
      maxResolution: 0.5625,
      projection: ol.proj.get('EPSG:4326'),
      extent: [-180, -90, 180, 90],
      center: [-80.87, 24.21],
      zoom: 6,
      maxZoom: 8
    }),
    target: 'map',
    renderer: ['canvas', 'dom']
  });

  function update() {
    // There is only one layer in this example, but remove them all
    // anyway
    clearLayers();

    // Add the new layer for the selected time
    map.addLayer(createLayer());

    // Update the day label
    document.querySelector('#day-label').textContent = dayParameter();
  };

  function clearLayers() {
    // Get a copy of the current layer list and then remove each
    // layer.
    var activeLayers = map.getLayers().getArray();
    for (var i = 0; i < activeLayers.length; i++) {
      map.removeLayer(activeLayers[i]);
    }
  };

  function createLayer() {
    var source = new ol.source.WMTS({
      url: 'https://gibs-{a-c}.earthdata.nasa.gov/wmts/epsg4326/best/wmts.cgi?TIME=' + dayParameter(),
      layer: 'MODIS_Terra_SurfaceReflectance_Bands121',
      format: 'image/jpeg',
      matrixSet: 'EPSG4326_250m',
      tileGrid: new ol.tilegrid.WMTS({
        origin: [-193, 94],
        resolutions: [
          0.5625,
          0.28125,
          0.140625,
          0.0703125,
          0.03515625,
          0.017578125,
          0.0087890625,
          0.00439453125,
          0.002197265625
        ],
        matrixIds: [0, 1, 2, 3, 4, 5, 6, 7, 8],
        tileSize: 512
      })
    });

    var layer = new ol.layer.Tile({ source: source });
    return layer;
  };

  update();

  // Slider values are in 'days from present'.
  document.querySelector('#day-slider')
    .addEventListener('change', function (event) {
      // Add the slider value (effectively subracting) to today's
      // date.
      var newDay = new Date(today.getTime());
      newDay.setUTCDate(today.getUTCDate() +
        Number.parseInt(event.target.value));
      day = newDay;
      update();
    });
};
```


- URL: https://gibs.earthdata.nasa.gov/wmts/
- Layer: (e.g. "MODIS / Terra")
- Matrix Set:'EPSG_(n)m'
- Origin:[Lat, Long]
- Resolution:[0...2]
- Tile: Open Layers

> Crear función Layer (Capa de Datos):

```
 function createLayer() {
    var source = new ol.source.WMTS({
      url: 'https://gibs.earthdata.nasa.gov/wmts/epsg4326/best/wmts.cgi?SERVICE='
      layer: 'MODIS_Terra_SurfaceReflectance_Bands121',
      format: 'image/jpeg',
      matrixSet: 'EPSG_(N)m',
      tileGrid: new ol.tilegrid.WMTS({
        origin: [Lat, Long],
        resolutions: [
          0...(n)
        ],
        matrixIds: [0, 1, 2, 3, 4, 5, 6, 7, 8],
        tileSize: 512
      })
    });
```

Términos Clave:

***MODIS (or Moderate Resolution Imaging Spectroradiometer)***: es un instrumento clave a bordo de los satélites Terra (originalmente conocido como EOS AM-1) y Aqua (originalmente conocido como EOS PM-1). 


> What's next? El Reto!
Con acceso a estos datos, crea una Aplicación o un modelo de impacto mundial!

+Tutoriales:
- Intro to Web Development & NASA API: Mars Rover Photos!
- Creado por Brandon Escamilla, Space Apps Lead en México

[![Brandon Escamilla](https://img.youtube.com/vi/KcyGr_onNiM/1.jpg)](https://youtu.be/KcyGr_onNiM)

+Recursos:

[Satelite Terra](https://terra.nasa.gov/about/terra-instruments/modis)

[MODIS](https://modis.gsfc.nasa.gov/data/)
 
[Open Layers](https://openlayers.org/)

[Earth Data Layers](https://wiki.earthdata.nasa.gov/display/GIBS/GIBS+Available+Imagery+Products#expand-CorrectedReflectance17Products)
 
[Earth Observation](https://earthdata.nasa.gov/earth-observation-data/near-real-time/download-nrt-data/modis-nrt
)
[GIBS API](https://wiki.earthdata.nasa.gov/display/GIBS/GIBS+API+for+Developers#GIBSAPIforDevelopers-ImageryAPI/Services)


### Terms of Use:
This workshop hosted by Space Apps Local Lead relies on a publicly accessible service web map service from NASA GIBS and is subject to its availability. GIBS is asking end users of this imagery to include the following acknowledgment in written and oral presentations:

We acknowledge the use of imagery from the Land Atmosphere Near real-time Capability for EOS (LANCE) system and services from the Global Imagery Browse Services (GIBS), both operated by the NASA/GSFC/Earth Science Data and Information System (ESDIS, https://earthdata.nasa.gov) with funding provided by NASA/HQ.
