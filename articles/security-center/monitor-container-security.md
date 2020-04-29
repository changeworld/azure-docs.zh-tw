---
title: 在 Azure 資訊安全中心中監視容器的安全性
description: 瞭解如何從 Azure 資訊安全中心檢查容器的安全性狀態
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77919527"
---
# <a name="monitoring-the-security-of-your-containers"></a>監視容器的安全性

此頁面說明如何使用我們的概念一節中[容器安全性一文](container-security.md)所述的容器安全性功能。

Azure 資訊安全中心涵蓋容器安全性的下列三個層面：

- **弱點管理**-如果您是在資訊安全中心的標準定價層（請參閱[定價](/azure/security-center/security-center-pricing)），您可以在每次推送新映射時掃描 ARM 型 Azure Container Registry。 掃描器（由 Qualys 提供技術支援）以資訊安全中心建議的形式呈現結果。
    如需詳細指示，請參閱下方[的掃描容器登錄是否有弱點](#scanning-your-arm-based-container-registries-for-vulnerabilities)。

- **強化容器的 Docker 主機**-資訊安全中心會尋找裝載于 IaaS Linux vm 或其他執行 Docker 之 Linux 電腦上的非受控容器，並持續比較容器的設定與中心，以進行網際網路安全性（CIS） Docker 基準測試。 如果您的容器不符合任何控制項，資訊安全中心會對您發出警示。 持續監視因錯誤而造成的安全性風險，是任何安全性計畫的重要元件。 
    如需詳細指示，請參閱以下的[強化容器的 Docker 主機](#hardening-your-containers-docker-hosts)。

- **強化 Azure Kubernetes Service**叢集-資訊安全中心會在 Azure Kubernetes Service 叢集的設定發現弱點時提供建議。 如需可能出現之特定建議的詳細資料，請參閱[Kubernetes 服務建議](recommendations-reference.md#recs-containers)。

- **執行時間保護**-如果您是資訊安全中心的標準定價層，您將會取得容器化環境的即時威脅防護。 資訊安全中心會在主機和 AKS 叢集層級產生可疑活動的警示。 如需可能出現之相關安全性警示的詳細資訊，請參閱警示參考資料表的 Azure Kubernetes Service 叢集的[警示](alerts-reference.md#alerts-akscluster)和[容器的警示-主機層級](alerts-reference.md#alerts-containerhost)區段。

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>掃描以 ARM 為基礎的容器登錄是否有弱點 

1. 若要啟用 Azure Container Registry 映射的弱點掃描：

    1. 請確定您是 Azure 資訊安全中心的標準定價層。

    1. 從**定價 & 設定**] 頁面上，為您的訂用帳戶啟用選用的![容器登錄套件組合：啟用容器登錄庫組合](media/monitor-container-security/enabling-container-registries-bundle.png)

        資訊安全中心現在已準備好掃描已推送至登錄的影像。 

        >[!NOTE]
        >這項功能會按映射收費。


1. 若要觸發映射掃描，請將其推送至您的登錄。 

    當掃描完成時（通常在大約10分鐘之後），資訊安全中心建議中會提供結果。
    

1. 若要查看結果，請移至 [**建議**] 頁面。 如果發現問題，您將會看到下列建議：

    ![補救問題的建議 ](media/monitor-container-security/acr-finding.png)


1. 選取建議。 
    [建議詳細資料] 頁面隨即開啟，其中包含其他資訊。 這項資訊包括具有易受攻擊的映射（「受影響的資源」）和補救步驟的登錄清單。 

1. 選取特定的登錄，以查看其中具有易受攻擊之存放庫的存放庫。

    ![選取登錄](media/monitor-container-security/acr-finding-select-registry.png)

    [登錄詳細資料] 頁面隨即開啟，其中包含受影響的存放庫清單。

1. 選取特定的存放庫，以查看其中具有易受攻擊影像的儲存機制。

    ![選取存放庫](media/monitor-container-security/acr-finding-select-repository.png)

    [存放庫詳細資料] 頁面隨即開啟。 它會列出易受攻擊的映射以及發現嚴重性的評量。

1. 選取特定的映射以查看弱點。

    ![選取影像](media/monitor-container-security/acr-finding-select-image.png)

    選取之影像的結果清單隨即開啟。

    ![發現的清單](media/monitor-container-security/acr-findings.png)

1. 若要深入瞭解尋找，請選取 [尋找]。 

    [發現詳細資料] 窗格隨即開啟。

    [![結果詳細資料窗格](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    此窗格包含問題的詳細描述，以及外部資源的連結，以協助降低威脅。

1. 請遵循此窗格的 [補救] 區段中的步驟。

1. 當您採取補救安全性問題所需的步驟時，請取代您登錄中的映射：

    1. 推送更新的映射。 這會觸發掃描。 
    
    1. 核取 [建議] 頁面中的「應補救 Azure Container Registry 映射中的弱點」建議。 
    
        如果仍出現建議，而且您已處理的映射仍然出現在易受攻擊的映射清單中，請再次檢查補救步驟。

    1. 當您確定更新的映射已推送、掃描，且不再出現于建議中時，請從您的登錄中刪除「舊的」易受攻擊的映射。


## <a name="hardening-your-containers-docker-hosts"></a>強化容器的 Docker 主機

資訊安全中心會持續監視 Docker 主機的設定，並產生反映業界標準的安全性建議。

若要為容器的 Docker 主機查看 Azure 資訊安全中心的安全性建議：

1. 從資訊安全中心導覽列中，開啟 [計算] [ **& 應用程式**]，然後選取 [**容器**] 索引標籤。

1. （選擇性）將您的容器資源清單篩選至容器主機主機。

    ![容器資源篩選](media/monitor-container-security/container-resources-filter.png)

1. 從您的容器主機電腦清單中，選取其中一個以進一步調查。

    ![容器主機建議](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    [**容器主機資訊] 頁面**隨即開啟，並提供主機的詳細資料和建議清單。

1. 從 [建議] 清單中，選取建議以進一步調查。

    ![容器主機建議清單](media/monitor-container-security/container-host-rec.png)

1. （選擇性）閱讀描述、資訊、威脅和補救步驟。 

1. 選取頁面底部的 [**採取動作**]。

    [![[採取動作] 按鈕](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics 隨即開啟，其中包含準備好執行的自訂作業。 預設的自訂查詢包含所有已評估失敗規則的清單，以及協助您解決問題的指導方針。

    [![Log Analytics 動作](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. 調整查詢參數，並在您確定它已準備好可供您的主機時，選取 [**執行**]。 



## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用資訊安全中心的容器安全性功能。 

如需其他相關內容，請參閱下列頁面： 

- [適用于容器的資訊安全中心建議](recommendations-reference.md#recs-containers)
- [AKS 叢集層級的警示](alerts-reference.md#alerts-akscluster)
- [容器主機層級的警示](alerts-reference.md#alerts-containerhost)
