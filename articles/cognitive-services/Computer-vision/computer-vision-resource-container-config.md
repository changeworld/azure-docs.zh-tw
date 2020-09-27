---
title: 設定容器 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 本文說明如何在電腦視覺中設定辨識文字容器的必要和選擇性設定。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 28116a373b66aa5bfa6d3ebbf027c2db6d24ba5d
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397125"
---
# <a name="configure-computer-vision-docker-containers"></a>設定電腦視覺 Docker 容器

您可以使用命令引數來設定電腦視覺容器的執行時間環境 `docker run` 。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、 [`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 設定會一起使用，您必須為所有三個值提供有效的值，否則您的容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](computer-vision-how-to-install-containers.md)。

容器也有下列容器特定的設定設定：

|必要|設定|目的|
|--|--|--|
|否|ReadEngineConfig:ResultExpirationPeriod| 僅限 v2.0 容器。 結果到期期間（以小時為單位）。 預設值為 48 小時。 設定會指定系統應該清除辨識結果的時間。 例如，如果為 `resultExpirationPeriod=1` ，則系統會在處理常式之後清除辨識結果1小時。 如果為 `resultExpirationPeriod=0` ，則系統會在取出結果之後清除辨識結果。|
|否|Cache： Redis| 僅限 v2.0 容器。 啟用 Redis 儲存體以儲存結果。 如果有多個讀取容器放在負載平衡器後方，就 *需要* 快取。|
|否|佇列： RabbitMQ|僅限 v2.0 容器。 啟用 RabbitMQ 來分派工作。 當有多個讀取容器放在負載平衡器後方時，此設定會很有用。|
|否|佇列： Azure： QueueVisibilityTimeoutInMilliseconds | 僅限 v3. x 容器。 當另一個背景工作正在處理訊息時，該訊息會顯示為不可見的時間。 |
|否|儲存體：:D ocumentStore：： MongoDB|僅限 v2.0 容器。 啟用 MongoDB 以永久儲存結果。 |
|否|儲存體： Objectstore 會： AzureBlob： ConnectionString| 僅限 v3. x 容器。 Azure blob 儲存體連接字串。 |

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

此 `ApiKey` 設定會指定 `Cognitive Services` 用來追蹤容器帳單資訊的 Azure 資源金鑰。 您必須指定 ApiKey 的值，且值必須是針對設定設定所指定之 _認知服務_ 資源的有效金鑰 [`Billing`](#billing-configuration-setting) 。

此設定可在下列位置找到：

* Azure 入口網站：**認知服務**資源管理，位於**金鑰**底下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

此 `Billing` 設定會指定 Azure 上用來計量容器帳單資訊之 _認知服務_ 資源的端點 URI。 您必須為此設定設定指定值，且該值必須是 Azure 上 _認知服務_ 資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 入口網站： **認知服務** 總覽，標示為 `Endpoint`

請記得將 `vision/v1.0` 路由新增至端點 URI，如下表所示。 

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費端點 URI<br><br>範例：<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy 認證設定

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。

電腦視覺容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](computer-vision-how-to-install-containers.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選擇性| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|不允許| `Input` | String | 電腦視覺容器不會使用此項目。|
|選用| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](computer-vision-how-to-install-containers.md#stop-the-container)。

* **行接續字元**：下列各節中的 Docker 命令會使用反斜線 `\` 作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **引數順序**：除非您非常熟悉 Docker 容器，否則請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | `Computer Vision`Azure 金鑰頁面上資源的端點金鑰 `Computer Vision` 。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 帳單端點值可在 Azure `Computer Vision` 總覽頁面上取得。| 請參閱收集明確範例 [所需的參數](computer-vision-how-to-install-containers.md#gathering-required-parameters) 。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](computer-vision-how-to-install-containers.md#billing)。
> ApiKey 值是 [Azure 資源金鑰] 頁面中的索引 **鍵** `Cognitive Services` 。

## <a name="container-docker-examples"></a>容器 Docker 範例

下列 Docker 範例適用于讀取容器。


# <a name="version-31-preview"></a>[版本 3.1-預覽](#tab/version-3-1)

### <a name="basic-example"></a>基本範例

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>記錄範例 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

### <a name="basic-example"></a>基本範例

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example"></a>記錄範例 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[版本 2.0-預覽](#tab/version-2)

### <a name="basic-example"></a>基本範例

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>記錄範例 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>後續步驟

* 請參閱 [如何安裝及執行容器](computer-vision-how-to-install-containers.md)。
