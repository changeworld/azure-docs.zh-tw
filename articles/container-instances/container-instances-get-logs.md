---
title: 獲取容器實例日誌&事件
description: 瞭解如何在 Azure 容器實例中檢索容器日誌和事件，以説明解決容器問題
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: 0991b9cb1f99606910dbdf2c87b111f67da6da7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250005"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>在 Azure 容器執行個體中擷取容器記錄和事件

在 Azure 容器實例中遇到行為不端的容器時，首先使用 az[容器日誌][az-container-logs]查看其日誌，然後使用[az 容器附加][az-container-attach]資料流其標準出和標準錯誤。 您還可以在 Azure 門戶中查看容器實例的日誌和事件，或者將容器組的日誌和事件資料發送到[Azure 監視器日誌](container-instances-log-analytics.md)。

## <a name="view-logs"></a>檢視記錄

若要在容器內檢視應用程式程式碼中的記錄，您可以使用 [az container logs][az-container-logs] 命令。

以下是在使用命令列重寫提供了無效 URL 後，容器[實例中"設置命令列"中](container-instances-start-command.md#azure-cli-example)的示例基於任務的容器的日誌輸出：

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>附加輸出資料流

[az container attach][az-container-attach] 命令會提供容器啟動期間的診斷資訊。 容器啟動之後，會將 STDOUT 和 STDERR 串流至您的本機主控台。

例如，在提供了要處理的大文字檔的有效 URL 後，在[容器實例中"設置命令列"中的](container-instances-start-command.md#azure-cli-example)基於任務的容器中輸出：

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>取得診斷事件

如果容器無法成功部署，請查看 Azure 容器實例資來源提供者提供的診斷資訊。 若要檢視容器的事件，請執行 [az container show][az-container-show]：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

輸出中會包含容器的核心屬性以及部署事件 (此處顯示已截斷)：

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>後續步驟
了解如何在 Azure 容器執行個體中[針對常見容器和部署問題進行疑難排解](container-instances-troubleshooting.md)。

瞭解如何將容器組的日誌和事件資料發送到 Azure[監視器日誌](container-instances-log-analytics.md)。

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show