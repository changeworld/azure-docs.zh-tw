---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996385"
---
## <a name="validate-that-a-container-is-running"></a>驗證容器正在執行 

有數種方式可驗證容器正在執行。 找出有問題的容器的 *外部 IP* 位址和公開的埠，然後開啟您最愛的網頁瀏覽器。 使用下列各種要求 Url 來驗證容器是否正在執行。 以下列出的範例要求 Url 為 `http://localhost:5000` ，但您的特定容器可能會有所不同。 請記住，您會依賴容器的 *外部 IP* 位址和公開的埠。

| 要求 URL | 用途 |
|--|--|
| `http://localhost:5000/` | 容器會提供首頁。 |
| `http://localhost:5000/ready` | 以 GET 要求，這會提供一個驗證，指出容器已準備好接受針對模型的查詢。  此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。 |
| `http://localhost:5000/status` | 此外，也會向 GET 要求，這會確認用來啟動容器的 api 金鑰是否有效，而不會導致端點查詢。 此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。 |
| `http://localhost:5000/swagger` | 容器會為端點提供一組完整的文件和 [立即試用] 功能。 使用此功能，您可以將自己的設定輸入至以 Web 為基礎的 HTML 表單並進行查詢，而無須撰寫任何程式碼。 當查詢傳回時，會提供範例 CURL 命令來示範所需的 HTTP 標頭和本文格式。 |

![容器的首頁](./media/cognitive-services-containers-api-documentation/container-webpage.png)
