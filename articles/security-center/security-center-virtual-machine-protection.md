---
title: 保護您的機器和應用程式
description: 本文件說明資訊安全中心為了協助您保護虛擬機器和電腦以及 Web 應用程式和 App Service 環境所提供的建議。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435638"
---
# <a name="protect-your-machines-and-applications"></a>保護您的機器和應用程式
當 Azure 安全中心識別潛在的安全漏洞時,它會創建建議,指導您完成配置所需控制項以強化和保護資源的過程。

本文介紹了安全中心資源安全部分的計算**和應用**頁面。

有關您可能在此頁上看到的建議的完整清單,請參閱[計算和應用建議](recommendations-reference.md#recs-computeapp)。


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>檢視計算及應用程式的安全性

[![資訊安全中心儀表板](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

要查看計算和應用資源的狀態,請在安全中心的左側窗格中選擇 **「計算&應用**」。 以下選項卡可用:

* **概述**:列出所有計算和應用資源及其當前安全狀態的建議 

* [**VM 與伺服器**](#vms-and-computers):列出對 VM、電腦和每個伺服器的目前安全狀態的建議

* [**VM 規模集**](#vmscale-sets):列出規模集的建議, 

* [**雲服務**](#cloud-services):列出安全中心監視的 Web 和輔助角色的建議

* [**套用服務**](#app-services):列出應用服務環境的建議以及每個環境的當前安全狀態

* [**容器**](#containers):列出容器的建議及其設定的安全評估

* **計算資源**:列出計算資源的建議,如服務結構群集和事件中心

### <a name="whats-in-each-tab"></a>每個選項卡中有什麼?

每個選項卡有多個部分,在每個部分中,您可以向下鑽取以查看有關顯示的專案的其他詳細資訊。

在每個選項卡中,您還將看到有關受監視環境中相關資源的建議。 第一列列出建議,第二列顯示受影響的資源總數,第三列顯示問題的嚴重性。

每個建議在經選取後，都會有一組可供執行的動作。 比方說，如果您選取 [遺漏系統更新]****，將會出現遺漏修補程式的 VM 和電腦數目，以及遺漏更新的嚴重性。

> [!NOTE]
> 安全建議與 **「建議」** 頁上的建議相同,但在此處篩選為您選擇的特定資源類型。 有關如何解決建議的詳細資訊,請參閱[Azure 安全中心中實現安全建議](security-center-recommendations.md)。
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>VM 和伺服器
"VM 和計算機"部分概述了 VM 和電腦的所有安全建議。 包括四種類型的機器:

![非 Azure 電腦](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) 非 Azure 電腦。

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM。

![Azure 傳統 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure 傳統 VM。

![從工作區識別的 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) 僅只從已檢視訂用帳戶中的工作區識別的 VM。 這包括報告為此訂閱中的工作區的其他訂閱的 VM,以及使用操作管理器直接代理安裝且沒有資源 ID 的 VM。

每個建議底下出現的圖示，可協助您快速識別需要關注的 VM 和電腦，以及建議的類型。 您也可以使用篩選，依**資源類型**及依**嚴重性**來搜尋清單。

若要向下切入到每個 VM 的安全性建議，請按一下該 VM。
您可在此處查看 VM 或電腦的安全性詳細資料。 在底部,您可以看到建議的操作和每個問題的嚴重性。

[![雲服務](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>虛擬機器擴展集
安全中心會自動發現您是否具有縮放集,並建議您在其中安裝日誌分析代理。

要安裝紀錄分析代理: 

1. 選取 [在虛擬機器擴展集上安裝監視代理程式]**** 建議。 您將取得未受監視的擴展集清單。

1. 選取狀況不良的擴展集。 請遵循下列指示，使用現有擴展工作區或建立新擴展工作區來安裝監視代理程式。 如果未設置工作區[定價層](security-center-pricing.md),請確保設置該層。

   ![安裝 MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

要設置新的規模集以自動安裝日誌分析代理,請執行以下規定:
1. 移至 Azure 原則，然後按一下 [定義]****。

1. 搜索 Windows**虛擬機縮放集的策略"部署日誌分析代理**",然後單擊它。

1. 按一下 [指派]****。

1. 設定 [範圍]**** 和 [Log Analytics 工作區]****，然後按一下 [指派]****。

如果要將所有現有規模集設置為安裝日誌分析代理,請在 Azure 策略中轉到 **"修正"** 並將現有策略應用於現有規模集。





### <a name="cloud-services"></a><a name="cloud-services"></a>雲服務
針對雲端服務，若作業系統版本已過期，便會建立建議。

![雲端服務](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

在有建議的情況下,請按照建議中的步驟更新操作系統。 當更新可用時,您將有一個警報(紅色或橙色 - 取決於問題的嚴重性)。 關於此建議的完整說明,請按下 **「描述」** 欄位下的 **「更新作業系統」 。**






### <a name="app-services"></a><a name="app-services"></a>應用服務
要查看應用服務資訊,您必須位於安全中心的標準定價層,並在訂閱中啟用應用服務。 有關啟用此功能的說明,請參閱[使用 Azure 安全中心保護應用服務](security-center-app-services.md)。

在 [應用程式服務]**** 下方，您會看到應用程式服務環境的清單，並可根據資訊安全中心執行的評估檢視健康情況摘要。

![應用程式服務](./media/security-center-virtual-machine-recommendations/app-services.png)

顯示三種類型的應用程式服務:

![應用程式服務環境](./media/security-center-virtual-machine-recommendations/ase.png) 應用程式服務環境

![Web 應用程式](./media/security-center-virtual-machine-recommendations/web-app.png) Web 應用程式

![函式應用程式](./media/security-center-virtual-machine-recommendations/function-app.png) 函式應用程式

如果選擇 Web 應用程式,將開啟摘要檢視,其中包含三個選項卡:

   - **建議**:基於安全中心執行的失敗的評估。
   - **已通過的評估**：資訊安全中心所執行、已通過的評估清單。
   - **無法使用的評估**：列出因錯誤或建議與特定應用程式服務無關而無法執行的評估

   [建議]**** 下方會列出為選取的 Web 應用程式提供的建議，和每項建議的嚴重性。

   ![應用程式服務建議](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

選取建議可查看建議的描述，以及狀況不良資源、狀況良好資源和未掃描資源的清單。

   - 「**已通過評估」** 列顯示已通過的評估清單。 這些評估的嚴重性永遠是綠色的。

   - 從清單中選取已通過的評估，以取得評估的描述，以及狀況不良和狀況良好的資源清單，和未掃描的資源清單。 狀況不良的資源會有索引標籤，但由於已通過評估，因此該清單一律是空白的。





### <a name="containers"></a><a name="containers"></a>容器

開啟 **「容器」** 選項卡時,根據您的環境,您可能會看到三種類型的資源中的任何一種:

![容器主機](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) 容器主機 ─執行 Docker 的 VM 

![Kubernetes 服務](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure 庫伯奈斯服務 (AKS) 群集

![容器登錄](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure 容器註冊表 (ACR) 註冊表 - 僅在您處於標準定價層和啟用 Azure 容器註冊表包時顯示。

有關如何使用容器安全功能的說明,請參閱[監察容器的安全性](monitor-container-security.md)。

[此處](azure-container-registry-integration.md)介紹了 Azure 容器註冊錶捆綁包的優點

庫伯內斯服務捆綁包的好處[在這裡](azure-kubernetes-service-integration.md)解釋

[![容器選項卡](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

要查看清單中特定資源的建議,請單擊該資源。

#### <a name="visibility-into-container-registries"></a>容器註冊表的可見性

例如,從上圖中顯示的清單中按一下 asc-demo ACR 註冊表,將引至此詳細資訊頁面:

[![針對特定 ACR 註冊表的建議](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>可看見在 IaaS Linux 電腦上裝載的容器

按下執行 Docker 的 VM 之一時,您將看到詳細資訊頁,其中包含與電腦上的容器相關的資訊,例如 Docker 版本和主機上執行的影像數。

![執行 Docker 的 VM 建議](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>根據 Docker 的 CIS 基準提出安全性建議

資訊安全中心會掃描您的 Docker 組態，並透過提供一份所有經評估為失敗規則的清單，讓您能夠看見錯誤的組態。 資訊安全中心提供指導方針協助您快速解決這些問題，並節省時間。 資訊安全中心會持續評估 Docker 設定，並提供給您其最新狀態。

![容器索引標籤](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>後續步驟
要瞭解有關適用於其他 Azure 資源類型的建議,請參閱以下文章:

* [Azure 安全中心安全建議的完整參考清單](recommendations-reference.md)
* [在 Azure 資訊安全中心監視身分識別和存取](security-center-identity-access.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
* [保護 Azure 資訊安全中心內的 Azure SQL 服務](security-center-sql-service-recommendations.md)