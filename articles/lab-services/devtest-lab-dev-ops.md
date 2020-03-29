---
title: 集成 Azure 開發人員測試實驗室和 DevOps |微軟文檔
description: 瞭解如何在企業環境中的連續集成 （CI）/連續交付 （CD） 管道中使用 Azure DevTest Labs 的實驗室。
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078920"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>集成 Azure 開發人員測試實驗室和 Azure 開發人員計畫
DevOps 是一種軟體發展方法，將軟體發展 （Dev） 與系統的操作 （Ops） 集成在一起。 此系統可能會根據業務目標提供新功能、更新和修復。 此方法涵蓋一切，包括根據目標、使用模式和客戶回函設計新功能;修復、恢復和強化系統時出現問題。 此方法的一個易於識別的元件是持續集成 （CI）/ 持續交付 （CD） 管道。 CI/CD 管道通過一系列步驟（包括生成、測試和部署）從提交中獲取資訊、代碼和資源，以生成系統。 本文重點介紹在企業環境中在管道中有效地使用實驗室的不同方法。 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>在 DevOps 工作流中使用實驗室的好處 

### <a name="focused-access"></a>重點訪問 
使用實驗室作為元件允許特定的生態系統與有限的群體相關聯。 通常，在公共區域或特定功能中工作的團隊或組將為其分配實驗室。   

### <a name="infrastructure-replication-in-the-cloud"></a>雲中的基礎架構複製 
開發人員可以快速設置開發生態系統，其中包含包含原始程式碼和工具的開發框。 開發人員還可以創建與生產配置幾乎相同的環境。 它有助於更快的內環路開發。 

### <a name="pre-production"></a>生產階段前 
在 CI/CD 管道中建立實驗室可以更輕鬆地讓多個不同的預生產環境或電腦同時運行以進行非同步測試。 可以在實驗室內部署和管理不同的支援基礎結構，如生成代理。 

## <a name="devops-with-devtest-labs"></a>使用開發人員測試實驗室進行開發 

### <a name="development--operation"></a>開發/運營 
實驗室應側重于在要素區域中工作的團隊。 此常見焦點允許共用特定于區域的資源，如工具、腳本或資源管理器範本。 它允許更快的更改，同時將負面影響限制為較小的組。 這些共用資源允許開發人員使用所有必要的代碼、工具和配置創建開發 VM。 它們可以動態創建，也可以有一個系統，使用自訂創建基本映射。 開發人員不僅可以創建 VM，還可以根據必要的範本創建 DevTest Labs 環境，在實驗室中創建適當的 Azure 資源。 任何更改或破壞性工作都可以針對實驗室環境進行，而不會影響其他人。 請考慮產品是安裝在客戶電腦上的獨立系統的情況。 在此方案中，DevTest Labs 改進了 VM 創建，包括使用工件安裝其他軟體，以及預構建客戶配置，以便更快地對代碼進行內部迴圈測試。 
  
## <a name="cicd-pipeline"></a>CI/CD 管道 
CI/CD 管道是 DevOps 中的關鍵元件之一，用於從開發人員的拉取請求中移動代碼，將其與現有代碼集成，並將其部署到生產生態系統。 所有資源不需要在實驗室內。 例如，可以在實驗室外部設置 Jenkins 主機，作為更持久的資源。 下面是將實驗室集成到管道中的一些具體示例。 

### <a name="build"></a>Build 
生成管道的重點是創建一個元件包，這些元件將一起測試，以便移交給發佈管道。 實驗室可以作為生成代理和其他支援資源的位置作為生成管道的一部分。 能夠動態構建基礎結構可以進行更大的控制。 借助在實驗室中具有多個環境的能力，可以在使用生成 ID 作為環境資訊的一部分時非同步運行每個生成，以唯一地標識特定生成的資源。   

對於生成代理，實驗室限制訪問的能力提高了安全性，並減少了意外損壞的可能性。  

### <a name="test"></a>測試 
DevTest Labs 允許 CI/CD 管道自動創建可用於自動和手動測試的 Azure 資源（VM、環境）。 VM 將使用使用生成過程中的資訊創建專案或公式來創建測試所需的不同自訂配置。   

### <a name="release"></a>版本 
在部署代碼之前，DevTest Labs 通常用於發佈部分的驗證。 它類似于在"生成"部分中的測試。 生產資源不應部署在 DevTest 實驗室中。 

### <a name="customization"></a>自訂 
在 Azure DevOps 中，存在允許在特定實驗室中操作 VM 和環境的現有任務。 雖然 Azure DevOps 服務是管理 CI/CD 管道的一種方式，但您可以將實驗室集成到支援調用 REST API、執行 PowerShell 腳本或使用 Azure CLI 的任何系統。 

雖然某些 CI/CD 管道管理器具有現有的開源外掛程式，可以管理 Azure 和 DevTest 實驗室中的資源。 您可能需要使用一些自訂腳本來滿足管道的特定需求。  考慮到這一點，在執行任務時，服務主體與適當的角色一起使用，以獲得對實驗室的訪問。 服務主體通常需要對實驗室的參與者角色存取權限。 有關詳細資訊，請參閱將[Azure 開發人員測試實驗室集成到 Azure DevOps 持續集成和交付管道中](devtest-lab-integrate-ci-cd-vsts.md)。 
 