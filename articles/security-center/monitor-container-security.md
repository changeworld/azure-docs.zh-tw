---
title: 在 Azure 資訊安全中心中監視容器的安全性
description: 瞭解如何從 Azure 資訊安全中心檢查容器的安全性狀態
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 8ec06fc23b147eb3e4a5922242aa922063f4172c
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514147"
---
# <a name="monitor-the-security-of-your-containers"></a>監視容器的安全性

此頁面說明如何使用 [概念] 區段中的 [容器安全性文章](container-security.md) 所述的容器安全性功能。


## <a name="scan-your-arm-based-container-registries-for-vulnerabilities"></a>掃描以 ARM 為基礎的容器登錄是否有弱點 

1. 若要啟用 Azure Container Registry 映射的弱點掃描：

    1. 確定您是 Azure 資訊安全中心的標準定價層。

    1. 從 [**定價 & 設定**] 頁面中，為您的訂用帳戶啟用選用的容器登錄套件組合：啟用容器登錄套件組合 ![](media/monitor-container-security/enabling-container-registries-bundle.png)

        安全性中心現在已準備好掃描推送至登錄的映射。 

      >[!NOTE]
      >這項功能是依每個映射收費。


1. 若要觸發映射掃描，請將其推送至您的登錄。 

    當掃描完成時 (通常是在大約2分鐘之後，但最多可能會有15分鐘的) ，結果會以安全性中心建議的形式提供。

1. 若要查看結果，請移至 [ **建議** ] 頁面。 如果發現問題，您將會看到下列建議：

    ![補救問題的建議 ](media/monitor-container-security/acr-finding.png)

1. 選取建議。 
    [建議詳細資料] 頁面隨即開啟，並提供其他資訊。 這項資訊包括具有易受攻擊的映射的登錄清單 ( 「受影響的資源」 ) 和補救步驟。 

1. 選取特定的登錄，以查看裡面有易受攻擊的存放庫的存放庫。

    ![選取登錄](media/monitor-container-security/acr-finding-select-registry.png)

    [登錄詳細資料] 頁面隨即開啟，其中包含受影響的存放庫清單。

1. 選取特定的存放庫，以查看裡面有易受攻擊的映射的存放庫。

    ![選取存放庫](media/monitor-container-security/acr-finding-select-repository.png)

    [存放庫詳細資料] 頁面隨即開啟。 它會列出易受攻擊的映射，並評估結果的嚴重性。

1. 選取特定的映射以查看弱點。

    ![選取影像](media/monitor-container-security/acr-finding-select-image.png)

    所選映射的結果清單隨即開啟。

    ![結果清單](media/monitor-container-security/acr-findings.png)

1. 若要深入瞭解尋找，請選取 [尋找]。 

    [結果詳細資料] 窗格隨即開啟。

    [![結果詳細資料窗格](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    此窗格包含問題的詳細描述，以及外部資源的連結，以協助減輕威脅。

1. 依照此窗格的 [補救] 區段中的步驟執行。

1. 當您採取補救安全性問題所需的步驟時，請更換您登錄中的映射：

    1. 推送更新的映射。 這將會觸發掃描。 
    
    1. 請查看建議頁面，以取得建議「應補救 Azure Container Registry 映射中的弱點」。 
    
        如果仍出現建議，且您已處理的影像仍出現在易受攻擊的映射清單中，請再次檢查補救步驟。

    1. 當您確定已推送、掃描並不再出現建議中的已更新映射時，請從您的登錄中刪除「舊」易受攻擊的映射。


## <a name="harden-your-containers-docker-hosts"></a>強化容器的 Docker 主機

安全性中心會持續監視 Docker 主機的設定，並產生反映業界標準的安全性建議。

若要查看 Azure 資訊安全中心的容器 Docker 主機安全性建議：

1. 從 [安全性中心] 巡覽列，開啟 [ **計算 & apps** ]，然後選取 [ **容器** ] 索引標籤。

1. （選擇性）將您的容器資源清單篩選至容器主機主機。

    ![容器資源篩選](media/monitor-container-security/container-resources-filter.png)

1. 從容器主機電腦的清單中，選取一個來進一步調查。

    ![容器主機建議](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    [ **容器主機資訊] 頁面** 隨即開啟，其中包含主機的詳細資料和建議清單。

1. 從建議清單中，選取建議以進一步調查。

    ![容器主機建議清單](media/monitor-container-security/container-host-rec.png)

1. （選擇性）閱讀描述、資訊、威脅和補救步驟。 

1. 選取頁面底部的 [ **採取動作** ]。

    [![採取動作按鈕](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics 隨即開啟，並已準備好執行自訂作業。 預設的自訂查詢包含已評估之所有失敗規則的清單，以及協助您解決問題的指導方針。

    [![Log Analytics 動作](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. 必要時，請調整查詢參數。

1. 當您確定命令適合您的主機時，請選取 [ **執行**]。



## <a name="next-steps"></a>接下來的步驟

在本文中，您已瞭解如何使用「安全性中心」的容器安全性功能。 

如需其他相關材質，請參閱下列頁面： 

- [適用于容器的安全性中心建議](recommendations-reference.md#recs-containers)
- [AKS 叢集層級的警示](alerts-reference.md#alerts-akscluster)
- [容器主機層級的警示](alerts-reference.md#alerts-containerhost)
