---
title: 使用自訂分析擴充 Azure IoT Central |Microsoft Docs
description: 身為解決方案開發人員，設定 IoT Central 應用程式來執行自訂分析和視覺效果。 此解決方案使用 Azure Databricks。
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 64a518c5a55d5e45ac057fee92e42040e5a26df5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987725"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks-preview-features"></a>使用 Azure Databricks （預覽功能）透過自訂分析擴充 Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本操作指南示範如何以解決方案開發人員的身分，以自訂分析和視覺效果擴充您的 IoT Central 應用程式。 此範例會使用[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)工作區來分析 IoT Central 遙測串流，並產生視覺效果（例如[box 繪圖](https://wikipedia.org/wiki/Box_plot)）。

本操作指南會示範如何使用[內建的分析工具](./howto-create-custom-analytics.md)，將 IoT Central 擴充超過其功能。

在本操作指南中，您將瞭解如何：

* 使用*連續資料匯出*，從 IoT Central 應用程式串流遙測。
* 建立 Azure Databricks 環境來分析並繪製裝置遙測。

## <a name="prerequisites"></a>必要條件

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 應用程式

使用下列設定在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立 IoT Central 應用程式：

| 設定 | 值 |
| ------- | ----- |
| 定價方案 | Standard |
| 應用程式範本 | 存放區內分析-條件監視 |
| 應用程式名稱 | 接受預設值，或選擇您自己的名稱 |
| URL | 接受預設值，或選擇您自己唯一的 URL 前置詞 |
| 目錄 | 您的 Azure Active Directory 租使用者 |
| Azure 訂用帳戶 | 您的 Azure 訂用帳戶 |
| 地區 | 您最接近的區域 |

本文中的範例和螢幕擷取畫面會使用**美國**地區。 選擇接近您的位置的位置，並確定您在相同的區域中建立所有資源。

此應用程式範本包含兩個傳送遙測的模擬控溫器裝置。

### <a name="resource-group"></a>資源群組

使用 Azure 入口網站建立名為**IoTCentralAnalysis**的[資源群組](https://portal.azure.com/#create/Microsoft.ResourceGroup)，以包含您所建立的其他資源。 在與 IoT Central 應用程式相同的位置中建立 Azure 資源。

### <a name="event-hubs-namespace"></a>事件中樞命名空間

使用 Azure 入口網站建立具有下列設定的[事件中樞命名空間](https://portal.azure.com/#create/Microsoft.EventHub)：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇您的命名空間名稱 |
| 價格層 | 基本 |
| 訂閱 | 您的訂用帳戶 |
| 資源群組 | IoTCentralAnalysis |
| 位置 | 美國東部 |
| 輸送量單位 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作區

使用 Azure 入口網站建立具有下列設定的[Azure Databricks 服務](https://portal.azure.com/#create/Microsoft.Databricks)：

| 設定 | 值 |
| ------- | ----- |
| 工作區名稱    | 選擇您的工作區名稱 |
| 訂閱 | 您的訂用帳戶 |
| 資源群組 | IoTCentralAnalysis |
| 位置 | 美國東部 |
| 價格層次 | Standard |

當您建立所需的資源時， **IoTCentralAnalysis**資源群組看起來會如下列螢幕擷取畫面所示：

![IoT Central 分析資源群組](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>建立事件中樞

您可以設定 IoT Central 應用程式，以持續將遙測匯出至事件中樞。 在本節中，您會建立事件中樞，以從您的 IoT Central 應用程式接收遙測。 事件中樞會將遙測傳遞至您的串流分析作業以進行處理。

1. 在 Azure 入口網站中，流覽至您的事件中樞命名空間，然後選取  **+ 事件中樞**。
1. 將您的事件中樞命名為**centralexport**，然後選取 [**建立**]。
1. 在命名空間中的事件中樞清單中，選取 [ **centralexport**]。 然後選擇 [**共用存取原則**]。
1. 選取 [+ 新增]。 建立名**為 [以接聽宣告** **接聽**] 的原則。
1. 當原則準備好時，請在清單中選取它，然後複製 [**連接字串-主要金鑰**] 值。
1. 記下此連接字串，稍後當您將 Databricks 筆記本設定為從事件中樞讀取時，就會用到它。

您的事件中樞命名空間看起來會像下列螢幕擷取畫面：

![事件中樞命名空間](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>在 IoT Central 中設定匯出

在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上，流覽至您從 Contoso 範本建立的 IoT Central 應用程式。 在本節中，您會設定應用程式，以將遙測從模擬的裝置串流至您的事件中樞。 若要設定匯出：

1. 流覽至 **資料匯出** 頁面，選取  **+ 新增**，然後**Azure 事件中樞**。
1. 使用下列設定來設定匯出，然後選取 [**儲存**]：

    | 設定 | 值 |
    | ------- | ----- |
    | 顯示名稱 | 匯出至事件中樞 |
    | 啟用 | 開啟 |
    | 事件中樞命名空間 | 您的事件中樞命名空間名稱 |
    | 事件中樞 | centralexport |
    | 度量 | 開啟 |
    | 裝置 | 關 |
    | 裝置範本 | 關 |

![資料匯出設定](media/howto-create-custom-analytics/cde-configuration.png)

等到匯出狀態為 [**正在**執行]，然後再繼續。

## <a name="configure-databricks-workspace"></a>設定 Databricks 工作區

在 Azure 入口網站中，流覽至您的 Azure Databricks 服務，然後選取 **啟動工作區**。 您會在瀏覽器中開啟新的索引標籤，並將您登入您的工作區。

### <a name="create-a-cluster"></a>建立叢集

在 [ **Azure Databricks** ] 頁面的 [一般工作] 清單底下，選取 [**新增**叢集]。

請使用下表中的資訊來建立您的叢集：

| 設定 | 值 |
| ------- | ----- |
| 叢集名稱 | centralanalysis |
| 叢集模式 | Standard |
| Databricks Runtime 版本 | 5.5 LTS （Scala 2.11，Spark 2.4.3） |
| Python 版本 | 3 |
| 啟用自動調整功能 | 否 |
| 在閒置幾分鐘後終止 | 30 |
| 背景工作類型 | Standard_DS3_v2 |
| 背景工作角色 | 1 |
| 驅動程式類型 | 與背景工作角色相同 |

建立叢集可能需要幾分鐘的時間，然後等候叢集建立完成，再繼續進行。

### <a name="install-libraries"></a>安裝程式庫

**在 [叢集] 頁面上**，等候叢集狀態為 [**正在**執行]。

下列步驟示範如何將您的範例所需的程式庫匯入叢集：

1. **在 [叢集] 頁面上**，等候**centralanalysis**互動式叢集的狀態為 [**正在**執行]。

1. 選取叢集，然後選擇 [連結**庫**] 索引標籤。

1. 在 [連結**庫**] 索引標籤上，選擇 [**安裝新**的]。

1. 在 [**安裝程式庫**] 頁面上，選擇 [ **Maven** ] 作為程式庫來源。

1. 在 [**座標**] 文字方塊中，輸入下列值： `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. 選擇 [**安裝**] 以在叢集上安裝程式庫。

1. 現在**已安裝**媒體櫃狀態：

    ![已安裝媒體櫃](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>匯入 Databricks 筆記本

使用下列步驟匯入包含 Python 程式碼的 Databricks 筆記本，以分析 IoT Central 遙測並加以視覺化：

1. 流覽至 Databricks 環境中的 [**工作區**] 頁面。 選取您帳戶名稱旁的下拉式清單，然後選擇 [匯**入**]。

1. 選擇 [從 URL 匯入]，然後輸入下列位址： [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 若要匯入筆記本，請選擇 [匯**入**]。

1. 選取**工作區**以查看匯入的筆記本：

    ![匯入的筆記本](media/howto-create-custom-analytics/import-notebook.png)

1. 編輯第一個 Python 儲存格中的程式碼，以新增您先前儲存的事件中樞連接字串：

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>執行分析

若要執行分析，您必須將筆記本連接到叢集：

1. 選取 [卸**離**]，然後選取**centralanalysis**叢集。
1. 如果叢集未執行，請啟動它。
1. 若要啟動筆記本，請選取 [執行] 按鈕。

您可能會在最後一個資料格中看到錯誤。 若是如此，請檢查先前的儲存格是否正在執行，並等候一分鐘，將某些資料寫入至儲存體，然後再次執行最後一個儲存格。

### <a name="view-smoothed-data"></a>觀看平滑資料

在筆記本中，向下流覽至 [儲存格 14]，以查看裝置類型的滾動平均濕度圖。 當串流遙測抵達時，此繪圖會持續更新：

![平滑的遙測繪圖](media/howto-create-custom-analytics/telemetry-plot.png)

您可以在筆記本中調整圖表的大小。

### <a name="view-box-plots"></a>視圖框繪圖

在筆記本中，向下流覽至資料格20以查看方塊[繪圖](https://en.wikipedia.org/wiki/Box_plot)。 盒狀圖是以靜態資料為基礎，因此若要更新它們，您必須重新執行資料格：

![盒狀圖](media/howto-create-custom-analytics/box-plots.png)

您可以在筆記本中調整繪圖的大小。

## <a name="tidy-up"></a>整理一下

若要在此操作說明之後進行整理，並避免不必要的成本，請刪除 Azure 入口網站中的**IoTCentralAnalysis**資源群組。

您可以從應用程式內的 [**管理**] 頁面中刪除 IoT Central 應用程式。

## <a name="next-steps"></a>後續步驟

在此操作指南中，您已了解如何：

* 使用*連續資料匯出*，從 IoT Central 應用程式串流遙測。
* 建立 Azure Databricks 環境來分析和繪製遙測資料。

現在您已瞭解如何建立自訂分析，建議的下一個步驟是瞭解如何[在 Power BI 儀表板中視覺化和分析您的 Azure IoT Central 資料](../core/howto-connect-powerbi.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)。
