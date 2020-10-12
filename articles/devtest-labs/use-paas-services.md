---
title: 在 Azure DevTest Labs 中使用平臺即服務 (PaaS) 服務
description: 瞭解如何使用平臺即服務 (在 Azure DevTest Labs 中傳遞) 服務。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478734"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中使用平臺即服務 (PaaS) 服務
DevTest Labs 透過「環境」功能支援 PaaS。 在 Git 存放庫中預先設定的 Azure Resource Manager 範本支援 DevTest Labs 中的環境。 環境可以同時包含 PaaS 和 IaaS 資源。 它們可讓您建立複雜的系統，其中包含 Azure 資源（例如虛擬機器、資料庫、虛擬網路和 Web 應用程式），這些資源已自訂為可一起運作。 這些範本可讓您使用原始程式碼控制來進行一致的部署和改良的環境管理。 

適當設定的系統可允許下列案例： 

- 開發人員擁有獨立和多個環境
- 以非同步方式測試不同的設定
- 無需任何範本變更即可整合到預備和生產管線
- 在相同的實驗室內同時擁有開發電腦和環境，可改善管理和成本報告的便利性。  

DevTest Labs 資源提供者會代表實驗室使用者建立資源，因此不需要提供額外的許可權給實驗室使用者，就能使用 PaaS 資源。 下圖顯示 Service Fabric 叢集作為實驗室中的環境。

![Service Fabric 叢集作為環境](./media/create-environment-service-fabric-cluster/cluster-created.png)

如需設定環境的詳細資訊，請參閱 [使用 Azure Resource Manager 範本建立多部 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。 DevTest Labs 有 Azure Resource Manager 範本的公用存放庫，您可以用來建立環境，而不需要自己連接到外部的 GitHub 來源。 如需公用環境的詳細資訊，請參閱 [在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)。

在大型組織中，開發小組通常會提供像是自訂/隔離測試環境的環境。 您可能會有一個環境，可供業務單位或部門內的所有小組使用。 IT 小組可能會想要提供組織中所有小組都可使用的環境。  

## <a name="customizations"></a>自訂

#### <a name="sandbox"></a>沙箱 
實驗室擁有者可以自訂實驗室環境，以將使用者的角色從「 **讀者** 」變更為資源群組內的「 **參與者** 」。 這項功能位於實驗室設定**和原則**的 [**實驗室設定**] 頁面中。 這項角色變更可讓使用者新增或移除該環境內的資源。 如果您想要進一步限制存取權，請使用 Azure 原則。 此功能可讓您自訂資源或設定，而不需要在訂用帳戶層級存取。

#### <a name="custom-tokens"></a>自訂權杖
資源群組之外有一些自訂實驗室資訊，而且是範本可以存取的環境特有的。 以下說明其中一部分： 

- 實驗室網路識別
- Location
- 儲存 Resource Manager 範本檔案的儲存體帳戶。 
 
#### <a name="lab-virtual-network"></a>實驗室虛擬網路
將 [環境連線到實驗室的虛擬網路](connect-environment-lab-virtual-network.md) 文章會說明如何修改您的 Resource Manager 範本以使用 `$(LabSubnetId)` 權杖。 建立環境時，會將 `$(LabSubnetId)` 權杖取代為第一個子網標示，其中 [ **在虛擬機器建立時使用** ] 選項設定為 [ **true**]。 它可讓我們的環境使用先前建立的網路。 如果您想要在測試環境中使用相同的 Resource Manager 範本作為預備和生產環境，請 `$(LabSubnetId)` 在 Resource Manager 範本參數中使用做為預設值。 

#### <a name="environment-storage-account"></a>環境儲存體帳戶
DevTest Labs 支援使用 [嵌套 Resource Manager 範本](../azure-resource-manager/templates/linked-templates.md)。 [[部署適用于測試環境的嵌套 Azure Resource Manager 範本](deploy-nested-template-environments.md) ] 一文說明如何使用  `_artifactsLocation` 和 `_artifactsLocationSasToken` 權杖，在與主要範本的嵌套資料夾相同的資料夾中，建立 Resource Manager 範本的 URI。 如需這兩個權杖的詳細資訊，請參閱[Azure Resource Manager-最佳作法指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)的**部署**成品一節。

## <a name="user-experience"></a>使用者體驗

## <a name="developer"></a>開發人員
開發人員會使用相同的工作流程來建立 VM，以建立特定的環境。 他們會選取環境與電腦映射，並輸入範本所需的必要資訊。 擁有環境的每個開發人員都允許部署變更和改善的內部迴圈偵錯工具。 您可以隨時使用最新的範本來建立環境。  這項功能可讓您終結及重新建立環境，以協助減少必須手動建立系統或從錯誤測試復原的停機時間。  

### <a name="testing"></a>測試
DevTest Labs 環境可讓您以非同步方式獨立測試特定程式碼和設定。 常見的作法是使用個別提取要求中的程式碼來設定環境，並啟動任何自動化測試。 一旦自動化測試完成執行後，就可以針對特定環境執行任何手動測試。 此程式通常是在 CI/CD 管線中完成。 

## <a name="management-experience"></a>管理體驗

### <a name="cost-tracking"></a>成本追蹤
成本追蹤功能包含不同環境內的 Azure 資源，作為整體成本趨勢的一部分。 依資源的成本不會細分環境內的不同資源，但會將環境顯示為單一成本。

### <a name="security"></a>安全性
使用 DevTest Labs 適當設定的 Azure 訂用帳戶，只能 [透過實驗室來限制對 azure 資源的存取](devtest-lab-add-devtest-user.md)。 使用環境時，實驗室擁有者可讓使用者使用已核准的設定來存取 PaaS 資源，而不允許存取任何其他 Azure 資源。 在實驗室使用者自訂環境的案例中，實驗室擁有者可以允許參與者存取權。 參與者存取權可讓實驗室使用者只在受控資源群組內新增或移除 Azure 資源。 它可讓您更輕鬆地追蹤及管理，而不允許使用者參與者存取訂用帳戶。

### <a name="automation"></a>自動化
自動化是大規模、有效生態系統的關鍵元件。 需要自動化，才能處理跨訂用帳戶和實驗室的多重環境管理或追蹤。

### <a name="cicd-pipeline"></a>CI/CD 管線
DevTest Labs 中的 PaaS 服務可以藉由取得實驗室所控制存取權的集中部署，協助改進 CI/CD 管線。

## <a name="next-steps"></a>接下來的步驟
如需環境的詳細資訊，請參閱下列文章： 

- 
- [使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [在 Azure DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)
- [在 Azure DevTest Labs 中建立具有獨立 Service Fabric 叢集的環境](create-environment-service-fabric-cluster.md)
- [在 Azure DevTest Labs 中將環境連線到實驗室的虛擬網路](connect-environment-lab-virtual-network.md)
- [將環境整合到您的 Azure DevOps CI/CD 管線](integrate-environments-devops-pipeline.md)
 





