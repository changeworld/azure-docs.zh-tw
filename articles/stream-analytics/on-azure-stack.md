---
title: '在 Azure Stack 上執行 Azure 串流分析 (預覽版) '
description: 建立 Azure 串流分析 edge 作業，並透過 IoT Edge 執行時間將其部署至 Azure Stack hub。
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: 3463b3eae96c0a65206023ed0f21efe44294d4eb
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020123"
---
# <a name="run-azure-stream-analytics-on-azure-stack-preview"></a>在 Azure Stack 上執行 Azure 串流分析 (預覽版) 

> [!IMPORTANT]
> 這項功能目前為預覽狀態，因此不建議用於生產環境。

您可以在 Azure Stack Hub 上執行 Azure 串流分析，作為 IoT Edge 模組。 已將設定新增至 IoT Edge 模組，讓它能夠與在 Azure Stack Hub 訂用帳戶中執行的 blob 儲存體、事件中樞和 IoT 中樞互動，方法是允許在每個 Azure Stack Hub 雇用中找到的自訂 Url。

透過 Azure Stack 上的串流分析，您可以在自己的私用雲端中建立串流處理的真正混合式架構，您可以在內部部署使用一致的 Azure 服務與雲端原生應用程式連線或中斷連線。 

本文說明如何將 IoT 中樞或事件中樞的資料串流至 Azure Stack Hub 訂用帳戶中的另一個事件中樞或 Blob 儲存體，並使用串流分析進行處理。

## <a name="set-up-environments"></a>設定環境

Azure 串流分析是 Azure Stack Hub 上的混合式服務。 它是在 Azure 中設定的 IoT Edge 模組，但可在 Azure Stack Hub 上執行。  

如果您不熟悉 Azure Stack Hub 或 IoT Edge，請依照下列指示來設定環境。

### <a name="prepare-the-azure-stack-hub-environment"></a>準備 Azure Stack Hub 環境

建立 Azure Stack Hub 訂用帳戶。 如需詳細資訊，請參閱[建立 Azure Stack Hub 訂](/azure-stack/user/azure-stack-subscribe-services/)用帳戶的教學課程。

如果您想要在自己的伺服器上評估 Azure Stack Hub，您可以使用 Azure Stack 開發套件 (ASDK) 。  如需 ASDK 的詳細資訊，請參閱 [ASDK 總覽](/azure-stack/asdk/)。

### <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

若要在 Azure Stack Hub 上執行 Azure 串流分析，您的裝置必須具有 IoT Edge 執行時間，而且必須具有 Azure Stack Hub 的網路連線，或是在 Azure Stack Hub 上執行的 VM。 IoT Edge 執行時間可讓串流分析 Edge 作業與在 Azure Stack Hub 上執行的 Azure 儲存體和 Azure 事件中樞進行整合。 如需詳細資訊，請參閱 [Azure IoT Edge 串流分析](stream-analytics-edge.md) 

除了具有 Azure Stack Hub 資源的網路存取權之外，IoT Edge 裝置 (或 VM) 需要存取 Azure 公用雲端中的 Azure IoT 中樞，以設定串流分析模組。 

下列指南說明如何在您的裝置或 VM 上設定 IoT Edge 執行時間：

