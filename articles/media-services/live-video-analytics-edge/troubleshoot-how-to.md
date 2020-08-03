---
title: 針對 IoT Edge 上的即時影片分析進行疑難排解-Azure
description: 本文涵蓋 IoT Edge 上即時影片分析的疑難排解步驟。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: bbd3cb88b017209adff58a646e274caf31ab425f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486437"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>針對 IoT Edge 上的即時影片分析進行疑難排解

本文涵蓋 Azure IoT Edge 上即時影片分析（LVA）的疑難排解步驟。

## <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

### <a name="diagnostics"></a>診斷

在您的即時影片分析部署過程中，您會設定 Azure 資源，例如 IoT 中樞和 IoT Edge 裝置。 為診斷問題的第一個步驟，請務必遵循下列指示，確保 Edge 裝置已正確設定：

1. [執行 `check` 命令](../../iot-edge/troubleshoot.md#run-the-check-command)。
1. [檢查您的 IoT Edge 版本](../../iot-edge/troubleshoot.md#check-your-iot-edge-version)。
1. [檢查 IoT Edge 安全性管理員及其記錄](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)檔的狀態。
1. [查看要通過 IoT Edge 中樞的訊息](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)。
1. [重新開機容器](../../iot-edge/troubleshoot.md#restart-containers)。
1. [檢查您的防火牆和埠設定規則](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="pre-deployment-issues"></a>部署前的問題

如果邊緣基礎結構正常，您可以尋找部署資訊清單檔案的問題。 若要在 IoT Edge 裝置上的 IoT Edge 模組與其他任何 IoT 模組一起部署即時影片分析，請使用包含 IoT Edge 中樞、IoT Edge 代理程式和其他模組及其屬性的部署資訊清單。 如果 JSON 程式碼的格式不正確，您可能會收到下列錯誤： 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

無法剖析引數 ' content ' 之檔案： ' <deployment manifest.json> ' 的 JSON，例外狀況：「額外資料：行101欄1（字元5325）」

如果您遇到這個錯誤，建議您檢查 JSON 是否遺漏括弧或其他與檔案結構相關的問題。 若要驗證檔案結構，您可以使用用戶端（例如 [[記事本 + +] 和 [Json 檢視器] 外掛程式](https://riptutorial.com/notepadplusplus/example/18201/json-viewer)）或線上工具（例如[json 格式器 & 驗證程式](https://jsonformatter.curiousconcept.com/)）。

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>部署期間：使用 media graph 直接方法進行診斷 

在 IoT Edge 裝置上正確部署 IoT Edge 模組的即時影片分析之後，您可以藉由叫用[直接方法](direct-methods.md)來建立和執行 media graph。 您可以使用 Azure 入口網站透過直接方法來執行媒體圖形的診斷：

1. 在 Azure 入口網站中，移至已連線至您 IoT Edge 裝置的 IoT 中樞。

1. 尋找 [**自動裝置管理**]，然後選取 [ **IoT Edge**]。  

1. 在 Edge 裝置清單中，選取您要診斷的裝置。  
         
    ![顯示 Edge 裝置清單 Azure 入口網站的螢幕擷取畫面](./media/troubleshoot-how-to/lva-sample-device.png)

1. 查看回應碼是否為*200-OK*。 [IoT Edge 運行](../../iot-edge/iot-edge-runtime.md)時間的其他回應碼包括：
    * 400 - 部署設定不正確或無效。
    * 417-裝置沒有部署設定集。
    * 412 - 部署設定中的結構描述版本無效。
    * 406 - IoT Edge 裝置已離線或無法傳送狀態報表。
    * 500 - IoT Edge 執行階段發生錯誤。

1. 如果您收到狀態501程式碼，請檢查以確定直接方法名稱正確。 如果方法名稱和要求承載是正確的，您應該會得到結果，以及成功的代碼 = 200。 如果要求裝載不正確，您將會收到狀態 = 400 和回應承載，指出應協助您使用直接方法呼叫來診斷問題的錯誤碼和訊息。
    * 檢查報告和所需屬性可以協助您瞭解模組屬性是否已與部署同步。 如果沒有，您可以重新開機 IoT Edge 裝置。 
    * 使用[直接方法](direct-methods.md)指南呼叫幾個方法，特別是簡單的方法，例如 GraphTopologyList。 本指南也會指定預期的要求和回應承載和錯誤碼。 簡單的直接方法成功之後，您就可以確保即時影片分析 IoT Edge 模組的功能正常。
        
       ![[IoT Edge] 模組之 [直接方法] 窗格的螢幕擷取畫面。](./media/troubleshoot-how-to/direct-method.png) 

1. 如果 [**部署**] 和 [**由裝置報告**] 資料行指出 [*是]*，您可以在 IoT Edge 模組上的即時影片分析上叫用直接方法。 選取要移至頁面的模組，您可以在其中檢查所需和報告的屬性，以及叫用直接方法。 請記住下列事項︰ 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>部署後：在執行期間診斷記錄是否有問題 

IoT Edge 模組的容器記錄應該包含診斷資訊，以協助在模組執行時間期間對問題進行偵錯工具。 您可以[檢查容器記錄中的問題](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)，並自行診斷問題。 

如果您已執行所有先前的檢查，但仍遇到問題，請[使用 `support bundle` 命令](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)從 IoT Edge 裝置收集記錄檔，以供 Azure 小組進一步分析。 您可以[聯絡我們](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以取得支援，並提交收集的記錄檔。

## <a name="common-error-resolutions"></a>常見錯誤解決方案

即時影片分析會部署為 IoT Edge 裝置上的 IoT Edge 模組，並與 IoT Edge 代理程式和中樞模組共同運作。 您會在即時影片分析部署中遇到的一些常見錯誤，是由基礎 IoT 基礎結構的問題所造成。 這些錯誤包括：

* [IoT Edge 代理程式會在大約一分鐘後停止](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute)。
* [IoT Edge 代理程式無法存取模組的映射（403）](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403)。
* [IoT Edge agent 模組會報告「空白的設定檔案」，而且裝置上不會啟動任何模組](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
* [IoT Edge 中樞無法啟動](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start)。
* [IoT Edge 的安全性守護程式因主機名稱無效而失敗](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
* [即時影片分析或任何其他自訂 IoT Edge 模組無法傳送訊息至 Edge 中樞，發生404錯誤](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
* [已成功部署 IoT Edge 模組，然後從裝置中消失](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device)。

### <a name="edge-setup-script-issues"></a>Edge 安裝程式腳本問題

在我們的檔中，我們提供了一個[安裝腳本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)來部署 edge 和雲端資源，並讓您開始使用即時影片分析 edge。 本節說明您可能會遇到的一些腳本錯誤，以及用來進行偵錯工具的解決方案。

問題：腳本會執行，部分建立幾個資源，但會失敗並顯示下列訊息：

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
若要修正此問題：

1. 執行以下命令：

    ```
    az --version
    ```
1. 請確定您已安裝下列延伸模組。 本文發行時，延伸模組及其版本如下：

    | 分機 | 版本 |
    |---|---|
    |azure-cli   |      2.5.1|
    |命令模組-nspkg         |   2.0.3|
    |core  |    2.5.1|
    |nspkg    | 3.0.4|
    |遙測資料| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. 如果您的已安裝延伸模組的版本早于此處所列的發行號碼，請使用下列命令來更新擴充功能：

    ```
    az extension update --name <Extension name>
    ```

    例如，您可能會執行 `az extension update --name azure-iot` 。

### <a name="sample-app-issues"></a>範例應用程式問題

在我們的版本中，我們提供了一些 .NET 範例程式碼，可協助開發人員小組開始進行。 本節說明當您執行範例程式碼時可能會遇到的一些錯誤，以及用來進行其調試的解決方案。

問題： Program.cs 因直接方法叫用時發生下列錯誤而失敗：

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. 請確定您已在 Visual Studio Code 環境中安裝[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ，且已設定與 IoT 中樞的連線。 若要這麼做，請選取 Ctrl + Shift + P，然後選擇 [**選取 IoT 中樞方法**]。

1. 查看您是否可以透過 Visual Studio Code 在 IoT Edge 模組上叫用直接方法。 例如，呼叫具有下列承載 { &nbsp; " @apiVersion "： "1.0"} 的 GraphTopologyList。 您應該會收到下列回應： 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code 中回應的螢幕擷取畫面。](./media/troubleshoot-how-to/visual-studio-code1.png)
1. 如果上述解決方案失敗，請嘗試下列方法：

    a. 移至 IoT Edge 裝置上的命令提示字元，然後執行下列命令：
    
      ```
      sudo systemctl restart iotedge
      ```

      此命令會重新開機 IoT Edge 裝置和所有模組。 請稍候幾分鐘，然後在您嘗試再次使用直接方法之前，執行下列命令以確認模組正在執行：

      ```
      sudo iotedge list
      ```

    b. 如果上述方法也失敗，請嘗試重新開機您的虛擬機器或電腦。

    c. 如果所有方法都失敗，請執行下列命令以取得具有所有[相關記錄](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)檔的壓縮檔案，並將它附加至[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. 如果您收到錯誤回應*400*程式碼，請根據[直接方法](direct-methods.md)指南，確定您的方法叫用裝載格式正確。
1. 如果您收到狀態*200*程式碼，則表示您的中樞正常運作，且您的模組部署正確且有回應。 

1. 查看應用程式設定是否正確。 您的應用程式設定包含檔案中*appsettings.js*的下欄欄位。 再次檢查以確定 deviceId 和 moduleId 都正確無誤。 簡單的檢查方式是前往 Visual Studio Code 的 Azure IoT 中樞延伸模組區段。 *appsettings.json*檔案和 IoT 中樞區段的值應該相符。
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. 在 [ *appsettings.js*檔案] 中，確定您已提供 IoT 中樞連接字串，而*不*是 IoT 中樞的裝置連接字串，因為[連接字串格式不同](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)。

### <a name="live-video-analytics-working-with-external-modules"></a>使用外部模組的即時影片分析

透過 HTTP 擴充處理器的即時影片分析可以擴充 media graph，使用 REST 透過 HTTP 傳送和接收來自其他 IoT Edge 模組的資料。 作為[特定範例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)，media graph 可以將影片畫面當做影像傳送到外部推斷模組（例如 Yolo v3），並接收以 JSON 為基礎的分析結果。 在這種拓撲中，事件的目的地主要是 IoT 中樞。 在您未在中樞上看到推斷事件的情況下，請檢查下列各項：

* 查看 media graph 是否正在發佈的中樞，以及您正在檢查的中樞是否相同。 當您建立多個部署時，最後可能會有多個中樞，並錯誤地檢查錯誤的中樞是否有事件。
* 在 Visual Studio Code 中，檢查外部模組是否已部署且正在執行。 在這裡的範例影像中，rtspsim 和 cv 是在 lvaEdge 模組外部執行的 IoT Edge 模組。

    ![在 Azure IoT 中樞中顯示模組執行狀態的螢幕擷取畫面。](./media/troubleshoot-how-to/iot-hub.png)

* 檢查您是否正在將事件傳送至正確的 URL 端點。 外部 AI 容器會公開 URL 和埠，它會透過它接收並傳回 POST 要求中的資料。 此 URL 會指定為 `endpoint: url` HTTP 擴充處理器的屬性。 如[拓撲 URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)所示，端點會設定為推斷 URL 參數。 請確定參數的預設值或傳入的值是正確的。 您可以使用用戶端 URL （捲曲）來進行測試，以查看其是否正常運作。  

    例如，以下是在本機電腦上執行且 IP 位址為172.17.0.3 的 Yolo v3 容器。 使用 Docker 檢查來尋找 IP 位址。

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    傳回的結果：

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* 如果您執行的是一個或多個使用 HTTP 延伸模組處理器的圖形實例，您應該在每個 HTTP 擴充處理器之前具有畫面播放速率篩選，以管理影片摘要的每秒畫面格數（fps）速率。 

   在某些情況下，邊緣機器的 CPU 或記憶體高度使用的情況下，您可能會遺失某些推斷事件。 若要解決此問題，請在 [畫面播放速率] 篩選器上設定 [maximumFps] 屬性的 [低] 值。 您可以在圖形的每個實例上將它設定為0.5 （"maximumFps"：0.5），然後重新執行實例以檢查中樞上的推斷事件。

   或者，您可以使用更高的 CPU 和記憶體來取得更強大的邊緣機器。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>平行處理多個直接方法–超時失敗 

IoT Edge 上的即時影片分析提供了以方法為基礎的直接程式設計模型，可讓您設定多個拓撲和多個圖形實例。 在拓撲和圖形設定過程中，您會在 IoT Edge 模組上叫用多個直接方法呼叫。 如果您以平行方式叫用這些多個方法呼叫，特別是啟動和停止圖形的方法，您可能會遇到逾時錯誤，如下所示： 

元件初始化方法 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync 擲回例外狀況。 IotHubException： IotHubException （常見例外狀況.）。一般除外。：<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

我們建議您*不要*以平行方式呼叫直接方法。 順序呼叫它們（也就是，只有在前一個方法完成後，才進行一個直接的呼叫）。

### <a name="collect-logs-for-submitting-a-support-ticket"></a>收集記錄以提交支援票證

當自行引導的疑難排解步驟無法解決您的問題時，請前往 Azure 入口網站並[開啟支援票證](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

> [!WARNING]
> 記錄檔可能包含個人識別資訊（PII），例如您的 IP 位址。 當我們完成檢查並關閉支援票證時，就會立即刪除記錄的所有本機複本。  

若要收集應新增至票證的相關記錄，請遵循下一節中的指示。 您可以在支援要求的**詳細資料**窗格上上傳記錄檔。

### <a name="use-the-support-bundle-command"></a>使用支援組合命令

當您需要從 IoT Edge 裝置收集記錄檔時，最簡單的方式是使用 `support-bundle` 命令。 此命令會收集：

- 模組記錄
- IoT Edge 安全性管理員和容器引擎記錄
- Iotedge 檢查 JSON 輸出
- 有用的調試資訊

1. 以 `support-bundle` *--[開始*] 旗標來執行命令，以指定您想要記錄涵蓋的時間量。 例如，下半年會取得過去兩小時的記錄檔。 您可以變更這個旗標的值，以包含不同期間的記錄。

    ```
    sudo iotedge support-bundle --since 2h
    ```

   此命令會在您執行命令的目錄中，建立名為*support_bundle.zip*的檔案。 
   
1. 將*support_bundle.zip*檔案附加至支援票證。

### <a name="live-video-analytics-debug-logs"></a>即時影片分析的調試記錄

若要在 IoT Edge 模組上設定即時影片分析以產生 debug 記錄，請執行下列動作：

1. 登入[Azure 入口網站](https://portal.azure.com)，並移至您的 IoT 中樞。
1. 在左窗格中，選取 [ **IoT Edge**]。
1. 在裝置清單中，選取目標裝置的識別碼。
1. 在窗格頂端，選取 [**設定模組**]。

   ![Azure 入口網站中 [設定模組] 按鈕的螢幕擷取畫面。](media/troubleshoot-how-to/set-modules.png)

1. 在 [ **IoT Edge 模組**] 區段中，尋找並選取 [ **lvaEdge**]。
1. 選取 [容器] [**建立選項**]。
1. **在 [系**結] 區段中，新增下列命令：

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > 此命令會系結 Edge 裝置與容器之間的 logs 資料夾。 如果您想要收集不同位置的記錄，請使用下列命令，將 **$LOG _LOCATION_ON_EDGE_DEVICE**取代為您要使用的位置：`/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. 選取 [更新]。
1. 選取 [檢閱 + 建立]。 成功的驗證訊息會以綠色橫幅張貼。
1. 選取 [建立]。
1. 更新**模組身分識別**對應項，以指向 DebugLogsDirectory 參數，其會指向收集記錄的目錄：

    a. 在 [**模組**] 資料表下，選取 [ **lvaEdge**]。  
    b. 在窗格頂端，選取 [模組身分**識別**對應項]。 可編輯的窗格隨即開啟。  
    c. 在 [**所需的金鑰**] 下，新增下列索引鍵/值組：  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > 此命令會系結 Edge 裝置與容器之間的 logs 資料夾。 如果您想要收集不同位置的記錄，請使用下列命令，將 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE**取代為您要使用的位置：  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE"`  

    d. 選取 [儲存]  。

1. 重現問題。
1. 從入口網站的 [ **IoT 中樞**] 頁面連線到虛擬機器。
1. 壓縮*debugLogs*資料夾中的所有檔案。

   > [!NOTE]
   > 這些記錄檔並非用於自我診斷。 其適用于 Azure 工程小組來分析您的問題。

   a. 在下列命令中，請務必使用您稍早設定的 Edge 裝置上的 DEBUG 記錄檔位置來取代 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 。  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. 將*debugLogs.zip*檔案附加至支援票證。

1. 您可以將**模組識別**對應項中的值設定為*null*，以停止記錄檔收集。 回到 [模組身分**識別**對應項] 頁面，並將下列參數更新為：

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>後續步驟

[教學課程：以事件為基礎的影片錄製到雲端並從雲端播放](event-based-video-recording-tutorial.md)
