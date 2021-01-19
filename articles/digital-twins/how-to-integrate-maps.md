---
title: 與 Azure 地圖服務整合
titleSuffix: Azure Digital Twins
description: 瞭解如何使用 Azure Functions 建立可使用對應項圖形和 Azure 數位 Twins 通知的函式，以更新 Azure 地圖服務室內地圖。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 6a654f74ff6a32ad37646021d504359c84942c12
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573032"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>使用 Azure 數位 Twins 來更新 Azure 地圖服務室內地圖

本文將逐步解說使用 Azure 數位 Twins 資料來更新 *室內地圖* 上顯示的資訊（使用 [Azure 地圖服務](../azure-maps/about-azure-maps.md)）所需的步驟。 Azure 數位 Twins 會儲存 IoT 裝置關聯性的圖表，並將遙測路由傳送至不同的端點，使其成為在地圖上更新資訊覆迭的最佳服務。

此操作說明將涵蓋：

1. 設定您的 Azure 數位 Twins 實例，以將對應項更新事件傳送至 [Azure Functions](../azure-functions/functions-overview.md)中的函式。
2. 建立可更新 Azure 地圖服務室內地圖功能 stateset 的函式。
3. 如何在 Azure 數位 Twins 圖形中儲存地圖識別碼和功能 stateset 識別碼。

### <a name="prerequisites"></a>必要條件

* 遵循 Azure 數位 Twins [*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)。
    * 您將會使用額外的端點和路由來擴充此對應項。 您也會從該教學課程將另一個函式新增至函數應用程式。 
* 遵循 Azure 地圖服務 [*教學課程：使用 Azure 地圖服務建立者來建立室內地圖*](../azure-maps/tutorial-creator-indoor-maps.md) ，以使用 *功能 stateset* 建立 Azure 地圖服務室內地圖。
    * [功能 statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) 是動態屬性的集合， (狀態) 指派給資料集功能，例如房間或設備。 在上述的 Azure 地圖服務教學課程中，功能 stateset 會儲存您將在地圖上顯示的房間狀態。
    * 您將需要您的功能 *STATESET 識別碼* 和 Azure 地圖服務訂用帳戶 *金鑰*。

### <a name="topology"></a>拓撲

下圖說明在本教學課程中，室內地圖整合元素如何融入較大型的端對端 Azure 數位 Twins 案例中。

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="在端對端案例中的 Azure 服務視圖，並反白顯示室內地圖整合片段" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>建立函式以在 twins 更新時更新對應

首先，您將在 Azure 數位 Twins 中建立路由，以將所有對應項更新事件轉寄至事件方格主題。 然後，您將使用函式來讀取這些更新訊息，並更新 Azure 地圖服務中的功能 stateset。 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>建立對應項更新通知的路由與篩選

當對應項的狀態更新時，Azure 數位 Twins 實例可以發出對應項更新事件。 Azure 數位 Twins [*教學課程：連接上述連結的端對端解決方案*](./tutorial-end-to-end.md) 逐步解說一個案例，其中的溫度計會用來更新附加至房間對應項的溫度屬性。 您將會藉由訂閱 twins 的更新通知，以及使用該資訊來更新您的對應，來擴充該解決方案。

此模式會直接讀取房間對應項，而不是 IoT 裝置，可讓您彈性地變更溫度的基礎資料來源，而不需要更新您的對應邏輯。 例如，您可以新增多個溫度計，或將這個空間設定為與其他房間共用溫度計，而不需要更新您的地圖邏輯。

1. 建立事件方格主題，這會從您的 Azure 數位 Twins 實例接收事件。
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. 建立端點以將事件方格主題連結至 Azure 數位 Twins。
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. 在 Azure Digital Twins 中建立路由，以將對應項更新事件傳送至您的端點。

    >[!NOTE]
    >目前 Cloud Shell 有 **已知問題** 會影響這些命令群組：`az dt route`、`az dt model`、`az dt twin`。
    >
    >若要解決此問題，請在執行命令之前，先在 Cloud Shell 中執行 `az login`；或使用[本機 CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)，而不是 Cloud Shell。 如需這方面的詳細資訊，請參閱[疑難排解：Azure Digital Twins 中的已知問題](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)。

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>建立函式以更新對應

您將從端對端教學課程中，在函式應用程式內建立 **事件方格觸發的函式** ([*教學課程：連接端對端解決方案*](./tutorial-end-to-end.md)) 。 此函式會將這些通知解壓縮，並將更新傳送至 Azure 地圖服務的功能 stateset，以更新一個房間的溫度。

請參閱下列檔以取得參考資訊： [*適用于 Azure Functions 的 Azure 事件方格觸發程式*](../azure-functions/functions-bindings-event-grid-trigger.md)。

以下列程式碼取代函式程式碼。 它只會篩選出空間 twins 的更新、讀取更新的溫度，然後將該資訊傳送至 Azure 地圖服務。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

您必須在函數應用程式中設定兩個環境變數。 其中一個是您的 [Azure 地圖服務主要訂](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)用帳戶金鑰，另一個則是您的 [Azure 地圖服務 stateset 識別碼](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset)。

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>在地圖上查看即時更新

若要查看即時更新溫度，請遵循下列步驟：

1. 從 Azure 數位 Twins 教學課程中執行 **devicesimulator.exe** 專案，開始傳送模擬的 IoT 資料 [*：連接端對端解決方案*](tutorial-end-to-end.md)。 的指示位於 [*設定和執行模擬*](././tutorial-end-to-end.md#configure-and-run-the-simulation) 一節中。
2. 使用 [ **Azure 地圖服務室內** 模組](../azure-maps/how-to-use-indoor-module.md) 來呈現您在 Azure 地圖服務 Creator 建立的室內地圖。
    1. 複製範例中的 HTML：使用室內地圖教學課程的 [*室內地圖模組*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) 一節 [*：使用 Azure 地圖服務室內地圖模組*](../azure-maps/how-to-use-indoor-module.md) 的本機檔案。
    1. 以您的值取代本機 HTML 檔案中的訂用帳戶 *金鑰*、 *tilesetId* 和 *statesetID*  。
    1. 在您的瀏覽器中開啟該檔案。

這兩個範例都會以相容的範圍傳送溫度，因此您應該會在地圖上看到大約每隔30秒的房間121更新色彩。

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="顯示房間121彩色橙色的辦公室地圖":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>將您的地圖服務資訊儲存在 Azure 數位 Twins 中

現在您已有可更新地圖資訊的硬式編碼解決方案，您可以使用 Azure 數位 Twins 圖形來儲存更新室內地圖所需的所有資訊。 這會分別包含 stateset 識別碼、地圖訂用帳戶識別碼，以及每個對應和位置的功能識別碼。 

此特定範例的解決方案會涉及更新每個最上層空間，使其具有 stateset ID 和 maps 訂用帳戶 ID 屬性，並將每個空間更新為具有功能識別碼。 您必須在初始化對應項圖形時設定這些值一次，然後針對每個對應項更新事件查詢這些值。

視拓撲的設定而定，您可以將這三個屬性儲存在與對應的資料細微性相關聯的不同層級上。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何管理、升級及從 twins 圖形中取得資訊，請參閱下列參考：

* [*How to：管理數位 twins*](./how-to-manage-twin.md)
* [*How to：查詢對應項圖形*](./how-to-query-graph.md)
