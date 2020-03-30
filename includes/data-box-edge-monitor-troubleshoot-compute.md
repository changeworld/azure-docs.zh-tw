---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/26/2019
ms.author: alkohli
ms.openlocfilehash: f3bb391dceb1948820d00c0d09229f2c106ffc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68601307"
---
在配置了計算角色的資料框邊緣設備上，可以使用 Docker 命令的子集來監視或排除模組。 要查看可用命令的清單，[請連接到 PowerShell 介面](#connect-to-the-powershell-interface)並使用該`dkrdbe`函數。

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
下表簡要描述了可用於`dkrdbe`的命令：

|命令  |描述 |
|---------|---------|
|`image`     | 管理映射。 要刪除未使用的圖像，請使用：`dkrdbe image prune -a -f`       |
|`images`     | 列出映像         |
|`inspect`     | 返回有關 Docker 物件的低級資訊         |
|`login`     | 登錄到 Docker 註冊表         |
|`logout`     | 從 Docker 註冊表登出         |
|`logs`     | 獲取容器的日誌        |
|`port`     | 列出容器的埠映射或特定映射        |
|`ps`     | 列出容器        |
|`pull`     | 從註冊表中提取映射或存儲庫         |
|`start`     | 啟動一個或多個已停止的容器         |
|`stats`     | 顯示容器資源使用方式統計資訊的即時流         |
|`stop`     | 停止一個或多個正在運行的容器        |
|`system`     | 管理 Docker         |
|`top`     | 顯示容器的運行進程         |

要獲取任何可用命令的説明，請使用`dkrdbe <command-name> --help`。

例如，要瞭解命令的用法，`port`可以鍵入：

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

函數的`dkrdbe`可用命令使用與用於普通 Docker 命令的參數相同的參數。 對於與 Docker 命令一起使用的選項和參數，轉到[使用 Docker 命令列](https://docs.docker.com/engine/reference/commandline/docker/)。

### <a name="to-check-if-the-module-deployed-successfully"></a>檢查模組部署是否成功

計算模組是實現業務邏輯的容器。 要檢查是否成功部署計算模組，請運行`ps`該命令並檢查容器（與計算模組對應）是否正在運行。

要獲取所有容器（包括已暫停的容器）的清單，運行該`ps -a`命令。

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

如果在創建容器映射或拉取映射時出錯，則運行`logs edgeAgent`。  `EdgeAgent`是負責預配其他容器的 IoT 邊緣運行時容器。

由於`logs edgeAgent`轉儲所有日誌，因此查看最近錯誤的一個好方法是使用 選項`--tail 20`。


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

### <a name="to-get-container-logs"></a>獲取容器日誌

要獲取特定容器的日誌，首先列出容器，然後獲取您感興趣的容器的日誌。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 要獲取正在運行的容器的清單，運行該`ps`命令。

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. 記下需要日誌的容器的容器 ID。

4. 要獲取特定容器的日誌，`logs`運行提供容器 ID 的命令。

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

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>監視設備的使用方式統計資訊

要監視設備上的記憶體、CPU 使用方式和 IO，請使用 命令`stats`。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 運行該`stats`命令以禁用即時流並僅拉第一個結果。

   ```powershell
   dkrdbe stats --no-stream
   ```

   下面的示例顯示了此 Cmdlet 的用法：

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

