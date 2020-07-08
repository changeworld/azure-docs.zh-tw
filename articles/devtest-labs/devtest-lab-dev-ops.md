---
title: Azure DevTest Labs 與 DevOps 的整合 |Microsoft Docs
description: 瞭解如何在企業環境中的持續整合（CI）/持續傳遞（CD）管線內使用 Azure DevTest Labs 的實驗室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8a5d35a541e079b7d39cae2ec43da608274533f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85481063"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Azure DevTest Labs 和 Azure DevOps 的整合
DevOps 是一種軟體發展方法，可將軟體發展（Dev）與系統的作業（Ops）整合在一起。 此系統可提供新的功能、更新和修正，以符合商務目標。 此方法包含根據目標設計新功能、使用模式和客戶意見反應的所有內容;在發生問題時修正、復原和強化系統。 此方法的一個容易識別的元件是持續整合（CI）/持續傳遞（CD）管線。 CI/CD 管線會透過一系列的步驟，從認可中取得資訊、程式碼和資源，包括建立、測試和部署，以產生系統。 本文著重于在企業環境的管線中有效使用實驗室的不同方式。 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>在 DevOps 工作流程中使用實驗室的優點 

### <a name="focused-access"></a>焦點存取 
使用實驗室做為元件，可讓特定生態系統與一組有限的人員建立關聯。 通常，在通用區域或特定功能中工作的小組或群組，將會獲派實驗室。   

### <a name="infrastructure-replication-in-the-cloud"></a>雲端中的基礎結構複寫 
開發人員可以快速地設定開發生態系統，其中包含具有原始程式碼和工具的 dev box。 開發人員也可以建立與生產設定幾乎完全相同的環境。 它有助於加速內部迴圈開發。 

### <a name="pre-production"></a>生產階段前 
在 CI/CD 管線中擁有實驗室，可以更輕鬆地讓多個不同的進入生產階段前環境或機器同時執行，以進行非同步測試。 您可以在實驗室中部署和管理不同的支援基礎結構，例如組建代理程式。 

## <a name="devops-with-devtest-labs"></a>DevOps 與 DevTest Labs 

### <a name="development--operation"></a>開發/作業 
實驗室應著重于在功能區中工作的小組。 這種常見的焦點允許共用區域特定資源，例如工具、腳本或 Resource Manager 範本。 它允許更快速的變更，同時將負面影響限制為較小的群組。 這些共用的資源可讓開發人員使用所有必要的程式碼、工具和設定來建立開發 Vm。 它們可以動態建立，或具有系統來建立具有自訂的基底映射。 開發人員不僅可以建立 Vm，也可以根據必要的範本建立 DevTest 實驗室環境，以在實驗室中建立適當的 Azure 資源。 任何變更或破壞性的工作都可以針對實驗室環境進行，而不會影響其他人。 請考慮產品是安裝在客戶電腦上的獨立系統案例。 在此案例中，DevTest Labs 已改善 VM 建立，包括使用成品安裝其他軟體，以及預先建立的客戶設定，以更快速地進行程式碼的內部迴圈測試。 
  
## <a name="cicd-pipeline"></a>CI/CD 管線 
CI/CD 管線是 DevOps 中的其中一個重要元件，可從開發人員的提取要求移動程式碼、將其與現有的程式碼整合，並將其部署到生產環境生態系統。 所有資源都不需要在實驗室內。 例如，Jenkins 主機可以在實驗室外部設定為更持久的資源。 以下是將實驗室整合到管線中的一些特定範例。 

### <a name="build"></a>組建 
組建管線的重點在於建立元件套件，這些元件會一起測試，以交給發行管線。 實驗室可以是組建管線的一部分，做為組建代理程式和其他支援資源的位置。 能夠以動態方式建立基礎結構，讓您有更大的控制權。 有了實驗室中的多個環境的功能，每個組建都可以非同步執行，同時使用組建識別碼作為環境資訊的一部分，以唯一識別特定組建的資源。   

針對組建代理程式，實驗室限制存取的能力會增加安全性，並降低意外損毀的可能性。  

### <a name="test"></a>測試 
DevTest Labs 可讓 CI/CD 管線自動建立可用於自動化和手動測試的 Azure 資源（Vm、環境）。 您可以使用成品或公式來建立 Vm，以使用組建程式中的資訊來建立測試所需的不同自訂設定。   

### <a name="release"></a>版本 
DevTest Labs 通常用於在部署程式碼之前的 [發行] 區段中進行驗證。 這類似于 [組建] 區段中的測試。 生產資源不應部署在 DevTest Labs 中。 

### <a name="customization"></a>自訂 
在 Azure DevOps 中，有可讓您在特定實驗室中操作 Vm 和環境的現有工作。 雖然 Azure DevOps Services 是管理 CI/CD 管線的一種方式，但您可以將實驗室整合到任何支援呼叫 REST Api、執行 PowerShell 腳本或使用 Azure CLI 之功能的系統。 

雖然有些 CI/CD 管線管理員具有現有的開放原始碼外掛程式，可以管理 Azure 和 DevTest Labs 中的資源。 您可能需要使用一些自訂腳本，以符合管線的特定需求。  考慮到這一點，在執行工作時，服務主體會與適當的角色搭配使用，以取得實驗室的存取權。 服務主體通常需要實驗室的「參與者」角色存取權。 如需詳細資訊，請參閱[將 Azure DevTests Labs 整合到您的 Azure DevOps 持續整合和傳遞管線](devtest-lab-integrate-ci-cd.md)。 
 