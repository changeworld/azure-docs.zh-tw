---
title: 在 Azure DevTest Labs 中使用平臺即服務（PaaS）服務
description: 瞭解如何在 Azure DevTest Labs 中使用平臺即服務（傳遞）服務。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169197"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中使用平臺即服務（PaaS）服務
DevTest Labs 透過環境功能支援 PaaS。 DevTest Labs 中的環境受到預先設定 Azure Resource Manager Git 存放庫中的範本所支援。 環境可以同時包含 PaaS 和 IaaS 資源。 它們可讓您建立複雜的系統，其中包含 Azure 資源，例如虛擬機器、資料庫、虛擬網路和 Web 應用程式，它們會經過自訂以搭配使用。 這些範本可讓您使用原始程式碼控制來一致部署和改善環境的管理。 

適當設定的系統允許下列案例： 

- 開發人員擁有獨立和多個環境
- 以非同步方式測試不同的設定
- 整合至預備和生產管線，無需任何範本變更
- 在相同的實驗室中同時擁有開發電腦和環境，可以改善管理和成本報告的便利性。  

DevTest Labs 資源提供者會代表實驗室使用者建立資源，因此，不需要提供任何額外的許可權給實驗室使用者，就能使用 PaaS 資源。 下圖顯示 Service Fabric 叢集作為實驗室中的環境。

![Service Fabric 叢集作為環境](./media/create-environment-service-fabric-cluster/cluster-created.png)

如需設定環境的詳細資訊，請參閱[使用 Azure Resource Manager 範本建立多 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。 DevTest Labs 具有 Azure Resource Manager 範本的公用存放庫，您可以用來建立環境，而不需要自行連接至外部 GitHub 來源。 如需公用環境的詳細資訊，請參閱[在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)。

在大型組織中，開發小組通常會提供一些環境，例如自訂/隔離的測試環境。 某些環境可能會供業務單位或部門內的所有小組使用。 IT 小組可能會想要提供可供組織中所有小組使用的環境。  

## <a name="customizations"></a>自訂

#### <a name="sandbox"></a>沙箱 
實驗室擁有者可以自訂實驗室環境，將使用者的角色從**讀者**變更為資源群組中的**參與者**。 這項功能位於實驗室的 [設定]**和 [原則**] 底下的 [**實驗室設定**] 頁面中。 角色中的這項變更可讓使用者新增或移除該環境中的資源。 如果您想要進一步限制存取權，請使用 Azure 原則。 此功能可讓您自訂資源或設定，而不需要訂用帳戶層級的存取權。

#### <a name="custom-tokens"></a>自訂權杖
有些自訂實驗室資訊是在資源群組之外，而且專屬於範本可以存取的環境。 以下說明其中一部分： 

- 實驗室網路識別
- Location
- 儲存 Resource Manager 範本檔案的儲存體帳戶。 
 
#### <a name="lab-virtual-network"></a>實驗室虛擬網路
將[環境連接到實驗室的虛擬網路](connect-environment-lab-virtual-network.md)一文說明如何修改您的 Resource Manager 範本以使用該`$(LabSubnetId)`權杖。 建立環境時，會將`$(LabSubnetId)`權杖取代為第一個子網標記，其中 [**在虛擬機器中使用**] 選項設定為 [ **true**]。 它可讓我們的環境使用先前建立的網路。 如果您想要在測試中的環境中使用相同的 Resource Manager 範本作為預備和生產`$(LabSubnetId)` ，請在 Resource Manager 範本參數中使用做為預設值。 

#### <a name="environment-storage-account"></a>環境儲存體帳戶
DevTest Labs 支援使用[嵌套 Resource Manager 範本](../azure-resource-manager/templates/linked-templates.md)。 [[部署適用于測試環境的嵌套 Azure Resource Manager 範本](deploy-nested-template-environments.md)] 一文說明`_artifactsLocation`如何`_artifactsLocationSasToken`使用和權杖，在與主要範本的嵌套資料夾中相同的資料夾內，建立 Resource Manager 範本的 URI。 如需這兩個權杖的詳細資訊，請參閱[Azure Resource Manager –最佳做法指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)的**部署構件**一節。

## <a name="user-experience"></a>使用者體驗

## <a name="developer"></a>開發人員
開發人員使用相同的工作流程來建立 VM，以建立特定的環境。 他們會選取環境與機器映射，並輸入範本所需的必要資訊。 每位擁有環境的開發人員都可以部署變更，並改善內部迴圈的偵錯工具。 您可以隨時使用最新的範本來建立環境。  這項功能可讓環境終結並重新建立，以協助減少必須以手動方式建立系統或從錯誤測試復原的停機時間。  

### <a name="testing"></a>測試
DevTest Labs 環境可讓您以非同步方式進行特定程式碼和設定的獨立測試。 常見的做法是使用個別提取要求中的程式碼來設定環境，並開始進行任何自動化測試。 一旦自動化測試執行完成之後，就可以針對特定環境執行任何手動測試。 此程式通常會在 CI/CD 管線中完成。 

## <a name="management-experience"></a>管理體驗

### <a name="cost-tracking"></a>成本追蹤
成本追蹤功能包含不同環境中的 Azure 資源，做為整體成本趨勢的一部分。 依資源的成本不會細分環境中的不同資源，而是將環境顯示為單一成本。

### <a name="security"></a>安全性
使用 DevTest Labs 正確設定的 Azure 訂用帳戶，可以[只透過實驗室來限制對 azure 資源的存取](devtest-lab-add-devtest-user.md)。 使用環境，實驗室擁有者可以讓使用者使用已核准的設定來存取 PaaS 資源，而不允許存取任何其他 Azure 資源。 在實驗室使用者自訂環境的案例中，實驗室擁有者可以允許參與者存取。 「參與者存取權」可讓實驗室使用者只在受控資源群組內新增或移除 Azure 資源。 它可讓您更輕鬆地追蹤和管理，並允許使用者參與者存取訂用帳戶。

### <a name="automation"></a>自動化
自動化是大規模、有效生態系統的重要元件。 需要自動化，才能處理跨訂用帳戶和實驗室的管理或追蹤多個環境。

### <a name="cicd-pipeline"></a>CI/CD 管線
DevTest Labs 中的 PaaS 服務有助於改善 CI/CD 管線，方法是在實驗室控制存取權的情況下進行焦點部署。

## <a name="next-steps"></a>後續步驟
如需環境的詳細資訊，請參閱下列文章： 

- 
- [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)
- [在 Azure DevTest Labs 中建立具有獨立 Service Fabric 叢集的環境](create-environment-service-fabric-cluster.md)
- [在 Azure DevTest Labs 中將環境連線至實驗室的虛擬網路](connect-environment-lab-virtual-network.md)
- [將環境整合到您的 Azure DevOps CI/CD 管線](integrate-environments-devops-pipeline.md)
 





