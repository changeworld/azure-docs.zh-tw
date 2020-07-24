---
title: 生產環境就緒和最佳做法-Azure
description: 本文提供有關如何在生產環境中的 IoT Edge 模組上設定及部署即時影片分析的指導方針。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c34e05e184cfa6f0933701a76177fae3eed70c0a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071928"
---
# <a name="production-readiness-and-best-practices"></a>生產環境就緒和最佳做法

本文提供有關如何在生產環境中的 IoT Edge 模組上設定及部署即時影片分析的指導方針。 關於準備您的 IoT Edge 解決方案，您也應該參閱[準備在生產環境中部署您的 IoT Edge 解決方案](../../iot-edge/production-checklist.md)。 

> [!NOTE]
> 您應該向組織的 IT 部門詢問安全性的相關層面。

## <a name="running-the-module-as-a-local-user"></a>以本機使用者身分執行模組

當您將 IoT Edge 模組上的即時影片分析部署到邊緣裝置時，預設會以較高的許可權執行。 當您這樣做時，如果您檢查模組（）上的記錄 `sudo iotedge logs {name-of-module}` ，將會看到下列內容：

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

下列各節會討論如何解決上述警告。

### <a name="creating-and-using-a-local-user-account"></a>建立和使用本機使用者帳戶

您可以使用具有最低許可權的帳戶，在生產環境中的 IoT Edge 模組上執行即時影片分析。 例如，下列命令會示範如何在 Linux VM 上建立本機使用者帳戶：

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

接下來，在部署資訊清單中，您可以將 LOCAL_USER_ID 和 LOCAL_GROUP_ID 環境變數設定為該非根使用者和群組：

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>授與許可權給裝置存放裝置

IoT Edge 模組上的即時影片分析需要能夠在下列情況中，將檔案寫入本機檔案系統：

* 使用模組對應項屬性 [[applicationDataDirectory](module-twin-configuration-schema.md#module-twin-properties)]，您應該在此指定本機檔案系統上用於儲存設定資料的目錄。
* 使用 media graph 將影片錄製到雲端時，模組需要使用邊緣裝置上的目錄作為快取（如需詳細資訊，請參閱[連續的影片錄製](continuous-video-recording-concept.md)一文）。
* [記錄到本機](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)檔案，您應該在其中指定錄製影片的檔案路徑。

如果您想要使用上述任何一項，您應該確定上述使用者帳戶具有相關目錄的存取權。 例如，請考慮 applicationDataDirectory。 您可以在 edge 裝置上建立目錄，並將裝置存放裝置連結至模組儲存體。 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

接下來，在部署資訊清單的 [建立 edge 模組的選項] 中，您可以將上述目錄（"var/local/windowsazure.mediaservices.extensions/"）對應至模組中的目錄（例如 "/var/lib/azuremediaservices/"），以新增系結設定。 而且您會使用第二個目錄做為 applicationDataDirectory 的值。

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

如果您查看範例媒體圖形以取得快速入門和教學課程（例如[連續影片錄製](continuous-video-recording-tutorial.md)），您會注意到媒體快取目錄（localMediaCachePath）會使用 applicationDataDirectory 底下的子目錄。 這是建議的方法，因為快取包含暫時性資料。

### <a name="naming-video-assets-or-files"></a>命名影片資產或檔案

媒體圖形可讓您在雲端中建立資產，或在邊緣的檔案。 媒體資產可以透過[連續影片錄製](continuous-video-recording-tutorial.md)或以[事件為基礎的影片錄製](event-based-video-recording-tutorial.md)來產生。 雖然這些資產和檔案可以用您想要的方式命名，但以錄影為基礎的連續媒體資產建議的命名結構是 " &lt; anytext &gt; -$ {GraphTopologyName}-$ {GraphInstanceName}"。 例如，您可以在資產接收器上設定 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

若為以事件為基礎的影片記錄產生的資產，建議的命名模式為 " &lt; anytext &gt; -$ {system.string}"。 系統變數可確保當事件同時發生時，不會覆寫資產。 例如，您可以在資產接收器上設定 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

如果您正在執行相同圖形的多個實例，您可以使用圖形拓撲名稱和實例名稱來區分。 例如，您可以在資產接收器上設定 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName} -${System.DateTime}"
```

若為以事件為基礎的影片記錄-在邊緣上產生的可操作影片剪輯，建議的命名模式應該包含 DateTime，而相同圖形的多個實例則建議使用系統變數 GraphTopologyName 和 GraphInstanceName。 例如，您可以在 file sink 上設定 filePathPattern，如下所示： 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

或者 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```

### <a name="keeping-your-vm-clean"></a>保持 VM 整潔

如果您用來做為 edge 裝置的 Linux VM 不會定期進行管理，則可能會變得沒有回應。 請務必讓快取保持乾淨、排除不必要的套件，並從 VM 中移除未使用的容器。 若要在這裡執行此動作，您可以在 edge VM 上使用一組建議的命令。

1. `sudo apt-get clean`

    [Apt-取得清除] 命令會清除已取得封裝檔案的本機存放庫，並保留在/var/cache. 中。 它所清除的目錄是/var/cache/apt/archives/和/var/cache/apt/archives/partial/。 它在/var/cache/apt/archives 中留下的唯一檔案是鎖定檔案和部分子目錄。 Apt-clear 命令通常用來清除所需的磁碟空間，通常是定期進行維護的一部分。 如需詳細資訊，請參閱[使用 apt-get 進行清除](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)。
1. `sudo apt-get autoclean`

    Apt-get autoclean 選項（例如 apt）會清除已取出封裝檔案的本機存放庫，但它只會移除無法下載且不實用的檔案。 這有助於讓快取的成長變得太大。
1. `sudo apt-get autoremove1`

    [自動移除] 選項會移除已自動安裝的套件，因為有些套件需要它們，但是移除其他套件後，就不再需要它們
1. `sudo docker image ls`–提供 edge 系統上的 Docker 映射清單
1. `sudo docker system prune `

    Docker 採用保守的方法來清除未使用的物件（通常稱為「垃圾收集」），例如映射、容器、磁片區和網路：除非您明確要求 Docker 執行此動作，否則通常不會移除這些物件。 這可能會導致 Docker 使用額外的磁碟空間。 針對每種類型的物件，Docker 提供了剪除命令。 此外，您可以使用 docker 系統剪除一次清除多種類型的物件。 如需詳細資訊，請參閱剪除[未使用的 Docker 物件](https://docs.docker.com/config/pruning/)。
1. `sudo docker rmi REPOSITORY:TAG`

    當 edge 模組上發生更新時，您的 docker 可以有較舊版本的 edge 模組仍存在。 在這種情況下，建議使用 docker rmi 命令來移除映射版本戳記所識別的特定映射。

## <a name="next-steps"></a>後續步驟

[快速入門：開始使用 - IoT Edge 上的 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)
