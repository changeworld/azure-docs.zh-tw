---
title: 顯示 android 地圖上的交通資料 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Android SDK 來顯示地圖上的流量資料。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 93cabb566db02de7ef991fe9cdd293f8c399c3a3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272951"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>使用 Azure 地圖服務 Android SDK 顯示地圖上的交通資料

Flow 資料和事件資料是可在地圖上顯示的兩種類型的流量資料。 本指南說明如何顯示這兩種類型的流量資料。 事件資料包含資料點和以行為基礎的資料，例如結構、路標和事故。 流程資料會顯示有關道路流量的計量。

## <a name="prerequisites"></a>Prerequisites

您必須先 [建立 Azure 帳戶](quick-demo-map-app.md#create-an-azure-maps-account)並 [取得訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，才能在地圖上顯示流量。 然後，您必須安裝 [Azure 地圖服務 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 並載入對應。

## <a name="incidents-traffic-data"></a>事件流量資料

您必須匯入下列程式庫，才能呼叫 `setTraffic` 和 `incidents` ：

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 下列程式碼片段說明如何在地圖上顯示交通資料。 我們會將布林值傳遞給 `incidents` 方法，並將它傳遞給 `setTraffic` 方法。 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>流量資料

首先，您必須匯入下列程式庫以呼叫 `setTraffic` 和 `flow` ：

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

使用下列程式碼片段來設定流量資料。 與上一節中的程式碼類似，我們會將方法的傳回值傳遞 `flow` 給 `setTraffic` 方法。 有四個值可傳遞至 `flow` ，而且每個值都會觸發 `flow` 以傳回個別的值。 然後，的 `flow` 傳回值會以引數的形式傳遞給 `setTraffic` 。 請參閱下表中的下列四個值：

|流程值 | 說明|
| :-- | :-- |
| TrafficFlow。無 | 不顯示地圖上的交通資料 |
| TrafficFlow。相對 | 顯示相對於道路的自由流動速度的流量資料 |
| TrafficFlow.RELATIVE_DELAY | 顯示比平均預期延遲更慢的區域 |
| TrafficFlow，絕對 | 顯示所有車輛在道路上的絕對速度 |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>按一下功能以顯示事件流量資料

若要取得特定功能的事件，您可以使用下列程式碼。 當您按一下某項功能時，程式碼邏輯會檢查事件，並建立事件的相關訊息。 畫面底部會顯示一則訊息，內含詳細資料。

1. 首先，您必須 **> activity_main.xml編輯 res > **配置，使其看起來如下所示。 您可以使用所 `mapcontrol_centerLat` 需的值來取代、 `mapcontrol_centerLng` 和 `mapcontrol_zoom` 。 回想一下，縮放層級是介於0到22之間的值。 在縮放層級0，整個世界都適合單一磚。

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

2. 將下列程式碼新增至 **>mainactivity** 檔案。 依預設會包含套件，因此請確定您將套件保持在最上層。

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

3. 當您在應用程式中納入上述程式碼之後，您將能夠按一下功能，並查看流量事件的詳細資料。 根據您在 **activity_main.xml** 檔案中使用的緯度、經度和縮放層級值，您會看到類似下圖的結果：

   <center>

   ![事件-地圖上的流量](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>後續步驟

若要瞭解如何將更多資料新增至您的地圖，請參閱下列指南：

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增圖格圖層](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [將圖形新增至 android 地圖](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)
