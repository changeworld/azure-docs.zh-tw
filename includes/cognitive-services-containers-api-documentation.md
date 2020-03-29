---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034435"
---
## <a name="validate-that-a-container-is-running"></a>驗證容器正在執行 

有數種方式可驗證容器正在執行。 找到有問題的容器*的外部 IP*位址和公開端口，然後打開您最喜愛的 Web 瀏覽器。 使用下面的各種請求 URL 驗證容器正在運行。 下面列出的示例請求 URL 是`http://localhost:5000`，但您的特定容器可能會有所不同。 請記住，您需要依賴容器*的外部 IP*位址和公開端口。

| 要求 URL | 目的 |
|--|--|
| `http://localhost:5000/` | 容器會提供首頁。 |
| `http://localhost:5000/status` | 使用 HTTP GET 請求，以驗證容器是否運行而不導致終結點查詢。 此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。 |
| `http://localhost:5000/swagger` | 該容器為終結點提供了一組完整的文檔，並提供了 **"試用"** 功能。 使用此功能，您可以將自己的設定輸入至以 Web 為基礎的 HTML 表單並進行查詢，而無須撰寫任何程式碼。 當查詢傳回時，會提供範例 CURL 命令來示範所需的 HTTP 標頭和本文格式。 |

![容器的首頁](./media/cognitive-services-containers-api-documentation/container-webpage.png)
