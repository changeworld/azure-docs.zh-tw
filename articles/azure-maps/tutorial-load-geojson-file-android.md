---
title: 教學課程：將 GeoJSON 資料載入 Azure 地圖服務 Android SDK |Microsoft Azure 地圖服務
description: 如何將 GeoJSON 資料檔案載入 Azure 地圖服務 Android 地圖 SDK 的教學課程。
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b527cd7b3f841b6cb3dcf2dce6930f3bd9bcc184
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681234"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>教學課程：將 GeoJSON 資料載入 Azure 地圖服務 Android SDK

本教學課程會引導您完成將位置資料的 GeoJSON 檔案匯入 Azure 地圖服務 Android SDK 的流程。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將 Azure 地圖服務新增至 Android 應用程式。
> * 建立資料來源，並從本機檔案或 Web 載入 GeoJSON 檔案。
> * 在地圖上顯示資料。

## <a name="prerequisites"></a>必要條件

1. 完成[快速入門：建立 Android 應用程式](quick-android-map.md)。 本教學課程會持續納入該快速入門中所使用的程式碼。
2. 下載 GeoJSON 檔案的[景點範例](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json)。

### <a name="import-geojson-data-from-web-or-assets-folder"></a>從 Web 或資產資料夾匯入 GeoJSON 資料

大部分的 GeoJSON 檔案會將所有資料包裝在 `FeatureCollection` 中。 因此，如果 GeoJSON 檔案是以字串的形式載入至應用程式，您就可以將該檔案傳遞至功能集合的靜態 `fromJson` 方法，將字串還原序列化成可以加入地圖的 GeoJSON `FeatureCollection` 物件。

下列步驟示範如何將 GeoJSON 檔案匯入應用程式，並還原序列化為 GeoJSON `FeatureCollection` 物件。

1. 完成[快速入門：建立 Android 應用程式](quick-android-map.md)，下列步驟會建立在此應用程式的最上層。
2. 在 Android studio 的專案面板中，以滑鼠右鍵按一下 [應用程式] 資料夾，然後移至 `New > Folder > Assets Folder`。
3. 將[景點範例](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GeoJSON 檔案拖放到資產資料夾中。
4. 建立名為 **Utils.java** 的新檔案，並將下列程式碼新增檔案中。 此程式碼提供名為 `importData` 的靜態方法，會使用 URL 作為字串，以非同步方式從應用程式的 `assets` 資料夾或 Web 匯入檔案，並使用簡單的回撥方法傳回給 UI 執行緒。

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. 進入 **MainActivity.java** 檔案，並在 `mapControl.onReady` 事件的回撥內新增下列程式碼，這位在 `onCreate` 方法內。 這段程式碼會使用匯入公用程式，以字串的形式讀取 **SamplePoiDataSet.json** 檔案，然後使用 `FeatureCollection` 類別的靜態 `fromJson` 方法，將其還原序列化為功能集合。 此程式碼也會計算功能集合中所有資料的周框方塊區域，並使用此方塊來設定地圖的相機，以專注於處理資料。

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. 現在有了程式碼，可以使用資料來源將 GeoJSON 資料載入地圖中，我們必須指定該資料應該如何在地圖上顯示。 地點資料有數個不同的轉譯層；例如[泡泡層](map-add-bubble-layer-android.md)、[符號層](how-to-add-symbol-to-android-map.md)和[熱度圖層](map-add-heat-map-layer-android.md)是最常使用的圖層。 新增下列程式碼，以在匯入資料的程式碼之後，在 `mapControl.onReady` 事件回撥的泡泡層中轉譯資料。

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

7. 執行應用程式。 系統會顯示美國地圖，並以重疊的圓形顯示 GeoJSON 檔案中的每個位置。

    ![包含 GeoJSON 檔案資料的美國地圖](media/tutorial-load-geojson-file-android/android-import-geojson.png)


## <a name="clean-up-resources"></a>清除資源

請採取下列步驟來清除本教學課程中的資源：

1. 關閉 Android Studio，並刪除您建立的應用程式。
2. 如果您已在外部裝置上測試應用程式，請將該裝置上的應用程式解除安裝。

## <a name="next-steps"></a>後續步驟

若要查看更多程式碼範例和互動式編碼體驗：

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增線條圖層](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](how-to-add-shapes-to-android-map.md)
