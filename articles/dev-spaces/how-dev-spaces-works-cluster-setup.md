---
title: 為 Azure 開發空間設置群集的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述為 Azure 開發人員空間設置 Azure 庫伯奈斯服務群集的工作原理
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241721"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>為 Azure 開發空間設置群集的工作原理

Azure 開發人員空間為您提供了多種方法來快速反覆運算和調試 Kubernetes 應用程式，並與您的團隊協作處理 Azure Kubernetes 服務 （AKS） 群集。 一種方法是在 AKS 群集上啟用 Azure 開發空間，以便您可以[直接在群集上運行服務][how-it-works-up]，並使用[其他網路和路由功能][how-it-works-routing]。 本文介紹準備群集和啟用 Azure 開發空間時會發生什麼情況。

## <a name="prepare-your-aks-cluster"></a>準備 AKS 群集

要為開發空間準備 AKS 群集，請驗證 AKS 群集位於[Azure 開發空間支援][supported-regions]的區域中，並且運行 Kubernetes 1.10.3 或更高版本。 要從 Azure 門戶在群集上啟用 Azure 開發空間，請導航到群集，請按一下 *"開發空間*"，將 *"使用開發空間*更改為*是*"，然後按一下"*保存*"。 還可以通過運行`az aks use-dev-spaces`從 Azure CLI 啟用 Azure 開發空間。

有關為開發人員空間設置 AKS 群集的示例，請參閱[團隊開發快速入門][quickstart-team]。

在 AKS 群集上啟用 Azure 開發空間後，它將安裝群集的控制器。 控制器位於 AKS 群集之外。 它驅動用戶端工具與 AKS 群集之間的行為和通信。 啟用後，您可以使用用戶端工具與控制器進行交互。

控制器執行以下操作：

* 管理開發空間的創建和選擇。
* 安裝應用程式的 Helm 圖表並創建庫伯內斯物件。
* 生成應用程式的容器映射。
* 將應用程式部署到 AKS。
* 當原始程式碼更改時，執行增量生成和重新開機。
* 管理日誌和 HTTP 跟蹤。
* 將穩貞和穩穩地轉發到用戶端工具。
* 為空間內的應用程式以及跨父空間和子空間配置路由。

控制器是群集外部的單獨 Azure 資源，對群集中的資源執行以下操作：

* 創建或指定 Kubernetes 命名空間以用作開發空間。
* 刪除任何名為*azds*的庫伯內斯命名空間（如果存在）並創建新的命名空間。
* 部署庫伯奈斯 Webhook 配置。
* 部署 Webhook 准入伺服器。

它使用與 AKS 群集用於對其他 Azure 開發人員空間元件進行服務調用相同的服務主體。

![Azure 開發空間準備群集](media/how-dev-spaces-works/prepare-cluster.svg)

為了使用 Azure 開發空間，必須至少有一個開發空間。 Azure 開發空間使用 AKS 群集中的庫伯內斯命名空間來開發空間。 安裝控制器時，它會提示您創建新的 Kubernetes 命名空間或選擇現有命名空間作為第一個開發空間使用。 預設情況下，控制器提供將現有*預設*Kubernetes 命名空間升級到第一個開發空間。

當命名空間被指定為開發空間時，控制器會向該命名空間添加*azds.io/space=true*標籤，以將其標識為開發空間。 預設情況下，在準備群集後，將選擇您創建或指定的初始開發空間。 選擇空間後，Azure 開發人員空間會將其用於創建新工作負荷。

您可以使用用戶端工具創建新的開發空間並刪除現有的開發空間。 由於 Kubernets 中的限制，無法刪除*預設*開發空間。 控制器還會刪除任何名為*azds*的現有 Kubernetes 命名空間，以避免`azds`與用戶端工具使用的命令發生衝突。

Kubernetes Webhook 准入伺服器用於在部署檢測期間向 Pod 注入三個容器：開發空間代理容器、開發空間代理-init 容器和開發空間生成容器。 **所有這些容器都使用 AKS 群集上的根訪問運行。** 它們還使用 AKS 群集用於對其他 Azure 開發人員空間元件進行服務調用的相同服務主體。

![Azure 開發空間庫伯內斯網訪問伺服器](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

開發空間代理容器是一個側車容器，用於處理進出應用程式容器的所有 TCP 流量，並説明執行路由。 如果使用某些空格，則開發空間代理容器將重新路由 HTTP 消息。 例如，它可以説明在父空間和子空間中的應用程式之間路由 HTTP 消息。 所有非 HTTP 流量都通過未修改的開發人員空間代理。 開發空間代理容器還記錄所有入站和出站 HTTP 消息，並將它們作為跟蹤發送到用戶端工具。 然後，開發人員可以查看這些跟蹤以檢查應用程式的行為。

開發空間代理-init 容器是一個[init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，它根據空間層次結構向應用程式的容器添加其他路由規則。 它通過在應用程式容器的 */etc/resolv.conf*檔和 iptables 配置啟動之前更新路由規則來添加路由規則。 對 */etc/resolv.conf*的更新允許在父空間中解析服務。 iptables 配置更新可確保通過開發空間代理路由進出應用程式容器的所有 TCP 流量。 除了 Kubernetes 添加的規則之外，來自開發空間-代理-它的所有更新都會發生。

開發空間生成容器是 init 容器，並裝載了專案原始程式碼和 Docker 通訊端。 專案原始程式碼和對 Docker 的訪問允許由 pod 直接生成應用程式容器。

> [!NOTE]
> Azure 開發空間使用相同的節點來構建應用程式的容器並運行它。 因此，Azure 開發人員空間不需要外部容器註冊表來構建和運行應用程式。

Kubernetes Webhook 進入伺服器偵聽在 AKS 群集中創建的任何新 pod。 如果該窗格部署到帶有*azds.io/space=true*標籤的任何命名空間，它將該窗格注入其他容器。 僅當使用用戶端工具運行應用程式的容器時，才會注入開發空間生成容器。

準備好 AKS 群集後，可以使用用戶端工具在開發空間中準備和運行代碼。

## <a name="client-side-tooling"></a>用戶端工具

用戶端工具允許使用者：
* 為應用程式生成 Dockerfile、Helm 圖表和 Azure 開發人員空間設定檔。
* 創建父和子開發空間。
* 告訴控制器生成和啟動應用程式。

應用程式運行時，用戶端工具也：
* 接收並顯示在 AKS 中運行的應用程式的穩貞和穩穩。
* 使用[埠轉發](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)允許使用 HTTP：\//localhost 對應用程式進行 Web 訪問。
* 將調試器附加到 AKS 中正在運行的應用程式。
* 當檢測到增量生成更改時，將原始程式碼同步到開發空間，從而允許快速反覆運算。
* 允許您將開發人員電腦直接連接到 AKS 群集。

可以使用命令列中的用戶端工具作為命令的一`azds`部分。 您還可以使用用戶端工具：

* 使用[Azure 開發空間擴展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)的視覺化工作室代碼 。
* 視覺工作室與[視覺工作室工具為庫伯內特](https://aka.ms/get-vsk8stools)斯。

## <a name="next-steps"></a>後續步驟

要瞭解有關使用用戶端工具在開發空間中準備和運行代碼的詳細資訊，請參閱[為 Azure 開發人員空間準備專案的工作原理][how-it-works-prep]。

要開始使用 Azure 開發人員空間進行團隊開發，請參閱[Azure 開發人員空間中的團隊開發][quickstart-team]快速入門。

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md