---
title: 庫伯奈斯上的 Azure 函數與 KEDA
description: 瞭解如何使用基於 Kubernetes 的事件驅動的自動縮放在雲中的 Kubernetes 或本地運行 Azure 函數。
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301670"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>庫伯奈斯上的 Azure 函數與 KEDA

Azure 函數運行時提供了在所需位置和方式託管的靈活性。  [KEDA（](https://keda.sh)基於庫伯奈斯的事件驅動自動縮放）與 Azure 函數運行時和工具無縫配對，以在 Kubernetes 中提供事件驅動縮放。

## <a name="how-kubernetes-based-functions-work"></a>基於庫伯奈斯的功能的工作原理

Azure 函數服務由兩個關鍵元件組成：運行時和縮放控制器。  函數運行時運行並執行代碼。  運行時包括有關如何觸發、記錄和管理函數執行的邏輯。  Azure 函數運行時可以*在任何地方*運行。  另一個元件是比例控制器。  縮放控制器監視針對函數的事件速率，並主動縮放運行應用的實例數。  若要深入了解，請參閱 [Azure Functions 規模調整和主控](functions-scale.md)。

基於庫伯奈斯的功能在[Docker 容器](functions-create-function-linux-custom-image.md)中提供函數運行時，通過 KEDA 提供事件驅動的縮放。  KEDA 可以擴展到 0 個實例（當沒有發生事件時）和實例到*n*個實例。 它通過公開庫伯內斯自動縮放器（水準 Pod 自動縮放器）的自訂指標來進行此工作。  使用與 KEDA 一起使用函數容器可以複製任何 Kubernetes 群集中的無伺服器函數功能。  這些功能也可以使用 Azure [Kubernetes 服務 （AKS） 虛擬節點](../aks/virtual-nodes-cli.md)功能部署，用於無伺服器基礎結構。

## <a name="managing-keda-and-functions-in-kubernetes"></a>管理庫貝內斯的KEDA和職能

要在 Kubernetes 群集上運行函數，必須安裝 KEDA 元件。 您可以使用[Azure 函數核心工具](functions-run-local.md)安裝此元件。

### <a name="installing-with-helm"></a>使用頭盔安裝

在包括Helm在內的任何庫伯奈斯群集中安裝 KEDA 的方法有多種。  部署選項記錄在[KEDA 網站](https://keda.sh/deploy/)上。

## <a name="deploying-a-function-app-to-kubernetes"></a>將函數應用部署到庫伯內斯

您可以將任何函數應用部署到運行 KEDA 的庫伯內斯群集。  由於函數在 Docker 容器中運行，因此專案需要`Dockerfile`。  如果還沒有，則可以通過在函數專案的根目錄上運行以下命令來添加 Dockerfile：

```cli
func init --docker-only
```

要生成映射並將函數部署到庫伯內斯，請運行以下命令：

> [!NOTE]
> 核心工具將利用 Docker CLI 構建和發佈映射。 確保已安裝 Docker 並與 帳戶連接`docker login`。

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> 以函式應用程式的名稱取代 `<name-of-function-deployment>`。

這將創建 Kubernetes`Deployment`資源、`ScaledObject`資源和`Secrets`，其中包括從檔`local.settings.json`導入的環境變數。

### <a name="deploying-a-function-app-from-a-private-registry"></a>從專用註冊表部署函數應用

上述流程也適用于私人註冊。  如果要從專用註冊表提取容器映射，請包括引用運行`--pull-secret``func kubernetes deploy`時持有私有註冊表憑據的 Kubernetes 機密的標誌。

## <a name="removing-a-function-app-from-kubernetes"></a>從庫伯內斯中刪除函數應用

部署後，可以通過刪除關聯的`Deployment`、、`ScaledObject`已創建的函數`Secrets`來刪除函數。

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>從庫伯內斯卸載KEDA

卸載 KEDA 的步驟記錄[在 KEDA 網站上](https://keda.sh/deploy/)。

## <a name="supported-triggers-in-keda"></a>KEDA 中支援觸發器

KEDA 支援以下 Azure 函數觸發器：

* [Azure 儲存體佇列](functions-bindings-storage-queue.md)
* [Azure 服務匯流排佇列](functions-bindings-service-bus.md)
* [Azure 事件/IoT 中心](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [兔子MQ佇列](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP 觸發器支援

可以使用公開 HTTP 觸發器的 Azure 函數，但 KEDA 不會直接管理它們。  可以利用 KEDA 參數觸發器將[HTTP Azure 函數從 1 縮放到*n*個實例](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)。

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [使用自訂映射創建函數](functions-create-function-linux-custom-image.md)
* [撰寫 Azure Functions 並在本機進行測試](functions-develop-local.md)
* [Azure 函數使用計畫的工作原理](functions-scale.md)