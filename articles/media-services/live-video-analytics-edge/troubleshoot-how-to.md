---
title: 針對 IoT Edge 上的即時影片分析進行疑難排解-Azure
description: 本文涵蓋 IoT Edge 上即時影片分析的疑難排解步驟。
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045566"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>針對 IoT Edge 上的即時影片分析進行疑難排解

本文涵蓋 IoT Edge 上即時影片分析的疑難排解步驟。

## <a name="troubleshoot-deployment-issues"></a>為部署問題進行疑難排解

### <a name="diagnostics"></a>診斷

在部署即時影片分析的過程中，您將會設定 Azure 資源，例如 IoT 中樞和 IoT Edge 裝置。 診斷問題的第一個步驟，一律會遵循下列指示來確保邊緣已正確設定：

1. [執行 ' check ' 命令](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [檢查您的 IoT Edge 版本](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [檢查 IoT Edge 安全性管理員及其記錄的狀態](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [查看通過 IoT Edge 中樞的訊息](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [重新啟動容器](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [檢查您的防火牆和埠設定規則](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>部署前的問題

如果邊緣基礎結構正常，您可以尋找部署資訊清單檔案的問題。 若要在邊緣裝置上的 IoT Edge 模組上部署即時影片分析，以及其他任何 IoT 模組，您將使用包含 edge 中樞、edge 代理程式和其他模組的部署資訊清單及其屬性。 如果 JSON 的格式不正確，您可以取得如下所示的錯誤： 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

無法剖析引數 ' content ' 之檔案： ' <deployment manifest.json> ' 的 json，例外狀況：「額外資料：行101欄1（字元5325）」

如果您遇到這個錯誤，建議您檢查 JSON 檔案中是否有遺漏括弧或檔案結構的其他問題。 您可以使用像是 [[記事本 + +] 的用戶端搭配 JSON 檢視器外掛程式](https://riptutorial.com/notepadplusplus/example/18201/json-viewer)或線上工具（例如） https://jsonformatter.curiousconcept.com/ 來驗證檔案結構。

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>部署-使用 media graph 直接方法進行診斷 

一旦在邊緣裝置上正確部署 IoT Edge 模組的即時影片分析，您就可以叫用[直接方法](direct-methods.md)來建立和執行 media graph。 您可以使用入口網站，透過直接方法來執行媒體圖形的診斷：

1. 透過入口網站，移至連線至 edge 裝置的 [IoT 中樞]。
    1. 在 [IoT 中樞] 分頁中，尋找 [自動裝置管理->] IoT Edge。
    1. 按一下 IoT Edge 應該會顯示 Edge 裝置的清單。 挑選您要診斷的裝置。
         
        ![邊緣裝置](./media/troubleshoot-how-to/lva-sample-device.png)
    1. 檢查回應碼是否為 200-OK。 [IoT Edge 運行](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime)時間還有各種不同的回應碼，例如：
        1. 400 - 部署設定不正確或無效。
        1. 417-裝置沒有部署設定集。
        1. 412 - 部署設定中的結構描述版本無效。
        1. 406 - IoT Edge 裝置已離線或無法傳送狀態報表。
        1. 500 - IoT Edge 執行階段發生錯誤。
    1. 按一下裝置也應該會顯示已部署的預期 IoT Edge 模組清單及其狀態
    1. 如果「部署中指定的」和「裝置所回報」資料行指出「是」，則您可以在 IoT Edge 模組上的即時影片分析上叫用直接方法。 按一下此模組，它會帶您前往螢幕，您可以在其中檢查所需和報告的屬性，並可叫用直接方法。 
        1. 檢查報告和所需的屬性，可協助您瞭解模組屬性是否已與部署同步。 如果沒有，您可以重新開機邊緣 
        1. 使用[直接方法](direct-methods.md)指南呼叫幾個方法，特別是簡單的方法，例如 GraphTopologyList。 本指南也會指定預期的要求和回應承載和錯誤碼。 一旦簡單的直接方法成功之後，您就可以確保即時影片分析 edge 模組的功能正常。
        
        ![直接方法](./media/troubleshoot-how-to/direct-method.png) 
1. 如果您收到狀態501程式碼，請檢查直接方法名稱是否正確。 如果方法名稱和要求承載是正確的，您應該會得到結果，並傳回成功碼 = 200。 如果要求裝載不正確，您將會收到狀態 = 400 和回應承載，指出應協助您使用直接方法呼叫來診斷問題的錯誤碼和訊息。 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>部署後–在執行期間診斷記錄是否有問題 

Edge 模組的容器記錄應該具有診斷<!--<todo:add link to diagnostics doc>--> 應該有助於在模組執行時間時，針對您的問題進行偵錯工具的資訊。 您可以[檢查容器記錄中的問題](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues)和自我診斷，不過，如果上述所有檢查都已執行，但您仍遇到問題，則請[使用 [支援](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command)配套] 命令從 IoT Edge 裝置收集記錄，以供 Azure 小組進一步分析。 您可以與我們[聯繫](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以取得支援，並提交收集的記錄檔。

## <a name="common-error-resolutions"></a>常見錯誤解決方案

即時影片分析會部署為 Edge 裝置上的 IoT Edge 模組，並與 IoT Edge 代理程式和中樞模組共同運作。 您會在即時影片分析部署中遇到的一些常見錯誤，將會因為基礎 IoT 基礎結構的問題。 IoT Edge 代理程式和中樞的一些常見錯誤如下：

1. [IoT Edge 代理程式會在大約一分鐘後停止](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute)。
1. [IoT Edge 代理程式無法存取模組的映射（403）](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403)。
1. [Edge 代理程式模組報告 [空白的設定檔]，且裝置上沒有任何模組啟動](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)。
1. [IoT Edge 中樞無法啟動](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start)。
1. [IoT Edge 安全性守護程式失敗，主機名稱無效](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname)。
1. [即時影片分析或任何其他自訂 IoT Edge 模組無法傳送訊息至 Edge 中樞，發生404錯誤](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error)。
1. [IoT Edge 模組部署成功，然後從裝置中消失](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device)。

### <a name="edge-set-up-script-issues"></a>Edge 設定腳本問題

在我們的檔中，我們提供了[設定腳本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)來部署 Edge 和雲端資源，以開始使用即時影片分析 edge。 在本節中，我們已捕捉到您在腳本中可能會遇到的錯誤，以及如何進行偵錯工具。

腳本執行部分建立幾個資源，但失敗並出現下列訊息：

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
    
解決此問題：

1. 執行下列命令：

    ```
    az --version
    ```
1. 請確定您已安裝下列延伸模組。 在撰寫本指南時，延伸模組的版本如下所示：

    | 分機 | 版本 |
    |---|---|
    |azure-cli   |      2.5.1|
    |命令模組-nspkg         |   2.0.3|
    |core  |    2.5.1|
    |nspkg    | 3.0.4|
    |遙測| 1.0.4|
    |擴充：    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. 如果任何延伸模組比上述版本號碼舊，請使用命令將延伸模組更新為最新版本：

    ```
    az extension update --name <Extension name>
    ```

    例如， `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>範例應用程式問題

在我們的版本中，我們提供了一些 .NET 範例程式碼，可讓我們的開發人員小組啟動。 在本節中，我們已在執行範例程式碼時，捕捉到您可能會遇到的錯誤，以及如何進行這類錯誤的偵錯工具。

1. Program.cs 失敗，並在直接方法叫用時發生下列錯誤：

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. 請確定您已在 VS code 環境中安裝[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)，以及與您的 IoT 中樞安裝程式連接。 （Ctrl + shift + P，然後挑選 [選取 IoT 中樞方法] 以連線至您的訂用帳戶，IoT 中樞）
1. 檢查您是否可以透過 VS Code 在邊緣模組上叫用直接方法（例如，使用下列承載呼叫 GraphToplogyList {" @apiVersion "： "1.0"}），您應該會收到下列回應。 

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

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. 如果上述動作失敗，請嘗試下列動作：
    1. 移至 edge 裝置上的命令提示字元，然後輸入。
    
    ```
    sudo systemctl restart iotedge
    ```

    這會重新開機 edge 裝置和所有模組。 請稍候幾分鐘，然後執行下列程式來確認模組正在執行，然後再嘗試再次使用 DirectMethod。

    ```
    sudo iotedge list
    ```
    1. 如果上述動作也失敗，請嘗試重新開機您的 VM 或電腦。
    1. 如果全部失敗，請執行下列程式來取得 ZIP 檔案，並將所有[相關記錄](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command)附加至該[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. 如果您收到錯誤回應400程式碼，請確定您的方法叫用裝載的格式是否正確，如同[直接方法](direct-methods.md)指南。
1. 如果您取得狀態200程式碼，則表示您的中樞正常運作，且您的模組部署正確且有回應。 下一個步驟是檢查應用程式設定是否正確。 您的應用程式設定包含檔案上 appsettings.js中的下欄欄位。 請再次檢查 deviceId 和 moduleId 是否正確。 簡單的方法是透過 VSCode 中的 Azure IoT 中樞延伸模組區段來檢查這項功能。 appsettings.json 檔案和 IoT 中樞區段的值應該相符。
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT 中樞](./media/troubleshoot-how-to/iot-hub.png)

1. 最後，請確定在 appsettings.js中，您已提供 IoT 中樞的連接字串，而不是 IoT 中樞的裝置連接字串，因為它們的[格式](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)不同。

### <a name="live-video-analytics-working-with-external-modules"></a>使用外部模組的即時影片分析

透過 HTTP 擴充處理器的即時影片分析可以擴充 media graph，使用 REST 透過 HTTP 傳送和接收來自其他 IoT Edge 模組的資料。  作為[特定範例](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)，media graph 可以將影片畫面當做影像傳送到外部推斷模組（例如 Yolo V3），並傳回 JSON 型分析結果。 在這種拓撲中，事件的結束目的地大多是 IoT 中樞。 在您未在中樞上看到推斷事件的情況下，請檢查下列各項：

1. 檢查 media graph 是否正在發佈至 vs 的中樞。您所檢查的是相同的。 有時候，當您建立多個部署時，您會得到多個中樞，而且可能會錯誤地檢查錯誤的中樞是否有事件。
1. 如果外部模組已部署且正在執行，請透過 VSCode 檢查。 在這裡的範例影像中，rtspsim 和 cv 是執行于 lvaEdge 模組外部的 IoT Edge 模組。

    ![IOT 中樞](./media/troubleshoot-how-to/iot-hub.png)
1. 檢查您是否要將事件傳送至正確的 URL 端點。 外部 AI 容器會公開 URL，以及用來接收並從 POST 要求傳回資料的埠。 此 URL 指定為 Http 擴充處理器的 [端點： URL] 屬性。 如[拓撲 url](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)所示，設定為推斷 url 參數。 請確定參數的預設值（ http://yolov3/score) 或傳入的值是正確的，而且您可以使用捲曲來測試其是否正常運作。  
    1. 例如，在本機電腦上執行的 yolo v3 容器和容器的 IP 位址是172.17.0.3 （使用 docker 檢查來尋找 IP 位址）。

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    傳回的結果：

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. 如果您使用 Http 延伸模組處理器執行圖形的一或多個實例，則在每個 Http 擴充處理器之前，您應該要有畫面播放速率篩選，才能管理影片摘要的每秒畫面格數（fps）。 在 edge 機器的 CPU/記憶體高度使用的某些情況下，您可能會遺失某些推斷事件。 若要解決這個情況，請在 [畫面播放速率] 篩選器上設定 maximumFps 屬性的低值。 您可以在圖形的每個實例上將它設定為0.5 （"maximumFps"：0.5），然後重新執行來檢查中樞上的推斷事件。
    1. 或者，您也可以使用更高的 CPU 和記憶體，取得更強大的邊緣機器。
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>平行處理多個直接方法–超時失敗 

IoT Edge 上的即時影片分析提供以直接方法為基礎的程式設計模型，可讓您設定多個拓撲和多個圖形實例。 在拓撲和圖形設定中，您將會在邊緣模組上叫用多個直接方法呼叫。 如果您叫用這些多個方法呼叫，尤其是啟動和停止圖形的情況，您可能會遇到一些超時失敗，如下所示。 

元件初始化方法 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync 擲回例外狀況。 IotHubException： IotHubException （常見例外狀況.）。一般除外。：<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

我們建議您不要以平行方式呼叫直接方法，而是以順序方式執行，例如 只有在前一個呼叫完成後，才會進行一個直接方法呼叫。

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>收集記錄以提交支援票證

當自行引導的疑難排解步驟無法解決您的問題時，您應該前往 Azure 入口網站並[開啟支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

請完成下列步驟，以收集應新增至票證的相關記錄檔。 您將能夠在支援要求的 [**詳細資料**] 索引標籤中上傳記錄檔。

### <a name="support-bundle"></a>支援-配套

當您需要從 IoT Edge 裝置收集記錄檔時，最簡單的方式是使用 `support-bundle` 命令。 此命令會收集：

- 模組記錄
- IoT Edge 安全性管理員和容器引擎記錄
- Iotedge 檢查 JSON 輸出
- 有用的調試資訊

#### <a name="use-the-iot-edge-security-manager"></a>使用 IoT Edge 安全性管理員
 
「IoT Edge 安全性管理員」負責在啟動和布建裝置時初始化 IoT Edge 系統的作業。 如果 IoT Edge 未啟動，安全性管理員記錄檔可能會提供有用的資訊。 若要查看更詳細的 IoT Edge 安全性管理員的記錄：

1. 編輯 IoT Edge 裝置上的 IoT Edge daemon 設定：

    ```
    sudo systemctl edit iotedge.service
    ```

1. 更新以下幾行：

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. 執行下列命令來重新開機 IoT Edge Security Daemon：

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. `support-bundle`使用--[開始] 旗標來執行命令，以指定過去要取得記錄的時間長度。 例如，從過去兩小時以來，將會取得記錄。 您可以變更這個旗標的值，以包含不同期間的記錄。

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>LVA debug 記錄

請遵循下列步驟，在 IoT Edge 模組上設定 LVA，以產生 debug 記錄檔：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取功能表中的 [IoT Edge]****。
1. 按一下裝置清單中目標裝置的識別碼。
1. 按一下上方功能表上的 [**設定模組**] 連結。

  ![設定模組 azure 入口網站](media/troubleshoot-how-to/set-modules.png)

5. 在 [IoT Edge 模組] 區段下，尋找並按一下 [ **lvaEdge**]。
1. 按一下 [**容器] [建立選項**]。
1. 在 [系結] 區段中，新增下列命令：

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    這會系結 edge 裝置與容器之間的 logs 資料夾。

1. 按一下 [**更新**] 按鈕
1. 按一下頁面底部的 [**審查 + 建立**] 按鈕。 將會進行簡單的驗證，並在綠色橫幅下張貼成功的驗證訊息。
1. 按一下 [建立]  按鈕。
1. 接下來，更新**模組身分識別**對應項，以將 DebugLogsDirectory 參數指向要收集記錄檔的目錄：
    1. 選取 [**模組**] 資料表底下的 [ **lvaEdge** ]。
    1. 按一下 [**模組身分識別**對應項] 連結。 您會在頁面頂端找到此位置。 這會開啟可編輯的窗格。
    1. 在**所需**的索引鍵下新增下列機碼值組：

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. 按一下 [ **儲存**]。

1. 重現問題。
1. 從入口網站的 [IoT 中樞] 頁面連線到虛擬機器。
1. 流覽至 `/var/local/mediaservices/logs` 資料夾，並壓縮此資料夾的 bin 內容，並與我們分享。 （這些記錄檔並非用於自我診斷。 它們的目的是要讓 Azure 工程人員分析您的問題）。

1. 您可以將**模組識別**對應項中的該值重新設定為*null* ，以停止記錄檔收集。 回到 [模組身分**識別**對應項] 頁面，並將下列參數更新為：

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>後續步驟

[教學課程：以事件為基礎的影片錄製到雲端並從雲端播放](event-based-video-recording-tutorial.md)
