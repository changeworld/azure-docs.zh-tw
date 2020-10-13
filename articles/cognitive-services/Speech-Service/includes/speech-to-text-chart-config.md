---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細說明語音轉換文字 helm 圖表設定選項。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82875989"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>語音轉換文字 (子圖表：圖表/speechToText) 

若要覆寫「傘」圖表，請 `speechToText.` 在任何參數上新增前置詞，使其更具體。 例如，它會覆寫對應的參數，例如 `speechToText.numberOfConcurrentRequest` 覆寫 `numberOfConcurrentRequest` 。

|參數|描述|預設|
| -- | -- | -- |
| `enabled` | 是否啟用 **語音轉換文字** 服務。 | `false` |
| `numberOfConcurrentRequest` | **語音轉換文字**服務的並行要求數目。 此圖表會根據此值自動計算 CPU 和記憶體資源。 | `2` |
| `optimizeForAudioFile`| 服務是否需要透過音訊檔案將音訊輸入優化。 如果 `true` 為，則此圖表會配置更多的 CPU 資源給服務。 | `false` |
| `image.registry`| **語音轉換文字**docker 映射登錄。 | `containerpreview.azurecr.io` |
| `image.repository` | **語音轉換文字**docker 映射存放庫。 | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **語音轉換文字**docker 映射標記。 | `latest` |
| `image.pullSecrets` | 用來提取 **語音轉換文字** docker 映射的影像秘密。 | |
| `image.pullByHash`| 是否由雜湊提取 docker 映射。 如果 `true` `image.hash` 為，則為必要項。 | `false` |
| `image.hash`| **語音轉換文字**docker 映射雜湊。 只有在使用時才會使用 `image.pullByHash: true` 。  | |
| `image.args.eula` (必要) | 指出您已接受授權。 唯一有效的值是 `accept` | |
| `image.args.billing` (必要) | 帳單端點 URI 值可在 Azure 入口網站的語音總覽頁面上取得。 | |
| `image.args.apikey` (必要) | 用來追蹤帳單資訊。 ||
| `service.type` | **語音轉換文字**服務的 Kubernetes 服務類型。 如需詳細資訊，請參閱 [Kubernetes 服務類型指示](https://kubernetes.io/docs/concepts/services-networking/service/) ，並確認雲端提供者支援。 | `LoadBalancer` |
| `service.port`|  **語音轉換文字**服務的通訊埠。 | `80` |
| `service.annotations` | 服務中繼資料的 **語音轉換文字** 批註。 批註是機碼值組。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | 是否啟用 [水準 Pod 自動調整程式](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 。 如果為 `true` ， `speech-to-text-autoscaler` 將會部署在 Kubernetes 叢集中。 | `true` |
| `service.podDisruption.enabled` | 是否啟用 [Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 。 如果為 `true` ， `speech-to-text-poddisruptionbudget` 將會部署在 Kubernetes 叢集中。 | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>情感分析 (子圖表：圖表/speechToText) 

從2.2.0 的語音轉換文字容器和 v 0.2.0 （Helm 圖表）開始，使用下列參數進行使用文字分析 API 的情感分析。

|參數|說明|值|預設|
| --- | --- | --- | --- |
|`textanalytics.enabled`| 是否啟用 **文字分析** 服務| true/false| `false`|
|`textanalytics.image.registry`| **文字分析**docker 映射登錄| 有效的 docker 映射登錄| |
|`textanalytics.image.repository`| **文字分析**docker 映射存放庫| 有效的 docker 映射存放庫| |
|`textanalytics.image.tag`| **文字分析**docker 映射標記| 有效的 docker 映射標記| |
|`textanalytics.image.pullSecrets`| 用於提取 **文字分析** docker 映射的影像秘密| 有效的秘密名稱| |
|`textanalytics.image.pullByHash`| 指定您是否要依雜湊提取 docker 映射。  如果為 `yes` ， `image.hash` 則也需要有。 如果 `no` 為，則將它設為 ' false '。 預設值為 `false`。| true/false| `false`|
|`textanalytics.image.hash`| **文字分析**docker 映射雜湊。 只能搭配使用 `image.pullByHash:true` 。| 有效的 docker 映射雜湊 | |
|`textanalytics.image.args.eula`| **文字分析**容器所需的其中一個引數，表示您已接受授權。 此選項的值必須是： `accept` 。| `accept`，如果您想要使用容器 | |
|`textanalytics.image.args.billing`| **文字分析**容器所需的其中一個引數，可指定計費端點 URI。 帳單端點 URI 值可在 Azure 入口網站的語音總覽頁面上取得。|有效的計費端點 URI||
|`textanalytics.image.args.apikey`| **文字分析**容器所需的其中一個引數，用來追蹤帳單資訊。| 有效的 apikey||
|`textanalytics.cpuRequest`| **文字分析**容器的要求 CPU| int| `3000m`|
|`textanalytics.cpuLimit`| 適用于 **文字分析** 容器的有限 CPU| | `8000m`|
|`textanalytics.memoryRequest`| **文字分析**容器的要求記憶體| | `3Gi`|
|`textanalytics.memoryLimit`| 適用于 **文字分析** 容器的記憶體有限| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| 情感分析 URI 尾碼，整個 URI 的格式為 "HTTP:// `<service>` ： `<port>` / `<sentimentURISuffix>` "。 | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Kubernetes 中的 **文字分析** 服務類型。 查看 [Kubernetes 服務類型](https://kubernetes.io/docs/concepts/services-networking/service/) | 有效的 Kubernetes 服務類型 | `LoadBalancer` |
|`textanalytics.service.port`| **文字分析**服務的埠| int| `50085`|
|`textanalytics.service.annotations`| 使用者可以新增至 **文字分析** 服務中繼資料的批註。 例如：<br/> **注釋：**<br/>`   `**some/annotation1： value1**<br/>`  `**some/annotation2： value2** | 批註，每一行各一個| |
|`textanalytics.serivce.autoScaler.enabled`| 是否啟用 [水準 Pod 自動調整程式](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 。 啟用時， `text-analytics-autoscaler` 將會部署在 Kubernetes 叢集中 | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| 是否啟用 [Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 。 啟用時， `text-analytics-poddisruptionbudget` 將會部署在 Kubernetes 叢集中| true/false| `true`|
