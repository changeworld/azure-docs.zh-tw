---
title: Azure DevTest Labs 資源的分散式協作式開發
description: 提供設定分散式和共同作業開發環境的最佳作法，以開發 DevTest Labs 資源。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1ef6d7aa7d3cfd4fcc64eaa45259684dfcb9ccee
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592359"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs 資源的分散式和共同開發的最佳作法
分散式共同作業開發可讓不同的小組或人員開發和維護程式碼基底。 若要成功，開發程式取決於建立、共用和整合資訊的能力。 這項主要開發原則可以在 Azure DevTest Labs 中使用。 實驗室中有數種類型的資源，通常會在企業內的不同實驗室之間散發。 不同類型的資源著重于兩個區域：

- 內部儲存在實驗室中的資源 (以實驗室為基礎) 
- 儲存在外部存放庫中的資源， [會連線至實驗室 (程式](devtest-lab-add-artifact-repo.md) 代碼存放庫型) 。 

本檔說明允許跨多個小組共同作業和散發的一些最佳作法，同時確保所有層級的自訂和品質。

## <a name="lab-based-resources"></a>以實驗室為基礎的資源

### <a name="custom-virtual-machine-images"></a>自訂虛擬機器映射
您可以有一個常見的自訂映射來源，每夜部署到實驗室。 如需詳細資訊，請參閱 [Image factory](image-factory-create.md)。    

### <a name="formulas"></a>公式
[公式](devtest-lab-manage-formulas.md) 是實驗室特定的，而且沒有散發機制。 實驗室成員會進行公式的所有開發。 

## <a name="code-repository-based-resources"></a>以程式碼存放庫為基礎的資源
有兩個不同的功能是以程式碼存放庫、成品和環境為基礎。 本文將探討這些功能，以及如何以最有效率的方式設定存放庫和工作流程，讓您能夠自訂群組織層級或小組層級的可用成品和環境。  此工作流程是以標準 [來源程式碼控制分支策略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)為基礎。 

### <a name="key-concepts"></a>重要概念
構件的來源資訊包括中繼資料和腳本。 環境的來源資訊包括具有任何支援檔案的中繼資料和 Resource Manager 範本，例如 PowerShell 腳本、DSC 腳本、Zip 檔案等等。  

### <a name="repository-structure"></a>存放庫結構  
原始程式碼控制 (SCC) 最常見的設定，就是設定多層結構來儲存程式碼檔案 (Resource Manager 範本、中繼資料，以及在實驗室中使用的腳本) 。 具體而言，您可以建立不同的儲存機制來儲存由不同企業層級所管理的資源：   

- 全公司的資源。
- 營業單位/全部門資源
- 小組專屬資源。

每個層級都會連結至不同的存放庫，其中主要分支必須是生產品質。 每個存放庫中的 [分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) 都是用於開發這些特定資源 (成品或範本) 。 此結構適用于 DevTest Labs，因為您可以輕鬆地將多個存放庫和多個分支同時連接到組織的實驗室。 存放庫名稱會包含在使用者介面中 (UI) 以避免在名稱、描述和發行者相同時產生混淆。
     
下圖顯示兩個存放庫：由 IT 部門維護的公司存放庫，以及 R&D 部門所維護的部門存放庫。

![分散式開發環境範例](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
此分層結構允許開發，同時在主要分支上保持較高的品質等級，同時讓多個存放庫連線至實驗室，以提供更大的彈性。

## <a name="next-steps"></a>後續步驟    
查看下列文章：

- 使用[Azure 入口網站](devtest-lab-add-artifact-repo.md)或透過[Azure 資源管理範本](add-artifact-repository.md)，將存放庫新增至實驗室
- [DevTest Labs 構件](devtest-lab-artifact-author.md)
- [DevTest Labs 環境](devtest-lab-create-environment-from-arm.md)。
