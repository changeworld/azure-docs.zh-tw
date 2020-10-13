---
title: 搭配 KEDA 使用 Kubernetes 上的 Azure Functions
description: 了解如何使用 KEDA (Kubernetes-based event driven autoscaling) 在雲端或內部部署環境中的 Kubernetes 上執行 Azure Functions。
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: eab0a54d30f2cd2829779dbfc6081445f5be0a71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648853"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>搭配 KEDA 使用 Kubernetes 上的 Azure Functions

Azure Functions 執行階段在裝載位置和裝載方式上有很大的彈性。  [KEDA](https://keda.sh) (Kubernetes-based Event Driven Autoscaling) 能與 Azure Functions 執行階段和工具完美搭配，在 Kubernetes 中提供由事件驅動的規模調整。

## <a name="how-kubernetes-based-functions-work"></a>以 Kubernetes 為基礎的函式如何運作

Azure Functions 服務是由兩個主要元件所組成：執行階段和調整控制器。  函式執行階段會執行您的程式碼。  執行階段包含如何觸發、記錄和管理函式執行的邏輯。  Azure Functions 執行階段可以在「任何位置」執行。  另一個元件則是調整控制器。  「調整控制器」會監視以您函式為目標的事件比率，並主動調整執行應用程式的執行個體數目。  若要深入了解，請參閱 [Azure Functions 規模調整和主控](functions-scale.md)。

以 Kubernetes 為基礎的函式會在 [Docker 容器](functions-create-function-linux-custom-image.md)式執行階段並透過 KEDA 進行事件驅動的規模調整。  KEDA 可以縮減為 0 個執行個體 (沒有發生任何事件時)，以及擴增到 n 個執行個體。 其運作原理是公開 Kubernetes 自動調整程式的自訂計量 (水平 Pod 自動調整程式)。  將 Functions 容器與 KEDA 搭配使用，可讓您在任何 Kubernetes 叢集中複寫無伺服器函式功能。  針對無伺服器基礎結構，這些函式也可以使用 [Azure Kubernetes Services (AKS) 虛擬節點](../aks/virtual-nodes-cli.md)功能來進行部署。

## <a name="managing-keda-and-functions-in-kubernetes"></a>在 Kubernetes 中管理 KEDA 和函式

若要在 Kubernetes 叢集上執行函式，您必須安裝 KEDA 元件。 您可以使用 [Azure Functions Core Tools](functions-run-local.md) 來安裝此元件。

### <a name="installing-with-helm"></a>使用 Helm 安裝

在任何 Kubernetes 叢集中安裝 KEDA 的方式有很多種，包括 Helm。  部署選項接記載於 [KEDA 網站](https://keda.sh/docs/1.4/deploy/)。

## <a name="deploying-a-function-app-to-kubernetes"></a>將函式應用程式部署至 Kubernetes

您可以將任何函式應用程式部署到執行 KEDA 的 Kubernetes 叢集。  因為您的函式會在 Docker 容器中執行，所以您的專案需要 `Dockerfile`。  如果您沒有 Dockerfile，可以在 Functions 專案的根目錄執行下列命令來新增 Dockerfile：

```cli
func init --docker-only
```

若要建立映像，並將您的函式部署至 Kubernetes，請執行下列命令：

> [!NOTE]
> Core Tools 會利用 Docker CLI 來建立和發佈映像。 請確定已安裝 Docker，並且已透過 `docker login` 連線到您的帳戶。

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> 以函式應用程式的名稱取代 `<name-of-function-deployment>`。

這會建立 Kubernetes `Deployment` 資源、`ScaledObject` 資源和 `Secrets`(包括從您 `local.settings.json` 檔案匯入的環境變數)。

### <a name="deploying-a-function-app-from-a-private-registry"></a>從私人登錄部署函式應用程式

上述流程也適用於私人登錄。  如果您要從私人登錄提取容器映像，請在執行 `func kubernetes deploy` 時，加入 `--pull-secret` 旗標，該旗標會參照保存私人登錄認證的 Kubernetes 祕密。

## <a name="removing-a-function-app-from-kubernetes"></a>從 Kubernetes 移除函式應用程式

部署之後，您可以藉由移除相關聯的 `Deployment`、`ScaledObject` 和 `Secrets` 來移除函式。

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>從 Kubernetes 解除安裝 KEDA

[KEDA 網站上](https://keda.sh/docs/1.4/deploy/)有解除安裝 KEDA 的步驟。

## <a name="supported-triggers-in-keda"></a>KEDA 中支援的觸發程序

KEDA 支援下列 Azure 函式觸發程序：

* [Azure 儲存體佇列](functions-bindings-storage-queue.md)
* [Azure 服務匯流排佇列](functions-bindings-service-bus.md)
* [Azure 事件/IoT 中樞](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ 佇列](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP 觸發程序支援

您可以使用公開 HTTP 觸發程序的 Azure Functions，但 KEDA 不會直接管理這些項目。  您可以利用 KEDA Prometheus 觸發程序，[將 HTTP Azure Functions 從 1 個執行個體擴增為 n 個執行個體](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)。

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [使用自訂映像建立函式](functions-create-function-linux-custom-image.md)
* [撰寫 Azure Functions 並在本機進行測試](functions-develop-local.md)
* [Azure 函式使用方案的運作方式](functions-scale.md)