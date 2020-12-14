---
title: 生產環境就緒和最佳做法-Azure
description: 本文提供有關如何在生產環境中的 IoT Edge 模組上設定和部署即時影片分析的指引。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56982d84b7ffac718072683076657d56a2691d6c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400551"
---
# <a name="production-readiness-and-best-practices"></a>生產環境就緒和最佳做法

本文提供有關如何在生產環境中的 IoT Edge 模組上設定和部署即時影片分析的指引。 您也應該複習「 [準備」，在生產環境中部署您的 IoT Edge 解決方案，以](../../iot-edge/production-checklist.md) 準備您的 IoT Edge 解決方案。 

> [!NOTE]
> 您應該向組織的 IT 部門諮詢安全性相關層面。

## <a name="running-the-module-as-a-local-user"></a>以本機使用者身分執行模組

當您將 IoT Edge 模組上的即時影片分析部署到邊緣裝置時，預設會以較高的許可權執行。 當您這樣做時，如果您檢查模組上的記錄檔 (`sudo iotedge logs {name-of-module}`) ，您將會看到下列內容：

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

下列各節會討論如何處理上述警告。

### <a name="creating-and-using-a-local-user-account"></a>建立和使用本機使用者帳戶

您可以和應該使用具有最少許可權的帳戶，在生產環境中的 IoT Edge 模組上執行即時影片分析。 例如，下列命令會示範如何在 Linux VM 上建立本機使用者帳戶：

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

接下來，在部署資訊清單中，您可以將 LOCAL_USER_ID 和 LOCAL_GROUP_ID 環境變數設定為非根使用者和群組：

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

IoT Edge 模組上的即時影片分析需要能夠在下列情況將檔案寫入本機檔案系統：

* 使用模組對應項屬性 [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties) ，您應該在此指定本機檔案系統上的目錄來儲存設定資料。
* 使用 media graph 將影片記錄到雲端時，模組需要使用 edge 裝置上的目錄作為快取 (如需詳細資訊，請參閱 [持續的影片記錄](continuous-video-recording-concept.md) 文章) 。
* [記錄到本機](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)檔案，您應該在其中指定錄製影片的檔案路徑。

如果您想要使用上述任一項，請確定上述使用者帳戶具有相關目錄的存取權。 例如，請考慮 applicationDataDirectory。 您可以在 edge 裝置上建立目錄，並將裝置儲存體連結至模組儲存體。 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

接下來，在部署資訊清單的 [建立 edge 模組的選項] 中，您可以將系結設定對應至模組中的目錄 ( "var/local/windowsazure.mediaservices/" )  (例如 "/var/lib/azuremediaservices/" ) 。 您可以使用第二個目錄作為 applicationDataDirectory 的值。

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

如果您查看快速入門和教學課程的範例媒體圖形，例如 [持續錄製影片](continuous-video-recording-tutorial.md)，您將會注意到 media cache 目錄 (localMediaCachePath) 使用 applicationDataDirectory 下的子目錄。 這是建議的方法，因為快取包含暫時性資料。

### <a name="naming-video-assets-or-files"></a>命名影片資產或檔案

Media graph 可讓您建立雲端中的資產或邊緣上的檔。 媒體資產可透過連續的 [影片錄製](continuous-video-recording-tutorial.md) 或以 [事件為基礎的影片錄製](event-based-video-recording-tutorial.md)來產生。 雖然這些資產和檔案可依您的需要命名，但持續以影片錄製為基礎之媒體資產的建議命名結構是 " &lt; anytext &gt; -$ {GraphTopologyName}-$ {GraphInstanceName}"。   

替代模式是由 $ 符號後面接著大括弧： **$ {variableName}** 所定義。  

例如，您可以在資產接收器上設定 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

如果是以事件為基礎的影片記錄產生的資產，建議的命名模式為 " &lt; anytext &gt; -$ {system.string}"。 系統變數可確保在事件發生時不會覆寫資產。 例如，您可以在資產接收器上設定 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

