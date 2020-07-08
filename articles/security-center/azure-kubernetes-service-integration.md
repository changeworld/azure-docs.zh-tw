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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800167"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes Services 與資訊安全中心整合

Azure Kubernetes Service （AKS）是 Microsoft 的受控服務，可用於開發、部署和管理容器化應用程式。 

如果您使用 Azure 資訊安全中心的標準層，您可以新增 AKS 配套（請參閱[定價](security-center-pricing.md)），以深入瞭解您的 AKS 節點、雲端流量和安全性控制。

資訊安全中心和 AKS 構成了最佳的雲端原生 Kubernetes 安全性供應專案。

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>資訊安全中心的 Kubernetes 保護有哪些元件？

資訊安全中心的 Kubernetes 保護是由兩個元素的組合所提供：

- **Azure 資訊安全中心的虛擬機器威脅防護**-使用資訊安全中心在其他 vm 上使用的相同 Log Analytics 代理程式，資訊安全中心會顯示 AKS 節點上發生的安全性問題。 代理程式也會監視容器特有的分析。

- **Azure 資訊安全中心的選擇性 Kubernetes**配套-Kubernetes 配套會透過 AKS 服務從 Kubernetes 子系統接收記錄和資訊。 這些記錄已透過 AKS 服務在 Azure 中提供。 當您啟用資訊安全中心的 Kubernetes 配套時，您會將資訊安全中心存取權授與記錄。 因此資訊安全中心使用已由 AKS 主要節點收集的資料，為您的 AKS 叢集帶來安全性優勢。 Azure 資訊安全中心從您的 Kubernetes 環境掃描的部分資料可能包含機密資訊。

    ![Azure 資訊安全中心和 Azure Kubernetes Service （AKS）高階總覽](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>提供哪些保護？

同時使用這兩個服務可提供：

* **安全性建議**-資訊安全中心識別您的 AKS 資源，並將其分類：從叢集到個別的虛擬機器。 接著，您可以針對每個資源來查看安全性建議。 如需詳細資訊，請參閱[建議的參考清單](recommendations-reference.md#recs-containers)中的容器建議。 

* **環境強化**-資訊安全中心會持續監視 Kubernetes 叢集和 Docker 設定的設定。 然後，它會產生反映業界標準的安全性建議。

* **執行時間保護**-透過持續分析下列 AKS 來源，資訊安全中心在主機*和*AKS 叢集層級偵測到威脅和惡意活動的警示。 [深入瞭解容器的威脅保護](threat-protection.md#azure-containers)。


     

![更詳細的 Azure 資訊安全中心和 Azure Kubernetes Service （AKS）](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>AKS 與資訊安全中心常見問題

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>如果沒有 Log Analytics 代理程式，仍然可以取得 AKS 保護嗎？

如先前所述，選擇性的 Kubernetes 配套提供叢集層級的保護，Azure 資訊安全中心標準層的 Log Analytics 代理程式可保護您的節點。 

我們建議您盡可能部署這兩個，以獲得最完整的保護。

如果您選擇不在您的主機上安裝代理程式，您只會收到威脅防護權益和安全性警示的子集。 您仍會收到與網路分析以及與惡意伺服器通訊有關的警示。



## <a name="next-steps"></a>後續步驟

若要深入瞭解資訊安全中心的容器安全性功能，請參閱：

* [Azure 資訊安全中心和容器安全性](container-security.md)

* [與 Azure Container Registry 整合](azure-container-registry-integration.md)

* [Microsoft 的資料管理](https://www.microsoft.com/trust-center/privacy/data-management)-說明 microsoft 服務（包括 Azure、Intune 和 Microsoft 365）的資料原則、microsoft 資料管理的詳細資訊，以及影響您資料的保留原則
