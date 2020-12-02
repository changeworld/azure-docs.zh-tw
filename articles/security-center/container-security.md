---
title: Azure 資訊安全中心的容器安全性 | Microsoft Docs
description: 了解 Azure 資訊安全中心的容器安全功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 7265195f9614928a2150a56a780ea7b36bc2e266
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030861"
---
# <a name="container-security-in-security-center"></a>資訊安全中心的容器安全性

Azure 資訊安全中心是用來保護您容器的 Azure 原生解決方案。

資訊安全中心可以保護下列容器資源類型：

| 資源類型 | 資訊安全中心提供的保護 |
|:--------------------:|-----------|
| ![Kubernetes 服務](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Azure Kubernetes Service (AKS) 叢集** | - 持續評估 AKS 叢集的設定，協助您發現錯誤的設定，並提供指導方針，協助您解決任何探索到的問題。<br>[深入了解透過安全性建議強化的環境](#environment-hardening)。<br><br>- 適用於 AKS 叢集和 Linux 節點的威脅防護。 [適用於 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md) (選用功能) 會提供可疑活動的警示。<br>[深入了解 AKS 節點和叢集的執行階段保護](#run-time-protection-for-aks-nodes-and-clusters)。|
| ![容器主機](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**容器主機**<br>(執行 Docker 的 VM) | - 持續評估 Docker 叢集的設定，協助您發現錯誤的設定，並提供指導方針，協助您使用[適用於伺服器的 Azure Defender](defender-for-servers-introduction.md) 選用功能，解決任何探索到的問題。<br>[深入了解透過安全性建議強化的環境](#environment-hardening)。|
| ![Container Registry](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)：<br>**Azure Container Registry (ACR) 登錄** | - Azure Resource Manager 型 ACR 登錄中映像的弱點評量和管理工具，以及[適用於容器登錄的 Azure Defender](defender-for-container-registries-introduction.md) 選用功能。<br>[深入了解掃描容器映像的弱點](#vulnerability-management---scanning-container-images)。 |
|||

本文說明如何使用資訊安全中心及適用於容器登錄、伺服器和 Kubernetes 的選擇性 Azure Defender 方案，來改善、監視和維護容器及其應用程式的安全性。

您將了解資訊安全中心如何針對容器安全性的下列核心層面提供協助：

- [弱點管理 - 掃描容器映像](#vulnerability-management---scanning-container-images)
- [強化環境](#environment-hardening)
- [AKS 節點和叢集的執行階段保護](#run-time-protection-for-aks-nodes-and-clusters)

下列螢幕擷取畫面顯示資產清查頁面，以及受資訊安全中心保護的各種容器資源類型。

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="資訊安全中心內資產清查頁面的容器相關資源" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>弱點管理 - 掃描容器映像

若要在 Azure Resource Manager 架構的 Azure 容器登錄中監視映像，請啟用[適用於容器登錄的 Azure Defender](defender-for-container-registries-introduction.md)。 資訊安全中心會掃描過去 30 天內提取的任何映像，以及推送至您登錄或匯入的映像。 整合式掃描程式是由領先業界的弱點掃描廠商 Qualys 所提供。

當 Qualys 或資訊安全中心發現問題時，您會在 [Azure Defender 儀表板](azure-defender-dashboard.md)中收到通知。 針對每個弱點，資訊安全中心都會提供可採取動作的建議及嚴重性分類，以及如何補救問題的指引。 如需資訊安全中心的容器建議詳細資料，請參閱[建議的參考清單](recommendations-reference.md#recs-containers)。

資訊安全中心會篩選並分類掃描器發現的結果。 當映像狀況良好時，資訊安全中心會據以標示。 資訊安全中心只會針對有問題待解決的映像產生安全性建議。 資訊安全中心只會在發生問題時發出通知，藉以減少非必要的資訊警示。

## <a name="environment-hardening"></a>強化環境

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持續監視 Docker 設定

Azure 資訊安全中心可識別 IaaS Linux VM 上裝載的非受控容器，或其他執行 Docker 容器的 Linux 機器。 資訊安全中心會持續評估這些容器的設定。 然後與[網際網路安全性 (CIS) Docker 基準測試中心](https://www.cisecurity.org/benchmark/docker/)進行比較。

資訊安全中心包含 CIS Docker 基準測試的整個規則集，並會在您的容器無法滿足任何控制項時發出警示。 資訊安全中心會在找到錯誤的設定時，產生安全性建議。 使用資訊安全中心的 **建議頁面** 來檢視建議並補救問題。 CIS 基準測試不會在 AKS 管理的執行個體或 Databricks 管理的 VM 上執行。

若要深入了解這項功能上可能會出現的相關資訊安全中心建議，請參閱建議參考資料表的[容器區段](recommendations-reference.md#recs-containers)。

當您探索 VM 的安全性問題時，資訊安全中心會提供有關機器上容器的其他資訊。 這類資訊包括 Docker 版本，以及在主機上執行的映像數目。 

若要監視 IaaS Linux VM 上所裝載的非受控容器，請啟用[適用於伺服器的 Azure Defender](defender-for-servers-introduction.md) 選用功能。


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>持續監視您的 Kubernetes 叢集
資訊安全中心會與 Azure Kubernetes Service (AKS) (Microsoft 的受控容器協調流程服務) 搭配運作，以用於開發、部署和管理容器化應用程式。

AKS 提供安全性控制項和叢集安全性狀態的可見度。 資訊安全中心會使用這些功能來執行下列動作：
* 持續監視 AKS 叢集的設定
* 產生與業界標準一致的安全性建議

若要深入了解這項功能上可能會出現的相關資訊安全中心建議，請參閱建議參考資料表的[容器區段](recommendations-reference.md#recs-containers)。

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>使用 Kubernetes 許可控制保護工作負載的最佳做法

如需保護 Kubernetes 容器工作負載的建議組合，請安裝 **適用於 Kubernetes 的 Azure 原則附加元件**。 您也可以自動部署此附加元件，如[啟用延伸模組自動佈建](security-center-enable-data-collection.md#enable-auto-provisioning-of-extensions)中所述。 當附加元件的自動佈建設定為「開啟」時，預設會在所有現有和未來的叢集 (符合附加元件安裝需求) 中啟用此延伸模組。

如[適用於 Kubernetes 的 Azure 原則頁面](../governance/policy/concepts/policy-for-kubernetes.md)中所述，附加元件會延伸  [Open Policy Agent](https://www.openpolicyagent.org/) 的 [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper)  (開放原始碼) 許可控制器 Webhook。 Kubernetes 許可控制器是可強制規定叢集使用方式的外掛程式。 附加元件會註冊為 Kubernetes 許可控制的 Webhook，讓您能夠以集中且一致的方式，在叢集上套用大規模的強制動作和防護。 

在 AKS 叢集上安裝附加元件後，則針對 Kubernetes API 伺服器提出的每個要求都會依據預先定義的最佳做法來加以監視，然後再保存到叢集。 接著，您可以進行設定以 **強制執行** 最佳做法，並要求未來的工作負載遵循。 

例如，您可以要求不得建立具特殊權限的容器，而且未來要求這麼做的任何要求都會遭到封鎖。

在＜[保護 Kubernetes 工作負載](kubernetes-workload-protections.md)＞中深入了解。


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>AKS 節點和叢集的執行階段保護

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>後續步驟

在此概觀中，您已了解 Azure 資訊安全中心內的容器安全性核心元素。 如需相關內容，請參閱：

- [適用於 Kubernetes 的 Azure Defender 簡介](defender-for-kubernetes-introduction.md)
- [適用於容器登錄的 Azure Defender 簡介](defender-for-container-registries-introduction.md)