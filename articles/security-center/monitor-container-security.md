---
title: 監視 Azure 安全中心中容器的安全性
description: 瞭解如何從 Azure 安全中心檢查容器的安全狀況
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919527"
---
# <a name="monitoring-the-security-of-your-containers"></a>監控容器的安全性

本頁介紹如何使用["容器安全"一文中所述的容器](container-security.md)安全功能。

Azure 安全中心涵蓋容器安全的以下三個方面：

- **漏洞管理**- 如果您位於安全中心的標準定價層（請參閱[定價](/azure/security-center/security-center-pricing)），則可以在每次推送新映射時掃描基於 ARM 的 Azure 容器註冊表。 掃描器（由 Qualys 提供支援）將發現作為安全中心的建議。
    有關詳細說明，請參閱[掃描下面的容器註冊表中的漏洞](#scanning-your-arm-based-container-registries-for-vulnerabilities)。

- **強化容器的 Docker 主機**- 安全中心發現託管在 IaaS Linux VM 或其他運行 Docker 的 Linux 電腦上的非託管容器，並持續將容器的配置與 Internet 安全中心 （CIS） Docker 基準進行比較。 如果容器不符合任何控制項，安全中心會提醒您。 持續監控配置不當造成的安全風險是任何安全計畫的關鍵組成部分。 
    有關詳細說明，請參閱[在下面強化容器的 Docker 主機](#hardening-your-containers-docker-hosts)。

- **強化 Azure 庫伯奈斯服務群集**- 安全中心在發現 Azure 庫伯內斯服務群集配置中的漏洞時提供建議。 有關可能顯示的具體建議的詳細資訊，請參閱[庫伯內斯服務建議](recommendations-reference.md#recs-containers)。

- **運行時保護**- 如果您位於安全中心的標準定價層，您將獲得容器化環境的即時威脅保護。 安全中心在主機和 AKS 群集級別生成可疑活動的警報。 有關可能出現的相關安全警報的詳細資訊，請參閱[Azure Kubernetes 服務群集的警報](alerts-reference.md#alerts-akscluster)和[容器的警報 -](alerts-reference.md#alerts-containerhost)警報參考表的主機分級部分。

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>掃描基於 ARM 的容器註冊表中的漏洞 

1. 要啟用 Azure 容器註冊表映射的漏洞掃描：

    1. 確保您位於 Azure 安全中心的標準定價層。

    1. 在**定價&設置**頁中，為訂閱啟用可選的容器註冊表包：![啟用容器註冊表包](media/monitor-container-security/enabling-container-registries-bundle.png)

        安全中心現在可以掃描推送到註冊表的圖像。 

        >[!NOTE]
        >此功能按圖像收費。


1. 要觸發映射的掃描，請將其推送到註冊表。 

    掃描完成後（通常在大約 10 分鐘後），安全中心建議中提供了調查結果。
    

1. 要查看調查結果，請訪問 **"建議"** 頁面。 如果發現問題，您將看到以下建議：

    ![修復問題的建議 ](media/monitor-container-security/acr-finding.png)


1. 選擇建議。 
    建議詳細資訊頁將打開，並包含其他資訊。 此資訊包括具有易受攻擊映射（"受影響的資源"）的註冊表的清單以及修正步驟。 

1. 選擇特定的註冊表以查看其中具有易受攻擊存儲庫的存儲庫。

    ![選擇註冊表](media/monitor-container-security/acr-finding-select-registry.png)

    註冊表詳細資訊頁將打開，並列出受影響的存儲庫。

1. 選擇特定存儲庫以查看其中具有易受攻擊映射的存儲庫。

    ![選取存放庫](media/monitor-container-security/acr-finding-select-repository.png)

    將打開存儲庫詳細資訊頁。 它列出了易受攻擊的圖像以及對調查結果嚴重性的評估。

1. 選擇特定圖像以查看漏洞。

    ![選擇圖像](media/monitor-container-security/acr-finding-select-image.png)

    將打開所選圖像的發現清單。

    ![調查結果清單](media/monitor-container-security/acr-findings.png)

1. 要瞭解有關查找的更多詳細資訊，請選擇該查找。 

    將打開"調查結果詳細資訊"窗格。

    [![調查結果詳細資訊窗格](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    此窗格包括問題的詳細說明和指向外部資源的連結，以説明緩解威脅。

1. 按照此窗格的修正部分中的步驟操作。

1. 執行修復安全問題所需的步驟後，請替換註冊表中的映射：

    1. 推送更新的圖像。 這將觸發掃描。 
    
    1. 請查看建議頁面，瞭解"應修復 Azure 容器註冊表映射中的漏洞"的建議。 
    
        如果建議仍然顯示，並且您處理的圖像仍顯示在易受攻擊圖像清單中，請再次檢查修正步驟。

    1. 當您確定已推送、掃描更新的圖像且不再顯示在建議中時，請從註冊表中刪除"舊"易受攻擊的圖像。


## <a name="hardening-your-containers-docker-hosts"></a>加固容器的 Docker 主機

安全中心不斷監視 Docker 主機的配置，並生成反映行業標準的安全建議。

要查看容器的 Docker 主機的 Azure 安全中心安全建議，

1. 在"安全中心"巡覽列中，打開 **"計算&應用**並選擇 **"容器**"選項卡。

1. 可以選擇將容器資源清單篩選到容器主機。

    ![容器資源篩選器](media/monitor-container-security/container-resources-filter.png)

1. 從容器主機清單中，選擇一台以進一步調查。

    ![容器主機建議](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    將打開 **"容器主機資訊"頁**，其中包含主機的詳細資訊和建議清單。

1. 從建議清單中，選擇一個建議以進一步調查。

    ![容器主機建議清單](media/monitor-container-security/container-host-rec.png)

1. 或者，請閱讀說明、資訊、威脅和補救步驟。 

1. 選擇頁面底部的 **"執行操作**"。

    [![執行操作按鈕](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    日誌分析在打開時，自訂操作可供運行。 預設自訂查詢包括評估的所有失敗規則的清單，以及説明您解決問題的準則。

    [![日誌分析操作](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. 調整查詢參數，並在確定主機準備就緒時選擇 **"運行**"。 



## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何使用安全中心的容器安全功能。 

有關其他相關材料，請參閱以下頁面： 

- [容器安全中心建議](recommendations-reference.md#recs-containers)
- [AKS 群集級別的警報](alerts-reference.md#alerts-akscluster)
- [容器主機分級的警報](alerts-reference.md#alerts-containerhost)
