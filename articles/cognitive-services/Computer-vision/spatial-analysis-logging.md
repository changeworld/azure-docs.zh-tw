---
title: 空間分析容器的遙測和記錄
titleSuffix: Azure Cognitive Services
description: 空間分析會為每個容器提供常見的設定架構見解、記錄和安全性設定。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: dd1b6d216f6225a13d86aa2435b5b1c807547ec3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014572"
---
# <a name="telemetry-and-troubleshooting"></a>遙測和疑難排解

空間分析包含一組功能，可監視系統的健康情況，並協助診斷問題。

## <a name="enable-visualizations"></a>啟用視覺效果

若要啟用影片畫面中 AI 見解事件的視覺效果，您需要在 `.debug` 桌上型電腦上使用 [空間分析](spatial-analysis-operations.md) 作業的版本。 Azure Stack Edge 裝置無法進行視覺效果。 有四個可用的調試作業。

如果您的裝置不是 Azure Stack Edge 裝置，請編輯 [桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) 的部署資訊清單檔，以使用正確的 `DISPLAY` 環境變數值。 它必須符合 `$DISPLAY` 主機電腦上的變數。 更新部署資訊清單之後，請重新部署容器。

完成部署之後，您可能必須將該檔案 `.Xauthority` 從主機電腦複製到容器，然後重新開機。 在下列範例中， `peopleanalytics` 是主機電腦上的容器名稱。

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>收集系統健康情況遙測

Telegraf 是可搭配空間分析使用的開放原始碼映射，可在 Microsoft Container Registry 中取得。 它會採用下列輸入，並將其傳送至 Azure 監視器。 您可以使用所需的自訂輸入和輸出來建立 telegraf 模組。 空間分析中的 telegraf 模組設定是部署資訊清單的一部分， (連結) 。 此模組是選擇性的，如果不需要，您可以從資訊清單中移除它。 

輸入： 
1. 空間分析計量
2. 磁碟計量
3. CPU 計量
4. Docker 計量
5. GPU 計量

輸出：
1. Azure 監視器

提供的空間分析 telegraf 模組會將空間分析容器發出的所有遙測資料發佈至 Azure 監視器。 如需有關將 Azure 監視器新增至訂用帳戶的詳細資訊，請參閱 [Azure 監視器](../../azure-monitor/overview.md) 。

設定 Azure 監視器之後，您將需要建立可讓模組傳送遙測的認證。 您可以使用 Azure 入口網站來建立新的服務主體，或使用下面的 Azure CLI 命令建立一個。

> [!NOTE] 
> 此命令會要求您擁有訂用帳戶的擁有者許可權。 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

