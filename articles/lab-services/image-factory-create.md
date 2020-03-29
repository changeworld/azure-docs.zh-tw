---
title: 在 Azure 開發人員測試實驗室中創建映射工廠 |微軟文檔
description: 本文介紹如何使用 Git 存儲庫（Azure DevTest Labs）中提供的示例腳本來設置自訂映射工廠。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759443"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure 開發人員測試實驗室中創建自訂映射工廠
本文介紹如何使用[Git 存儲庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中的示例腳本來設置自訂映射工廠。

## <a name="whats-an-image-factory"></a>什麼是圖像工廠？
映射工廠是一種按代碼配置的解決方案，它定期使用所有所需的配置自動生成和分發映射。 映射工廠中的圖像始終處於最新狀態，一旦整個過程自動化，持續維護幾乎為零。 而且，由於所有必需的配置都已在映射中，因此在使用基本作業系統創建 VM 後，它節省了手動設定系統的時間。

在 DevTest Labs 中，將開發人員桌面安裝到就緒狀態的重要加速器是使用自訂映射。 自訂圖像的缺點是實驗室中還有額外的維護功能。 例如，產品試用版會隨著時間的推移過期（或）未應用新發佈的安全更新，這迫使我們定期刷新自訂映射。 使用映射工廠，您將映射的定義簽入到原始程式碼控制，並具有根據定義生成自訂映射的自動化過程。

該解決方案可實現從自訂映射創建虛擬機器的速度，同時消除額外的持續維護成本。 使用此解決方案，您可以自動創建自訂映射、將其分發到其他 DevTest 實驗室，並停用舊映射。 在下面的視頻中，您將瞭解映射工廠以及如何使用 DevTest Labs 實現映射工廠。  所有 Azure Powershell 腳本都免費提供，並位於[https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)此處： 。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>解決方案的高級視圖
該解決方案可實現從自訂映射創建虛擬機器的速度，同時消除額外的持續維護成本。 使用此解決方案，您可以自動創建自訂映射並將其分發到其他 DevTest 實驗室。 您可以使用 Azure DevOps（以前的視覺化工作室團隊服務）作為業務流程引擎，用於自動執行 DevTest 實驗室中的所有操作。

![解決方案的高級視圖](./media/create-image-factory/high-level-view-of-solution.png)

DevTest Labs 的[VSTS 擴展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)使您能夠執行以下各個步驟：

- 建立自訂影像
- 建立 VM
- 刪除 VM
- 創建環境
- 刪除環境
- 填充環境

使用 DevTest Labs 擴展是在 DevTest 實驗室中自動創建自訂映射的簡便方法。

對於更複雜的方案，使用 PowerShell 腳本有一個備用實現。 使用 PowerShell，您可以完全自動化基於 DevTest Labs 的圖像工廠，該實驗室可用於您的持續集成和連續交付 （CI/CD） 工具鏈。 此備用解決方案遵循的原則是：

- 常見更新不應對映射工廠進行任何更改。 （例如，添加新類型的自訂映射、自動停用舊映射、添加新的"終結點"DevTest Labs 以接收自訂映射等。
- 常見更改由原始程式碼控制（基礎結構作為代碼）支援
- 接收自訂映射的開發人員測試實驗室可能不在同一 Azure 訂閱中（實驗室跨訂閱）
- PowerShell 腳本必須是可重用的，以便我們可以根據需要啟動其他工廠

## <a name="next-steps"></a>後續步驟
繼續學習本節中的下一篇文章：從[Azure DevOps 運行映射工廠](image-factory-set-up-devops-lab.md)
