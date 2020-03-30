---
title: 在 Android 地圖上顯示流量資料 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Android SDK 在地圖上顯示流量資料。
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335383"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>使用 Azure 地圖 Android SDK 在地圖上顯示流量資料

流資料和事件資料是可在地圖上顯示的兩種類型的流量資料。 本指南介紹如何顯示這兩種類型的流量資料。 事件資料包括建築、道路封閉和事故等基於點和線的資料。 流量資料顯示有關道路上交通流量的指標。

## <a name="prerequisites"></a>Prerequisites

在地圖上顯示流量之前，需要[創建 Azure 帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)[並獲取訂閱金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 然後，您需要安裝 Azure[映射 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)並載入地圖。

## <a name="incidents-traffic-data"></a>事件流量資料 

您需要導入以下庫才能調用`setTraffic`和`incidents`：

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 以下程式碼片段演示如何在地圖上顯示流量資料。 我們將布林值傳遞給`incidents`方法，並將其傳遞給`setTraffic`方法。 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>流量資料

您首先需要導入以下庫來調用`setTraffic`和`flow`：

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

使用以下程式碼片段設置流量流資料。 與上一節中的代碼類似，我們將`flow`方法的傳回值傳遞給 方法。 `setTraffic` 有四個值可以傳遞給`flow`，每個值將觸發`flow`以返回相應的值。 然後，返回`flow`值將作為 參數傳遞給`setTraffic`。 有關以下四個值，請參閱下表：

| | |
| :-- | :-- |
| 流量流.無 | 地圖上不顯示流量資料 |
| 流量流. | 顯示與道路自由流動速度相關的交通資料 |
| 流量流.RELATIVE_DELAY | 顯示低於平均預期延遲的區域 |
| 流量。 | 顯示道路上所有車輛的絕對速度 |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>通過按一下功能顯示事件流量資料

要獲取特定功能的事件，可以使用下面的代碼。 按一下某個功能時，代碼邏輯會檢查事件並生成有關事件的消息。 螢幕底部將顯示一條消息，其中顯示詳細資訊。

1. 首先，您需要> **activity_main.xml 編輯>佈局**，以便它看起來像下面的佈局。 您可以用所需的值`mapcontrol_centerLat`替換`mapcontrol_centerLng`和`mapcontrol_zoom`。 回想一下，縮放級別的值介於 0 和 22 之間。 在縮放級別 0 時，整個世界都適合單個磁貼。

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

2. 將以下代碼添加到**主活動.java**檔中。 預設情況下，包包含在內，因此請確保將包保持在頂部。

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

3. 將上述代碼合併到應用程式中後，即可按一下某個功能並查看流量事件的詳細資訊。 根據**activity_main.xml**檔中使用的緯度、經度和縮放級別值，您將看到類似于下圖的結果：

   <center>

   ![地圖上的事件流量](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>後續步驟

查看以下指南，瞭解如何向地圖添加更多資料：

> [!div class="nextstepaction"]
> [添加符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增圖格圖層](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [將形狀添加到 Android 地圖](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)
