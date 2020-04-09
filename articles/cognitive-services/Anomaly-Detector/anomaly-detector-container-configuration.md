---
title: 如何為例外偵測器 API 設定容器
titleSuffix: Azure Cognitive Services
description: 使用`docker run`指令參數設定異常檢測器 API 容器執行時環境。 此容器有數個必要的設定，和一些選擇性的設定。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 569499002c5e047d7030575342790e9a074b9404
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875175"
---
# <a name="configure-anomaly-detector-containers"></a>設定異常偵測器容器

使用`docker run`指令參數設定**異常檢測器**容器執行時環境。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

## <a name="configuration-settings"></a>組態設定

此容器具有下列組態設定：

|必要|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|用來追蹤帳單資訊。|
|否|[應用洞察](#applicationinsights-setting)|可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援新增至容器。|
|是|[計費](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄 (和選擇性的計量資料) 寫入至 Fluentd 伺服器。|
|否|[Http Proxy](#http-proxy-credentials-settings)|設定 HTTP Proxy 以進行輸出要求。|
|否|[記錄](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[Mounts](#mount-settings)|從主機電腦將資料讀取和寫入至容器，以及從容器將資料讀取和寫回主機電腦。|

> [!IMPORTANT]
> [`Billing`](#billing-configuration-setting)與[`ApiKey`](#apikey-configuration-setting)設定一起使用,您必須為所有三個設置提供有效值;因此,您必須為所有三個設置[`Eula`](#eula-setting)提供有效值。否則您的容器將無法啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](anomaly-detector-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須為 ApiKey 指定值,並且該值必須[`Billing`](#billing-configuration-setting)是為 設定設定指定的_異常檢測器_資源的有效金鑰。

此設定可在下列位置找到：

* Azure 門戶:**異常偵測器**的資源管理,在**金鑰**下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

該`Billing`設置指定 Azure 上的_異常檢測器_資源的終結點 URI,用於計量容器的計費資訊。 您必須為此設定設定指定值,並且該值必須是 Azure 上_異常檢測器_資源的有效終結點 URI。

此設定可在下列位置找到：

* Azure 門戶:**異常檢測器的**概述 標記為`Endpoint`

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費終結點URI。 有關取得計費 URI 的詳細資訊,請參閱[收集所需的參數](anomaly-detector-container-howto.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP Proxy 認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。

異常檢測器容器不使用輸入或輸出載入來存儲訓練或服務數據。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](anomaly-detector-container-howto.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選用| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|不允許| `Input` | String | 異常檢測器容器不使用此容器。|
|選用| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令 

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](anomaly-detector-container-howto.md#stop-the-container)。

* **行延續字元**:以下部分中的 Docker 命令使用反`\`斜杠 , 作為 bash shell 的行延續字元。 請根據您主機作業系統的需求加以替換或移除。 例如，適用於 Windows 的行接續字元是插入號 `^`。 以插入號取代反斜線。 
* **參數順序**:除非非常熟悉 Docker 容器,否則不要更改參數的順序。

將括號`{}`的值取代為您自己的值:

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **[API_KEY]** | Azure`Anomaly Detector``Anomaly Detector`鍵頁上資源的終結點鍵。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **[ENDPOINT_URI]** | 計費終結點值在 Azure`Anomaly Detector`概述頁上可用。| 有關顯式範例,請參閱[收集所需的參數](anomaly-detector-container-howto.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](anomaly-detector-container-howto.md#billing)。
> ApiKey 值是 Azure 異常檢測器資源鍵頁中的 **「鍵**」。 

## <a name="anomaly-detector-container-docker-examples"></a>例外偵測器容器 Docker 範例

以下 Docker 範例適用於異常檢測器容器。 

### <a name="basic-example"></a>基本範例 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>使用命令列引數的記錄範例

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>後續步驟

* [將例外偵測器容器部署到 Azure 容器實體](how-to/deploy-anomaly-detection-on-container-instances.md)
* [瞭解有關例外偵測器 API 服務的更多資訊](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
