---
title: 設定容器 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 本文介紹如何在電腦視覺中為"識別文本"容器配置所需的設置和可選設置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: ddbee3695c2a7ef7cb63c48cccacbd2d53a8c1a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718983"
---
# <a name="configure-computer-vision-docker-containers"></a>配置電腦視覺 Docker 容器

通過使用`docker run`命令參數配置電腦視覺容器的運行時環境。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。 

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`Billing`](#billing-configuration-setting) [`Eula`](#eula-setting)和[`ApiKey`](#apikey-configuration-setting)設置一起使用，您必須為所有三個設置提供有效值;因此，您必須為所有三個設置提供有效值。否則您的容器將無法啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](computer-vision-how-to-install-containers.md)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

該`ApiKey`設置指定用於跟蹤`Cognitive Services`容器的計費資訊的 Azure 資源金鑰。 您必須為 ApiKey 指定值，並且該值必須是為[`Billing`](#billing-configuration-setting)配置設置指定的_認知服務_資源的有效金鑰。

此設定可在下列位置找到：

* Azure 門戶：在**金鑰**下**進行認知服務**資源管理

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

該`Billing`設置指定 Azure 上的_認知服務_資源的終結點 URI，用於計量容器的計費資訊。 您必須為此配置設置指定值，並且該值必須是 Azure 上的_認知服務_資源的有效終結點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

* Azure 門戶：**認知服務**概述，標記為`Endpoint`

請記住將`vision/v1.0`路由添加到終結點 URI，如下表所示。 

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 計費端點 URI<br><br>範例：<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理憑據設置

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。

電腦視覺容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外，[主機電腦](computer-vision-how-to-install-containers.md#the-host-computer)的裝載位置可能會因為 Docker 服務帳戶所使用的權限與主機裝載位置的權限互相衝突，而無法存取。 

|選用| 名稱 | 資料類型 | 描述 |
|-------|------|-----------|-------------|
|不允許| `Input` | String | 電腦視覺容器不會使用此項目。|
|選用| `Output` | String | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。  開始執行後，容器就會持續執行，直到您加以[停止](computer-vision-how-to-install-containers.md#stop-the-container)。

* **行延續字元**：以下部分中的 Docker 命令使用反斜線 ，`\`作為行延續字元。 請根據您主機作業系統的需求加以替換或移除。 
* **參數順序**：除非非常熟悉 Docker 容器，否則不要更改參數的順序。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
|-------------|-------|---|
| **[API_KEY]** | Azure`Computer Vision``Computer Vision`鍵頁上資源的終結點鍵。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **[ENDPOINT_URI]** | 計費終結點值在 Azure`Computer Vision`概述頁上可用。| 有關顯式示例，請參閱[收集所需的參數](computer-vision-how-to-install-containers.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](computer-vision-how-to-install-containers.md#billing)。
> ApiKey 值是 Azure`Cognitive Services`資源鍵頁中的 **"鍵**"。

## <a name="container-docker-examples"></a>容器 Docker 示例

以下 Docker 示例適用于讀取容器。

### <a name="basic-example"></a>基本範例

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>日誌記錄示例 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>後續步驟

* 查看[如何安裝和運行容器](computer-vision-how-to-install-containers.md)。
