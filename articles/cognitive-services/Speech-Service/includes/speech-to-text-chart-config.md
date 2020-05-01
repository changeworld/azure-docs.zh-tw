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
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: 267c2fb72b38053429019746a573c740d812c38c
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608367"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>語音轉換文字（子圖表：圖表/speechToText）

若要覆寫「傘」圖表，請在`speechToText.`任何參數上加入前置詞，使其更明確。 例如，它會覆`speechToText.numberOfConcurrentRequest` `numberOfConcurrentRequest`寫對應的參數，例如，override。

|參數|描述|預設|
| -- | -- | -- |
| `enabled` | **語音轉換文字**服務是否已啟用。 | `false` |
| `numberOfConcurrentRequest` | **語音轉換文字**服務的並行要求數目。 此圖表會根據此值，自動計算 CPU 和記憶體資源。 | `2` |
| `optimizeForAudioFile`| 服務是否需要針對透過音訊檔案的音訊輸入進行優化。 如果`true`為，則此圖表會將更多 CPU 資源配置給服務。 | `false` |
| `image.registry`| **語音轉換文字**docker 映射登錄。 | `containerpreview.azurecr.io` |
| `image.repository` | **語音轉換文字**docker 映射存放庫。 | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **語音轉換文字**docker 映射標記。 | `latest` |
| `image.pullSecrets` | 用來提取**語音轉換文字**docker 映射的映射秘密。 | |
| `image.pullByHash`| 是否由雜湊提取 docker 映射。 如果`true`為`image.hash` ，則為必要。 | `false` |
| `image.hash`| **語音轉換文字**docker 映射雜湊。 僅用於`image.pullByHash: true`。  | |
| `image.args.eula` (必要) | 表示您已接受授權。 唯一有效的值是`accept` | |
| `image.args.billing` (必要) | [計費端點 URI] 值可在 Azure 入口網站的語音總覽頁面上取得。 | |
| `image.args.apikey` (必要) | 用來追蹤帳單資訊。 ||
| `service.type` | **語音轉換文字**服務的 Kubernetes 服務類型。 如需詳細資訊，請參閱[Kubernetes 服務類型指示](https://kubernetes.io/docs/concepts/services-networking/service/)，並確認雲端提供者支援。 | `LoadBalancer` |
| `service.port`|  **語音轉換文字**服務的埠。 | `80` |
| `service.annotations` | 服務中繼資料的**語音轉換文字**注釋。 注釋是機碼值組。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | [水準 Pod 自動調整程式](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)是否已啟用。 如果`true`為， `speech-to-text-autoscaler`將會部署到 Kubernetes 叢集中。 | `true` |
| `service.podDisruption.enabled` | 是否啟用[Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 如果`true`為， `speech-to-text-poddisruptionbudget`將會部署到 Kubernetes 叢集中。 | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>情感分析（子圖表：圖表/speechToText）

從語音轉換文字容器的 v 2.2.0 開始，下列參數可用於使用文字分析 API 進行情感分析。

|參數|說明|值|預設|
| --- | --- | --- | --- |
|`textanalytics.enabled`| **文字分析**服務是否已啟用| true/false| `false`|
|`textanalytics.image.registry`| **文字分析**docker 映射登錄| 有效的 docker 映射登錄| |
|`textanalytics.image.repository`| **文字分析**docker 映射存放庫| 有效的 docker 映射存放庫| |
|`textanalytics.image.tag`| **文字分析**docker 映射標記| 有效的 docker 映射標記| |
|`textanalytics.image.pullSecrets`| 用於提取**文字分析**docker 映射的影像秘密| 有效的秘密名稱| |
|`textanalytics.image.pullByHash`| 指定是否要依雜湊提取 docker 映射。  如果`yes`是`image.hash` ，也必須有。 如果`no`為，則將它設定為 ' false '。 預設值為 `false`。| true/false| `false`|
|`textanalytics.image.hash`| **文字分析**docker 映射雜湊。 請只將它`image.pullByHash:true`與搭配使用。| 有效的 docker 映射雜湊 | |
|`textanalytics.image.args.eula`| **文字分析**容器的其中一個必要引數，這表示您已接受授權。 此選項的值必須是： `accept`。| `accept`，如果您想要使用容器 | |
|`textanalytics.image.args.billing`| **文字分析**容器的其中一個必要引數，可指定計費端點 URI。 [計費端點 URI] 值可在 Azure 入口網站的語音總覽頁面上取得。|有效的計費端點 URI||
|`textanalytics.image.args.apikey`| **文字分析**容器的其中一個必要引數，用來追蹤帳單資訊。| 有效的 apikey||
|`textanalytics.cpuRequest`| 針對**文字分析**容器所要求的 CPU| int| `3000m`|
|`textanalytics.cpuLimit`| **文字分析**容器的有限 CPU| | `8000m`|
|`textanalytics.memoryRequest`| **文字分析**容器的要求記憶體| | `3Gi`|
|`textanalytics.memoryLimit`| **文字分析**容器的記憶體有限| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| 情感分析 URI 尾碼，整個 URI 的格式為 "`<service>`HTTP://：`<port>`/`<sentimentURISuffix>`"。 | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Kubernetes 中**文字分析**服務的類型。 請參閱[Kubernetes 服務類型](https://kubernetes.io/docs/concepts/services-networking/service/) | 有效的 Kubernetes 服務類型 | `LoadBalancer` |
|`textanalytics.service.port`| **文字分析**服務的埠| int| `50085`|
|`textanalytics.service.annotations`| 使用者可以新增至**文字分析**服務中繼資料的批註。 例如：<br/> **備註**<br/>`   `**some/annotation1： value1**<br/>`  `**some/annotation2： value2** | 批註，每一行一個| |
|`textanalytics.serivce.autoScaler.enabled`| 是否啟用[水準 Pod 自動調整程式](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。 若已啟用`text-analytics-autoscaler` ，將會部署在 Kubernetes 叢集中 | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| 是否啟用[Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 若已啟用`text-analytics-poddisruptionbudget` ，將會部署在 Kubernetes 叢集中| true/false| `true`|
