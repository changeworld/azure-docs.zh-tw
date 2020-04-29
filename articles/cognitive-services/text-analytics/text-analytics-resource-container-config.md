---
title: 設定容器-文字分析
titleSuffix: Azure Cognitive Services
description: 文字分析為每個容器提供了常見的設定架構，因此您可以輕易地設定及管理您容器的儲存體、記錄和遙測，以及安全性設定。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f062fb2f3a653bc1b2845b92e373fdb67ba583d8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878650"
---
# <a name="configure-text-analytics-docker-containers"></a>設定文字分析 Docker 容器

文字分析為每個容器提供了常見的設定架構，因此您可以輕易地設定及管理您容器的儲存體、記錄和遙測，以及安全性設定。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、 [`Billing`](#billing-configuration-setting)和[`Eula`](#eula-setting)設定會一起使用，而且您必須為其中三個提供有效的值;否則，您的容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](how-tos/text-analytics-how-to-install-containers.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 的值，該值必須是有效金鑰，且適用於為 [`Billing`](#billing-configuration-setting) 組態設定而指定的_文字分析_資源。

此設定可在下列位置找到：

* Azure 入口網站：**文字分析**的資源管理，位於 [**金鑰**] 下方

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing` 設定會指定 Azure 上_文字分析_資源的端點 URI，用來對容器計算帳單資訊。 您必須指定此設定的值，且該值必須是 Azure 上的 __文字分析_資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 入口網站：**文字分析**總覽，加上標籤`Endpoint`

|必要| Name | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費端點 URI。 如需取得帳單 URI 的詳細資訊，請參閱[收集必要的參數](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

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

文字分析容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，由於 docker 服務帳戶所使用的許可權與主機裝載位置許可權之間的衝突，[主機電腦](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)的裝載位置可能無法存取。 

|選擇性| Name | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|不允許| `Input` | 字串 | 文字分析容器不會使用此項目。|
|選擇性| `Output` | 字串 | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令 

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)。

* **行接續字元**：下列各節中的 docker 命令會使用反斜線`\`作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **引數順序**：除非您非常熟悉 docker 容器，否則請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | [Azure `Text Analytics`金鑰] 頁面`Text Analytics`上可用資源的端點金鑰。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 計費端點值可在 Azure `Text Analytics`總覽頁面上取得。| 如需明確的範例，請參閱[收集必要的參數](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)。 |

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](how-tos/text-analytics-how-to-install-containers.md#billing)。
> ApiKey 值是 [Azure **Key** `Text Analytics`資源金鑰] 頁面中的金鑰。 

#### <a name="key-phrase-extraction"></a>[關鍵片語擷取](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[語言偵測](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[情感分析](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>後續步驟

* 檢閱[如何安裝及執行容器](how-tos/text-analytics-how-to-install-containers.md)
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
