---
title: 建立 Android 地圖的資料來源 |Microsoft Azure 對應
description: 瞭解如何建立對應的資料來源。 瞭解 Azure 地圖服務 Android SDK 所使用的資料來源： GeoJSON 來源和向量圖格。
author: rbrundritt
ms.author: richbrun
ms.date: 12/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 78b0cbbdccc7d6853d4ce2821bf659e888680a5f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674638"
---
# <a name="create-a-data-source-android-sdk"></a>建立資料來源 (Android SDK) 

Azure 地圖服務 Android SDK 會將資料儲存在資料來源中。 使用資料來源可將查詢和轉譯的資料作業優化。 目前有兩種類型的資料來源：

- **GeoJSON 來源**：在本機管理 GeoJSON 格式的原始位置資料。 適用于小型至中型的資料集 () 的上百個圖形。
- **向量圖格來源**：根據地圖並排顯示系統，將格式化為目前地圖視圖之向量磚的資料載入。 適用于大型至大型資料集 (上百萬或數十億個圖形) 。

## <a name="geojson-data-source"></a>GeoJSON 資料來源

Azure 地圖服務使用 GeoJSON 做為它的其中一個主要資料模型。 GeoJSON 是以 JSON 格式表示地理空間資料的開放地理空間標準方式。 Azure 地圖服務 Android SDK 中提供的 GeoJSON 類別可讓您輕鬆地建立和序列化 GeoJSON 資料。 載入並儲存類別中的 GeoJSON 資料 `DataSource` ，並使用圖層來呈現。 下列程式碼顯示如何在 Azure 地圖服務中建立 GeoJSON 物件。

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

或者，您可以先將屬性載入至 >jsonobject，然後在建立時傳遞至功能，如下所示。

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

一旦您建立了 GeoJSON 功能，就可以透過對應的屬性，將資料來源新增至地圖 `sources` 。 下列程式碼示範如何建立 `DataSource` 、將其加入至對應，以及將功能加入至資料來源。

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

下列程式碼顯示數種建立 GeoJSON 功能、FeatureCollection 和幾何的方式。

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

### <a name="serialize-and-deserialize-geojson"></a>序列化和還原序列化 GeoJSON

Feature collection、feature 和 geometry 類別都有 `fromJson()` 和 `toJson()` 靜態方法，可協助進行序列化。 透過方法傳遞的格式化有效 JSON 字串 `fromJson()` 將會建立 geometry 物件。 這個 `fromJson()` 方法也表示您可以使用 Gson 或其他序列化/還原序列化策略。 下列程式碼示範如何取得 stringified GeoJSON 功能，並將它還原序列化成 Feature 類別，然後將它序列化回 GeoJSON 字串。

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

### <a name="import-geojson-data-from-web-or-assets-folder"></a>從 Web 或資產資料夾匯入 GeoJSON 資料

大部分的 GeoJSON 檔案都包含 FeatureCollection。 以字串形式讀取 GeoJSON 檔，並使用 `FeatureCollection.fromJson` 方法將其還原序列化。

下列程式碼是一種可重複使用的類別，可將 web 或本機資產資料夾中的資料匯入為字串，並透過回呼函式將其傳回至 UI 執行緒。

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

下列程式碼示範如何使用此公用程式，將 GeoJSON 資料匯入為字串，並透過回呼將其傳回至 UI 執行緒。 在回呼中，字串資料可以序列化為 GeoJSON 功能集合，並新增至資料來源。 （選擇性）更新地圖攝影機以將焦點放在資料上。

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

## <a name="vector-tile-source"></a>向量圖格來源

向量圖格來源說明如何存取向量圖格圖層。 使用 `VectorTileSource` 類別可具現化向量圖格來源。 向量圖格圖層類似于圖格圖層，但它們並不相同。 圖格圖層是點陣影像。 向量圖格圖層是 **PBF** 格式的壓縮檔案。 這個壓縮檔案包含向量地圖資料，以及一或多個圖層。 您可以根據每個圖層的樣式，在用戶端上轉譯和樣式檔。 向量圖格中的資料會以點、線條和多邊形的形式包含地理特徵。 使用向量圖格圖層有幾個優點，而不是使用點陣圖格圖層：

- 向量磚的檔案大小通常遠小於相等的點陣磚。 因此，使用的頻寬較少。 這表示較低的延遲、更快速的地圖，以及更好的使用者體驗。
- 由於向量圖格會在用戶端上轉譯，因此會調整為其顯示所在裝置的解析度。 因此，轉譯的地圖會以 crystal clear 標籤更妥善地定義。
- 變更向量地圖中的資料樣式不需要再次下載資料，因為新的樣式可以套用至用戶端。 相反地，變更點陣磚圖層的樣式通常需要從伺服器載入磚，然後套用新的樣式。
- 因為資料是以向量形式傳遞，所以準備資料需要較少的伺服器端處理。 如此一來，較新的資料可以更快地提供使用。

