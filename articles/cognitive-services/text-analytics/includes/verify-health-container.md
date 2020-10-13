---
title: 確認健康情況容器實例
titleSuffix: Azure Cognitive Services
description: 瞭解如何驗證健康情況容器實例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779053"
---
### <a name="verify-that-a-container-is-running"></a>確認容器正在執行

1. 選取 [ **總覽** ] 索引標籤，然後複製 IP 位址。
1. 開啟新的瀏覽器索引標籤，然後輸入 IP 位址。 例如，輸入 `http://<IP-address>:5000 (http://55.55.55.55:5000`) 。 容器的首頁隨即顯示，讓您知道容器正在執行。

    ![查看容器首頁以確認其正在執行](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 選取 [ **服務 API 描述** ] 連結，以移至容器的 Swagger 頁面。

1. 選擇任何 **貼** 文 api，然後選取 [立即 **試用**]。會顯示參數，其中包含範例輸入。

您也可以使用數個 Url 來確認容器是否正在執行。

|要求|用途|
|--|--|
|`http://localhost:5000/`|容器會提供首頁。|
|`http://localhost:5000/ready`|以 GET 要求，這會提供一個驗證，指出容器已準備好接受針對模型的查詢。 此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。|
|`http://localhost:5000/status`|以 GET 要求，例如/ready 端點，這會驗證容器是否正在執行，而不會對模型產生查詢。 此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。|
|`http://localhost:5000/swagger`|透過此 URL，容器會提供一組完整的端點和 `Try it now` 功能檔。 使用此功能，您可以將自己的設定輸入至以 Web 為基礎的 HTML 表單並進行查詢，而無須撰寫任何程式碼。 當查詢傳回時，會提供範例 CURL 命令來示範所需的 HTTP 標頭和本文格式。 |
|`http://localhost:5000/demo`| 這項功能是透過瀏覽器要求的，可針對輸入文字範例或您提供的查詢，提供其結果的互動式視覺效果。  |

使用此要求 URL- `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` -將查詢提交至容器。
