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
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3be302019c712c13bd29d7ed3781151a1648e847
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879304"
---
# <a name="configure-computer-vision-docker-containers"></a>設定電腦視覺 Docker 容器

您可以使用`docker run`命令引數來設定電腦視覺容器的執行時間環境。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、 [`Billing`](#billing-configuration-setting)和[`Eula`](#eula-setting)設定會一起使用，而且您必須為其中三個提供有效的值;否則，您的容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](computer-vision-how-to-install-containers.md)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey`設定會指定用來`Cognitive Services`追蹤容器帳單資訊的 Azure 資源金鑰。 您必須指定 ApiKey 的值，且值必須是為設定所指定[`Billing`](#billing-configuration-setting)之_認知服務_資源的有效金鑰。

此設定可在下列位置找到：

* Azure 入口網站：**認知服務**的資源管理，位於 [**金鑰**] 下方

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

`Billing`設定會指定 Azure 上用來計量容器帳單資訊之_認知服務_資源的端點 URI。 您必須指定此設定的值，且該值必須是 Azure 上_認知服務_資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 入口網站：**認知服務**總覽，加上標籤`Endpoint`

請記得將`vision/v1.0`路由新增至端點 URI，如下表所示。 

|必要| Name | 資料類型 | 描述 |
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

|選擇性| Name | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|不允許| `Input` | 字串 | 電腦視覺容器不會使用此項目。|
|選擇性| `Output` | 字串 | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](computer-vision-how-to-install-containers.md#stop-the-container)。

* **行接續字元**：下列各節中的 Docker 命令會使用反斜線`\`作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **引數順序**：除非您非常熟悉 Docker 容器，否則請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **{API_KEY}** | [Azure `Computer Vision`金鑰] 頁面`Computer Vision`上資源的端點金鑰。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 計費端點值可在 Azure `Computer Vision`總覽頁面上取得。| 如需明確的範例，請參閱[收集必要的參數](computer-vision-how-to-install-containers.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](computer-vision-how-to-install-containers.md#billing)。
> ApiKey 值是 [Azure **Key** `Cognitive Services`資源金鑰] 頁面中的金鑰。

## <a name="container-docker-examples"></a>容器 Docker 範例

下列是讀取容器的 Docker 範例。

### <a name="basic-example"></a>基本範例

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>記錄範例 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>後續步驟

* 請參閱[如何安裝及執行容器](computer-vision-how-to-install-containers.md)。
