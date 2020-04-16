---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細說明語音到文本的掌舵圖配置選項。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422317"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>語音文字(子圖表:圖表/語音到文字)

要覆蓋"保護傘"圖表,請使用任何參數的首`speechToText.`碼使其更具體。 例如,它會重寫相應的參數,例如,`speechToText.numberOfConcurrentRequest`重寫`numberOfConcurrentRequest`, 199 。

|參數|描述|預設|
| -- | -- | -- |
| `enabled` | 是否啟用**語音到文字**服務。 | `false` |
| `numberOfConcurrentRequest` | **語音到文字**服務的併發請求數。 此圖表根據此值自動計算 CPU 和記憶體資源。 | `2` |
| `optimizeForAudioFile`| 該服務是否需要通過音訊檔優化音訊輸入。 如果`true`,此圖表將分配更多的 CPU 資源用於服務。 | `false` |
| `image.registry`| **語音到文字**的 Docker 映像註冊表。 | `containerpreview.azurecr.io` |
| `image.repository` | **語音到文字**的 Docker 映像儲存庫。 | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **語音到文字**的 Docker 映像標記。 | `latest` |
| `image.pullSecrets` | 用於拉**取語音到文字**的 docker 圖像的圖像機密。 | |
| `image.pullByHash`| docker 映射是否由哈希拉。 如果需要`true``image.hash`,則為。 | `false` |
| `image.hash`| **語音到文字**的 Docker 圖像哈希。 僅在時`image.pullByHash: true`使用。  | |
| `image.args.eula` (必要) | 指示您已接受許可證。 唯一有效的值是`accept` | |
| `image.args.billing` (必要) | 計費終結點 URI 值在 Azure 門戶的語音概述頁面上可用。 | |
| `image.args.apikey` (必要) | 用來追蹤帳單資訊。 ||
| `service.type` | **語音到文字**服務的 Kubernetes 服務類型。 有關詳細資訊,請參閱[Kubernetes 服務類型說明](https://kubernetes.io/docs/concepts/services-networking/service/)並驗證雲供應商支援。 | `LoadBalancer` |
| `service.port`|  **語音到文字**服務的埠。 | `80` |
| `service.annotations` | 服務元資料的**語音到文本**註釋。 註釋是關鍵值對。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | 是否開啟[水平 Pod 自動縮放器](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。 如果`true`將`speech-to-text-autoscaler`部署在庫伯奈斯群集中。 | `true` |
| `service.podDisruption.enabled` | 是否開啟[Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 如果`true`將`speech-to-text-poddisruptionbudget`部署在庫伯奈斯群集中。 | `true` |