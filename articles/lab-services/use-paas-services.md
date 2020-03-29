---
title: 在 Azure 開發人員測試實驗室中使用平臺即服務 （PaaS） 服務
description: 瞭解如何在 Azure 開發人員測試實驗室中使用平臺即服務 （通過） 服務。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169197"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>在 Azure 開發人員測試實驗室中使用平臺即服務 （PaaS） 服務
開發人員測試實驗室通過環境功能支援 PaaS。 開發人員測試實驗室中的環境由 Git 存儲庫中預先配置的 Azure 資源管理器範本提供支援。 環境可以同時包含 PaaS 和 IaaS 資源。 它們允許您創建複雜的系統，這些系統可以包括 Azure 資源，如虛擬機器、資料庫、虛擬網路和 Web 應用，這些資源被自訂以協同工作。 這些範本允許使用原始程式碼控制進行一致的部署和改進環境管理。 

正確設置的系統允許以下方案： 

- 具有獨立和多種環境的開發人員
- 非同步對不同配置進行測試
- 集成到暫存和生產管道中，無需任何範本更改
- 在同一實驗室中同時使用開發機器和環境可簡化管理和成本報告。  

DevTest Labs 資來源提供者代表實驗室使用者創建資源，因此無需向實驗室使用者授予額外的許可權即可啟用 PaaS 資源。 下圖將服務結構群集作為實驗室中的環境顯示。

![將服務結構群集作為環境](./media/create-environment-service-fabric-cluster/cluster-created.png)

有關設置環境的詳細資訊，請參閱使用 Azure[資源管理器範本創建多 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。 DevTest Labs 具有 Azure 資源管理器範本的公共存儲庫，可用於創建環境，而無需自己連接到外部 GitHub 源。 有關公共環境的詳細資訊，請參閱在[Azure 開發人員測試實驗室中配置和使用公共環境](devtest-lab-configure-use-public-environments.md)。

在大型組織中，開發團隊通常提供自訂/隔離測試環境等環境。 業務單位或部門內的所有團隊都可以使用一些環境。 IT 組可能希望提供其環境，以便組織中的所有團隊都可以使用這些環境。  

## <a name="customizations"></a>自訂

#### <a name="sandbox"></a>沙箱 
實驗室擁有者可以自訂實驗室環境，以將使用者的角色從**讀取器**更改為資源組中**的參與者**。 此功能位於實驗室**的"配置和策略**"下的 **"實驗室設置**"頁中。 角色的此更改允許使用者添加或刪除該環境中的資源。 如果要進一步限制訪問，請使用 Azure 策略。 此功能允許您自訂資源或配置，而無需在訂閱級別訪問。

#### <a name="custom-tokens"></a>自訂權杖
有一些自訂實驗室資訊位於資源組之外，特定于範本可以訪問的環境。 以下說明其中一部分： 

- 實驗室網路識別
- Location
- 存儲存儲資源管理器範本檔的存儲帳戶。 
 
#### <a name="lab-virtual-network"></a>實驗室虛擬網路
將[環境連接到實驗室的虛擬網路](connect-environment-lab-virtual-network.md)文章介紹如何修改資源管理器範本以使用權杖`$(LabSubnetId)`。 創建環境時，`$(LabSubnetId)`權杖將被第一個子網標記替換，其中**在虛擬機器創建選項中使用**設置為**true**。 它允許我們的環境使用以前創建的網路。 如果要在測試環境中使用與暫存和生產相同的資源管理器範本，請使用`$(LabSubnetId)`資源管理器範本參數中的預設值。 

#### <a name="environment-storage-account"></a>環境存儲帳戶
DevTest 實驗室支援使用[嵌套資源管理器範本](../azure-resource-manager/templates/linked-templates.md)。 [[部署嵌套 Azure 資源管理器範本用於測試環境》](deploy-nested-template-environments.md)一文解釋了如何使用`_artifactsLocation`和`_artifactsLocationSasToken`權杖在主範本的嵌套資料夾中或主範本的嵌套資料夾中創建資源管理器範本的 URI。 有關這兩個權杖的詳細資訊，請參閱 Azure 資源管理器的部署**專案**部分[- 最佳實踐指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)。

## <a name="user-experience"></a>使用者經驗

## <a name="developer"></a>開發人員
開發人員使用相同的工作流來創建 VM 來創建特定環境。 他們選擇環境與機器映射，並輸入範本所需的必要資訊。 每個具有環境的開發人員都允許部署更改和改進的內部迴圈調試。 可以隨時使用最新的範本創建環境。  此功能允許銷毀和重新創建環境，以説明減少手動創建系統或從故障測試恢復的停機時間。  

### <a name="testing"></a>測試
DevTest Labs 環境允許非同步獨立測試特定代碼和配置。 常見做法是使用單個拉取請求中的代碼設置環境，並開始任何自動測試。 一旦自動測試回合到完成，任何手動測試都可以針對特定環境執行。 通常此過程作為 CI/CD 管道的一部分完成。 

## <a name="management-experience"></a>管理經驗

### <a name="cost-tracking"></a>成本追蹤
成本跟蹤功能包括不同環境中的 Azure 資源，作為總體成本趨勢的一部分。 按資源進行的成本不會分解環境中的不同資源，而是將環境顯示為單個成本。

### <a name="security"></a>安全性
使用 DevTest 實驗室正確配置的 Azure 訂閱[只能通過實驗室限制對 Azure 資源的訪問](devtest-lab-add-devtest-user.md)。 使用環境，實驗室擁有者可以允許使用者使用批准的配置訪問 PaaS 資源，而無需訪問任何其他 Azure 資源。 在實驗室使用者自訂環境的方案中，實驗室擁有者可以允許參與者訪問。 參與者存取權限允許實驗室使用者僅在託管資源組中添加或刪除 Azure 資源。 它允許更輕鬆地跟蹤和管理，而不是允許使用者參與者訪問訂閱。

### <a name="automation"></a>自動化
自動化是大規模、有效生態系統的關鍵組成部分。 自動化對於跨訂閱和實驗室管理或跟蹤多個環境是必需的。

### <a name="cicd-pipeline"></a>CI/CD 管道
DevTest Labs 中的 PaaS 服務可通過在實驗室控制訪問的集中部署中説明改進 CI/CD 管道。

## <a name="next-steps"></a>後續步驟
有關環境的詳細資訊，請參閱以下文章： 

- 
- [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)
- [在 Azure 開發人員測試實驗室中使用自包含的服務結構群集創建環境](create-environment-service-fabric-cluster.md)
- [在 Azure 開發人員測試實驗室中將環境連接到實驗室的虛擬網路](connect-environment-lab-virtual-network.md)
- [將環境集成到 Azure DevOps CI/CD 管道中](integrate-environments-devops-pipeline.md)
 





