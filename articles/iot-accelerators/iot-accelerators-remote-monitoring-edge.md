---
title: 偵測解決方案中邊緣異常狀況的教學課程 - Azure | Microsoft Docs
description: 在本教學課程中，您將了解如何使用遠端監視解決方案加速器來監視 IoT Edge 裝置。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 43ba14845765230b9a54c2b34dbc7ccd53af950b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969998"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>教學課程：使用遠端監視解決方案加速器來偵測異常狀況

在本教學課程中，您會設定遠端監視解決方案，以因應 IoT Edge 裝置所偵測到的異常狀況。 IoT Edge 裝置可讓您在 Edge 處理遙測資料，以減少傳送至解決方案的遙測資料數量，並加快對裝置事件的回應速度。 若要深入了解 Edge 處理的優點，請參閱[什麼是 Azure IoT Edge](../iot-edge/about-iot-edge.md)。

為了介紹遠端監視的 Edge 處理，本教學課程將使用模擬的油泵機裝置。 此油泵機裝置是由名為 Contoso 的組織所管理，且已連線到遠端監視解決方案加速器。 油泵機上的感應器會測量溫度和壓力。 Contoso 的操作員知道溫度異常升高可能會導致油泵機速度變慢。 在溫度未超出正常範圍時，Contoso 的操作員不需要監視裝置的溫度。

Contoso 想要將智慧型 Edge 模組部署到油泵機，以偵測溫度異常狀況。 另一個 Edge 模組會將警示傳送至遠端監視解決方案。 在收到警示時，Contoso 操作員可派遣維修技術人員前往處理。 Contoso 也可以設定會在解決方案收到警示時執行的自動化動作，例如傳送電子郵件。

下圖顯示教學課程案例的主要元件：

![圖表顯示在 IoT Edge 裝置中，針對遙測和命令連接到 IoT 串流分析模組的油泵連動機。 篩選的遙測會移至雲端中遠端監視解決方案加速器中的 IoT Edge 裝置。 雲端也包含部署和套件。 部署會在裝置中部署 IoT Edge 執行階段。](media/iot-accelerators-remote-monitoring-edge/overview.png)

在本教學課程中，您：

>[!div class="checklist"]
> * 將 IoT Edge 裝置新增至解決方案
> * 建立 Edge 資訊清單
> * 匯入資訊清單作為套件，以定義要在裝置上執行的模組
> * 將套件部署到您的 IoT Edge 裝置
> * 從裝置檢視警示

在 IoT Edge 裝置上：

* 執行階段會收到套件並安裝模組。
* 串流分析模組會偵測幫浦的溫度異常狀況，並傳送命令以解決此問題。
* 串流分析模組會將已篩選的資料轉送到解決方案加速器。

本教學課程會使用 Linux 虛擬機器作為 IoT Edge 裝置。 您也安裝了 Edge 模組來模擬油料幫浦千斤頂裝置。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>新增 IoT Edge 裝置

要將 IoT Edge 裝置新增至遠端監視解決方案加速器，必須執行兩個步驟。 本節說明如何：

* 在遠端監視 Web UI 中的 [Device Explorer]**** 頁面上新增 IoT Edge 裝置。
* 在 Linux 虛擬機器 (VM) 上安裝 IoT Edge 執行階段。

### <a name="add-an-iot-edge-device-to-your-solution"></a>將 IoT Edge 裝置新增至解決方案

若要將 IoT Edge 裝置新增至遠端監視解決方案加速器，請瀏覽至 Web UI 中的 [Device Explorer]**** 頁面，然後按一下 [+ 新增裝置]****。

在 [新增裝置]**** 面板中選擇 **IoT Edge 裝置**，然後輸入 **oil-pump** 作為裝置識別碼。 您可以將其他設定保留為預設值。 然後，按一下 [套用]：

