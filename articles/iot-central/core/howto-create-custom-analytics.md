---
title: 通過自訂分析擴展 Azure IoT 中心 |微軟文檔
description: 作為解決方案開發人員，配置 IoT 中心應用程式以執行自訂分析和視覺化。 此解決方案使用 Azure 資料塊。
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158192"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>使用 Azure 資料塊自訂分析擴展 Azure IoT 中心

本指南介紹您作為解決方案開發人員如何使用自訂分析和視覺化來擴展 IoT Central 應用程式。 該示例使用[Azure 資料塊](https://docs.microsoft.com/azure/azure-databricks/)工作區來分析 IoT 中央遙測流並生成視覺化效果（如[框圖](https://wikipedia.org/wiki/Box_plot)）。

此操作指南演示如何將 IoT Central 擴展到其已經可以使用[內置分析工具](./howto-create-custom-analytics.md)時可以完成的內容。

在此指南中，您將瞭解如何：

* 使用*連續資料匯出*從 IoT 中央應用程式資料流遙測。
* 創建 Azure 資料塊環境以分析和繪製設備遙測。

## <a name="prerequisites"></a>Prerequisites

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 應用程式

在[Azure IoT 中央應用程式管理器](https://aka.ms/iotcentral)網站上創建具有以下設置的 IoT 中心應用程式：

| 設定 | 值 |
| ------- | ----- |
| 定價方案 | 標準 |
| 應用程式範本 | 店內分析 + 狀態監控 |
| 應用程式名稱 | 接受預設值或選擇您自己的名稱 |
| URL | 接受預設值或選擇您自己的唯一 URL 首碼 |
| 目錄 | Azure 活動目錄租戶 |
| Azure 訂用帳戶 | 您的 Azure 訂用帳戶 |
| 區域 | 您最近的區域 |

本文中的示例和螢幕截圖使用**美國**區域。 選擇靠近您的位置，並確保在同一區域中創建所有資源。

此應用程式範本包括兩個發送遙測的類比恒溫器設備。

### <a name="resource-group"></a>資源群組

使用[Azure 門戶創建](https://portal.azure.com/#create/Microsoft.ResourceGroup)名為**IoTCentralAnalysis**的資源組以包含您創建的其他資源。 在 IoT 中央應用程式相同的位置創建 Azure 資源。

### <a name="event-hubs-namespace"></a>事件中樞命名空間

使用[Azure 門戶創建具有以下設置的事件中心命名空間](https://portal.azure.com/#create/Microsoft.EventHub)：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇命名空間名稱 |
| 定價層 | 基本 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | 物聯網分析 |
| Location | 美國東部 |
| 輸送量單位 | 1 |

### <a name="azure-databricks-workspace"></a>Azure 資料塊工作區

使用[Azure 門戶創建具有以下設置的 Azure 資料磚塊服務](https://portal.azure.com/#create/Microsoft.Databricks)：

| 設定 | 值 |
| ------- | ----- |
| 工作區名稱    | 選擇工作區名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | 物聯網分析 |
| Location | 美國東部 |
| 定價層 | 標準 |

創建所需資源後 **，IoTCentralAnalysis**資源組如下所示：

![IoT 中央分析資源組](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>建立事件中樞

您可以將 IoT 中央應用程式佈建為連續將遙測資料匯出到事件中心。 在本節中，您將創建一個事件中心來接收來自 IoT 中央應用程式的遙測資料。 事件中心將遙測資料傳遞到流分析作業進行處理。

1. 在 Azure 門戶中，導航到事件中心命名空間，然後選擇 **+ 事件中心**。
1. 命名事件中心**集中匯出**，然後選擇 **"創建**"。
1. 在命名空間中的事件中心清單中，選擇**集中匯出**。 然後選擇**共用訪問策略**。
1. 選擇 **= 添加**。 使用 **"偵聽"** 聲明創建名為 **"偵聽**"的策略。
1. 策略準備就緒後，在清單中選擇它，然後複製**連接字串主鍵**值。
1. 記下此連接字串，稍後在將 Databricks 筆記本配置為從事件中心讀取時使用它。

事件中心命名空間類似于以下螢幕截圖：

![事件中樞命名空間](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>在 IoT 中心設定匯出

在[Azure IoT 中央應用程式管理器](https://aka.ms/iotcentral)網站上，導航到從 Contoso 範本創建的 IoT 中央應用程式。 在本節中，您將應用程式佈建為將遙測資料從其類比設備資料流到事件中心。 要設定匯出：

1. 導航到 **"資料匯出"** 頁，選擇 **" 新建**"，然後選擇**Azure 事件中心**。
1. 使用以下設置設定匯出，然後選擇 **"保存**" ：

    | 設定 | 值 |
    | ------- | ----- |
    | 顯示名稱 | 匯出到事件中心 |
    | 啟用 | 另一 |
    | 事件中樞命名空間 | 事件中心命名空間名稱 |
    | 事件中樞 | 中央出口 |
    | 量測 | 另一 |
    | 裝置 | 關閉 |
    | 裝置範本 | 關閉 |

![資料匯出配置](media/howto-create-custom-analytics/cde-configuration.png)

等待匯出狀態**為"正在運行"，** 然後再繼續。

## <a name="configure-databricks-workspace"></a>配置資料磚塊工作區

在 Azure 門戶中，導航到 Azure 資料磚塊服務並選擇 **"啟動工作區**"。 瀏覽器中將打開一個新選項卡，並將您登錄到工作區。

### <a name="create-a-cluster"></a>建立叢集

在**Azure 資料磚塊**頁上，在常見工作清單下，選擇 **"新群集**"。

使用下表中的資訊創建群集：

| 設定 | 值 |
| ------- | ----- |
| 叢集名稱 | 中央分析 |
| 群集模式 | 標準 |
| 資料磚執行階段版本 | 5.5 LTS （Scala 2.11， 火花 2.4.3） |
| Python 版本 | 3 |
| 啟用自動調整 | 否 |
| 在非活動幾分鐘後終止 | 30 |
| 輔助角色類型 | Standard_DS3_v2 |
| 背景工作角色 | 1 |
| 驅動程式類型 | 與工作人員相同 |

創建群集可能需要幾分鐘時間，等待群集創建完成，然後再繼續。

### <a name="install-libraries"></a>安裝程式庫

在 **"群集"** 頁上，等待群集狀態**正在運行**。

以下步驟演示如何將示例所需的庫導入群集：

1. 在 **"群集"** 頁上，等待集中**分析**互動式群集的狀態**正在運行**。

1. 選擇群集，然後選擇"**庫"** 選項卡。

1. 在 **"庫"** 選項卡上，選擇 **"安裝新建**"。

1. 在 **"安裝庫"** 頁上，選擇**Maven**作為庫源。

1. 在 **"座標**"文字方塊中，輸入以下值：`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. 選擇 **"安裝**"以在群集上安裝庫。

1. 庫狀態現在**已安裝**：

    ![已安裝庫](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>導入資料磚塊筆記本

使用以下步驟導入包含 Python 代碼的資料磚筆記本來分析和視覺化 IoT 中心遙測：

1. 導航到資料磚塊環境中的**工作區**頁面。 選擇帳戶名稱旁邊的下拉，然後選擇 **"導入**"。

1. 選擇從 URL 導入並輸入以下位址：[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 要導入筆記本，請選擇 **"導入**"。

1. 選擇**工作區**以查看導入的筆記本：

    ![導入的筆記本](media/howto-create-custom-analytics/import-notebook.png)

1. 編輯第一個 Python 儲存格中的代碼以添加以前保存的事件中心連接字串：

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>運行分析

要運行分析，必須將筆記本附加到群集：

1. 選擇 **"分離"，** 然後選擇**中央分析**群集。
1. 如果群集未運行，則啟動它。
1. 要啟動筆記本，請選擇運行按鈕。

您可能會在最後一個儲存格中看到錯誤。 如果是這樣，請檢查以前的儲存格正在運行，等待一分鐘，等待一些資料寫入存儲，然後再次運行最後一個儲存格。

### <a name="view-smoothed-data"></a>查看平滑資料

在筆記本中，向下滾動到儲存格 14 以查看按裝置類型滾動平均濕度的繪圖。 當流式遙測到達時，此繪圖會不斷更新：

![平滑遙測圖](media/howto-create-custom-analytics/telemetry-plot.png)

您可以在筆記本中調整圖表的大小。

### <a name="view-box-plots"></a>查看框圖

在筆記本中，向下滾動到儲存格 20 以查看[框圖](https://en.wikipedia.org/wiki/Box_plot)。 框圖基於靜態資料，因此要更新它們，必須重新運行儲存格：

![框圖](media/howto-create-custom-analytics/box-plots.png)

您可以調整筆記本中的繪圖大小。

## <a name="tidy-up"></a>整理一下

要在此之後進行整理並避免不必要的成本，請刪除 Azure 門戶中的**IoTCentralAnalysis**資源組。

可以從**應用程式中的管理頁面**中刪除 IoT 中心應用程式。

## <a name="next-steps"></a>後續步驟

在此操作指南中，您已了解如何：

* 使用*連續資料匯出*從 IoT 中央應用程式資料流遙測。
* 創建 Azure 資料塊環境以分析和繪製遙測資料。

現在您已經知道如何創建自訂分析，建議的下一步是瞭解如何[管理應用程式](howto-administer.md)。
