---
title: Docker 容器設定 - LUIS
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
ms.openlocfilehash: 48a9856c58a815eabcc0b105efcd548e66ddd552
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874206"
---
# <a name="configure-language-understanding-docker-containers"></a>設定 Language Understanding 的 Docker 容器 

使用`docker run`指令參數設定**語言理解**(LUIS) 容器執行時環境。 LUIS 有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括輸入[裝載設定](#mount-settings)和計費設定。 

## <a name="configuration-settings"></a>組態設定

此容器具有下列組態設定：

|必要|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-setting)|用來追蹤帳單資訊。|
|否|[應用洞察](#applicationinsights-setting)|可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援新增至容器。|
|是|[計費](#billing-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄 (和選擇性的計量資料) 寫入至 Fluentd 伺服器。|
|否|[Http Proxy](#http-proxy-credentials-settings)|設定 HTTP Proxy 以進行輸出要求。|
|否|[記錄](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|是|[Mounts](#mount-settings)|從主機電腦將資料讀取和寫入至容器，以及從容器將資料讀取和寫回主機電腦。|

> [!IMPORTANT]
> [`Billing`](#billing-setting)與[`ApiKey`](#apikey-setting)設定一起使用,您必須為所有三個設置提供有效值;因此,您必須為所有三個設置[`Eula`](#eula-setting)提供有效值。否則您的容器將無法啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](luis-container-howto.md#billing)。

## <a name="apikey-setting"></a>ApiKey 設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須為 ApiKey 指定值,並且該值必須[`Billing`](#billing-setting)是為 設定設定指定的_認知服務_資源的有效金鑰。

此設定可在下列位置找到：

* Azure 門戶:在**金鑰**下**進行認知服務**資源管理
* LUIS 門戶:**鍵和終結點設置**頁面。 

請勿使用入門金鑰或撰寫金鑰。 

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>帳單支援

該`Billing`設置指定 Azure 上的_認知服務_資源的終結點 URI,用於計量容器的計費資訊。 您必須為此設定設定指定值,並且該值必須是 Azure 上的_認知服務_資源的有效終結點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 門戶:**認知服務**概述 標記為`Endpoint`
* LUIS 門戶:作為終結點 URI 的一部分的**鍵和終結點設置**頁。

| 必要 | 名稱 | 資料類型 | 描述 |
|----------|------|-----------|-------------|
| 是      | `Billing` | 字串 | 計費終結點URI。 有關取得計費 URI 的詳細資訊,請參閱[收集所需的參數](luis-container-howto.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。 

LUIS 容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外,由於 Docker 服務帳戶使用的許可權與主機裝載位置許可權之間的衝突,[主機](luis-container-howto.md#the-host-computer)的裝載位置可能無法訪問。 

下表說明支援的設定。

|必要| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|是| `Input` | String | 輸入裝載的目標。 預設值是 `/input`。 這是 LUIS 套件檔案的位置。 <br><br>範例：<br>`--mount type=bind,src=c:\input,target=/input`|
|否| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 其中包括 LUIS 查詢記錄和容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](luis-container-howto.md#stop-the-container)。

* 這些示例使用`C:`驅動器上的目錄來避免 Windows 上的任何許可權衝突。 如果您需要使用特定目錄作為輸入目錄，您可能需要授與 Docker 服務權限。 
* 若非十分熟悉 Docker 容器，請勿變更引數的順序。
* 如果您使用的是其他作業系統,請使用正確的主控台/終端、裝載的資料夾語法和系統的行延續字元。 這些示例假定具有行延續字元`^`的 Windows 主控台。 由於容器是 Linux 作業系統,因此目標裝載使用 Linux 風格的資料夾語法。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **[API_KEY]** | Azure`LUIS``LUIS`鍵頁上資源的終結點鍵。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **[ENDPOINT_URI]** | 計費終結點值在 Azure`LUIS`概述頁上可用。| 有關顯式範例,請參閱[收集所需的參數](luis-container-howto.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。 如需詳細資訊，請參閱[帳單](luis-container-howto.md#billing)。
> ApiKey 值是 LUIS 門戶中「鍵」和「終結點」頁上的 **「鍵**」,在`Cognitive Services`Azure 資源鍵頁上也可用。 

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

### <a name="logging-example"></a>紀錄記錄範例 

以下指令將紀錄紀錄等級`Logging:Console:LogLevel`設定, 以將紀錄紀錄等級設定為[`Information`](https://msdn.microsoft.com)。 

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
