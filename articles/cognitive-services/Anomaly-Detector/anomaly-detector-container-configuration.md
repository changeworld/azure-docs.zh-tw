---
title: 如何設定異常偵測器 API 的容器
titleSuffix: Azure Cognitive Services
description: 異常偵測器 API 容器執行時間環境是使用 `docker run` 命令引數進行設定。 此容器有數個必要的設定，和一些選擇性的設定。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: aahi
ms.openlocfilehash: c0bf08ae0b2d26b2f4992181d2e300e9dbeed818
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903537"
---
# <a name="configure-anomaly-detector-containers"></a>設定異常偵測器容器

**異常**偵測器容器執行時間環境是使用 `docker run` 命令引數進行設定。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

## <a name="configuration-settings"></a>組態設定

此容器具有下列組態設定：

|必要|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|用來追蹤帳單資訊。|
|否|[ApplicationInsights](#applicationinsights-setting)|可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援新增至容器。|
|是|[Billing](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄 (和選擇性的計量資料) 寫入至 Fluentd 伺服器。|
|否|[Http Proxy](#http-proxy-credentials-settings)|設定 HTTP Proxy 以進行輸出要求。|
|否|[Logging](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[Mounts](#mount-settings)|從主機電腦將資料讀取和寫入至容器，以及從容器將資料讀取和寫回主機電腦。|

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、 [`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 設定會一起使用，您必須為所有三個值提供有效的值，否則您的容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](anomaly-detector-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 的值，且值必須是有效的索引鍵，以用於為設定設定所指定的 _異常_ 偵測器資源 [`Billing`](#billing-configuration-setting) 。

此設定可在下列位置找到：

* Azure 入口網站： **異常** 偵測器的資源管理，位於索引 **鍵**

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

此 `Billing` 設定會指定 Azure 上用來計量容器帳單資訊的 _異常_ 偵測器資源端點 URI。 您必須為此設定設定指定值，且該值必須是 Azure 上 _異常_ 偵測器資源的有效端點 URI。

此設定可在下列位置找到：

* Azure 入口網站： **異常** 偵測器的總覽，加上標籤 `Endpoint`

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費端點 URI。 如需取得帳單 URI 的詳細資訊，請參閱 [收集必要參數](anomaly-detector-container-howto.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

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

異常偵測器容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](anomaly-detector-container-howto.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選用| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|不允許| `Input` | 字串 | 異常偵測器容器不會使用這個。|
|選用| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令 

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](anomaly-detector-container-howto.md#stop-the-container)。

* **行接續字元**：下列各節中的 Docker 命令會使用反斜線做為 `\` bash shell 的行接續字元。 請根據您主機作業系統的需求加以替換或移除。 例如，適用於 Windows 的行接續字元是插入號 `^`。 以插入號取代反斜線。 
* **引數順序**：除非您非常熟悉 Docker 容器，否則請勿變更引數的順序。

以您自己的值取代括弧中的值 `{}` ：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | `Anomaly Detector`Azure 金鑰頁面上資源的端點金鑰 `Anomaly Detector` 。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 帳單端點值可在 Azure `Anomaly Detector` 總覽頁面上取得。| 請參閱收集明確範例 [所需的參數](anomaly-detector-container-howto.md#gathering-required-parameters) 。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](anomaly-detector-container-howto.md#billing)。
> ApiKey 值是 [Azure 異常偵測器資源金鑰] 頁面中的索引 **鍵** 。 

## <a name="anomaly-detector-container-docker-examples"></a>異常偵測器容器 Docker 範例

下列 Docker 範例適用于異常偵測器容器。 

### <a name="basic-example"></a>基本範例 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>使用命令列引數的記錄範例

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>後續步驟

* [將異常偵測器容器部署至 Azure 容器實例](how-to/deploy-anomaly-detection-on-container-instances.md)
* [深入瞭解異常偵測器 API 服務](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
