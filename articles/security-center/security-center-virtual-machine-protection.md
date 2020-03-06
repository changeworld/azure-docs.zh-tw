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
ms.openlocfilehash: bcf92838483fbb6b54802cc0d44cc44ea086d705
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380826"
---
# <a name="protect-your-machines-and-applications"></a>保護您的機器和應用程式
當 Azure 資訊安全中心識別潛在的安全性弱點時，它會建立建議，引導您完成設定所需控制項來強化和保護資源的程式。

本文說明資訊安全中心的資源安全性區段的 [**計算和應用程式**] 頁面。

如需您可能會在此頁面上看到之建議的完整清單，請參閱[計算和應用程式建議](recommendations-reference.md#recs-computeapp)。


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>查看您的計算和應用程式資源的安全性

[![資訊安全中心儀表板](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

若要查看計算和應用程式資源的狀態，請從資訊安全中心的左窗格中選取 [計算] [ **& 應用程式**]。 下列索引標籤可供使用：

* **總覽**：列出所有計算和應用程式資源的建議，以及其目前的安全性狀態 

* [**Vm 和伺服器**](#vms-and-computers)：列出您的 vm、電腦和其目前安全性狀態的建議

* [**VM 擴展集**](#vmscale-sets)：列出您擴展集的建議， 

* [**雲端服務**](#cloud-services)：列出所監視的 web 和背景工作角色的建議資訊安全中心

* [**應用程式服務**](#app-services)：列出您 App service 環境的建議，以及各項服務的目前安全性狀態

* [**容器**](#containers)：列出容器的建議和其設定的安全性評估

* **計算資源**：列出計算資源的建議，例如 Service Fabric 叢集和事件中樞

### <a name="whats-in-each-tab"></a>每個索引標籤的內容為何？

每個索引標籤都有多個區段，而在每個區段中，您可以向下切入，以查看所顯示專案的其他詳細資料。

在每個索引標籤中，您也會看到受監視環境中相關資源的建議。 第一個資料行會列出建議，第二個數據行則顯示受影響的資源總數，而第三個數據行會顯示問題的嚴重性。

每個建議在經選取後，都會有一組可供執行的動作。 比方說，如果您選取 [遺漏系統更新]，將會出現遺漏修補程式的 VM 和電腦數目，以及遺漏更新的嚴重性。

> [!NOTE]
> 安全性建議與 [**建議**] 頁面上的相同，但在這裡會篩選至您所選取的特定資源類型。 如需如何解決建議的詳細資訊，請參閱[在 Azure 資訊安全中心中執行安全性建議](security-center-recommendations.md)。
>




### <a name="vms-and-computers"></a>Vm 和伺服器
[Vm 和電腦] 區段可讓您瞭解 Vm 和電腦的所有安全性建議。 包含四種類型的機器：

![非 Azure 電腦](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) 非 Azure 電腦。

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM。

![Azure 傳統 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure 傳統 VM。

![從工作區識別的 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) 僅只從已檢視訂用帳戶中的工作區識別的 VM。 這包括向此訂用帳戶中的工作區報告的其他訂用帳戶中的 Vm，以及與 Operations Manager 直接代理程式一起安裝，且沒有資源識別碼的 vm。

每個建議底下出現的圖示，可協助您快速識別需要關注的 VM 和電腦，以及建議的類型。 您也可以使用篩選，依**資源類型**及依**嚴重性**來搜尋清單。

若要向下切入到每個 VM 的安全性建議，請按一下該 VM。
您可在此處查看 VM 或電腦的安全性詳細資料。 在底部，您可以看到建議的動作和每個問題的嚴重性。

[![雲端服務](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="vmscale-sets"></a>虛擬機器擴展集
資訊安全中心會自動探索您是否有擴展集，並建議您在其上安裝 Microsoft Monitoring Agent。

安裝 Microsoft Monitoring Agent： 

1. 選取 [在虛擬機器擴展集上安裝監視代理程式] 建議。 您將取得未受監視的擴展集清單。

1. 選取狀況不良的擴展集。 請遵循下列指示，使用現有擴展工作區或建立新擴展工作區來安裝監視代理程式。 如果未設定工作區[定價層](security-center-pricing.md)，請務必加以設定。

   ![安裝 MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

若要將新的擴展集設定為自動安裝 Microsoft Monitoring Agent：
1. 移至 Azure 原則，然後按一下 [定義]。

1. 搜尋 [**部署適用于 Windows 虛擬機器擴展集的 Log Analytics 代理程式**] 原則，然後按一下它。

1. 按一下 [指派]。

1. 設定 [範圍] 和 [Log Analytics 工作區]，然後按一下 [指派]。

如果您想要設定所有現有擴展集來安裝 Microsoft Monitoring Agent，請在 Azure 原則中移至 [修復]，並將現有原則套用到現有的擴展集。





### <a name="cloud-services"></a>雲端服務
針對雲端服務，若作業系統版本已過期，便會建立建議。

![雲端服務](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

在您有建議的案例中，請依照建議中的步驟來更新作業系統。 當有可用的更新時，您將會有警示（紅色或橙色），視問題的嚴重性而定。 如需這項建議的完整說明，請按一下 [**描述**] 欄底下的 [**更新 OS 版本**]。






### <a name="app-services"></a>應用程式服務
若要查看 App Service 資訊，您必須是資訊安全中心的標準定價層，並在您的訂用帳戶中啟用 App Service。 如需啟用這項功能的指示，請參閱[使用 Azure 資訊安全中心保護 App Service](security-center-app-services.md)。

在 [應用程式服務] 下方，您會看到應用程式服務環境的清單，並可根據資訊安全中心執行的評估檢視健康情況摘要。

![應用程式服務](./media/security-center-virtual-machine-recommendations/app-services.png)

顯示的應用程式服務有三種類型：

![應用程式服務環境](./media/security-center-virtual-machine-recommendations/ase.png) 應用程式服務環境

![Web 應用程式](./media/security-center-virtual-machine-recommendations/web-app.png) Web 應用程式

![函式應用程式](./media/security-center-virtual-machine-recommendations/function-app.png) 函式應用程式

如果您選取 web 應用程式，則會開啟摘要視圖並包含三個索引標籤：

   - **建議**：根據失敗資訊安全中心所執行的評量。
   - **已通過的評估**：資訊安全中心所執行、已通過的評估清單。
   - **無法使用的評估**：列出因錯誤或建議與特定應用程式服務無關而無法執行的評估

   [建議] 下方會列出為選取的 Web 應用程式提供的建議，和每項建議的嚴重性。

   ![應用程式服務建議](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

選取建議可查看建議的描述，以及狀況不良資源、狀況良好資源和未掃描資源的清單。

   - [**通過的評估**] 資料行會顯示已通過的評量清單。 這些評估的嚴重性永遠是綠色的。

   - 從清單中選取已通過的評估，以取得評估的描述，以及狀況不良和狀況良好的資源清單，和未掃描的資源清單。 狀況不良的資源會有索引標籤，但由於已通過評估，因此該清單一律是空白的。





### <a name="containers"></a>那裡

當您開啟 [**容器**] 索引標籤時，視您的環境而定，您可能會看到三種資源類型的其中一種：

![容器主機](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) 容器主機-執行 docker 的 Vm 

![Kubernetes 服務](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure Kubernetes Service （AKS）叢集

![容器登錄](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure Container Registry （ACR）登錄-只有當您在標準定價層，且已啟用 Azure Container Registry 配套時，才會顯示。

如需如何使用容器安全性功能的指示，請參閱[監視容器的安全性](monitor-container-security.md)。

[此處](azure-container-registry-integration.md)說明 Azure Container Registry 套件組合的優點

[這裡](azure-kubernetes-service-integration.md)說明 Kubernetes Services 配套的優點

[![容器 索引標籤](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

若要查看清單中特定資源的建議，請按一下該資源。

#### <a name="visibility-into-container-registries"></a>容器登錄的可見度

例如，從上圖所示的清單按一下 asc 示範 ACR 登錄，會導致此詳細資料頁面：

[針對特定 ACR 登錄 ![建議](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>查看 IaaS Linux 機器上裝載的容器

當您按一下其中一個執行 docker 的 Vm 時，您會看到詳細資料頁面，其中包含電腦上容器的相關資訊，例如 Docker 版本和主機上執行的映射數目。

![執行 docker 之 VM 的建議](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>以適用于 Docker 的 CIS 基準為基礎的安全性建議

資訊安全中心會掃描您的 Docker 組態，並透過提供一份所有經評估為失敗規則的清單，讓您能夠看見錯誤的組態。 資訊安全中心提供指導方針協助您快速解決這些問題，並節省時間。 資訊安全中心會持續評估 Docker 設定，並提供給您其最新狀態。

![容器索引標籤](./media/security-center-container-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>後續步驟
若要深入瞭解適用于其他 Azure 資源類型的建議，請參閱下列文章：

* [Azure 資訊安全中心安全性建議的完整參考清單](recommendations-reference.md)
* [在 Azure 資訊安全中心監視身分識別和存取](security-center-identity-access.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
* [保護 Azure 資訊安全中心內的 Azure SQL 服務](security-center-sql-service-recommendations.md)