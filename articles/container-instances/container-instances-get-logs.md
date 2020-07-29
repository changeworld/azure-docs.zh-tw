---
title: 取得容器實例記錄 & 事件
description: 瞭解如何在 Azure 容器實例中取得容器記錄和事件，以協助針對容器問題進行疑難排解
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: 0991b9cb1f99606910dbdf2c87b111f67da6da7b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78250005"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>在 Azure 容器執行個體中擷取容器記錄和事件

當您在 Azure 容器實例中有異常的容器時，請先使用[az 容器記錄][az-container-logs]來查看其記錄，然後使用[az container attach][az-container-attach]來串流其標準輸出和標準錯誤。 您也可以在 Azure 入口網站中查看容器實例的記錄和事件，或將容器群組的記錄檔和事件資料傳送至[Azure 監視器記錄](container-instances-log-analytics.md)。

## <a name="view-logs"></a>檢視記錄

若要在容器內檢視應用程式程式碼中的記錄，您可以使用 [az container logs][az-container-logs] 命令。

以下是在使用命令列覆寫提供不正確 URL 之後，在[容器實例中設定命令列](container-instances-start-command.md#azure-cli-example)中，以工作為基礎的容器範例中的記錄輸出：

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

例如，在提供要處理之大型文字檔的有效 URL 之後，在[容器實例的設定命令列](container-instances-start-command.md#azure-cli-example)中，以工作為基礎的容器輸出如下：

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

如果您的容器無法順利部署，請參閱 Azure 容器實例資源提供者所提供的診斷資訊。 若要檢視容器的事件，請執行 [az container show][az-container-show]：

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

瞭解如何將容器群組的記錄檔和事件資料傳送至[Azure 監視器記錄](container-instances-log-analytics.md)。

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show