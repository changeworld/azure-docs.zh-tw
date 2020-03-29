---
title: 如何為表單識別器配置容器
titleSuffix: Azure Cognitive Services
description: 了解如何設定表單辨識器容器來剖析表單和資料表資料。
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 5439ec0c0aab5b8c127b651147e4b25d27c58390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379618"
---
# <a name="configure-form-recognizer-containers"></a>設定表單辨識器容器

通過使用 Azure 表單識別器容器，可以構建經過優化的應用程式體系結構，以利用強大的雲功能和邊緣區域性。

通過使用`docker run`命令參數配置表單識別器容器運行時環境。 此容器具有多個必需的設置和幾個可選設置。 有關一些示例，請參閱["示例 Docker 運行命令"](#example-docker-run-commands)部分。 容器專屬設定包括計費設定。

> [!IMPORTANT]
> 表單識別器容器當前使用表單識別器 API 的版本 1.0。 您可以使用託管服務來訪問最新版本的 API。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> 一[`ApiKey`](#apikey-configuration-setting)起使用[`Billing`](#billing-configuration-setting)，[`Eula`](#eula-setting)和 設置一起使用。 您必須為所有三個設置提供有效值;但是，您必須為所有三個設置提供有效的值。否則，您的容器將無法啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](form-recognizer-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

該`ApiKey`設置指定用於跟蹤容器的計費資訊的 Azure 資源金鑰。 ApiKey 的值必須是"計費配置設置"部分中`Billing`指定的_表單識別器_資源的有效金鑰。

您可以在 Azure 門戶（在"**鍵**"下的**表單識別器資源管理**中找到此設置）。

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

該`Billing`設置指定 Azure 上_表單識別器_資源的終結點 URI，用於計量容器的計費資訊。 此配置設置的值必須是 Azure 上_表單識別器_資源的有效終結點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

您可以在 Azure 門戶（在 **"終結點**"下**的表單識別器概述**中找到此設置）。

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費終結點 URI。 有關獲取計費 URI 的詳細資訊，請參閱[收集所需的參數](form-recognizer-container-howto.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理憑據設置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 可以通過在`--mount`[`docker run`命令](https://docs.docker.com/engine/reference/commandline/run/)中指定選項來指定輸入裝載或輸出裝載。

表單識別器容器需要輸入裝載和輸出裝載。 輸入裝載可以唯讀，並且訪問用於訓練和評分的資料是必需的。 輸出裝載必須可寫，並且使用它來存儲模型和臨時資料。

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，由於 Docker 服務帳戶許可權與主機裝載位置許可權之間的衝突，[主機的](form-recognizer-container-howto.md#the-host-computer)裝載位置可能無法訪問。

|選用| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|必要| `Input` | String | 輸入裝載的目標。 預設值是 `/input`。    <br><br>範例：<br>`--mount type=bind,src=c:\input,target=/input`|
|必要| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。  <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。 當它運行時，容器將繼續運行，直到您[停止它](form-recognizer-container-howto.md#stop-the-container)。

* **行延續字元**：以下部分中的 Docker 命令使用反斜線\\（ ） 作為行延續字元。 根據主機作業系統的要求，替換或刪除此字元。
* **參數順序**：除非您熟悉 Docker 容器，否則不要更改參數的順序。

將下表中的 [_argument_name]_ 替換為您自己的值：

| 預留位置 | 值 |
|-------------|-------|
| **[FORM_RECOGNIZER_API_KEY]** | 用於啟動容器的鍵。 它在 Azure 門戶表單識別器金鑰頁上可用。 |
| **[FORM_RECOGNIZER_ENDPOINT_URI]** | 計費終結點 URI 值在 Azure 門戶表單識別器概述頁上可用。|
| **{COMPUTER_VISION_API_KEY}** | 該鍵在 Azure 門戶電腦視覺 API 金鑰頁上可用。|
| **{COMPUTER_VISION_ENDPOINT_URI}** | 計費端點。 如果使用基於雲的電腦視覺資源，URI 值可在 Azure 門戶電腦視覺 API 概述頁上使用。 如果您使用的是*認知服務-識別文本*容器，請使用傳遞到`docker run`命令中的容器的計費終結點 URL。 |

有關如何獲取這些值的詳細資訊[，請參閱收集所需參數](form-recognizer-container-howto.md#gathering-required-parameters)。

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 要運行容器，請指定`Eula`、`Billing`和`ApiKey`選項;否則，容器將無法啟動。 如需詳細資訊，請參閱[帳單](#billing-configuration-setting)。

## <a name="form-recognizer-container-docker-examples"></a>表單辨識器容器 Docker 範例

以下是表單辨識器容器的 Docker 範例。

### <a name="basic-example-for-form-recognizer"></a>表單辨識器的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>表單辨識器的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>後續步驟

* 查看[安裝和運行容器](form-recognizer-container-howto.md)。
