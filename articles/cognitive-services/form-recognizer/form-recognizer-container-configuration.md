---
title: 如何設定表單辨識器的容器
titleSuffix: Azure Cognitive Services
description: 了解如何設定表單辨識器容器來剖析表單和資料表資料。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537998"
---
# <a name="configure-form-recognizer-containers"></a>設定表單辨識器容器

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

藉由使用 Azure 表單辨識器容器，您可以建立經過優化的應用程式架構，以充分利用健全的雲端功能和邊緣位置。

您可以使用命令引數來設定表單辨識器容器執行時間環境 `docker run` 。 此容器有幾個必要的設定和一些選擇性的設定。 如需一些範例，請參閱「 [docker run 命令範例](#example-docker-run-commands) 」一節。 容器專屬設定包括計費設定。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、 [`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 設定會一起使用。 您必須為所有三個設定提供有效的值;否則，您的容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](form-recognizer-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

此 `ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure 資源金鑰。 ApiKey 的值必須是針對在 [帳單設定] 區段中為指定的 _表單辨識器_ 資源有效的金鑰 `Billing` 。

您可以在 [ **表單辨識器資源管理**] 的 [索引 **鍵**] 下的 [Azure 入口網站] 中找到這項設定。

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

此 `Billing` 設定會指定 Azure 上用來計量容器帳單資訊的 _表單辨識器_ 資源的端點 URI。 針對 Azure 上的 _表單辨識器_ 資源，此設定設定的值必須是有效的端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

您可以在 [ **表單辨識器] 總覽**的 [ **端點**] 下的 [Azure 入口網站中找到這項設定。

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費端點 URI。 如需取得帳單 URI 的詳細資訊，請參閱 [收集必要參數](form-recognizer-container-howto.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy 認證設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以 `--mount` 在[ `docker run` 命令](https://docs.docker.com/engine/reference/commandline/run/)中指定選項，以指定輸入裝載或輸出裝載。

表單辨識器容器需要輸入掛接和輸出裝載。 輸入裝載可以是唯讀的，而且必須存取用於定型和計分的資料。 輸出裝載必須是可寫入的，且您可以使用它來儲存模型和暫存資料。

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外， [主機電腦](form-recognizer-container-howto.md#the-host-computer) 的裝載位置可能無法存取，因為 Docker 服務帳戶許可權與主機裝載位置的許可權之間發生衝突。

|選用| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|必要| `Input` | String | 輸入裝載的目標。 預設值為 `/input`。    <br><br>範例：<br>`--mount type=bind,src=c:\input,target=/input`|
|必要| `Output` | String | 輸出裝載的目標。 預設值為 `/output`。  <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。 當容器正在執行時，容器會繼續執行，直到您將 [它停止](form-recognizer-container-howto.md#stop-the-container)為止。

* **行接續字元**：下列各節中的 Docker 命令會使用反斜線 (\\) 作為行接續字元。 根據您的主機作業系統需求，取代或移除此字元。
* **引數順序**：除非您熟悉 Docker 容器，否則請勿變更引數的順序。

以您自己的值取代下表中的 {_argument_name_}：

| 預留位置 | 值 |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | 用來啟動容器的金鑰。 可以在 Azure 入口網站表單辨識器金鑰] 頁面上取得。 |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | 帳單端點 URI 值可在 Azure 入口網站表單辨識器總覽頁面上取得。|
| **{COMPUTER_VISION_API_KEY}** | 您可以在 Azure 入口網站電腦視覺 API 金鑰] 頁面上取得金鑰。|
| **{COMPUTER_VISION_ENDPOINT_URI}** | 計費端點。 如果您使用以雲端為基礎的電腦視覺資源，您可以在 Azure 入口網站電腦視覺 API 總覽頁面上取得 URI 值。 如果您使用 *認知服務辨識文字* 容器，請使用在命令中傳遞給容器的計費端點 URL `docker run` 。 |

如需如何取得這些值的詳細資訊，請參閱 [收集必要參數](form-recognizer-container-howto.md#gathering-required-parameters) 。

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 若要執行容器，請指定 `Eula` 、 `Billing` 和 `ApiKey` 選項，否則容器將不會啟動。 如需詳細資訊，請參閱[帳單](#billing-configuration-setting)。

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

## <a name="next-steps"></a>接下來的步驟

* 請參閱 [安裝和執行容器](form-recognizer-container-howto.md)。
