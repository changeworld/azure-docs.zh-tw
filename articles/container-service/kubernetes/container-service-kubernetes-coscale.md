---
title: (已淘汰) 使用 CoScale 監視 Azure Kubernetes 叢集
description: 使用 CoScale 監視 Azure Container Service 中的 Kubernetes 叢集
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f1d0ca1ffc2e7a3d645ac5acbaafdf45f85550be
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271101"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(已淘汰) 使用 CoScale 監視 Azure Container Service Kubernetes 叢集

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

在本文中，我們會示範如何部署 [CoScale](https://web.archive.org/web/20180317071550/https://www.coscale.com/) 代理程式，監視 Azure Container Service 中 Kubernetes 叢集的所有節點和容器。 您需要 CoScale 帳戶以進行這項設定。 


## <a name="about-coscale"></a>關於 CoScale 

CoScale 是監視平台，收集數個協調流程平台上所有容器的計量和事件。 CoScale 提供 Kubernetes 環境的全方位監視。 它提供堆疊中所有圖層的視覺效果和分析：作業系統、Kubernetes、Docker 和容器內執行的應用程式。 CoScale 提供數個內建的監視儀表板，而且具有內建的異常偵測，可讓操作人員和開發人員快速找出基礎結構和應用程式的問題。

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

如本文所示，您可以在 Kubernetes 叢集上安裝代理程式，將 CoScale 當成 SaaS 解決方案執行。 如果您想要在現場保留資料，CoScale 也提供內部部署安裝。


## <a name="prerequisites"></a>必要條件

您需要先[建立 CoScale 帳戶](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial)。

本逐步解說假設您已[使用 Azure Container Service 建立 Kubernetes 叢集](container-service-kubernetes-walkthrough.md)。

同時也假設您已經安裝 `az` Azure CLI 和 `kubectl` 工具。

您可以藉由執行下列操作來測試是否已安裝 `az` 工具：

```azurecli
az --version
```

如果您尚未安裝 `az` 工具，[這裡](/cli/azure/install-azure-cli)有指示。

您可以藉由執行下列操作來測試是否已安裝 `kubectl` 工具：

```bash
kubectl version
```

如果您尚未安裝 `kubectl`，可以執行︰

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>使用 DaemonSet 安裝 CoScale 代理程式
[DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 是 Kubernetes 用來在叢集中每個主機上執行容器的單一執行個體。
它們非常適合執行監視代理程式，例如 CoScale 代理程式。

登入 CoScale 之後，請移至[代理程式頁面](https://app.coscale.com/)使用 DaemonSet 在您的叢集上安裝 CoScale 代理程式。 CoScale UI 提供引導式設定步驟，讓您建立代理程式並開始監視完整的 Kubernetes 叢集。

![CoScale 代理程式設定](./media/container-service-kubernetes-coscale/installation.png)

若要在叢集上啟動代理程式，請執行提供的命令：

![啟動 CoScale 代理程式](./media/container-service-kubernetes-coscale/agent_script.png)

就這麼簡單！ 當代理程式啟動並執行之後，幾分鐘之內您應該會在主控台中看到資料。 請瀏覽[代理程式頁面](https://app.coscale.com/)查看叢集摘要，執行其他設定步驟並查看儀表板，例如 **Kubernetes 叢集概觀**。

![Kubernetes 叢集概觀](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

CoScale 代理程式會自動部署在叢集中的新機器上。 新版本發行時，代理程式會自動更新。


## <a name="next-steps"></a>後續步驟

如需 CoScale 監視解決方案的詳細資訊，請參閱 [CoScale 文件](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/)和[部落格](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog)。 

