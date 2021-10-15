# Leaflet Traffic By HereTrafficAPI  

## Jam Factor
[Here Traffic API](https://developer.here.com/documentation/traffic-api/dev_guide/topics/concepts/flow.html#jam-factor)

[Demo](https://somnuekm.github.io/Leaflet-Traffic-By-HereTrafficAPI/indexGeojson.html)

![image](https://user-images.githubusercontent.com/58202287/137466490-6fe49d82-cf28-49bf-bd80-83fbad45f486.png)

![image](https://user-images.githubusercontent.com/58202287/137466650-aa18e8e4-5f15-40fb-8f65-fd2f37531ccf.png)

```javascript
function getColor(jamFactor) {
         return jamFactor > 10 ? '#961614' :
                jamFactor > 8 ? '#E71F28' :
                jamFactor > 5 ? '#FEC802' :
                '#56B167';
};
        
$.getJSON(url, (dataJson) => {

                let dateTime = new Date(dataJson.sourceUpdated);
                console.log(dateTime);
                
                var features = [];

                dataJson.results.forEach((item) => {

                    item.location.shape.links.forEach((el) => {

                        var lineData = [];
                        var dataLineString = {
                            "type": "Feature",
                            "properties": {
                                "sourceUpdated": dateTime,
                                "description": item.location.description,
                                "length": item.location.length,
                                "points_length": el.length,
                                "currentFlow": item.currentFlow
                            },
                            "geometry": {
                                "type": "LineString",
                                "coordinates": lineData
                            }
                        }

                        el.points.forEach((elPoint) => {
                            let loc = [elPoint.lon, elPoint.lat]
                            lineData.push(loc)
                        });

                        features.push(dataLineString);

                    });

                });

                //Geojson Format
                var geoData = {
                        "type": "FeatureCollection",
                        "features": features
                    }

                var lineWeight = 5,
                    segmentWidth = 1 * (lineWeight + 1);
                lineStringTraffic = L.geoJson(geoData.features, {
                    style: function(feature) {
                        return {
                            "color": getColor(feature.properties.currentFlow.jamFactor),
                            "opacity": 1,
                            "weight": lineWeight,
                            "offset": 0 * (lineWeight + 1) - (segmentWidth / 2) + ((lineWeight + 1) / 2),
                        }
                    },
                    onEachFeature: function(feature, layer) {
                        var popupContent = `<b>${feature.properties.description}</b><br/>
                                <p>Point Length:${feature.properties.points_length} M.</p>
                                <p>Path Length:${feature.properties.length} M.</p>
                                `;
                        if (feature.properties && feature.properties.popupContent) {
                            popupContent += feature.properties.popupContent;
                        }
                        layer.bindPopup(popupContent);

                    }
                }).addTo(trafficLayer);

});
```
![image](https://user-images.githubusercontent.com/58202287/137470517-87567526-2c0f-4199-9b56-37074e931ec5.png)
