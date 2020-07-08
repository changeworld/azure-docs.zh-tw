---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/26/2019
ms.author: alkohli
ms.openlocfilehash: 350d41980e3128a8747a673ebea82afbe4fab49b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85313200"
---
在已設定計算角色的 Azure Stack Edge 裝置上，可使用 docker 命令的子集來監視或疑難排解模組。 若要查看可用的命令清單，請[連接到 PowerShell 介面](#connect-to-the-powershell-interface)，並使用函式 `dkrdbe` 。

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
下表提供可供使用之命令的簡短描述 `dkrdbe` ：

|命令  |Description |
|---------|---------|
|`image`     | 管理影像。 若要移除未使用的映射，請使用：`dkrdbe image prune -a -f`       |
|`images`     | 列出映像         |
|`inspect`     | 傳回 Docker 物件的低層級資訊         |
|`login`     | 登入 Docker 登錄         |
|`logout`     | 從 Docker registry 登出         |
|`logs`     | 提取容器的記錄        |
|`port`     | 列出埠對應或容器的特定對應        |
|`ps`     | 列出容器        |
|`pull`     | 從登錄中提取映射或存放庫         |
|`start`     | 啟動一或多個已停止的容器         |
|`stats`     | 顯示容器資源使用方式統計資料的即時串流         |
|`stop`     | 停止一或多個正在執行的容器        |
|`system`     | 管理 Docker         |
|`top`     | 顯示容器的執行中進程         |

若要取得任何可用命令的說明，請使用 `dkrdbe <command-name> --help` 。

例如，若要瞭解命令的使用方式 `port` ，請輸入：

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

函式的可用命令會 `dkrdbe` 使用與一般 docker 命令所使用的相同參數。 如需搭配 docker 命令使用的選項和參數，請移至[使用 docker 命令列](https://docs.docker.com/engine/reference/commandline/docker/)。

### <a name="to-check-if-the-module-deployed-successfully"></a>檢查模組是否已成功部署

計算模組是已實作為商務邏輯的容器。 若要檢查是否已成功部署計算模組，請執行 `ps` 命令，並檢查容器（對應至計算模組）是否正在執行。

若要取得所有容器（包括已暫停的容器）的清單，請執行 `ps -a` 命令。

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ&euro;¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ&euro;¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â&euro;¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â&euro;¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

如果在建立容器映射或提取映射時發生錯誤，請執行 `logs edgeAgent` 。  `EdgeAgent`是負責布建其他容器的 IoT Edge 執行時間容器。

因為傾印 `logs edgeAgent` 所有的記錄，所以查看最近錯誤的好方法是使用選項 `--tail 20` 。


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>取得容器記錄

若要取得特定容器的記錄，請先列出容器，然後取得您感興趣之容器的記錄。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 若要取得執行中容器的清單，請執行 `ps` 命令。

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ&euro;¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ&euro;¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â&euro;¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â&euro;¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. 記下您需要記錄之容器的容器識別碼。

4. 若要取得特定容器的記錄，請執行 `logs` 提供容器識別碼的命令。

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>監視裝置的使用方式統計資料

若要監視裝置上的記憶體、CPU 使用量和 IO，請使用 `stats` 命令。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 執行命令，以 `stats` 停用即時資料流，並只提取第一個結果。

   ```powershell
   dkrdbe stats --no-stream
   ```

   下列範例顯示此 Cmdlet 的使用方式：

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

