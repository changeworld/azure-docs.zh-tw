---
title: Azure DevTest Labs 資源的分散式合作開發
description: 提供設定分散式與共同作業開發環境以開發 DevTest Labs 資源的最佳作法。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c18bbbfd3ad727811cc28c424381e5caf32b1cfc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483800"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs 資源的分散式和共同作業開發最佳做法
分散式共同作業開發可讓不同的小組或人員開發和維護程式碼基底。 若要成功，開發程式取決於建立、共用和整合資訊的能力。 此主要開發原則可以在 Azure DevTest Labs 內使用。 實驗室中有數種類型的資源，通常會在企業內的不同實驗室之間散發。 不同類型的資源著重于兩個區域：

- 內部儲存在實驗室中的資源（以實驗室為基礎）
- 儲存在[連接至實驗室之外部存放庫](devtest-lab-add-artifact-repo.md)中的資源（以程式碼存放庫為基礎）。 

本檔說明一些最佳做法，允許跨多個小組進行共同作業和散佈，同時確保所有層級的自訂和品質。

## <a name="lab-based-resources"></a>以實驗室為基礎的資源

### <a name="custom-virtual-machine-images"></a>自訂虛擬機器映射
您可以使用自訂映射的常見來源，並于夜間部署至實驗室。 如需詳細資訊，請參閱[Image factory](image-factory-create.md)。    

### <a name="formulas"></a>公式
[公式](devtest-lab-manage-formulas.md)是實驗室特有的，而且沒有散發機制。 實驗室成員會執行公式的所有開發。 

## <a name="code-repository-based-resources"></a>以程式碼存放庫為基礎的資源
根據程式碼存放庫、成品和環境，有兩個不同的功能。 本文將探討這些功能，以及如何最有效地設定存放庫和工作流程，以允許在組織層級或小組層級自訂可用的成品和環境。  此工作流程是以標準[原始程式碼控制分支策略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)為基礎。 

### <a name="key-concepts"></a>重要概念
成品的來源資訊包含中繼資料、腳本。 環境的來源資訊包括具有任何支援檔案（例如 PowerShell 腳本、DSC 腳本、Zip 檔案等）的中繼資料和 Resource Manager 範本。  

### <a name="repository-structure"></a>存放庫結構  
原始程式碼控制（SCC）最常見的設定是設定多層結構，以便儲存在實驗室中使用的程式碼檔案（Resource Manager 範本、中繼資料和腳本）。 具體來說，您可以建立不同的存放庫來儲存不同企業層級所管理的資源：   

- 全公司資源。
- 業務單位/全部門資源
- 小組特有的資源。

這些層級都會連結至不同的存放庫，其中主要分支必須是生產環境的品質。 每個存放庫中的[分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops)都是用來開發這些特定資源（成品或範本）。 此結構適用于 DevTest Labs，因為您可以輕鬆地將多個存放庫和多個分支同時連接到組織的實驗室。 存放庫名稱會包含在使用者介面（UI）中，以避免在有相同名稱、描述和發行者時產生混淆。
     
下圖顯示兩個存放庫：由 IT 部門維護的公司儲存機制，以及 R&D 部門維護的分割儲存機制。

![分散式開發環境範例](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
此分層結構允許開發，同時在主要分支上保持較高的品質，同時將多個存放庫連線至實驗室，以提供更大的彈性。

## <a name="next-steps"></a>後續步驟    
查看下列文章：

- 使用[Azure 入口網站](devtest-lab-add-artifact-repo.md)或透過[Azure 資源管理範本](add-artifact-repository.md)，將存放庫新增至實驗室
- [DevTest Labs 成品](devtest-lab-artifact-author.md)
- [DevTest Labs 環境](devtest-lab-create-environment-from-arm.md)。
