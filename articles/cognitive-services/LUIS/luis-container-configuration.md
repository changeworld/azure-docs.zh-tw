---
title: Docker 容器設定-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 容器執行階段環境可使用 `docker run` 命令引數來設定。 LUIS 有數個必要的設定，和一些選擇性的設定。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4f2b07edc6c290fa030621a4dc400ab50890bba
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001138"
---
# <a name="configure-language-understanding-docker-containers"></a>設定 Language Understanding 的 Docker 容器 

**Language Understanding** (LUIS) 容器執行時間環境是使用 `docker run` 命令引數進行設定。 LUIS 有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括輸入[裝載設定](#mount-settings)和計費設定。 

## <a name="configuration-settings"></a>組態設定

此容器具有下列組態設定：

|必要|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-setting)|用來追蹤帳單資訊。|
|否|[ApplicationInsights](#applicationinsights-setting)|可讓您將 [Azure Application Insights](/azure/application-insights) 遙測支援新增至容器。|
|是|[Billing](#billing-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄 (和選擇性的計量資料) 寫入至 Fluentd 伺服器。|
|否|[Http Proxy](#http-proxy-credentials-settings)|設定 HTTP Proxy 以進行輸出要求。|
|否|[Logging](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|是|[Mounts](#mount-settings)|從主機電腦將資料讀取和寫入至容器，以及從容器將資料讀取和寫回主機電腦。|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)、 [`Billing`](#billing-setting) 和 [`Eula`](#eula-setting) 設定會一起使用，您必須為所有三個值提供有效的值，否則您的容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](luis-container-howto.md#billing)。

## <a name="apikey-setting"></a>ApiKey 設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 的值，且值必須是針對設定設定所指定之 _認知服務_ 資源的有效金鑰 [`Billing`](#billing-setting) 。

此設定可在下列位置找到：

* Azure 入口網站：**認知服務** 資源管理，位於 **金鑰** 底下
* LUIS 入口網站： [ **金鑰和端點設定** ] 頁面。 

請勿使用入門金鑰或撰寫金鑰。 

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>帳單支援

此 `Billing` 設定會指定 Azure 上用來計量容器帳單資訊之 _認知服務_ 資源的端點 URI。 您必須為此設定設定指定值，且該值必須是 Azure 上 _認知服務_ 資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 入口網站： **認知服務** 總覽，標示為 `Endpoint`
* LUIS 入口網站： [ **金鑰和端點設定** ] 頁面，作為端點 URI 的一部分。

| 必要 | 名稱 | 資料類型 | 描述 |
|----------|------|-----------|-------------|
| 是      | `Billing` | 字串 | 計費端點 URI。 如需取得帳單 URI 的詳細資訊，請參閱 [收集必要參數](luis-container-howto.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy 認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。 

LUIS 容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外， [主機電腦](luis-container-howto.md#the-host-computer)的裝載位置可能無法存取，因為 docker 服務帳戶所使用的許可權與主機裝載位置的許可權之間發生衝突。 

下表說明支援的設定。

|必要| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|是| `Input` | String | 輸入裝載的目標。 預設值是 `/input`。 這是 LUIS 套件檔案的位置。 <br><br>範例：<br>`--mount type=bind,src=c:\input,target=/input`|
|否| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 其中包括 LUIS 查詢記錄和容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](luis-container-howto.md#stop-the-container)。

* 這些範例會使用 `C:` 磁片磁碟機上的目錄，以避免在 Windows 上發生任何許可權衝突。 如果您需要使用特定目錄作為輸入目錄，您可能需要授與 Docker 服務權限。 
* 若非十分熟悉 Docker 容器，請勿變更引數的順序。
* 如果您使用的是不同的作業系統，請使用正確的主控台/終端機、適用于裝載的資料夾語法，以及您系統的行接續字元。 這些範例假設 Windows 主控台含有行接續字元 `^` 。 因為容器是 Linux 作業系統，所以目標裝載會使用 Linux 樣式的資料夾語法。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | `LUIS`Azure 金鑰頁面上資源的端點金鑰 `LUIS` 。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 帳單端點值可在 Azure `LUIS` 總覽頁面上取得。| 請參閱收集明確範例 [所需的參數](luis-container-howto.md#gathering-required-parameters) 。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。 如需詳細資訊，請參閱[帳單](luis-container-howto.md#billing)。
> ApiKey 值是 LUIS 入口網站的 [金鑰和端點] 頁面中的索引 **鍵** ，而且也可在 [Azure `Cognitive Services` 資源金鑰] 頁面上取得。 

### <a name="basic-example"></a>基本範例

下列範例會以最少的引數來執行容器：

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights 範例

下列範例會設定 ApplicationInsights 引數，以在容器執行時將遙測資料傳送至 Application Insights：

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>記錄範例 

下列命令會設定記錄層級， `Logging:Console:LogLevel` 以將記錄層級設定為 [`Information`](https://msdn.microsoft.com) 。 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>後續步驟

* 檢閱[如何安裝及執行容器](luis-container-howto.md)
* 請參閱[疑難排解](troubleshooting.md)來解決與 LUIS 功能相關的問題。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)