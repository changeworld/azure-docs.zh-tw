---
title: 顯示 android 地圖上的流量資料 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Android SDK，在地圖上顯示流量資料。
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 73f490ec069ff44929ca70f4ecf2ab3aca52934d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209346"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>使用 Azure 地圖服務 Android SDK 顯示地圖上的流量資料

「流量資料」和「事件資料」是可在地圖上顯示的兩種類型的流量資料。 本指南說明如何顯示這兩個流量資料。 事件資料包含像是結構、後端和事故等專案的點和行式資料。 流量資料會顯示有關道路流量的計量。

## <a name="prerequisites"></a>必要條件

在您可以顯示地圖上的流量之前，您必須先安裝[Azure 地圖服務 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)並載入對應。

## <a name="incidents-traffic-data"></a>事件流量資料 

您必須匯入下列程式庫，才能呼叫 `setTraffic` 並 `incidents`：

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 下列程式碼片段說明如何在地圖上顯示流量資料。 我們會將布林值傳遞給 `incidents` 方法，並將其傳遞給 `setTraffic` 方法。 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
}
}
```

## <a name="flow-traffic-data"></a>流量流量資料

首先，您必須匯入下列程式庫，以呼叫 `setTraffic` 並 `flow`：

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

使用下列程式碼片段來設定流量資料。 與上一節中的程式碼類似，我們會將 `flow` 方法的傳回值傳遞給 `setTraffic` 方法。 有四個可以傳遞給 `flow`的值，而每個值都會觸發 `flow` 傳回個別的值。 然後，`flow` 的傳回值會當做引數傳遞給 `setTraffic`。 請參閱下表中的這四個值：

| | |
| :-- | :-- |
| TrafficFlow。無 | 不會在地圖上顯示流量資料 |
| TrafficFlow。相對 | 顯示相對於道路自由流量速度的流量資料 |
| TrafficFlow。 RELATIVE_DELAY | 顯示比平均預期延遲慢的區域 |
| TrafficFlow。絕對 | 顯示道路上所有車輛的絕對速度 |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> 
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>按一下功能來顯示事件流量資料

若要取得特定功能的事件，您可以使用下列程式碼。 當您按一下某項功能時，程式碼邏輯會檢查事件，並建立事件的相關訊息。 畫面底部會顯示訊息，其中包含詳細資料。

1. 首先，您必須編輯**res > layout > activity_main .xml**，使其看起來如下所示。 您可以使用您想要的值來取代 `mapcontrol_centerLat`、`mapcontrol_centerLng`和 `mapcontrol_zoom`。 回想一下，縮放層級是介於0和22之間的值。 在縮放層級0，整個世界都適合單一磚。

   ```XML
   <?xml version="1.0" encoding="utf-8"?>
   <FrameLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       >
    
       <com.microsoft.azure.maps.mapcontrol.MapControl
           android:id="@+id/mapcontrol"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. 將下列程式碼新增至您的**MainActivity**檔案。 預設會包含套件，因此請確定您已將套件保留在頂端。

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. 在您的應用程式中納入上述程式碼之後，您將能夠按一下功能，並查看流量事件的詳細資料。 根據您在**activity_main .xml**檔案中使用的緯度、經度和縮放層級值，您會看到類似下圖的結果：

   <center>

   ![事件-地圖上的流量](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>後續步驟

若要瞭解如何將更多資料新增至地圖，請參閱下列指南：

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增圖格圖層](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [將圖形新增至 android 地圖](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)
