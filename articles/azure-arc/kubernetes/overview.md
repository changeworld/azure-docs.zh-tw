---
title: 已啟用 Azure Arc 的 Kubernetes 概觀
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, 容器
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665272"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>什麼是已啟用 Azure Arc 的 Kubernetes (預覽)

您可以使用已啟用 Azure Arc 的 Kubernetes (預覽)，在 Azure 內部或外部附加及設定 Kubernetes 叢集。 當 Kubernetes 叢集附加至 Azure Arc 時，其會出現在 Azure 入口網站中、具有 Azure Resource Manager 識別碼，以及受控識別。 叢集會附加至標準 Azure 訂用帳戶 (位於資源群組中)，且可以如同任何其他 Azure 資源接收標籤。 


將 Kubernetes 叢集連線到 Azure 時，需要叢集管理員才能部署代理程式。 這些代理程式會在名為 `azure-arc` 的 Kubernetes 命名空間中執行，且為標準 Kubernetes 部署。 代理程式會負責連線至 Azure、收集 Azure Arc 記錄和計量，以及監看設定要求。  
 
 > [!NOTE]
> 啟用 Azure Arc 的 Kubernetes 處於預覽階段，不建議用於生產工作負載。 


## <a name="supported-scenarios"></a>支援的案例 

啟用 Azure Arc 的 Kubernetes 支援下列案例： 

* 連線在 Azure 外部執行的 Kubernetes，以進行清查、分組和標記 

* 使用以 GitOps 為基礎的設定管理來部署應用程式並套用設定 

* 使用容器的 Azure 監視器來檢視及監視您的叢集 

* 使用適用於 Kubernetes 的 Azure 原則來套用原則 

 
## <a name="supported-regions"></a>支援的區域 

目前支援下列區域中啟用 Azure Arc 的 Kubernetes： 

* 美國東部 
* 西歐 


## <a name="next-steps"></a>後續步驟

* [連線叢集](./connect-cluster.md)