* [在 Windows 上安裝 Azure IoT Edge 執行階段](../iot-edge/how-to-install-iot-edge.md)
* [在以 Debian 為基礎的 Linux 系統上安裝 Azure IoT Edge 執行時間](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>建立 Azure 串流分析 Edge 作業

ASA Edge 作業在部署到 Azure IoT Edge 裝置的容器中執行。 它們是由兩個部分組成：
* 負責作業定義的雲端部分：使用者在雲端中定義輸入、輸出、查詢和其他設定 (出問題的事件等)。
* 在您的 IoT 裝置上執行的模組。 它包含 ASA 引擎，並會從雲端接收作業定義。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

當您建立要在 IoT Edge 裝置上執行的 Azure 串流分析作業時，該作業必須以可從裝置呼叫的方式儲存。 您可以使用現有的 Azure 儲存體帳戶，或建立一個新帳戶。
1. 在 Azure 入口網站中，移至 [ **建立資源] > 儲存體 > 儲存體帳戶-blob、檔案、資料表、佇列**。
2. 提供下列值以建立您的儲存體帳戶：

   | 欄位 | 值 |
   | --- | --- |
   | 名稱 | 提供儲存體帳戶的唯一名稱。 |
   | Location | 選擇接近您的位置。|
   | 訂用帳戶 | 選擇與您的 IoT 中樞相同的訂用帳戶。|
   | 資源群組 | 建議您在 [IoT Edge 快速入門](../iot-edge/quickstart.md) 和教學課程期間建立的所有測試資源，使用相同的資源群組。 例如 **IoTEdgeResources**。 |

3. 將其他欄位保留為預設值，然後選取 [建立]。


### <a name="create-a-new-job"></a>建立新作業

1. 在 Azure 入口網站中，移至 [ **建立資源] > 物聯網 > 串流分析作業**。
2. 提供下列值以建立您的儲存體帳戶：

   | 欄位 | 值 |
   | --- | --- |
   | 作業名稱 | 為您的作業提供名稱。 例如 **IoTEdgeJob** |
   | 訂用帳戶 | 選擇與您的 IoT 中樞相同的訂用帳戶。|
   | 資源群組 | 建議您在 [IoT Edge 快速入門](../iot-edge/quickstart.md) 和教學課程期間建立的所有測試資源，使用相同的資源群組。 例如 **IoTEdgeResources**。 |
   | Location | 選擇接近您的位置。 |
   | 裝載環境 | 選取 [邊緣]。 |

3. 選取 [建立]。

### <a name="configure-your-job"></a>設定您的作業

在 Azure 入口網站中建立您的串流分析作業後，您可以為其設定輸入、輸出和查詢，以對通過的資料執行。 您可以從 IoT 中樞或 Azure Stack Hub 訂用帳戶中的事件中樞手動指定輸入。

1. 在 Azure 入口網站中瀏覽至您的串流分析作業。
2. 在 [ **設定**] 底下，選取 [ **儲存體帳戶設定** ]，然後選擇您在上一個步驟中建立的儲存體帳戶。
   > [!div class="mx-imgBorder"]
   > [![作業儲存體帳戶設定 ](media/on-azure-stack/storage-account-settings.png)](media/on-azure-stack/storage-account-settings.png#lightbox)
3. 在 [ **作業拓撲**] 下，選取 [ **輸入** ]，然後 **新增資料流程輸入。**
4. 從下拉式清單中選擇 **IoT 中樞**、 **事件中樞** 或 **Edge 中樞** 。 
5. 如果輸入是 Azure Stack Hub 訂用帳戶中的事件中樞或 IoT 中樞，請以手動方式提供資訊，如下所示。

   #### <a name="event-hub"></a>事件中樞

   | 欄位 | 值 |
   | --- | --- |
   | 輸入別名 | 在作業查詢中用來參考這個輸入的易記名稱。 |
   | 服務匯流排命名空間 | 命名空間是一組訊息實體的容器。 在建立新的事件中樞時，也會建立此命名空間。  (範例： *sb:// <Event Hub Name> . eventhub.shanghai.azurestack.corp.microsoft.com*)  |
   | 事件中樞名稱 | 作為輸入的事件中樞名稱。 |
   | 事件中樞原則名稱 | 支援存取事件中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 此選項會自動填入，除非您選取手動提供事件中樞設定的選項。 |
   | 事件中樞原則金鑰 | 用來授權存取事件中樞的共用存取金鑰。 此選項會自動填入，除非您選取手動提供事件中樞設定的選項。 您可以在事件中樞設定中找到它。 |
   | 事件中樞取用者群組 (選用)  | 強烈建議您為每一個串流分析作業使用不同的取用者群組。 此字串可識別要用來從事件中樞擷取資料的取用者群組。 若未指定取用者群組，串流分析作業會使用 $Default 取用者群組。 |
   | 資料分割計數 | 分割區計數是事件中樞內的資料分割數目。 |

   > [!div class="mx-imgBorder"]
   > [![事件中樞輸入 ](media/on-azure-stack/event-hub-input.png)](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT 中樞

   | 欄位 | 值 |
   | --- | --- |
   | 輸入別名 | 在作業查詢中用來參考這個輸入的易記名稱。 |
   | IoT 中樞 | 要作為輸入的 IoT 中樞的名稱。  (範例：*<IoT Hub Name> . shanghai.azurestack.corp.microsoft.com*)  |
   | 共用存取原則名稱 | 支援存取 IoT 中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
   | 共用存取原則金鑰 | 用來授與 IoT 中樞存取權的共用存取金鑰。 此選項會自動填入，除非您選取手動提供 IoT 中樞設定的選項。 |
   | 取用者群組 (選用)  | 強烈建議讓每個串流分析作業使用不同的取用者群組。 用來從 IoT 中樞擷取資料的取用者群組。 串流分析會使用 $Default 取用者群組，除非您另有指定。 |
   | 資料分割計數 | 分割區計數是事件中樞內的資料分割數目。 |

   > [!div class="mx-imgBorder"]
   > [![IoT 中樞輸入 ](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

6. 保留其他欄位的預設值，然後選取 [儲存]。
7. 在 [作業拓撲] 下方開啟 [輸出]，然後選取 [新增]。
8. 從下拉式清單中選擇 Blob 儲存體、事件中樞或 Edge 中樞。
9. 如果輸出是 Azure Stack Hub 訂用帳戶中的事件中樞或 Blob 儲存體，請以手動方式提供資訊，如下所示。

   #### <a name="event-hub"></a>事件中樞

   | 欄位 | 值 |
   | --- | --- |
   | 輸出別名 | 此為易記名稱，用於在查詢中將查詢輸出指向這個事件中樞。 |
   | 服務匯流排命名空間 | 一組訊息實體的容器。 當您建立新的事件中樞時，也會建立服務匯流排命名空間。 (範例： *sb:// <Event Hub Name> . eventhub.shanghai.azurestack.corp.microsoft.com*)  |
   | 事件中樞名稱 | 事件中樞輸出的名稱。 |
   | 事件中樞原則名稱 | 共用存取原則，您可以在事件中樞的 [設定] 索引標籤上建立。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
   | 事件中樞原則金鑰 | 用來驗證事件中樞命名空間存取權的共用存取金鑰。 |

   > [!div class="mx-imgBorder"]
   > [![事件中樞輸出 ](media/on-azure-stack/event-hub-output.png)](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob 儲存體 

   | 欄位 | 值 |
   | --- | --- |
   | 輸出別名 | 此為易記名稱，用於在查詢中將查詢輸出指向這個 blob 儲存體。 |
   | 儲存體帳戶 | 您要將輸出傳送至其中的儲存體帳戶名稱。 (範例： *<Storage Account Name> . blob.shanghai.azurestack.corp.microsoft.com*)  |
   | 儲存體帳戶金鑰 | 與儲存體帳戶相關聯的密碼金鑰。 此選項會自動填入，除非您選取手動提供 Blob 儲存體設定的選項。 |

> [!NOTE]
> Azure Stack Hub 上的 edge 作業不支援 Parquet 格式。 針對最小資料列和最大時間，請使用0或將其保留空白。


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>在連線到 Azure Stack 的 VM 或裝置上部署串流分析

1. 在 Azure 入口網站中，開啟 IoT 中樞。 流覽至 **IoT Edge** ，然後按一下您想要作為此部署目標的裝置 (VM) 。
2. 選取 [設定模組]。 然後，選取 [ **+ 新增** ] 並選擇 [ **Azure 串流分析模組**]。 
3. 選取您所建立的訂用帳戶和串流分析 Edge 作業。 按一下 [ **儲存** ]，然後選取 **[下一步：路由]**。

   > [!div class="mx-imgBorder"]
   > [![新增模組 ](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

4. 按一下 [ **審核 + 建立 >**]。
5. 在 [ **審核 + 建立** ] 步驟中，選取 [ **建立**]。 
   > [!div class="mx-imgBorder"]
   > [![資訊清單 ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. 確認模組已新增至清單。
   > [!div class="mx-imgBorder"]
   > [![部署頁面 ](media/on-azure-stack/edge-deployment.png)](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>後續步驟
- [Azure IoT Edge 串流分析](./stream-analytics-edge.md)
- [開發串流分析 Edge 作業](/stream-analytics-query/stream-analytics-query-language-reference)