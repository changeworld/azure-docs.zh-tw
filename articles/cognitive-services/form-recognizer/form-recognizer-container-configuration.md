---
title: 如何為表單辨識器設定容器
titleSuffix: Azure Cognitive Services
description: 了解如何設定表單辨識器容器來剖析表單和資料表資料。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bc48c0ba23e73adec312adfeeb1fcd57dba6ceec
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879151"
---
# <a name="configure-form-recognizer-containers"></a>設定表單辨識器容器

通過使用 Azure 窗體識別器容器,可以構建經過優化的應用程式體系結構,以利用強大的雲端功能和邊緣區域性。

通過使用`docker run`命令參數配置窗體識別器容器運行時環境。 此容器具有多個必需的設置和幾個可選設置。 有關一些示例,請參閱[「示例 Docker 運行命令」](#example-docker-run-commands)部分。 容器專屬設定包括計費設定。

> [!IMPORTANT]
> 表單辨識器容器目前使用表單辨識器 API 的版本 1.0。 您可以使用託管服務來存取最新版本的 API。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> 一[`ApiKey`](#apikey-configuration-setting)起使用[`Billing`](#billing-configuration-setting),[`Eula`](#eula-setting)並設定一起使用。 您必須為所有三個設置提供有效值;但是,您必須為所有三個設置提供有效的值。否則,您的容器將無法啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](form-recognizer-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

該`ApiKey`設定指定用於追蹤容器的計費資訊的 Azure 資源密鑰。 ApiKey 的值必須是「計費設定設定」部分`Billing`中 指定的_窗體識別器_資源的有效金鑰。

您可以在 Azure 門戶(在「**鍵**」下的**窗體識別器資源管理**中找到此設定)。

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

該`Billing`設置指定 Azure 上_表單識別器_資源的終結點 URI,用於計量容器的計費資訊。 此設定設定的值必須是 Azure 上_窗體識別器_資源的有效終結點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

您可以在 Azure 門戶(在 **"終結點**"下**的表單識別器概述**中找到此設定)。

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費終結點URI。 有關取得計費 URI 的詳細資訊,請參閱[收集所需的參數](form-recognizer-container-howto.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 可以通過`--mount`在[`docker run`指令](https://docs.docker.com/engine/reference/commandline/run/)中指定選項來指定輸入載入或輸出載入。

窗體識別器容器需要輸入裝載和輸出裝載。 輸入裝載可以唯讀,並且存取用於訓練和評分的數據是必需的。 輸出裝載必須可寫,並且使用它來存儲模型和臨時數據。

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外,由於 Docker 服務帳戶許可權與主機裝載位置許可權之間的衝突,[主機的](form-recognizer-container-howto.md#the-host-computer)裝載位置可能無法訪問。

|選用| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|必要| `Input` | String | 輸入裝載的目標。 預設值是 `/input`。    <br><br>範例：<br>`--mount type=bind,src=c:\input,target=/input`|
|必要| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。  <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。 當它執行時,容器將繼續執行,直到您[停止它](form-recognizer-container-howto.md#stop-the-container)。

* **行延續字元**:以下部分中的 Docker 命令使用\\反斜杠 ( ) 作為行延續字元。 根據主機作業系統的要求,替換或刪除此字元。
* **參數順序**:除非您熟悉 Docker 容器,否則不要更改參數的順序。

將下表中的 [_argument_name]_ 取代為您自己的值:

| 預留位置 | 值 |
|-------------|-------|
| **[FORM_RECOGNIZER_API_KEY]** | 用於啟動容器的鍵。 它在 Azure 門戶窗體識別器密鑰頁上可用。 |
| **[FORM_RECOGNIZER_ENDPOINT_URI]** | 計費終結點 URI 值在 Azure 門戶窗體識別器概述頁上可用。|
| **{COMPUTER_VISION_API_KEY}** | 該鍵在 Azure 門戶電腦視覺 API 密鑰頁上可用。|
| **{COMPUTER_VISION_ENDPOINT_URI}** | 計費端點。 如果使用基於雲的計算機視覺資源,URI 值可在 Azure 門戶計算機視覺 API 概述頁上使用。 如果您使用的是*認知服務-識別文本*容器,請使用傳遞到`docker run`命令中的容器的計費終結點 URL。 |

有關如何取得這些值的詳細資訊[,請參閱收集所需參數](form-recognizer-container-howto.md#gathering-required-parameters)。

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 要執行容器,請指定`Eula``Billing`、`ApiKey`和 選項;否則,容器將無法啟動。 如需詳細資訊，請參閱[帳單](#billing-configuration-setting)。

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

* 檢視[安裝及執行容器](form-recognizer-container-howto.md)。
