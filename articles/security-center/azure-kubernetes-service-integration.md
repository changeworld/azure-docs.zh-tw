---
title: Azure 資訊安全中心和 Azure Kubernetes Service
description: 瞭解 Azure 資訊安全中心與 Azure Kubernetes Services 的整合
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9d8c5f98cfd8b4b3831bcbd7e65285f93e6c323f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441947"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Azure Kubernetes Services 與資訊安全中心整合（預覽）
Azure Kubernetes Service （AKS）是 Microsoft 的受控服務，可用於開發、部署和管理容器化應用程式。 

使用 AKS 搭配 Azure 資訊安全中心的標準層（請參閱[定價](security-center-pricing.md)），以深入瞭解您的 AKS 節點、雲端流量和安全性控制。

資訊安全中心使用 AKS 主要節點已收集的資料，為您的 AKS 叢集帶來安全性優勢。 

![Azure 資訊安全中心和 Azure Kubernetes Service （AKS）高階總覽](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

這兩種工具共同構成了最佳的雲端原生 Kubernetes 安全性供應專案。 

## <a name="benefits-of-integration"></a>整合的優點

同時使用這兩個服務可提供：

* **安全性建議**-資訊安全中心識別您的 AKS 資源，並將其分類：從叢集到個別的虛擬機器。 接著，您可以針對每個資源來查看安全性建議。 如需詳細資訊，請參閱[建議的參考清單](recommendations-reference.md#recs-computeapp)中的容器建議。 

    > [!NOTE]
    > 如果資訊安全中心建議的名稱以 "（Preview）" 標記為結尾，則會參考建議的預覽性質，而不是功能。

* **環境強化**-資訊安全中心會持續監視 Kubernetes 叢集和 Docker 設定的設定。 然後，它會產生反映業界標準的安全性建議。

* **執行時間保護**-透過持續分析下列 AKS 來源，資訊安全中心在主機*和*AKS 叢集層級偵測到威脅和惡意活動的警示：
    * 原始的安全性事件，例如網路資料和進程建立
    * Kubernetes audit 記錄檔

    如需詳細資訊，請參閱[Azure 容器的威脅偵測](security-center-alerts-compute.md#azure-containers-)

    如需可能的警示清單，請參閱警示參考資料表中的下列各節： [AKS 叢集層級警示](alerts-reference.md#alerts-akscluster)和[容器主機層級警示](alerts-reference.md#alerts-containerhost)。  

![更詳細的 Azure 資訊安全中心和 Azure Kubernetes Service （AKS）](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Azure 資訊安全中心從您的 Kubernetes 環境掃描的部分資料可能包含機密資訊。


## <a name="next-steps"></a>後續步驟

若要深入瞭解資訊安全中心的容器安全性功能，請參閱：

* [Azure 資訊安全中心和容器安全性](container-security.md)

* [與 Azure Container Registry 整合](azure-container-registry-integration.md)

* [Microsoft 的資料管理](https://www.microsoft.com/trust-center/privacy/data-management)-說明 microsoft 服務的資料原則（包括 Azure、Intune 和 Office 365）、microsoft 資料管理的詳細資料，以及影響您資料的保留原則