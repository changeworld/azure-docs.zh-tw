---
title: Azure 資訊安全中心中的容器安全性 |Microsoft Docs
description: 瞭解 Azure 資訊安全中心的容器安全性功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 3edd7ddf79f8400462b52f964b7677840a7e86df
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301953"
---
# <a name="container-security-in-security-center"></a>安全性中心的容器安全性

Azure 資訊安全中心是 Azure 原生解決方案，用來保護您的容器。

安全性中心可以保護下列容器資源類型：

| 資源類型 | 安全中心提供的保護 |
|:--------------------:|-----------|
| ![Kubernetes 服務](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Azure Kubernetes Service (AKS) 叢集** | -持續評估您的 AKS 叢集設定，以提供錯誤設定的可見度，以及協助您解決任何已發現問題的指導方針。<br>[深入瞭解透過安全性建議進行的環境強化](#environment-hardening)。<br><br>-AKS 叢集和 Linux 節點的威脅防護。 可疑活動的警示是由選用的  [Azure Defender For Kubernetes](defender-for-kubernetes-introduction.md)所提供。<br>[深入瞭解 AKS 節點和叢集的執行時間保護](#run-time-protection-for-aks-nodes-and-clusters)。|
| ![容器主機](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**容器主機**<br>執行 Docker)  (Vm | -持續評量您的 Docker 設定，以提供錯誤設定的可見度，以及協助您解決選用的  [Azure Defender for server](defender-for-servers-introduction.md)所發現的任何問題的指導方針。<br>[深入瞭解透過安全性建議進行的環境強化](#environment-hardening)。|
| ![Container registry](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure Container Registry (ACR) 登錄** | -使用 [適用于容器](defender-for-container-registries-introduction.md)登錄的選擇性 Azure Defender，以 AZURE RESOURCE MANAGER 型 ACR 登錄中映射的弱點評定和管理工具。<br>[深入瞭解如何掃描容器映射是否有弱點](#vulnerability-management---scanning-container-images)。 |
|||

本文說明如何使用「安全性中心」以及適用于 container registry、「處理」和「Kubernetes」的選擇性 Azure Defender 方案，來改善、監視及維護容器及其應用程式的安全性。

您將瞭解安全性中心如何協助這些容器安全性的核心層面：

- [弱點管理-掃描容器映射](#vulnerability-management---scanning-container-images)
- [環境強化](#environment-hardening)
- [AKS 節點和叢集的執行時間保護](#run-time-protection-for-aks-nodes-and-clusters)

下列螢幕擷取畫面顯示資產清查頁面，以及由安全中心保護的各種容器資源類型。

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="安全性中心的資產清查頁面中的容器相關資源" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>弱點管理-掃描容器映射

若要監視以 Azure Resource Manager 為基礎的 Azure container registry 中的映射，請 [為容器登錄啟用 Azure Defender](defender-for-container-registries-introduction.md)。 「安全性中心」會掃描在過去30天內提取、推送至您的登錄或匯入的任何映射。 整合式掃描器是由領先業界的弱點掃描廠商（Qualys）所提供。

當您發現問題時（透過 Qualys 或安全性中心），您將會在 [Azure Defender 儀表板](azure-defender-dashboard.md)中收到通知。 針對每個弱點，「安全性中心」提供可操作的建議，以及嚴重性分類，以及如何修復問題的指引。 如需資訊安全中心的容器建議詳細資訊，請參閱 [建議的參考清單](recommendations-reference.md#recs-containers)。

安全性中心會篩選並分類掃描器的結果。 當映射狀況良好時，「安全性中心」會將它標示為如此。 安全性中心只會針對有問題要解決的映射產生安全性建議。 藉由只在發生問題時通知，資訊安全中心會降低不必要資訊警示的可能性。

## <a name="environment-hardening"></a>環境強化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持續監視您的 Docker 設定

Azure 資訊安全中心識別裝載于 IaaS Linux Vm 上的非受控容器，或執行 Docker 容器的其他 Linux 機器。 安全性中心會持續評估這些容器的設定。 然後，它會將它們與 [網際網路安全性 (CIS) Docker 基準測試的中心](https://www.cisecurity.org/benchmark/docker/)進行比較。

如果您的容器無法滿足任何控制項，則 [安全性中心] 會包含 CIS Docker 基準測試的完整規則集，併發出警示。 當它找到錯誤配置時，安全性中心會產生安全性建議。 使用 [資訊中心的 **建議] 頁面** 來查看建議和補救問題。 CIS 基準測試檢查不會在 AKS 管理的實例或 Databricks 管理的 Vm 上執行。

如需此功能可能出現之相關資訊安全中心建議的詳細資訊，請參閱建議參考表的 [容器一節](recommendations-reference.md#recs-containers) 。

當您探索 VM 的安全性問題時，資訊安全中心會提供電腦上容器的其他相關資訊。 這類資訊包括 Docker 版本以及在主機上執行的映射數目。 

若要監視裝載在 IaaS Linux Vm 上的非受控容器，請啟用選用的 [Azure Defender for servers](defender-for-servers-introduction.md)。


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>持續監視您的 Kubernetes 叢集
「安全性中心」搭配 Azure Kubernetes Service (AKS) ，也就是 Microsoft 管理的容器協調流程服務，可用於開發、部署和管理容器化應用程式。

AKS 可提供安全性控制和您叢集安全性狀態的可見度。 [安全性中心] 會使用下列功能：
* 持續監視 AKS 叢集的設定
* 產生與產業標準一致的安全性建議

如需此功能可能出現之相關資訊安全中心建議的詳細資訊，請參閱建議參考表的 [容器一節](recommendations-reference.md#recs-containers) 。

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>使用 Kubernetes 許可控制的工作負載保護最佳做法

安裝  **適用于 Kubernetes 的 Azure 原則附加** 元件，以取得保護 Kubernetes 容器工作負載的建議套件組合。

如同[此 Azure 原則 For Kubernetes 頁面](../governance/policy/concepts/policy-for-kubernetes.md)中所述，附加元件會針對開啟的原則代理程式擴充開放原始碼[閘道管理員](https://github.com/open-policy-agent/gatekeeper)的   許可控制器 webhook。 [ ](https://www.openpolicyagent.org/) Kubernetes 的許可控制器是外掛程式，可強制使用您的叢集。 附加元件會註冊為 Kubernetes 許可控制的 webhook，並可讓您以集中、一致的方式在叢集上套用大規模大規模地規範和保護。 

當您已在 AKS 叢集上安裝附加元件時，對 Kubernetes API 伺服器的每個要求都會針對預先定義的最佳作法集進行監視，然後再保存到叢集。 然後，您可以設定以 **強制執行** 最佳作法，並針對未來的工作負載進行強制執行。 

例如，您可以強制不應建立特殊許可權的容器，而任何未來的要求將會遭到封鎖。

深入瞭解如何 [保護您的 Kubernetes 工作負載](kubernetes-workload-protections.md)。


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>AKS 節點和叢集的執行時間保護

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>後續步驟

在此總覽中，您已瞭解 Azure 資訊安全中心中容器安全性的核心元素。 如需相關內容，請參閱：

- [Azure Defender for Kubernetes 簡介](defender-for-kubernetes-introduction.md)
- [適用于容器登錄的 Azure Defender 簡介](defender-for-container-registries-introduction.md)