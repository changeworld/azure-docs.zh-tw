---
title: 教學課程：建立地理柵欄並在地圖上追蹤裝置 | Microsoft Azure 地圖服務
description: 了解如何使用 Microsoft Azure 地圖服務的空間服務來設定地理柵欄，並追蹤與地理柵欄相關的裝置。
author: farah-alyasari
ms.author: v-faalya
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e0e918a2508bb65de176b030a73598e221aff676
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370155"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>教學課程：使用 Azure 地圖服務設定地理柵欄

本教學課程會逐步引導您完成使用「Azure 地圖服務」來設定地理柵欄的基本步驟。 請考量站台管理員的結構必須監視潛在危險設備的情況。 管理員必須確保設備保留在選擇的整體建築區域中。 這個整體建築區域是一個硬參數。 法規要求設備必須維持在此參數內，且若有違規，必須回報給 Operations Manager。  

我們使用 Data Upload API 來儲存地理柵欄，並使用地理柵欄 API 來檢查設備與地理柵欄的相對位置。 Data Upload API 和地理柵欄 API 都來自 Azure 地圖服務。 我們也使用 Azure 事件方格來串流地理柵欄結果，並設定以地理柵欄結果為基礎的通知。 若要深入了解事件方格，請參閱 [Azure 事件方格](https://docs.microsoft.com/azure/event-grid/overview)。

在本教學課程中，我們說明如何：

> [!div class="checklist"]
> * 使用 Data Upload API 在 Azure 地圖服務的資料服務中上傳地理柵欄區域。
> *   設定用來處理地理柵欄的事件方格。
> *   設定地理柵欄事件處理常式。
> *   使用 Logic Apps 設定回應地理柵欄事件的警示。
> *   使用 Azure 地圖服務的地理柵欄服務 API 來追蹤某項建構資產是否位於建築工地內。


## <a name="prerequisites"></a>Prerequisites

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶 

依照[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的指示，在 S1 定價層中建立 Azure 地圖服務帳戶訂用帳戶。 [取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟會說明如何取得帳戶的主要金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

## <a name="upload-geofences"></a>上傳地理柵欄

我們假設主要地理柵欄為 subsite1，且具有已設定的到期時間。 您可以依據本身的需求建立更多巢狀地理柵欄。 這幾組柵欄可用來追蹤整體建築區域內的不同建築區域。 例如，subsite1 可以是將在預定進度的第 1 到 4 週進行施工的工地。 而 subsite2 則是在第 5 到 7 週施工的工地。 這類柵欄全都可以在專案開始之初載入作為單一資料集。 這些柵欄可根據時間和空間用來追蹤規則。 

為了使用 Data Upload API 來上傳建築工地的地理柵欄，我們將使用 Postman 應用程式。 請安裝 [postman 應用程式](https://www.getpostman.com/)並建立免費帳戶。 

安裝 Postman 應用程式後，請依照下列步驟使用 Azure 地圖服務的 Data Upload API 上傳建築工地地理柵欄。

1. 開啟 Postman 應用程式，然後按一下 [New] (新增) | [Create new] (新建)，然後選取 [Request] (要求)。 輸入上傳地理柵欄資料的要求名稱，選取集合或資料夾加以儲存，然後按一下 [Save] (儲存)。

    ![使用 Postman 上傳地理柵欄](./media/tutorial-geofence/postman-new.png)

2. 選取建立器索引標籤上的 POST HTTP 方法，然後輸入下列 URL 以提出 POST 要求。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL 路徑中的 GEOJSON 參數代表上傳資料的資料格式。

3. 按一下 [Params]  (參數)，然後輸入下列金鑰/值組用於 POST 要求 URL。 將 {subscription-key} 取代為您的 Azure 地圖服務訂用帳戶金鑰，也就是主要金鑰。
   
    ![Postman 中的上傳資料 (地理柵欄) 參數](./media/tutorial-geofence/postman-key-vals.png)

4. 按一下 [主體]  ，然後選取 [原始輸入格式]，並從下拉式清單中選擇 [JSON] 作為輸入格式。 提供下列 JSON 作為要上傳的資料：

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. 按一下 [Send] (傳送)，然後檢視回應標頭。 要求成功時，[位置]  標頭將會包含狀態 URI。 狀態 URI 的格式如下。 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. 複製您的狀態 URI，並附加訂用帳戶金鑰。 狀態 URI 格式應會如下所示。 請注意，使用下列格式時，您會以訂用帳戶金鑰變更 {subscription-key}，包括 {} 在內。

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. 若要取得 `udId`，請在 Postman 應用程式中開啟新的索引標籤，然後在 [建立器] 索引標籤上選取 GET HTTP 方法。對上一個步驟的狀態 URI 提出 GET 要求。 如果您的資料上傳成功，您將會在回應本文中收到 udId。 請複製 udId 以供稍後使用。

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>設定事件處理常式

在本節中，我們會建立可接收通知的事件處理常式。 此事件處理常式應就任何設備的進入和退出事件對 Operations Manager 發出相關通知。

我們將建立兩個 [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) 服務來處理進入和退出事件。 在觸發 Logic Apps 中的事件時，將會依序觸發更多事件。 其概念是要傳送警示；就此案例而言，是要傳送電子郵件給營運經理。 下圖說明如何建立適用於地理柵欄進入事件的邏輯應用程式。 您可以比照相同方式，為退出事件建立另一個應用程式。 您可以查看所有[支援的事件處理常式](https://docs.microsoft.com/azure/event-grid/event-handlers)以取得詳細資訊。

1. 在 Azure 入口網站中建立邏輯應用程式。 在 Azure Marketplace 中選取邏輯應用程式。 選取 [建立]  按鈕。

   ![建立 Azure Logic Apps 來處理地理柵欄事件](./media/tutorial-geofence/logic-app.png)

2. 在邏輯應用程式的設定功能表上，瀏覽至 [邏輯應用程式設計工具] 

3. 選取 HTTP 要求觸發程序，然後選取 [新增步驟]。 在 Outlook 連接器中，選取 [傳送電子郵件] 作為動作
  
   ![Logic Apps 結構描述](./media/tutorial-geofence/logic-app-schema.png)

4. 填寫傳送電子郵件的欄位。 請跳過 HTTP URL，此項目將在您按一下 [儲存] 後自動產生

   ![產生 Logic Apps 端點](./media/tutorial-geofence/logic-app-endpoint.png)

5. 儲存邏輯應用程式以產生 HTTP URL 端點，並複製 HTTP URL。

## <a name="create-an-azure-maps-events-subscription"></a>建立 Azure 地圖服務事件訂用帳戶

Azure 地圖服務支援三種事件類型。 您可以在[這裡](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)查看 Azure 地圖服務支援的事件類型。 我們需要兩個不同的事件訂用帳戶，一個用於進入事件，另一個用於退出事件。

請依照下列步驟，建立地理柵欄進入事件的事件訂用帳戶。 您可以用類似的方式訂閱地理柵欄退出事件。

1. 瀏覽至您的 Azure 地圖服務帳戶。 在儀表板中，選取 [訂用帳戶]。 按一下您的訂用帳戶名稱，然後從設定功能表中選取 [事件]  。

   ![瀏覽至 Azure 地圖服務帳戶的事件](./media/tutorial-geofence/events-tab.png)

2. 若要建立事件訂用帳戶，請從事件頁面中選取事件訂用帳戶。

   ![建立 Azure 地圖服務事件訂用帳戶](./media/tutorial-geofence/create-event-subscription.png)

3. 為事件訂用帳戶命名，並訂閱進入事件類型。 現在，選取 [Web Hook] 作為 [端點類型]。 按一下 [選取端點]，並將您邏輯應用程式的 HTTP URL 端點複製到 "{Endpoint}" 中

   ![Azure 地圖服務事件訂用帳戶的詳細資料](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>使用地理柵欄 API

您可以使用地理柵欄 API 來檢查某項**裝置** (在此案例中為設備) 是否位於地理柵欄內。 我們將對特定設備隨著時間而轉換工地的不同位置，來查詢地理柵欄 GET API。 下圖說明具有五個建築設備的五個位置。 

> [!Note]
> 此案例和行為以相同的**裝置識別碼**為基礎，因此反映出五個不同的位置，如下圖所示。

"deviceId" 是您在查詢裝置的位置時，在 GET 要求中為裝置提供的唯一識別碼。 當您對**搜尋地理柵欄 - GET API** 進行非同步要求時，"deviceId" 有助於發佈該裝置的地理柵欄事件 (相對於指定的地理柵欄)。 在本教學課程中，我們已使用唯一的 "deviceId" 對 API 進行非同步要求。 本教學課程中的要求依照時間順序進行，如圖所示。 每當裝置進入或退出地理柵欄時，就會發佈回應中的 "isEventPublished" 屬性。 您不需要依照本教學課程中的指示註冊裝置。

我們回過頭來看一下圖表。 這五個位置會分別用來根據柵欄評估地理柵欄的進入或退出狀態變更。 發生狀態變更時，地理柵欄服務就會觸發事件，並由事件方格傳送至邏輯應用程式。 因此，營運經理將透過電子郵件收到對應的進入或退出通知。

![Azure 地圖服務中的地理柵欄地圖](./media/tutorial-geofence/geofence.png)

在 Postman 應用程式中，在您於上方建立的相同集合中開啟新的索引標籤。 選取建立器索引標籤上的 GET HTTP 方法：

以下是五個 HTTP GET 地理柵欄 API 要求，設備各有不同的位置座標。 座標依照時間先後順序顯示。 每個要求後面都有回應主體。
 
1. 位置 1：
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![地理柵欄查詢 1](./media/tutorial-geofence/geofence-query1.png)

   在上述回應中，與主要地理柵欄的距離顯示為負數，表示設備位於地理柵欄內。 與子工地地理柵欄的距離顯示為正數，表示設備位於子工地地理柵欄外。 

2. 位置 2： 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![地理柵欄查詢 2](./media/tutorial-geofence/geofence-query2.png)

   仔細查看上述 JSON 回應時，您會發現設備位於子工地以外，但位於主要柵欄內。 這並不會觸發事件，也不會傳送電子郵件。

3. 位置 3： 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![地理柵欄查詢 3](./media/tutorial-geofence/geofence-query3.png)

   發生了狀態變更，此時設備位於主要和子工地地理柵欄內。 發生此變更時，將會發佈事件，並傳送通知電子郵件給營運經理。

4. 位置 4： 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![地理柵欄查詢 4](./media/tutorial-geofence/geofence-query4.png)

   仔細觀察對應的回應時您會發現，即使設備已退出子工地地理柵欄，仍未發佈任何事件。 如果您查看使用者在 GET 要求中指定的時間，您會發現子工地地理柵欄已超過此時間。 而設備仍位於主要地理柵欄內。 您也會在回應主體中的 `expiredGeofenceGeometryId` 下方看到子工地地理柵欄的幾何識別碼。


5. 位置 5：
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![地理柵欄查詢 5](./media/tutorial-geofence/geofence-query5.png)

   您可以看到設備已退出主要建築工地地理柵欄。 此時會發佈事件，並傳送警示電子郵件給營運經理。

## <a name="next-steps"></a>後續步驟

在本教學課程中您了解到，如何使用 Data Upload API 在 Azure 地圖服務的資料服務中上傳地理柵欄，藉以設定地理柵欄。 您也已了解如何使用 Azure 地圖服務的事件方格來訂閱及處理地理柵欄事件。 

* 請參閱[處理 Azure Logic Apps 中的內容類型](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)，了解如何使用 Logic Apps 剖析 JSON 以建置更複雜的邏輯。
* 若要深入了解事件方格中的事件處理常式，請參閱[事件方格中支援的事件處理常式](https://docs.microsoft.com/azure/event-grid/event-handlers)。
