---
title: Azure 開發人員測試實驗室資源的分散式協作開發
description: 提供用於設置分散式和協作開發環境以開發 DevTest Labs 資源的最佳做法。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170114"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure 開發人員測試實驗室資源的分散式和協作開發最佳實踐
分散式協作開發允許不同的團隊或人員開發和維護代碼庫。 要取得成功，開發過程取決於創建、共用和集成資訊的能力。 此關鍵開發原則可在 Azure 開發人員測試實驗室中使用。 實驗室中有幾種類型的資源通常分佈在企業內的不同實驗室之間。 不同類型的資源集中在兩個領域：

- 內部存儲在實驗室內的資源（基於實驗室）
- 存儲在[連接到實驗室的外部存儲庫](devtest-lab-add-artifact-repo.md)中的資源（基於代碼存儲庫）。 

本文檔介紹了一些最佳實踐，這些最佳實踐允許跨多個團隊進行協作和分發，同時確保所有級別的自訂和品質。

## <a name="lab-based-resources"></a>基於實驗室的資源

### <a name="custom-virtual-machine-images"></a>自訂虛擬機器映射
您可以擁有每晚部署到實驗室的自訂映射的常見源。 有關詳細資訊，請參閱[映射工廠](image-factory-create.md)。    

### <a name="formulas"></a>公式
[公式](devtest-lab-manage-formulas.md)特定于實驗室，沒有分佈機制。 實驗成員做公式的所有開發。 

## <a name="code-repository-based-resources"></a>基於代碼存儲庫的資源
有兩個不同的功能基於代碼存儲庫、工件和環境。 本文介紹了這些功能以及如何最有效地設置存儲庫和工作流，以便能夠在組織級別或團隊級別自訂可用的工件和環境。  此工作流基於標準[原始程式碼控制分支策略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)。 

### <a name="key-concepts"></a>重要概念
工件的源資訊包括中繼資料和腳本。 環境的源資訊包括中繼資料和資源管理器範本，其中包含任何支援檔，如 PowerShell 腳本、DSC 腳本、Zip 檔等。  

### <a name="repository-structure"></a>存放庫結構  
原始程式碼管理 （SCC） 的最常見配置是設置多層結構，用於存儲實驗室中使用的代碼檔（資源管理器範本、中繼資料和腳本）。 具體而言，創建不同的存儲庫來存儲由不同業務級別管理的資源：   

- 全公司資源。
- 業務單位/全部門資源
- 特定于團隊的資源。

每個級別連結到不同的存儲庫，其中主分支需要具有生產品質。 每個存儲庫中的[分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops)將用於開發這些特定資源（工件或範本）。 此結構與 DevTest Labs 很好地一致，因為您可以輕鬆地同時將多個存儲庫和多個分支連接到組織的實驗室。 存儲庫名稱包含在使用者介面 （UI） 中，以避免在名稱、說明和發行者相同時出現混淆。
     
下圖顯示了兩個存儲庫：由 IT 部門維護的公司存儲庫和由 R&D 部門維護的部門存儲庫。

![分配和協作開發環境示例](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
這種分層結構允許開發，同時在主分支保持更高級別的品質，同時將多個存儲庫連接到實驗室，從而獲得更大的靈活性。

## <a name="next-steps"></a>後續步驟    
查看下列文章：

- 使用[Azure 門戶](devtest-lab-add-artifact-repo.md)或通過[Azure 資源管理範本](add-artifact-repository.md)將存儲庫添加到實驗室
- [開發人員測試實驗室專案](devtest-lab-artifact-author.md)
- [開發人員測試實驗室環境](devtest-lab-create-environment-from-arm.md)。