在 [Azure Stack Edge 裝置](https://go.microsoft.com/fwlink/?linkid=2142179) 或其他 [桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)的部署資訊清單中，尋找 *telegraf* 模組，並將下列值取代為上一個步驟中的服務主體資訊，然後重新部署。

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

部署 telegraf 模組之後，就可以透過 Azure 監視器服務存取回報的度量，或在 Azure 入口網站的 IoT 中樞中選取 [ **監視** ]。

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure 監視器遙測報告":::

### <a name="system-health-events"></a>系統健康情況事件

| 活動名稱 | Description|
|------|---------|
|archon_exit    |當 *使用者將空間* 分析模組狀態從「執行」變更為「 *已停止*」時傳送。  |
|archon_error   |當容器內的任何進程損毀時傳送。 這是嚴重錯誤。  |
|InputRate  |圖形處理影片輸入的速率。 每隔5分鐘回報一次。 | 
|OutputRate     |圖形輸出 AI 見解的速率。 每隔5分鐘回報一次。 |
|archon_allGraphsStarted | 在所有圖形都完成啟動時傳送。 |
|archon_configchange    | 在圖形設定變更時傳送。 |
|archon_graphCreationFailed     |當報告的圖表 `graphId` 無法啟動時傳送。 |
|archon_graphCreationSuccess    |當報告的圖表 `graphId` 成功啟動時傳送。 |
|archon_graphCleanup    | 當具有報告的圖形清除並結束時傳送 `graphId` 。 |
|archon_graphHeartbeat  |每分鐘傳送的每個技能圖表的信號。 |
|archon_apiKeyAuthFail |當電腦視覺資源金鑰因為下列原因而無法驗證容器超過24小時時傳送，原因如下：配額不足、無效、離線。 |
|VideoIngesterHeartbeat     |每小時傳送一次，表示影片會從影片來源進行串流處理，並顯示該小時的錯誤數目。 針對每個圖表報告。 |
|VideoIngesterState | *已停止* 或 *啟動* 影片串流的報告。 針對每個圖表報告。 |

##  <a name="troubleshooting-an-iot-edge-device"></a>針對 IoT Edge 裝置進行疑難排解

您可以使用 `iotedge` 命令列工具來檢查正在執行之模組的狀態和記錄。 例如：
* `iotedge list`：報告正在執行的模組清單。 
  您可以進一步檢查的錯誤 `iotedge logs edgeAgent` 。 如果 `iotedge` 停滯，您可以嘗試將它重新開機 `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` 重新開機特定模組 

## <a name="collect-log-files-with-the-diagnostics-container"></a>使用診斷容器收集記錄檔

空間分析會產生可供您用來診斷執行時間問題或包含在支援票證中的 Docker 偵錯工具記錄。 您可在 Microsoft Container Registry 中取得空間分析診斷模組，以供您下載。 在 [Azure Stack Edge 裝置](https://go.microsoft.com/fwlink/?linkid=2142179) 或其他 [桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)的資訊清單部署檔案中，尋找 *診斷* 模組。

在 [env] 區段中，新增下列設定：

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

若要將上傳至遠端端點的記錄優化，例如 Azure Blob 儲存體，我們建議您維持小型檔案大小。 請參閱下列範例，以取得建議的 Docker 記錄設定。

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>設定記錄層級

記錄層級設定可讓您控制所產生記錄的詳細資訊。 支援的記錄層級為： `none` 、 `verbose` 、 `info` 、 `warning` 和 `error` 。 節點和平臺的預設記錄詳細資訊層級為 `info` 。 

您可以將 `ARCHON_LOG_LEVEL` 環境變數設定為其中一個允許的值，以全域修改記錄層級。
您也可以針對所有已部署的技能，或設定的值（如下所示），透過 IoT Edge 模組對應項檔（全域）、所有已部署的技能，或針對每個特定技能來設定它 `platformLogLevel` `nodeLogLevel` 。

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>收集記錄

> [!NOTE]
> 此 `diagnostics` 模組不會影響記錄內容，只有助于收集、篩選和上傳現有的記錄。
> 您必須擁有 Docker API 1.40 版或更高版本，才能使用此模組。

[Azure Stack Edge 裝置](https://go.microsoft.com/fwlink/?linkid=2142179)或其他[桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)的部署資訊清單檔範例包含一個名為 `diagnostics` 的模組，該模組會收集和上傳記錄。 此模組預設為停用，而且當您需要存取記錄時，應該透過 IoT Edge 模組設定來啟用。 

`diagnostics`集合視需要而受到 IoT Edge 直接方法控制，並可將記錄傳送至 Azure Blob 儲存體。

### <a name="configure-diagnostics-upload-targets"></a>設定診斷上傳目標

在 IoT Edge 入口網站中，選取您的裝置，然後選取 [ **診斷** ] 模組。 在 [Azure Stack Edge 裝置](https://go.microsoft.com/fwlink/?linkid=2142179) 或其他 [桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)的部署資訊清單檔範例中，尋找 [診斷] 的 [ **環境變數** ] 區段， `env` 並將其命名為，然後新增下列資訊：

**設定上傳至 Azure Blob 儲存體**

1. 建立您自己的 Azure Blob 儲存體帳戶（如果您尚未這樣做）。
2. 從 Azure 入口網站取得儲存體帳戶的 **連接字串** 。 它會位於 **存取金鑰** 中。
3. 空間分析記錄會自動上傳至名為 *rtcvlogs* 的 Blob 儲存體容器，並使用下列檔案名格式： `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` 。

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>上傳空間分析記錄

記錄會隨選隨 IoT Edge 的方法上傳至 `getRTCVLogs` 模組中 `diagnostics` 。 


1. 移至您的 IoT 中樞入口網站頁面，選取 [ **Edge 裝置**]，然後選取您的裝置和診斷模組。 
2. 移至模組的 [詳細資料] 頁面，然後按一下 [*_直接方法_* _] 索引標籤。
3. 輸入 `getRTCVLogs` 方法名稱，以及裝載中的 json 格式字串。 您可以輸入 `{}` ，這是空的承載。 
4. 設定連接和方法超時，然後按一下 _ * [叫用方法]。
5. 選取您的目標容器，並使用 **記錄語法** 一節中所述的參數建立承載 json 字串。 按一下 [叫用 **方法** ] 以執行要求。

>[!NOTE]
> 使用空的承載叫 `getRTCVLogs` 用方法，將會傳回在裝置上部署的所有容器的清單。 方法名稱會區分大小寫。 如果指定了不正確的方法名稱，您將會收到501錯誤。

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="叫用 getRTCVLogs 方法 ":::
![getRTCVLogs 直接方法頁面](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>記錄語法

下表列出查詢記錄時可使用的參數。

| 關鍵字 | 描述 | 預設值 |
|--|--|--|
| StartTime | 需要的記錄開始時間（以毫秒為單位）。 | `-1`，容器執行時間的起點。 當當做 `[-1.-1]` 時間範圍使用時，API 會傳回過去一小時內的記錄。|
| EndTime | 需要的記錄結束時間（以毫秒為單位）。 | `-1`，目前的時間。 `[-1.-1]`使用時間範圍時，api 會傳回過去一小時內的記錄。 |
| ContainerId | 用於提取記錄的目標容器。| `null`，沒有容器識別碼時。 API 會傳回所有可用的容器資訊，並提供識別碼。|
| DoPost | 執行上傳作業。 當這個設定為時 `false` ，它會執行要求的作業，並傳回上傳大小，而不需執行上傳。 當設定為時 `true` ，會起始所選記錄的非同步上傳 | `false`，請勿上傳。|
| 節流 | 指出每個批次上傳的記錄行數 | `1000`，請使用此參數來調整 post 速度。 |
| 篩選器 | 篩選要上傳的記錄 | `null`您可以根據空間分析記錄結構，將篩選指定為索引鍵值組： `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` 。 例如：`{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

下表列出查詢回應中的屬性。

| 關鍵字 | 描述|
|--|--|
|DoPost| 是 *true* 或 *false*。 指出是否已上傳記錄。 當您選擇不上傳記錄時，api 會以 **同步** 方式傳回信息 *。 當您選擇上傳記錄時，如果要求有效，api 會傳回200，並以 _*_非同步方式_*_ 開始上傳記錄。|
|TimeFilter| 套用至記錄的時間篩選準則。|
|ValueFilters| 套用至記錄檔的關鍵字篩選。 |
|TimeStamp| 方法執行開始時間。 |
|ContainerId| 目標容器識別碼。 |
|FetchCounter| 記錄行總數。 |
|FetchSizeInByte| 記錄資料的總量（以位元組為單位）。 |
|MatchCounter| 記錄行的有效位數。 |
|MatchSizeInByte| 有效的記錄資料量（以位元組為單位）。 |
|FilterCount| 套用篩選之後的記錄行總數。 |
|FilterSizeInByte| 套用篩選之後的總記錄資料量（以位元組為單位）。 |
|FetchLogsDurationInMiliSec| 提取作業持續時間。 |
|PaseLogsDurationInMiliSec| 篩選作業持續時間。 |
|PostLogsDurationInMiliSec| Post 作業持續時間。 |

#### <a name="example-request"></a>範例要求 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>範例回應 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

檢查 fetch 記錄行、時間和大小（如果這些設定看起來很不錯，請將 _*_DoPost_*_ 取代為， `true` 並將具有相同篩選的記錄推送至目的地）。 

針對問題進行疑難排解時，您可以從 Azure Blob 儲存體匯出記錄。 

## <a name="common-issues"></a>常見問題

如果您在模組記錄檔中看到下列訊息，可能表示您的 Azure 訂用帳戶需要核准： 

「容器不是處於有效的狀態。 訂用帳戶驗證失敗，狀態為「不相符」。 Api 金鑰並非適用于指定的容器類型。」

如需詳細資訊，請參閱 [要求核准以執行容器](spatial-analysis-container.md#request-approval-to-run-the-container)。

## <a name="troubleshooting-the-azure-stack-edge-device"></a>針對 Azure Stack Edge 裝置進行疑難排解

下一節是為了協助您進行 Azure Stack Edge 裝置的狀態的偵測和驗證。

### <a name="access-the-kubernetes-api-endpoint"></a>存取 Kubernetes API 端點。 

1. 在裝置的本機 UI 中，移至 [_ *裝置*] 頁面。 
2. 在 **裝置端點** 下，複製 Kubernetes API 服務端點。 此端點的字串為下列格式：`https://compute..[device-IP-address]`。
3. 儲存端點字串。 您稍後會在設定以存取 Kubernetes 叢集時使用此設定 `kubectl` 。

### <a name="connect-to-powershell-interface"></a>連接到 PowerShell 介面

從遠端連線到 Windows 用戶端。 建立 Kubernetes 叢集之後，您可以透過此叢集來管理應用程式。 您將需要連接到裝置的 PowerShell 介面。 視用戶端的作業系統而定，從遠端連線到裝置的程式可能會不同。 下列步驟適用于執行 PowerShell 的 Windows 用戶端。

> [!TIP]
> * 開始之前，請確定您的 Windows 用戶端正在執行 Windows PowerShell 5.0 或更新版本。
> * PowerShell 也 [可在 Linux 上使用](/powershell/scripting/install/installing-powershell-core-on-linux)。

1. 以系統管理員身分執行 Windows PowerShell 會話。 
    1. 確定您的用戶端上正在執行 Windows 遠端管理服務。 在命令提示字元中，輸入 `winrm quickconfig`。

2. 指派裝置 IP 位址的變數。 例如： `$ip = "<device-ip-address>"` 。

3. 使用下列命令將裝置的 IP 位址新增至用戶端的受信任主機清單。 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. 在裝置上啟動 Windows PowerShell 會話。 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. 出現提示時，請提供密碼。 使用與用來登入本機 web 介面相同的密碼。 預設的本機 web 介面密碼為 `Password1` 。 

### <a name="access-the-kubernetes-cluster"></a>存取 Kubernetes 叢集

建立 Kubernetes 叢集之後，您可以使用 `kubectl` 命令列工具來存取叢集。

1. 建立新的命名空間。 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. 建立使用者並取得設定檔。 此命令會輸出 Kubernetes 叢集的設定資訊。 複製此資訊，並將其儲存在名為 *config* 的檔案中。請勿將檔案儲存為副檔名。
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. 將 *配置* 檔新增至本機電腦上的使用者設定檔中的 *kube 資料夾。*   

4. 將命名空間與您建立的使用者產生關聯。

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. `kubectl`使用下列命令，在您的 Windows 用戶端上安裝：
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. 將 DNS 專案新增至系統上的 hosts 檔案。 
    1. 以系統管理員身分執行 [記事本]，然後開啟位於的 *主機* 檔案 `C:\windows\system32\drivers\etc\hosts` 。 
    2. 在主機檔案中建立一個專案，其中包含您從本機 UI 的 [ **裝置** ] 頁面取得的裝置 IP 位址和 DNS 網域。 您應該使用的端點看起來會像這樣： `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` 。

7. 確認您可以連線至 Kubernetes pod。

    ```powershell
    kubectl get pods -n "iotedge"
    ```

若要取得容器記錄，請執行下列命令：

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>有用的命令

|命令  |說明  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | 產生 Kubernetes 設定檔。 使用命令時，請將資訊複製到名為 *config* 的檔案中。請勿使用副檔名儲存檔案。        |
| `Get-HcsApplianceInfo` | 傳回您裝置的相關資訊。 |
| `Enable-HcsSupportAccess` | 產生存取認證以啟動支援會話。 |

## <a name="next-steps"></a>下一步

* [部署人員計數 web 應用程式](spatial-analysis-web-app.md)
* [設定空間分析作業](./spatial-analysis-operations.md)
* [攝影機放置指南](spatial-analysis-camera-placement.md)
* [區域和行放置指南](spatial-analysis-zone-line-placement.md)