Azure 地圖服務遵守 [Mapbox 向量圖格規格](https://github.com/mapbox/vector-tile-spec)，也就是開放式標準。 Azure 地圖服務提供下列向量圖格服務作為平臺的一部分：

- 道路磚[檔](/rest/api/maps/renderv2/getmaptilepreview)  |  [資料格式詳細資料](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 流量事件[檔](/rest/api/maps/traffic/gettrafficincidenttile)  |  [資料格式詳細資料](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 交通流程[檔](/rest/api/maps/traffic/gettrafficflowtile)  |  [資料格式詳細資料](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure 地圖服務 Creator 也可讓您透過[取得圖](/rest/api/maps/renderv2/getmaptilepreview)格轉譯 V2 來建立和存取自訂向量磚

若要在地圖上顯示向量圖格來源的資料，請將來源連接到其中一個資料轉譯圖層。 使用向量來源的所有圖層都必須 `sourceLayer` 在選項中指定值。 下列程式碼會將 Azure 地圖服務交通流程向量圖格服務載入為向量圖格來源，然後使用線條圖層將它顯示在地圖上。 此向量圖格來源在來源層中有一組稱為「流量流程」的資料。 此資料集中的行資料具有一個名為 `traffic_level` 的屬性，此程式碼會使用此屬性來選取色彩並調整線條大小。

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://atlas.microsoft.com/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}&subscription-key=" + AzureMaps.getSubscriptionKey();

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource("flowLayer",
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

![地圖與色彩編碼的道路線，顯示交通流程層級](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>將資料來源連接至圖層

使用轉譯圖層在地圖上呈現資料。 單一資料來源可由一個或多個呈現圖層參考。 下列呈現圖層需要資料來源：

- 反升[圖圖層](map-add-bubble-layer-android.md)-將點資料呈現為地圖上的縮放圓形。
- [符號圖層](how-to-add-symbol-to-android-map.md) -將點資料呈現為圖示或文字。
- [熱度圖圖層](map-add-heat-map-layer-android.md) -將點資料呈現為密度熱度圖。
- [線條圖層](android-map-add-line-layer.md) -轉譯線條和或轉譯多邊形的外框。
- [多邊形圖層](how-to-add-shapes-to-android-map.md) -以純色或影像樣式填滿多邊形的區域。

下列程式碼會示範如何建立資料來源、將其新增至地圖，以及將它連接到反升圖圖層。 然後，將 GeoJSON 點資料從遠端位置匯入資料來源。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

有其他轉譯層不會連接到這些資料來源，但會直接載入資料以進行轉譯。

- 圖格[圖層](how-to-add-tile-layer-android-map.md)-在地圖上方但將點陣圖格圖層。

## <a name="one-data-source-with-multiple-layers"></a>一個具有多層的資料來源

多層可以連接到單一資料來源。 在許多不同的情況下，此選項很有用。 例如，請考慮使用者繪製多邊形的案例。 當使用者將點新增至地圖時，我們應轉譯和填滿多邊形區域。 新增已設定樣式的線條來勾勒出多邊形，可讓您在使用者繪製時更輕鬆地查看多邊形的邊緣。 為了方便編輯多邊形中的個別位置，我們可以在每個位置的上方新增一個控點，例如圖釘或標記。

![顯示從單一資料來源轉譯資料之多個圖層的地圖](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

在大部分的對應平臺中，您需要多邊形物件、線條物件，以及多邊形中每個位置的釘選。 修改多邊形時，您需要手動更新線條和釘選，這可能很快就會變得複雜。

使用 Azure 地圖服務，您只需要資料來源中的單一多邊形，如下列程式碼所示。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

> [!TIP]
> 使用方法將圖層加入至地圖時 `map.layers.add` ，現有圖層的識別碼或實例可以作為第二個參數傳入。 這會告訴 map 要插入新的圖層，並將其新增至現有的圖層下方。 在除了中，若要傳入圖層識別碼，這個方法也支援下列值。
>
> - `"labels"` -在地圖標籤圖層下方插入新圖層。
> - `"transit"` -在地圖道路和傳輸層下方插入新的圖層。

## <a name="next-steps"></a>下一步

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [新增線條圖層](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [新增熱度圖](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Web SDK 程式碼範例](/samples/browse/?products=azure-maps)