如果您正在執行相同圖形的多個實例，您可以使用圖形拓撲名稱和實例名稱來區分。 例如，您可以在資產接收器上設定 assetNamePattern，如下所示：

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

如果是以事件為基礎的影片在邊緣上產生的多型影片剪輯，建議的命名模式應該包含日期時間，而相同圖表的多個實例則建議使用系統變數 GraphTopologyName 和 GraphInstanceName。 例如，您可以在檔案接收上設定 filePathPattern，如下所示： 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

Or 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> 在上述範例中，變數 **fileSinkOutputName** 是您在圖形拓撲中定義的範例變數名稱。 這 **不** 是系統變數。 

#### <a name="system-variables"></a>系統變數
您可以使用的一些系統定義變數如下：

|系統變數|描述|範例|
|-----------|-----------|-----------|
|System.DateTime|UTC 日期時間，以 ISO8601 檔相容格式 (基本表示 YYYYMMDDThhmmss) 。|20200222T173200Z|
|System. PreciseDateTime|UTC 日期時間（以 ISO8601 檔案相容格式），以毫秒為單位 (基本表示 YYYYMMDDThhmmss。 sss) 。|20200222T 173200.123 Z|
|System. GraphTopologyName|使用者提供的執行中圖形拓撲名稱。|IngestAndRecord|
|System. GraphInstanceName|使用者提供的執行中圖形實例名稱。|camera001|

>[!TIP]
> 命名資產時無法使用 PreciseDateTime，因為 "." 在名稱中
### <a name="keeping-your-vm-clean"></a>保持您的 VM 乾淨

如果您用來作為 edge 裝置的 Linux VM 不會定期進行管理，則會變成沒有回應。 將快取保持乾淨、消除不必要的套件，並從 VM 中移除未使用的容器是不可或缺的。 若要這麼做，您可以在 edge VM 上使用這組建議的命令。

1. `sudo apt-get clean`

    Apt-取得清除命令會清除/var/cache. 中已取出之套件檔案的本機存放庫 它所清除的目錄為/var/cache/apt/archives/和/var/cache/apt/archives/partial/。 它在/var/cache/apt/archives 中留下的唯一檔案是鎖定檔案和部分子目錄。 Apt-get clean 命令通常用來清除所需的磁碟空間，通常是定期排程維護的一部分。 如需詳細資訊，請參閱 [使用 apt 取得](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)。
1. `sudo apt-get autoclean`

    Apt-get autoclean 選項（例如 apt-取得清除）會清除已抓取套件檔案的本機儲存機制，但它只會移除無法再下載且不實用的檔案。 它有助於保持快取的成長過大。
1. `sudo apt-get autoremove1`

    自動移除選項會移除自動安裝的套件，因為有些其他套件需要它們，但已移除其他套件，所以不再需要它們
1. `sudo docker image ls` –提供 edge 系統上的 Docker 映射清單
1. `sudo docker system prune`

    Docker 採用保守的方法來清除未使用的物件 (通常稱為「垃圾收集」 ) ，例如映射、容器、磁片區和網路：除非您明確要求 Docker，否則這些物件通常不會移除。 這可能會導致 Docker 使用額外的磁碟空間。 針對每一種類型的物件，Docker 會提供剪除命令。 此外，您可以使用 docker 系統剪除一次清除多個類型的物件。 如需詳細資訊，請參閱剪除 [未使用的 Docker 物件](https://docs.docker.com/config/pruning/)。
1. `sudo docker rmi REPOSITORY:TAG`

    當 edge 模組上發生更新時，您的 docker 可以有較舊版本的 edge 模組仍然存在。 在這種情況下，建議使用 docker rmi 命令來移除映射版本戳記所識別的特定映射。

## <a name="next-steps"></a>後續步驟

[快速入門：開始使用 - IoT Edge 上的 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md)