[![新增 IoT Edge 裝置](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

記下裝置連接字串，您在本教學課程的下一節將用到此字串。

當您在遠端監視解決方案加速器中對 IoT 中樞註冊裝置時，該裝置會列在 Web UI 中的 [Device Explorer]**** 頁面上：

[![新增 IoT Edge 裝置](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

為了方便管理解決方案中的 IoT Edge 裝置，請建立裝置群組，並新增 IoT Edge 裝置：

1. 在 [Device Explorer] **** 頁面上的清單中選取 **oil-pump** 裝置，然後按一下 [作業]****。

1. 使用下列設定，建立將 **IsEdge** 標記新增至裝置的作業：

    | 設定 | 值 |
    | ------- | ----- |
    | 工作 (Job)     | 標籤  |
    | 作業名稱 | AddEdgeTag |
    | 答案     | IsOilPump |
    | 值   | Y     |
    | 類型    | Text  |

    [![新增標記](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. 依序按一下 [套用]**** 和 [關閉]****。

1. 在 [Device Explorer] 頁面上，按一下 [管理裝置群組]。

1. 按一下 [建立新的裝置群組]****。 使用下列設定建立新的裝置群組：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱    | OilPumps |
    | 欄位   | Tags.IsOilPump |
    | 運算子 | = Equals |
    | 值    | Y |
    | 類型     | Text |

    [![建立裝置群組](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. 按一下 [檔案]  。

您的 IoT Edge 裝置現在位於 **OilPumps** 群組中。

### <a name="install-the-edge-runtime"></a>安裝 Edge 執行階段

Edge 裝置需要安裝 Edge 執行階段。 在本教學課程中，您將在 Azure Linux VM 中安裝 Edge 執行階段以測試案例。 下列步驟會使用 Azure Cloud Shell 來安裝及設定 VM：

1. 若要在 Azure 中建立 Linux VM，請執行下列命令。 您可以使用您附近的位置：

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. 若要使用裝置連接字串設定 Edge 執行階段，請使用您先前記下的裝置連接字串來執行下列命令：

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    請務必將連接字串包含在雙引號內。

您現已在 Linux 裝置上安裝並設定 IoT Edge 執行階段。 在本教學課程的後續步驟中，您將使用遠端監視解決方案將 IoT Edge 模組部署至此裝置。

## <a name="create-an-edge-manifest"></a>建立 Edge 資訊清單

若要模擬油泵機裝置，您必須將下列模組新增至 Edge 裝置：

* 溫度模擬模組。
* Azure 串流分析異常偵測。

下列步驟說明如何建立包含這些模組的 Edge 部署資訊清單。 在本教學課程的後續步驟中，您將匯入此資訊清單，作為遠端監視解決方案加速器中的套件。

### <a name="create-the-azure-stream-analytics-job"></a>建立 Azure 串流分析作業

您在入口網站中定義串流分析作業，然後將其封裝為 Edge 模組。

1. 在 Azure 入口網站中，使用 **IoTEdgeDevices** 資源群組中的預設選項建立 Azure 儲存體帳戶。 請記下您選擇的名稱。

1. 在 Azure 入口網站中，在 **IoTEdgeDevices** 資源群組中建立**串流分析作業**。 請使用下列組態值︰

    | 選項 | 值 |
    | ------ | ----- |
    | 作業名稱 | EdgeDeviceJob |
    | 訂用帳戶 | 您的 Azure 訂用帳戶 |
    | 資源群組 | IoTEdgeDevices |
    | 位置 | 美國東部 |
    | 裝載環境 | Edge |
    | 串流單位 | 1 |

1. 在入口網站中開啟 **EdgeDeviceJob** 串流分析作業，按一下 [輸入]，然後新增名為**遙測**的 **Edge 中樞**資料流輸入。

1. 在入口網站中的 **EdgeDeviceJob** 串流分析作業中，按一下 [輸出]****，然後新增名為**輸出**的 **Edge 中樞**輸出。

1. 在入口網站中的 **EdgeDeviceJob** 串流分析作業中，按一下 [輸出]****，然後新增名為**警示**的第二個 **Edge 中樞**輸出。

1. 在入口網站中的 **EdgeDeviceJob** 串流分析作業中，按一下 [查詢]****，然後新增下列 **select** 陳述式：

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. 在入口網站中的 **EdgeDeviceJob** 串流分析作業中，按一下 [儲存體帳戶設定]****。 新增您在本節一開始新增至 **IoTEdgeDevices** 資源群組的儲存體帳戶。 建立名為 **edgeconfig** 的新容器。

下列螢幕擷取畫面顯示已儲存的串流分析作業：

[![串流分析作業](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

您現在已定義要在 Edge 裝置上執行的串流分析作業。 此作業會計算以 5 秒為時間範圍的平均溫度。 此作業也會在以 3 秒為時間範圍的平均溫度超過 400 時傳送警示。

### <a name="create-the-iot-edge-deployment"></a>建立 IoT Edge 部署

接下來，您可以建立 IoT Edge 部署資訊清單，以定義要在 Edge 裝置上執行的模組。 在下一節中，您將匯入此資訊清單，作為遠端監視解決方案中的套件。

1. 在 Azure 入口網站中，瀏覽至您遠端監視解決方案中的 IoT 中樞。 您可以在與您的遠端監視解決方案同名的資源群組中找到此 IoT 中樞。

1. 在 IoT 中樞中，按一下 [自動裝置管理]**** 區段中的 [IoT 中樞]****。 按一下 [新增 IoT Edge 部署]****。

1. 在 [建立部署 > 名稱和標籤]**** 頁面上，輸入名稱 **oil-pump-device**。 按 [下一步]  。

1. 在 [建立部署 > 新增模組]**** 頁面上，按一下 [+ 新增]****。 選擇 [IoT Edge 模組]****。

1. 在 [IoT Edge 自訂模組]**** 面板中輸入 **temperatureSensor** 作為名稱，並輸入 **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** 作為映像 URI。 按一下 [檔案]  。

1. 在 [建立部署 > 新增模組]**** 頁面上，按一下 [+ 新增]**** 以新增第二個模組。 選擇 [Azure 串流分析模組]****。

1. 在 [Edge 部署]**** 面板中，選取您的訂用帳戶和您在上一節中建立的 **EdgeDeviceJob**。 按一下 [檔案]  。

1. 在 [建立部署 > 新增模組]**** 頁面上，按 [下一步]****。

1. 在 [建立部署 > 指定路由]**** 頁面上，新增下列程式碼：

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    此程式碼會將串流分析模組的輸出路由至正確的位置。

    按 [下一步]  。

1. 在 [建立部署 > 指定計量]**** 頁面上，按 [下一步]****。

1. 在 [建立部署 > 目標裝置]**** 頁面上，輸入 10 作為優先順序。 按 [下一步]  。

1. 在 [建立部署 > 檢閱部署]**** 頁面上，按一下 [提交]****：

    [![檢閱部署](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. 在主要 [IoT Edge]**** 頁面上，按一下 [IoT Edge 部署]****。 您會在部署清單中看到 **oil-pump-device**。

1. 按一下 **oil-pump-device** 部署，然後按一下 [下載 IoT Edge 資訊清單]****。 將檔案以 **oil-pump-device.json** 的形式儲存至本機電腦上的適當位置。 您會本教學課程的下一節中將需要此檔案。

您現在已建立要在遠端監視解決方案中匯入作為套件的 IoT Edge 資訊清單。 一般而言，開發人員會建立 IoT Edge 模組和資訊清單檔案。

## <a name="import-a-package"></a>匯入套件

在本節中，您已匯入 Edge 資訊清單，作為遠端監視解決方案中的套件。

1. 在遠端監視 Web UI 中瀏覽至 [套件]**** 頁面，然後按一下 [+ 新增套件]****：

    [![新增套件](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. 在 [新增套件]**** 面板上，選擇 [Edge 資訊清單]**** 作為套件類型，按一下 [瀏覽]**** 以尋找您本機電腦上的 **oil-pump-device.json** 檔案，然後按一下 [上傳]****：

    [![上傳套件](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    套件清單此時會包含 **oil-pump-device.json** 套件。

在下一節中，您將建立會將套件套用至 Edge 裝置的部署。

## <a name="deploy-a-package"></a>部署套件

現在，您已準備好要將套件部署至裝置。

1. 在遠端監視 Web UI 中瀏覽至 [部署]**** 頁面，然後按一下 [+ 新增部署]****：

    [![新增部署](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. 在 [新增部署]**** 面板中，使用下列設定建立部署：

    | 選項 | 值 |
    | ------ | ----- |
    | 名稱   | OilPumpDevices |
    | 套件類型 | Edge 資訊清單 |
    | 套件 | oil-pump-device.json |
    | 裝置群組 | OilPumps |
    | 優先順序 | 10 |

    [![建立部署](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    按一下 [套用]。

您需要等候幾分鐘讓套件部署至裝置，以及讓遙測資料開始從裝置流出。

[![部署作用中](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

[部署]**** 頁面會顯示下列計量：

* [已設定目標]**** 會顯示裝置群組中的裝置數目。
* [已套用]**** 會顯示指定已套用部署內容的裝置數目。
* [已成功]**** 會顯示從 IoT Edge 用戶端執行階段報告為成功的部署所包含的 Edge 裝置數目。
* [失敗]**** 會顯示從 IoT Edge 用戶端執行階段報告為失敗的部署所包含的 Edge 裝置數目。

## <a name="monitor-the-device"></a>監視裝置

您可以在遠端監視 Web UI 中檢視來自油泵裝置的溫度遙測資料：

1. 瀏覽至 [Device Explorer]**** 頁面，然後選取您的油泵裝置。
1. 在 [裝置詳細資料]**** 面板的 [遙測]**** 區段中，按一下 [溫度]****：

    [![檢視遙測](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

您可以檢視溫度在達到閾值之前的上升情形。 串流分析 Edge 模組會在溫度達到此閾值加以偵測，並將命令傳送到裝置以立即降低溫度。 其所有處理都會在裝置上執行，而無須與雲端通訊。

如果您想要在達到閾值時通知操作員，您可以在遠端監視 Web UI 中建立規則：

1. 瀏覽至 [規則]**** 頁面，然後按一下 [+ 新增規則]****。
1. 使用下列設定建立新的規則：

    | 選項 | 值 |
    | ------ | ----- |
    | 規則名稱 | 油泵溫度 |
    | 描述 | 油泵溫度超過 300 度 |
    | 裝置群組 | OilPumps |
    | 計算 | 立即 |
    | 欄位 | 溫度 |
    | 運算子 | > |
    | 值 | 300 |
    | 嚴重性層級 | 資訊 |

    [![建立規則](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    按一下 [套用]。

1. 瀏覽至 [儀表板]**** 頁面。 當 **oil-pump** 裝置中的溫度超過 300 時，[警示]**** 面板中會顯示警示。

## <a name="next-steps"></a>下一步

本教學課程說明了如何在遠端監視解決方案加速器中新增和設定 IoT Edge 裝置。 若要深入了解如何使用遠端監視解決方案中的 IoT Edge 套件，請參閱下列操作說明指南：

> [!div class="nextstepaction"]
> [在遠端監視解決方案加速器中匯入 IoT Edge 套件](iot-accelerators-remote-monitoring-import-edge-package.md)

若要深入了解如何安裝 IoT Edge 執行階段，請參閱[在 Linux (x64) 上安裝 Azure IoT Edge 執行階段](../iot-edge/how-to-install-iot-edge-linux.md)。

若要深入了解 Edge 裝置上的 Azure 串流分析，請參閱[將 Azure 串流分析部署為 IoT Edge 模組](../iot-edge/tutorial-deploy-stream-analytics.md)。
