---
title: 確認健康情況容器實例
titleSuffix: Azure Cognitive Services
description: 瞭解如何驗證健康情況容器實例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 5c598807f36000a18211e32eba53220bfbeea2f7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108700"
---
### <a name="verify-that-a-container-is-running"></a>確認容器正在執行

1. 選取 [**總覽**] 索引標籤，然後複製 [IP 位址]。
1. 開啟新的瀏覽器索引標籤，並輸入 IP 位址。 例如，輸入 `http://<IP-address>:5000 (http://55.55.55.55:5000` ）。 容器的首頁隨即顯示，讓您知道容器正在執行。

    ![查看容器首頁以確認它正在執行](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 選取 [**服務 API 描述**] 連結，以移至容器的 Swagger 頁面。

1. 選擇任何**張貼**api，然後選取 [立即**試用**]。會顯示參數，其中包括範例輸入。

有數個 Url，您也可以用來確認容器正在執行。

|要求|目的|
|--|--|
|`http://localhost:5000/`|容器會提供首頁。|
|`http://localhost:5000/ready`|以 GET 要求，這會提供容器已準備好接受針對模型進行查詢的確認。 此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。|
|`http://localhost:5000/status`|以 GET 要求，如同/ready 端點，這會驗證容器是否正在執行，而不會對模型產生查詢。 此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。|
|`http://localhost:5000/swagger`|透過這個 URL，容器會提供一組完整的端點和 `Try it now` 功能檔。 使用此功能，您可以將自己的設定輸入至以 Web 為基礎的 HTML 表單並進行查詢，而無須撰寫任何程式碼。 當查詢傳回時，會提供範例 CURL 命令來示範所需的 HTTP 標頭和本文格式。 |
|`http://localhost:5000/demo`| 這項功能透過瀏覽器要求，可提供輸入文字範例查詢或您提供的結果的互動式視覺效果。  |

使用此要求 URL- `http://localhost:5000/text/analytics/v3.0-preview.1/domains/health` -將查詢提交至容器。
