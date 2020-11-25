---
title: 設定語音容器
titleSuffix: Azure Cognitive Services
description: 語音服務會提供每個容器的通用設定架構，讓您可以輕鬆地設定及管理容器的儲存體、記錄和遙測，以及安全性設定。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: e65bb7c7d8fc04baec6b50a53519e689e748fbe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012164"
---
# <a name="configure-speech-service-containers"></a>設定語音服務容器

語音容器可讓客戶建立一個已優化的語音應用程式架構，以充分利用健全的雲端功能和邊緣位置。 我們現在支援的五個語音容器是、 **語音轉換文字**、 **自訂語音轉換文字**、 **文字轉換語音**、類 **神經文字到語音** 轉換和 **自訂文字轉換語音**。

**語音** 容器執行時間環境是使用 `docker run` 命令引數進行設定。 此容器有數個必要的設定，和一些選擇性的設定。 命令有相關[範例](#example-docker-run-commands)可供參考。 容器專屬設定包括計費設定。

## <a name="configuration-settings"></a>組態設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、 [`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 設定會一起使用，您必須為所有三個值提供有效的值，否則您的容器將不會啟動。 如需使用這些組態設定來將容器具現化的詳細資訊，請參閱[帳單](speech-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 組態設定

`ApiKey` 設定會指定用來追蹤容器帳單資訊的 Azure資源金鑰。 您必須指定 ApiKey 的值，且值必須是有效的金鑰，以用於為設定設定所指定的 _語音_ 資源 [`Billing`](#billing-configuration-setting) 。

此設定可在下列位置找到：

- Azure 入口網站：**語音的** 資源管理，在 **金鑰** 底下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 組態設定

此 `Billing` 設定會指定 Azure 上用來計量容器帳單資訊的 _語音_ 資源端點 URI。 您必須為此設定設定指定值，且該值必須是 Azure 上 _語音_ 資源的有效端點 URI。 容器會每隔 10 到 15 分鐘回報使用量。

此設定可在下列位置找到：

- Azure 入口網站： **語音的** 總覽，加上標籤 `Endpoint`

| 必要 | 名稱 | 資料類型 | 描述 |
| -------- | ---- | --------- | ----------- |
| 是 | `Billing` | String | 計費端點 URI。 如需取得帳單 URI 的詳細資訊，請參閱 [收集必要參數](speech-container-howto.md#gathering-required-parameters)。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy 認證設定

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>記錄設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>裝載設定

使用繫結裝載將資料讀取和寫入至容器，及從中讀取和寫入。 您可以在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 選項，以指定輸入裝載或輸出裝載。

標準語音容器不會使用輸入或輸出裝載來儲存訓練或服務資料。 不過，自訂語音容器會依賴磁片區裝載。

主機裝載位置的正確語法會隨著主機作業系統而有所不同。 此外， [主機電腦](speech-container-howto.md#the-host-computer)的裝載位置可能無法存取，因為 docker 服務帳戶所使用的許可權與主機裝載位置的許可權之間發生衝突。

| 選擇性 | 名稱 | 資料類型 | 描述 |
| -------- | ---- | --------- | ----------- |
| 不允許 | `Input` | String | 標準語音容器不會使用此功能。 自訂語音容器會使用 [磁片區裝載](#volume-mount-settings)。                                                                                    |
| 選用 | `Output` | String | 輸出裝載的目標。 預設值是 `/output`。 這是記錄的位置。 這包括容器記錄。 <br><br>範例：<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>磁片區掛接設定

自訂語音容器會使用 [磁片](https://docs.docker.com/storage/volumes/) 區掛接來保存自訂模型。 您可以藉由將 `-v` (或 `--volume`) 選項新增至 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令來指定磁片區掛接。

當您第一次將新模型內嵌為自訂語音容器 docker run 命令的一部分時，就會下載自訂模型。 `ModelId`自訂語音容器的循序執行會使用先前下載的模型。 如果未提供磁片區掛接，則無法保存自訂模型。

磁片區掛接設定包含三個 `:` 以色彩分隔的欄位：

1. 第一個欄位是主機電腦上的磁片區名稱，例如 _C:\input_。
2. 第二個欄位是容器中的目錄，例如 _/usr/local/models_。
3. 第三個欄位 (選擇性的) 是以逗號分隔的選項清單。如需詳細資訊，請參閱 [使用磁片](https://docs.docker.com/storage/volumes/)區。

### <a name="volume-mount-example"></a>磁片區掛接範例

```bash
-v C:\input:/usr/local/models
```

此命令會將主機電腦 _C:\input_ 目錄裝載到容器 _/usr/local/models_ 目錄。

> [!IMPORTANT]
> 磁片區掛接設定只適用于 **自訂的語音轉換文字** 和 **自訂文字轉換語音的** 容器。 **語音轉換文字**、類 **神經文字轉換語音** 和 **文字轉換語音** 的容器都不會使用磁片區裝載。

## <a name="example-docker-run-commands"></a>範例 docker run 命令

下列範例會使用組態設定來說明如何撰寫和使用 `docker run` 命令。 開始執行後，容器就會持續執行，直到您加以[停止](speech-container-howto.md#stop-the-container)。

- **行接續字元**：下列各節中的 Docker 命令會使用反斜線 `\` 作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。
- **引數順序**：除非您熟悉 Docker 容器，否則請勿變更引數的順序。

請將 {_argument_name_} 取代為您自己的值：

| 預留位置 | 值 | 格式或範例 |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | `Speech`Azure 金鑰頁面上資源的端點金鑰 `Speech` 。   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | 帳單端點值可在 Azure `Speech` 總覽頁面上取得。 | 請參閱收集明確範例 [所需的參數](speech-container-howto.md#gathering-required-parameters) 。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。 如需詳細資訊，請參閱[帳單](#billing-configuration-setting)。
> ApiKey 值是 [Azure 語音資源金鑰] 頁面中的索引 **鍵** 。

## <a name="speech-container-docker-examples"></a>語音容器 Docker 範例

以下是適用于語音容器的 Docker 範例。

## <a name="speech-to-text"></a>[語音轉換文字](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>語音轉換文字的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>語音轉換文字的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[自訂語音轉換文字](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>自訂語音轉換文字的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>自訂語音轉換文字的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[文字轉換語音](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>文字轉換語音的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>文字轉換語音的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[自訂文字轉換語音](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>自訂文字轉換語音的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>自訂文字轉換語音的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="neural-text-to-speech"></a>[神經文字轉換語音](#tab/ntts)

### <a name="basic-example-for-neural-text-to-speech"></a>神經文字轉換語音的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-neural-text-to-speech"></a>神經文字轉換語音的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="speech-language-detection"></a>[語音語言偵測](#tab/lid)

### <a name="basic-example-for-speech-language-detection"></a>語音語言偵測的基本範例

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-language-detection"></a>語音語言偵測的記錄範例

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>後續步驟

- 檢閱[如何安裝及執行容器](speech-container-howto.md)
