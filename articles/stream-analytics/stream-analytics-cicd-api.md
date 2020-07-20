---
title: 使用 REST Api 來執行 CI/CD 以進行 Azure IoT Edge 串流分析
description: 了解如何使用 REST API 實作適用於 Azure 串流分析的持續整合和部署管線。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: ed11488f397704be782a092d6cdc6463449cc71e
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039070"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>為 IoT Edge 串流分析實作 CI/CD

您可以使用 REST API 啟用適用於 Azure 串流分析作業的持續整合和部署。 本文提供關於應使用哪些 API 及其使用方式的範例。 Azure Cloud Shell 不支援 REST API。

## <a name="call-apis-from-different-environments"></a>從不同的環境呼叫 API

REST API 可從 Linux 和 Windows 呼叫。 下列命令示範呼叫 API 的正確語法。 本文的後續幾節將概述特定 API 的使用方式。

### <a name="linux"></a>Linux

針對 Linux，您可以使用 `Curl` 或 `Wget` 命令：

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

針對 Windows 可以使用 PowerShell： 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>在 Edge 上建立 ASA 作業 
 
若要建立串流分析作業，請使用串流分析 API 呼叫 PUT 方法。

|方法|要求 URL|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
使用 **curl** 的命令範例：

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
JSON 中的要求本文範例：

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
如需詳細資訊，請參閱 [API 文件](/rest/api/streamanalytics/stream-analytics-job)。  
 
## <a name="publish-edge-package"></a>發佈 Edge 套件 
 
若要在 IoT Edge 上發佈串流分析作業，請使用 Edge 套件發佈 API 呼叫 POST 方法。

|方法|要求 URL|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

這個非同步作業會傳回狀態 202，直到作業已成功發佈為止。 位置回應標頭包含用來取得程序狀態的 URI。 在程序執行期間呼叫位置標頭中的 URI，會傳回狀態 202。 程序完成後，位置標頭中的 URI 會傳回狀態 200。 

使用 **curl** 的 Edge 套件發佈呼叫範例： 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
發出 POST 呼叫後，應會傳回含有空本文的回應。 請找出位於回應標頭中的 URL 並加以記錄，以供進一步使用。
 
回應標頭中的 URL 範例：

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
請等候一到兩分鐘再執行下列命令，以對您在回應標頭中找到的 URL 進行 API 呼叫。 若未收到 200 的回應，請重試命令。
 
使用 **curl** 對傳回的 URL 進行 API 呼叫的範例：

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

回應會包含您需要新增至 Edge 部署指令碼的資訊。 下列範例說明您需要收集的資訊以及應將其新增至部署資訊清單中的何處。
 
成功發佈後的範例回應本文：

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

部署資訊清單的範例： 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

部署資訊清單設定完成後，請參閱[使用 Azure CLI 部署 Azure IoT Edge 模組](../iot-edge/how-to-deploy-modules-cli.md)進行部署。


## <a name="next-steps"></a>後續步驟 
 
* [Azure IoT Edge 串流分析](stream-analytics-edge.md)
* [ASA IoT Edge 教學課程](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [使用 Visual Studio 工具來開發串流分析 Edge 作業](stream-analytics-tools-for-visual-studio-edge-jobs.md)
