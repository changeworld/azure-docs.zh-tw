---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細解說文字到語音轉換 helm 圖表設定選項。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80874329"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>文字轉換語音 (子圖表：圖表/textToSpeech) 

若要覆寫「傘」圖表，請 `textToSpeech.` 在任何參數上新增前置詞，使其更具體。 例如，它會覆寫對應的參數，例如 `textToSpeech.numberOfConcurrentRequest` 覆寫 `numberOfConcurrentRequest` 。

|參數|描述|預設|
| -- | -- | -- |
| `enabled` | **文字轉換語音**服務是否已啟用。 | `false` |
| `numberOfConcurrentRequest` | **文字轉換語音**服務的並行要求數目。 此圖表會根據此值自動計算 CPU 和記憶體資源。 | `2` |
| `optimizeForTurboMode`| 服務是否需要針對透過文字檔的文字輸入進行優化。 如果 `true` 為，則此圖表會配置更多的 CPU 資源給服務。 | `false` |
| `image.registry`| **文字轉換語音**的 docker 映射登錄。 | `containerpreview.azurecr.io` |
| `image.repository` | **文字轉換語音**的 docker 映射存放庫。 | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **文字轉換語音**的 docker 映射標記。 | `latest` |
| `image.pullSecrets` | 用來提取 **文字轉換語音** docker 映射的影像秘密。 | |
| `image.pullByHash`| 是否由雜湊提取 docker 映射。 如果 `true` `image.hash` 為，則為必要項。 | `false` |
| `image.hash`| **文字轉換語音**的 docker 映射雜湊。 只有在使用時才會使用 `image.pullByHash: true` 。  | |
| `image.args.eula` (必要) | 指出您已接受授權。 唯一有效的值是 `accept` | |
| `image.args.billing` (必要) | 帳單端點 URI 值可在 Azure 入口網站的語音總覽頁面上取得。 | |
| `image.args.apikey` (必要) | 用來追蹤帳單資訊。 ||
| `service.type` | **文字轉換語音**服務的 Kubernetes 服務類型。 如需詳細資訊，請參閱 [Kubernetes 服務類型指示](https://kubernetes.io/docs/concepts/services-networking/service/) ，並確認雲端提供者支援。 | `LoadBalancer` |
| `service.port`|  **文字轉換語音**服務的埠。 | `80` |
| `service.annotations` | 服務中繼資料的 **文字轉換語音** 注釋。 批註是機碼值組。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | 是否啟用 [水準 Pod 自動調整程式](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 。 如果為 `true` ， `text-to-speech-autoscaler` 將會部署在 Kubernetes 叢集中。 | `true` |
| `service.podDisruption.enabled` | 是否啟用 [Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 。 如果為 `true` ， `text-to-speech-poddisruptionbudget` 將會部署在 Kubernetes 叢集中。 | `true` |