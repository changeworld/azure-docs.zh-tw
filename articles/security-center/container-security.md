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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800593"
---
# <a name="container-security-in-security-center"></a>資訊安全中心中的容器安全性

Azure 資訊安全中心是用來保護您的容器的 Azure 原生解決方案。 資訊安全中心可以保護下列容器資源類型：



|資源 |Name  |詳細資料  |
|:---------:|---------|---------|
|![容器主機](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|容器主機（執行 Docker 的虛擬機器）|資訊安全中心會掃描您的 Docker 組態，並透過提供一份所有經評估為失敗規則的清單，讓您能夠看見錯誤的組態。 資訊安全中心提供指導方針協助您快速解決這些問題，並節省時間。 資訊安全中心會持續評估 Docker 設定，並提供給您其最新狀態。|
|![Kubernetes 服務](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Azure Kubernetes Service （AKS）叢集|針對標準層使用者，利用[資訊安全中心的選擇性 AKS](azure-kubernetes-service-integration.md)配套，取得 AKS 節點、雲端流量和安全性控制的更深入可見度。|
|![容器登錄](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Azure Container Registry （ACR）登錄|針對標準層使用者，使用[資訊安全中心的選擇性 acr](azure-kubernetes-service-integration.md)配套，深入瞭解以 ARM 為基礎的 acr 登錄中的映射弱點。|
||||


本文說明如何使用這些配套來改善、監視和維護容器及其應用程式的安全性。 您將瞭解資訊安全中心如何協助下列容器安全性的核心層面：

- [弱點管理-掃描容器映射](#vulnerability-management---scanning-container-images)
- [強化環境-持續監視 Docker 設定和 Kubernetes 叢集](#environment-hardening)
- [執行時間保護-即時威脅偵測](#run-time-protection---real-time-threat-detection)

[![Azure 資訊安全中心的 [容器安全性] 索引標籤](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

如需有關如何使用這些功能的指示，請參閱[監視容器的安全性](monitor-container-security.md)。

## <a name="vulnerability-management---scanning-container-images"></a>弱點管理-掃描容器映射
若要監視 ARM 型 Azure Container Registry，請確定您是在資訊安全中心的標準層（請參閱[定價](/azure/security-center/security-center-pricing)）。 然後，啟用選擇性的容器登錄套件組合。 推送新的映射時，資訊安全中心會使用領先業界的弱點掃描廠商 Qualys 掃描映射。

找到問題時（依 Qualys 或資訊安全中心），您會在資訊安全中心儀表板中收到通知。 針對每個弱點，資訊安全中心提供可採取動作的建議，以及嚴重性分類，以及如何修復問題的指引。 如需資訊安全中心的容器建議詳細資料，請參閱[建議的參考清單](recommendations-reference.md#recs-containers)。

資訊安全中心篩選並分類掃描器的發現結果。 當影像狀況良好時，資訊安全中心將其標示為。 資訊安全中心只會針對具有要解決之問題的映射產生安全性建議。 藉由只在發生問題時發出通知，資訊安全中心降低不必要資訊警示的可能性。

## <a name="environment-hardening"></a>強化環境

### <a name="continuous-monitoring-of-your-docker-configuration"></a>持續監視 Docker 設定
Azure 資訊安全中心可識別 IaaS Linux Vm 上裝載的非受控容器，或其他執行 Docker 容器的 Linux 電腦。 資訊安全中心會持續評估這些容器的設定。 然後將它們與「[網際網路安全性」（CIS） Docker 基準的中心](https://www.cisecurity.org/benchmark/docker/)進行比較。

資訊安全中心包含 CIS Docker 基準測試的整個規則集，並在您的容器無法滿足任何控制項時發出警示。 當它找到錯誤的錯誤時，資訊安全中心會產生安全性建議。 使用 [**建議] 頁面**來查看建議並補救問題。 您也會在 [**容器**] 索引標籤上看到建議，其中會顯示使用 Docker 部署的所有虛擬機器。 

如需有關這項功能可能會出現之相關資訊安全中心建議的詳細資訊，請參閱建議參考資料表的[容器一節](recommendations-reference.md#recs-containers)。

當您探索 VM 的安全性問題時，資訊安全中心提供有關機器上容器的其他資訊。 這類資訊包括 Docker 版本，以及在主機上執行的映射數目。 

>[!NOTE]
> 這些 CIS 基準測試不會在 AKS 管理的實例或 Databricks 管理的 Vm 上執行。

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>連續監視您的 Kubernetes 叢集
資訊安全中心與 Azure Kubernetes Service （AKS）搭配運作，這是 Microsoft 管理的容器協調流程服務，可用於開發、部署和管理容器化應用程式。

AKS 提供安全性控制和叢集安全性狀態的可見度。 資訊安全中心使用下列功能來執行下列動作：
* 持續監視 AKS 叢集的設定
* 產生與業界標準一致的安全性建議

如需有關這項功能可能會出現之相關資訊安全中心建議的詳細資訊，請參閱建議參考資料表的[容器一節](recommendations-reference.md#recs-containers)。

## <a name="run-time-protection---real-time-threat-detection"></a>執行時間保護-即時威脅偵測

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>後續步驟

在此總覽中，您已瞭解 Azure 資訊安全中心中容器安全性的核心元素。 繼續[瞭解如何監視容器的安全性](monitor-container-security.md)。
> [!div class="nextstepaction"]
> [監視容器的安全性](monitor-container-security.md)