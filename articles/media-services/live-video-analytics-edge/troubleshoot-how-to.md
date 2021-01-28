---
title: 針對 IoT Edge 上的即時影片分析進行疑難排解-Azure
description: 本文涵蓋 IoT Edge 上即時影片分析的疑難排解步驟。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d519193d55c9535dc71206d2d9f72661d7a40d71
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954407"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>針對 IoT Edge 上的即時影片分析進行疑難排解

本文涵蓋 Azure IoT Edge 上的即時影片分析 (LVA) 的疑難排解步驟。

## <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

### <a name="diagnostics"></a>診斷

作為即時影片分析部署的一部分，您可以設定 Azure 資源，例如 IoT 中樞和 IoT Edge 裝置。 在診斷問題的第一個步驟中，請務必依照下列指示正確地設定 Edge 裝置：

1. [執行 `check` 命令](../../iot-edge/troubleshoot.md#run-the-check-command)。
1. [檢查您的 IoT Edge 版本](../../iot-edge/troubleshoot.md#check-your-iot-edge-version)。
1. [檢查 IoT Edge 安全性管理員與其記錄的狀態](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)。
1. [查看正在通過 IoT Edge 中樞的訊息](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)。
1. [重新開機容器](../../iot-edge/troubleshoot.md#restart-containers)。
1. [檢查您的防火牆和埠設定規則](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="pre-deployment-issues"></a>部署前問題

如果 edge 基礎結構沒問題，您可以尋找部署資訊清單檔的問題。 若要將即時影片分析部署在 IoT Edge 裝置上的 IoT Edge 模組，以及任何其他 IoT 模組，您可以使用包含 IoT Edge hub、IoT Edge 代理程式和其他模組及其屬性的部署資訊清單。 您可以使用下列命令來部署資訊清單檔案：

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
如果 JSON 程式碼的格式不正確，您可能會收到下列錯誤：   
&nbsp;&nbsp;&nbsp;**無法剖析自 <deployment manifest.json> 變數 ' content ' 的 JSON （引數 ' content '），例外狀況：「額外的資料：行101欄 1 (char 5325)** 」

如果您遇到這個錯誤，建議您檢查 JSON 是否有遺失的括弧或其他檔案結構的問題。 若要驗證檔案結構，您可以使用 [具有 Json 檢視器外掛程式](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) 的用戶端（例如記事本 + +）或線上工具，例如 [json 格式器 & 驗證程式](https://jsonformatter.curiousconcept.com/)。

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>部署期間：使用 media graph 直接方法進行診斷 

在 IoT Edge 裝置上正確部署 IoT Edge 模組的即時影片分析之後，您可以藉由叫用 [直接方法](direct-methods.md)來建立並執行 media graph。  
>[!NOTE]
>  直接方法呼叫應該 **`lvaEdge`** 只對模組進行。

您可以使用 Azure 入口網站，使用直接方法來執行 media graph 的診斷：

1. 在 Azure 入口網站中，移至已連線到您 IoT Edge 裝置的 IoT 中樞。

1. 尋找 [ **自動裝置管理**]，然後選取 [ **IoT Edge**]。  

1. 在 Edge 裝置清單中，選取您要診斷的裝置。  
         
    ![顯示 Edge 裝置清單 Azure 入口網站的螢幕擷取畫面](./media/troubleshoot-how-to/lva-sample-device.png)


1. 查看回應碼是否為 *200-確定*。 [IoT Edge 運行](../../iot-edge/iot-edge-runtime.md)時間的其他回應碼包括：
    * 400 - 部署設定不正確或無效。
    * 417-裝置沒有部署設定集。
    * 412 - 部署設定中的結構描述版本無效。
    * 406 - IoT Edge 裝置已離線或無法傳送狀態報表。
    * 500 - IoT Edge 執行階段發生錯誤。

    > [!TIP]
    > 如果您在環境中執行 Azure IoT Edge 模組時遇到問題，請 **[Azure IoT Edge 標準的診斷步驟](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** ，作為疑難排解和診斷的指南。
### <a name="post-deployment-direct-method-error-code"></a>部署後：直接方法錯誤碼
1. 如果您收到狀態 `501 code` ，請檢查以確定直接方法名稱正確無誤。 如果方法名稱和要求承載都是正確的，您應該會得到結果，以及成功碼 = 200。 
1. 如果要求承載不正確，您將會取得狀態 `400 code` 和回應承載，以指出可協助您在直接方法呼叫中診斷問題的錯誤碼和訊息。
    * 檢查報告和所需屬性可協助您瞭解模組屬性是否已與部署同步處理。 如果沒有，您可以重新開機 IoT Edge 裝置。 
    * 使用 [直接方法](direct-methods.md) 指南來呼叫一些方法，尤其是簡單的方法，例如 GraphTopologyList。 本指南也會指定預期的要求和回應承載和錯誤碼。 順利完成簡單的直接方法之後，您就可以確保即時影片分析 IoT Edge 課程模組的功能正常。
        
       ![IoT Edge 模組的 [直接方法] 窗格螢幕擷取畫面。](./media/troubleshoot-how-to/direct-method.png) 

1. 如果指定的 [ **部署** ] 和 [ **依裝置報告** ] 資料行指出 [ *是]*，您可以在 IoT Edge 模組上的即時影片分析上叫用直接方法。 選取要移至頁面的模組，您可以在其中檢查所需屬性和報告屬性，並叫用直接方法。 請記住下列事項︰ 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>部署後：在執行期間診斷記錄是否有問題 

您 IoT Edge 模組的容器記錄檔中應該包含診斷資訊，以協助您在模組執行時間中偵測問題。 您可以 [檢查容器記錄是否有問題](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) ，並自行診斷問題。 

如果您已執行所有先前的檢查，但仍遇到問題，請 [使用 `support bundle` 命令](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) 從 IoT Edge 裝置收集記錄，以供 Azure 小組進一步分析。 您可以 [與我們聯繫](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 以取得支援，並提交所收集的記錄。

## <a name="common-error-resolutions"></a>常見錯誤解決方案

即時影片分析會部署為 IoT Edge 裝置上的 IoT Edge 模組，而且會與 IoT Edge 代理程式和中樞模組共同運作。 您會在即時影片分析部署時遇到的一些常見錯誤，是因為基礎 IoT 基礎結構的問題所引起。 這些錯誤包括：

* [IoT Edge 代理程式會在大約一分鐘後停止](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute)。
* [IoT Edge 代理程式無法存取模組的映射 (403) ](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403)。
* [IoT Edge agent 模組會報告「空白設定檔」，且裝置上不會啟動任何模組](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
* [IoT Edge 中樞無法啟動](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start)。
* [IoT Edge 的安全性背景程式失敗，主機名稱無效](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
* [即時影片分析或任何其他自訂 IoT Edge 模組無法將訊息傳送至 Edge 中樞，並出現404錯誤](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
* 已[成功部署 IoT Edge 模組，然後從裝置中消失](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device)。

    > [!TIP]
    > 如果您在環境中執行 Azure IoT Edge 模組時遇到問題，請 **[Azure IoT Edge 標準的診斷步驟](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** ，作為疑難排解和診斷的指南。

執行 **[即時影片分析資源設定腳本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)** 時，您可能也會遇到問題。 常見的問題包括：

* 使用您沒有擁有者許可權的訂用帳戶。 這會導致腳本因 **ForbiddenError** 或 **AuthorizationFailed** 錯誤而失敗。
    * 若要解決這個問題，請確定您有想要使用之訂用帳戶的 **擁有** 者許可權。 如果您無法自行執行，請洽詢訂用帳戶管理員以授與適當的許可權。
* **範本部署因為原則違規而失敗。**
    * 若要通過此問題，請與您的 IT 系統管理員合作，以確保呼叫 (s) 建立虛擬機器，以略過封鎖 ssh 驗證。 因為我們使用的安全防禦網路需要使用者名稱和密碼來與 Azure 資源通訊，所以不需要這麼做。 這些認證會儲存在 Cloud Shell 的 **~/clouddrive/lva-sample/vm-edge-device-credentials.txt** 檔案中，一旦虛擬機器成功建立之後，就會部署並附加至 IoT 中樞。
* 安裝腳本無法建立服務主體及（或） Azure 資源。
    * 若要解決此問題，請確認您的訂用帳戶和 Azure 租使用者尚未達到其最大服務限制。 深入瞭解 [Azure AD 服務限制和限制](https://docs.microsoft.com/azure/active-directory/enterprise-users/directory-service-limits-restrictions) ，以及 [Azure 訂用帳戶和服務限制、配額和條件約束。](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)


### <a name="live-video-analytics-working-with-external-modules"></a>使用外部模組的即時影片分析

透過 media graph 擴充處理器的即時影片分析可以擴充媒體圖形，以使用 HTTP 或 gRPC 通訊協定從其他 IoT Edge 模組傳送和接收資料。 在 [特定範例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)中，此媒體圖形可將影片框架作為影像傳送至外部推斷模組（例如 Yolo v3），並使用 HTTP 通訊協定接收以 JSON 為基礎的分析結果。 在這種拓撲中，事件的目的地大多是 IoT 中樞。 在您沒有在中樞上看到推斷事件的情況下，請檢查下列各項：

* 檢查以查看是否正在發佈 media graph 的中樞，以及您正在檢查的中樞是否相同。 當您建立多個部署時，最後可能會有多個中樞，並錯誤地檢查事件的中樞是否正確。
* 在 Azure 入口網站中，檢查外部模組是否已部署且正在執行。 在這裡的範例影像中，rtspsim、yolov3、tinyyolov3 和 logAnalyticsAgent 是在 lvaEdge 模組外部執行的 IoT Edge 模組。

    [![顯示 Azure IoT 中樞中的模組執行狀態的螢幕擷取畫面。 ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* 查看是否要將事件傳送到正確的 URL 端點。 外部 AI 容器會公開 URL 和埠，以從中接收並傳回 POST 要求中的資料。 此 URL 會指定為 `endpoint: url` HTTP 擴充功能處理器的屬性。 如 [拓撲 URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json)中所示，端點會設定為推斷 URL 參數。 請確定參數的預設值或傳入的值是正確的。 您可以使用用戶端 URL (捲曲) 來進行測試，以瞭解其是否正常運作。  

    例如，以下是在 IP 位址為172.17.0.3 的本機電腦上執行的 Yolo v3 容器。  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    傳回的結果：

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > 使用 **[Docker 檢查命令](https://docs.docker.com/engine/reference/commandline/inspect/)** 來尋找電腦的 IP 位址。
    
* 如果您執行的是一或多個使用 media graph 擴充功能處理器的圖形實例，您應該使用此 `samplingOptions` 欄位來管理影片摘要的每秒畫面格數 (fps) 速率。 

   * 在某些情況下，邊緣機器的 CPU 或記憶體會高度使用，您可能會遺失某些推斷事件。 若要解決這個問題，請 `maximumSamplesPerSecond` 在欄位上設定屬性的低值 `samplingOptions` 。 您可以將它設定為 0.5 ( "maximumSamplesPerSecond"： "0.5" ) 在圖形的每個實例上，然後重新執行實例以檢查中樞上的推斷事件。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>平行的多重直接方法–超時失敗 

IoT Edge 上的即時影片分析提供直接以方法為基礎的程式設計模型，可讓您設定多個拓撲和多個圖形實例。 在拓撲和圖形設定中，您會在 IoT Edge 模組上叫用多個直接方法呼叫。 如果您平行叫用這些多個方法呼叫，尤其是啟動和停止圖形的方法呼叫，您可能會遇到如下的逾時錯誤： 

元件初始化方法 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync 擲回例外狀況。 IotHubException： IotHubException： <.... 例外狀況.-：<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

我們建議您 *不要* 平行呼叫直接方法。 請依序呼叫它們 (也就是，只有在上一個呼叫完成) 之後，才進行一個直接方法呼叫。

### <a name="collect-logs-for-submitting-a-support-ticket"></a>收集用來提交支援票證的記錄

當自我引導式疑難排解步驟無法解決您的問題時，請前往 Azure 入口網站並 [開啟支援票證](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

> [!WARNING]
> 記錄檔可能包含個人識別資訊 (PII) 例如您的 IP 位址。 所有記錄檔的本機複本只要完成檢查並關閉支援票證，就會一併刪除。  

若要收集應新增至票證的相關記錄，請依照下列指示進行，並在支援要求的 **詳細資料** 窗格中上傳記錄檔。  
1. [設定即時影片分析模組以收集詳細資訊記錄](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [開啟 Debug 記錄](#live-video-analytics-debug-logs)
1. 重現問題
1. 從入口網站中的 [ **IoT 中樞** ] 頁面連線至虛擬機器
    1. 壓縮 *debugLogs* 資料夾中的所有檔案。

       > [!NOTE]
       > 這些記錄檔並非供自我診斷之用。 它們的目的是要讓 Azure 工程團隊分析您的問題。

       * 在下列命令中，請務必使用您稍早在 **步驟 2** 中設定的 EDGE 裝置上的 DEBUG 記錄檔位置，取代 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 。  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. 將 *debugLogs.zip* 檔案附加至支援票證。
1. 執行 [支援配套命令](#use-the-support-bundle-command)、收集記錄並附加至支援票證。

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>設定即時影片分析模組以收集詳細資訊記錄
設定您的即時影片分析模組，藉由設定，以收集詳細資訊記錄，如下所示 `logLevel` `logCategories` ：
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

您可以在下列任一項中執行此動作：
* 在 **Azure 入口網站** 中，更新即時影片分析模組 [ ![ 模組身分識別 ](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)對應項屬性的模組身分識別對應項屬性。    
* 或者，在您的 **部署資訊清單** 檔案中，您可以在即時影片分析模組的 [屬性] 節點中新增這些專案。

### <a name="use-the-support-bundle-command"></a>使用支援套件組合命令

當您需要從 IoT Edge 裝置收集記錄時，最簡單的方式是使用 `support-bundle` 命令。 此命令會收集：

- 模組記錄
- IoT Edge 安全性管理員和容器引擎記錄
- IoT Edge 檢查 JSON 輸出
- 有用的調試資訊

1. `support-bundle`使用 *--[自*] 旗標來執行命令，以指定您希望記錄涵蓋的時間量。 例如，在下半年會取得過去兩個小時的記錄。 您可以變更此旗標的值，以包含不同期間的記錄。

    ```
    sudo iotedge support-bundle --since 2h
    ```

   此命令會在您執行命令的目錄中建立名為 *support_bundle.zip* 的檔案。 
   
1. 將 *support_bundle.zip* 檔案附加至支援票證。

### <a name="live-video-analytics-debug-logs"></a>即時影片分析調試記錄

若要在 IoT Edge 模組上設定即時影片分析以產生 debug 記錄，請執行下列動作：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後移至您的 IoT 中樞。
1. 在左窗格中，選取 [ **IoT Edge**]。
1. 在裝置清單中，選取目標裝置的識別碼。
1. 在窗格頂端，選取 [ **設定模組**]。

   ![Azure 入口網站中 [設定模組] 按鈕的螢幕擷取畫面。](media/troubleshoot-how-to/set-modules.png)

1. 在 [ **IoT Edge 模組** ] 區段中，尋找並選取 [ **lvaEdge**]。
1. 選取 [ **容器建立選項**]。
1. **在 [系** 結] 區段中，新增下列命令：

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > 此命令會系結 Edge 裝置與容器之間的記錄資料夾。 如果您想要在不同的位置收集記錄，請使用下列命令，以您想要使用的位置取代 **$LOG _LOCATION_ON_EDGE_DEVICE** ： `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. 選取 [更新]。
1. 選取 [檢閱 + 建立]  。 成功的驗證訊息會以綠色橫幅張貼。
1. 選取 [建立]  。
1. 更新 **模組身分識別** 對應項以指向 DebugLogsDirectory 參數，指向收集記錄檔的目錄：

    a. 在 [ **模組** ] 資料表下，選取 [ **lvaEdge**]。  
    b. 在窗格頂端，選取 [模組身分 **識別** 對應項]。 可編輯的窗格隨即開啟。  
    c. 在 [ **所需的金鑰**] 下，新增下列索引鍵/值組：  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > 此命令會系結 Edge 裝置與容器之間的記錄資料夾。 如果您想要在裝置上的其他位置收集記錄：
    > 1. **在系結區段中** 建立 Debug 記錄檔位置的系結，以您想要的位置取代 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 和 **$DEBUG _LOG_LOCATION** ：`/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. 使用下列命令，將 **$DEBUG _LOG_LOCATION** 取代為上一個步驟中使用的位置：  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. 選取 [儲存]。


1. 您可以將 [ **模組身分識別** 對應項] 中的值設定為 *null*，以停止記錄收集。 返回至 [ **模組身分識別** 對應項] 頁面，並將下列參數更新為：

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>關於記錄的最佳作法

[監視和記錄](monitoring-logging.md) 應該有助於瞭解分類法，以及如何產生記錄，以協助您在 LVA 時進行偵錯工具的問題。 

由於 gRPC 伺服器的執行方式與不同語言不同，因此在伺服器內新增記錄沒有標準方式。  

例如，如果您使用 .NET core 建立 gRPC 伺服器，gRPC 服務會在 **gRPC** 類別下新增記錄。 若要啟用 gRPC 的詳細記錄，請將下列專案新增至記錄檔中的 LogLevel 子區段，以將 Grpc 前置詞設定為檔案 appsettings.js中的 Debug 層級： 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

您也可以在 Startup.cs 檔案中使用 ConfigureLogging 設定此項： 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[.Net 中的 GRPC 記錄和診斷功能](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) 提供了一些指引，說明如何從 gRPC 伺服器收集一些診斷記錄。 

### <a name="a-failed-grpc-connection"></a>失敗的 gRPC 連接 

如果圖形正在使用中，並從相機進行串流處理，則會由即時影片分析維護連線。 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>當這些資源變成瓶頸時，監視及平衡 CPU 和 GPU 資源的負載

Live Video Analytics 不會監視或提供任何硬體資源監視。 開發人員將必須使用硬體製造商監視解決方案。 不過，如果您使用 Kubernetes 容器，您可以使用 [Kubernetes 儀表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)來監視裝置。 

.NET core 檔中的 gRPC 也會分享一些有關 [效能最佳做法](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) 和 [負載平衡](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing)的重要資訊。  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>當推斷伺服器未收到任何框架，而您收到「未知」的通訊協定錯誤時進行疑難排解 

有幾件事您可以做，以取得有關問題的詳細資訊。  

* 在即時影片分析模組的所需屬性中包含「**ediaPipeline** 記錄」類別，並確保記錄層級設定為 `Information` 。  
* 若要測試網路連線能力，您可以從 edge 裝置執行下列命令。 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   如果命令輸出混雜文字的簡短字串，則 telnet 已成功開啟與您的推斷伺服器的連接，並開啟二進位 gRPC 通道。 如果您看不到此錯誤，telnet 將會報告網路錯誤。 
* 在您的推斷伺服器中，您可以在 gRPC 程式庫中啟用其他記錄。 這可提供有關 gRPC 通道本身的其他資訊。 這會因語言而異，以下是 [c #](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1)的指示。 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>從 gRPC 的緩衝區挑選更多影像，而不會傳回第一個緩衝區的結果

在 gRPC 資料傳輸合約中，您應確認即時影片分析傳送到 gRPC 推斷伺服器的所有訊息。 未確認收到的影像框會中斷資料合約，而且可能會導致不想要的情況。  

若要搭配使用 gRPC 伺服器與即時影片分析，可以使用共用記憶體來獲得最佳效能。 這會要求您使用程式設計語言/環境所公開的 Linux 共用記憶體功能。 

1. 開啟 Linux 共用記憶體控制碼。
1. 在接收到框架時，存取共用記憶體內的位址位移。
1. 確認畫面格處理完成，以便即時影片分析可回收其記憶體。

   > [!NOTE]
   > 如果您在確認畫面格的接收期間有很長一段時間時延遲，可能會導致共用記憶體變滿，並導致資料下降。
1. 將每個框架儲存在您選擇的資料結構中 (清單、陣列等等，在推斷伺服器上) 。
1. 然後，當您擁有所需的影像框架數目時，就可以執行處理邏輯。
1. 準備好時，請將推斷結果回復為即時影片分析。

## <a name="next-steps"></a>下一步

[教學課程：以事件為基礎的影片錄製到雲端並從雲端播放](event-based-video-recording-tutorial.md)