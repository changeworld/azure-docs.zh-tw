---
title: 使用自訂分析擴充 Azure IoT Central |Microsoft Docs
description: 作為解決方案開發人員，設定 IoT Central 應用程式來進行自訂分析和視覺效果。 此解決方案會使用 Azure Databricks。
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1e261e8d5d9cd147f3157303b7a2a50db7c33e58
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123040"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>使用 Azure Databricks 以自訂分析擴充 Azure IoT Central

本操作指南會示範如何以解決方案開發人員的身分，使用自訂分析和視覺效果擴充您的 IoT Central 應用程式。 此範例會使用 [Azure Databricks](/azure/azure-databricks/) 工作區來分析 IoT Central 遙測串流，以及產生視覺效果（例如 [盒狀繪圖](https://wikipedia.org/wiki/Box_plot)）。

本操作指南會示範如何將 IoT Central 延伸到超出 [內建分析工具](./howto-create-custom-analytics.md)的功能。

在本操作指南中，您將瞭解如何：

* 使用 *連續資料匯出*從 IoT Central 應用程式串流遙測資料。
* 建立 Azure Databricks 環境來分析和繪製裝置遙測。

## <a name="prerequisites"></a>必要條件

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 應用程式

使用下列設定，在 [Azure IoT Central application manager](https://aka.ms/iotcentral) 網站上建立 IoT Central 應用程式：

| 設定 | 值 |
| ------- | ----- |
| 定價方案 | 標準 |
| 應用程式範本 | 店內分析-條件監視 |
| 應用程式名稱 | 接受預設值或選擇您自己的名稱 |
| URL | 接受預設值，或選擇您自己唯一的 URL 前置詞 |
| 目錄 | 您的 Azure Active Directory 租使用者 |
| Azure 訂用帳戶 | 您的 Azure 訂用帳戶 |
| 區域 | 您最近的區域 |

本文中的範例和螢幕擷取畫面會使用 **美國** 地區。 選擇接近您的位置，並確定您在相同區域中建立所有資源。

此應用程式範本包含兩個會傳送遙測資料的模擬控溫器裝置。

### <a name="resource-group"></a>資源群組

使用 Azure 入口網站建立名為**IoTCentralAnalysis**的[資源群組](https://portal.azure.com/#create/Microsoft.ResourceGroup)，以包含您所建立的其他資源。 在與您的 IoT Central 應用程式相同的位置中建立您的 Azure 資源。

### <a name="event-hubs-namespace"></a>事件中樞命名空間

使用 Azure 入口網站來建立具有下列設定的 [事件中樞命名空間](https://portal.azure.com/#create/Microsoft.EventHub) ：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇您的命名空間名稱 |
| 定價層 | 基本 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | IoTCentralAnalysis |
| 位置 | 美國東部 |
| 輸送量單位 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作區

使用 [Azure 入口網站來建立](https://portal.azure.com/#create/Microsoft.Databricks) 具有下列設定的 Azure Databricks 服務：

| 設定 | 值 |
| ------- | ----- |
| 工作區名稱    | 選擇您的工作區名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | IoTCentralAnalysis |
| 位置 | 美國東部 |
| 定價層 | 標準 |

當您建立必要的資源之後，您的 **IoTCentralAnalysis** 資源群組看起來會像下列螢幕擷取畫面：

![IoT Central 分析資源群組](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>建立事件中樞

您可以設定 IoT Central 的應用程式，以持續將遙測匯出至事件中樞。 在本節中，您會建立事件中樞，以從您的 IoT Central 應用程式接收遙測。 事件中樞會將遙測傳遞給您的串流分析作業，以進行處理。

1. 在 Azure 入口網站中，流覽至您的事件中樞命名空間，然後選取 [ **+ 事件中樞**]。
1. 為您的事件中樞命名 **centralexport**，然後選取 [ **建立**]。
1. 在命名空間中的事件中樞清單中，選取 [ **centralexport**]。 然後選擇 [ **共用存取原則**]。
1. 選取 [+ 新增] 。 使用**接聽**宣告來建立名為**接聽**的原則。
1. 當原則就緒時，請在清單中選取該原則，然後複製 [ **連接字串-主要金鑰** ] 值。
1. 記下此連接字串，您稍後會在設定 Databricks 筆記本以從事件中樞讀取時使用它。

您的事件中樞命名空間如下列螢幕擷取畫面所示：

![事件中樞命名空間](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>在 IoT Central 中設定匯出

在 [Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral) 網站上，流覽至您從 Contoso 範本建立的 IoT Central 應用程式。 在本節中，您會設定應用程式，以將遙測從模擬裝置串流至您的事件中樞。 若要設定匯出：

1. 流覽至 [ **資料匯出** ] 頁面，選取 [ **+ 新增**]，然後 **Azure 事件中樞**]。
1. 使用下列設定來設定匯出，然後選取 [ **儲存**]：

    | 設定 | 值 |
    | ------- | ----- |
    | 顯示名稱 | 匯出至事件中樞 |
    | 啟用 | 開啟 |
    | 事件中樞命名空間 | 您的事件中樞命名空間名稱 |
    | 事件中樞 | centralexport |
    | 量測 | 開啟 |
    | 裝置 | 關閉 |
    | 裝置範本 | 關閉 |

![資料匯出設定](media/howto-create-custom-analytics/cde-configuration.png)

等候匯出狀態為 [ **正在** 執行]，然後再繼續。

## <a name="configure-databricks-workspace"></a>設定 Databricks 工作區

在 Azure 入口網站中，流覽至您的 Azure Databricks 服務，然後選取 [ **啟動工作區**]。 在您的瀏覽器中開啟新的索引標籤，並將您登入您的工作區。

### <a name="create-a-cluster"></a>建立叢集

在 [ **Azure Databricks** ] 頁面的 [一般工作] 清單下，選取 [ **新增**叢集]。

使用下表中的資訊來建立您的叢集：

| 設定 | 值 |
| ------- | ----- |
| 叢集名稱 | centralanalysis |
| 叢集模式 | 標準 |
| Databricks Runtime 版本 | 5.5 LTS (Scala 2.11、Spark 2.4.3)  |
| Python 版本 | 3 |
| 啟用自動調整 | 否 |
| 在停止活動幾分鐘後終止 | 30 |
| 背景工作類型 | Standard_DS3_v2 |
| 背景工作角色 | 1 |
| 驅動程式類型 | 與背景工作相同 |

建立叢集可能需要幾分鐘的時間，等候叢集建立完成，再繼續進行。

### <a name="install-libraries"></a>安裝程式庫

**在 [叢集] 頁面上**，等候叢集狀態為 [**正在**執行]。

下列步驟說明如何將範例所需的程式庫匯入到叢集中：

1. **在 [叢集] 頁面上**，等待**centralanalysis**互動式叢集的狀態為 [**正在**執行]。

1. 選取叢集，然後選擇 [連結 **庫** ] 索引標籤。

1. 在 [ **媒體** 櫃] 索引標籤上，選擇 [ **安裝新**的]。

1. 在 [ **安裝程式庫** ] 頁面上，選擇 [ **Maven** ] 做為 [程式庫來源]。

1. 在 [ **座標** ] 文字方塊中，輸入下列值： `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. 選擇 [ **安裝** ] 以在叢集上安裝程式庫。

1. 現在 **已安裝**程式庫狀態：

    ![程式庫已安裝](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>匯入 Databricks 筆記本

使用下列步驟來匯入包含 Python 程式碼的 Databricks 筆記本，以分析您的 IoT Central 遙測並將其視覺化：

1. 流覽至 Databricks 環境中的 [ **工作區** ] 頁面。 選取帳戶名稱旁邊的下拉式清單，然後選擇 [匯 **入**]。

1. 選擇從 URL 匯入，然後輸入下列位址： [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 若要匯入筆記本，請選擇 [匯 **入**]。

1. 選取 **工作區** 以查看匯入的筆記本：

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

若要執行分析，您必須將筆記本附加至叢集：

1. 選取 [已卸 **離** ]，然後選取 **centralanalysis** 叢集。
1. 如果叢集未執行，請啟動它。
1. 若要啟動筆記本，請選取 [執行] 按鈕。

您可能會在最後一個資料格中看到錯誤。 若是如此，請檢查先前的儲存格是否正在執行，等候幾分鐘的時間，將某些資料寫入至儲存體，然後再次執行最後一個資料格。

### <a name="view-smoothed-data"></a>查看平滑資料

在筆記本中，向下流覽至儲存格14，以查看依裝置類型的輪流平均濕度繪圖。 當串流遙測抵達時，此圖會持續更新：

![平滑的遙測繪圖](media/howto-create-custom-analytics/telemetry-plot.png)

您可以調整筆記本中圖表的大小。

### <a name="view-box-plots"></a>視圖框繪圖

在筆記本中，向下滾動至儲存格20以查看 [盒](https://en.wikipedia.org/wiki/Box_plot)狀圖。 盒狀圖是以靜態資料為基礎，因此若要進行更新，您必須重新執行資料格：

![盒狀圖](media/howto-create-custom-analytics/box-plots.png)

您可以調整筆記本中的繪圖大小。

## <a name="tidy-up"></a>整理一下

若要在此操作說明之後整理，並避免不必要的成本，請刪除 Azure 入口網站中的 **IoTCentralAnalysis** 資源群組。

您可以從應用程式內的 **管理** 頁面刪除 IoT Central 應用程式。

## <a name="next-steps"></a>下一步

在此操作指南中，您已了解如何：

* 使用 *連續資料匯出*從 IoT Central 應用程式串流遙測資料。
* 建立 Azure Databricks 環境來分析和繪製遙測資料。

現在您已瞭解如何建立自訂分析，建議的下一個步驟是瞭解如何 [在 Power BI 儀表板中視覺化及分析您的 Azure IoT Central 資料](howto-connect-powerbi.md)。