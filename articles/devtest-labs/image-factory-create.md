---
title: 在 Azure DevTest Labs 中建立映射 factory |Microsoft Docs
description: 本文說明如何使用 Git 存放庫（Azure DevTest Labs）中提供的範例腳本來設定自訂映射 factory。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482066"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中建立自訂映射 factory
本文說明如何使用[Git 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中提供的範例腳本來設定自訂映射 factory。

## <a name="whats-an-image-factory"></a>什麼是映射 factory？
映射處理站是一種設定即程式碼的解決方案，會定期自動建立和散發影像，並具有所有所需的設定。 映射處理站中的影像一律是最新的，而且在整個程式自動化後，進行中的維護幾乎是零。 而且，由於所有必要的設定都已在映射中，因此在使用基底 OS 建立 VM 之後，會節省手動設定系統的時間。

在 DevTest Labs 中讓開發人員桌面進入就緒狀態的重要加速器，就是使用自訂映射。 自訂映射的缺點在於實驗室中有額外的維護。 例如，產品的試用版會在一段時間後過期（或）不會套用新發行的安全性更新，這會強制我們定期重新整理自訂映射。 使用映射處理站時，您可以在原始程式碼控制中簽入影像的定義，並具有根據定義產生自訂映射的自動化流程。

此解決方案可讓您從自訂映射建立虛擬機器的速度，同時排除額外的持續維護成本。 透過此解決方案，您可以自動建立自訂映射、將其散發給其他 DevTest Labs，以及淘汰舊的映射。 在下列影片中，您將瞭解映射處理站，以及如何使用 DevTest Labs 來實現它。  所有 Azure Powershell 腳本都可免費使用，位於此處： [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) 。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>解決方案的高階觀點
此解決方案可讓您從自訂映射建立虛擬機器的速度，同時排除額外的持續維護成本。 透過此解決方案，您可以自動建立自訂映射，並將其散發給其他 DevTest Labs。 您可以使用 Azure DevOps （之前稱為 Visual Studio Team Services）做為協調流程引擎，以便將 DevTest Labs 中的所有作業自動化。

![解決方案的高階觀點](./media/create-image-factory/high-level-view-of-solution.png)

有一個[適用于 DevTest Labs 的 VSTS 延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)模組，可讓您執行下列個別步驟：

- 建立自訂影像
- 建立 VM
- 刪除 VM
- 建立環境
- 刪除環境
- 填入環境

使用 DevTest Labs 延伸模組是一種簡單的方式，可讓您在 DevTest Labs 中自動建立自訂映射。

有一個替代的執行方式，使用 PowerShell 腳本來進行更複雜的案例。 使用 PowerShell，您可以根據可在持續整合和持續傳遞（CI/CD）工具鏈中使用的 DevTest Labs，將映射工廠完全自動化。 此替代解決方案中所遵循的原則包括：

- 一般更新不需要變更映射 factory。 （例如，新增新的自訂映射類型、自動淘汰舊的映射、新增「端點」 DevTest Labs 以接收自訂映射等等）。
- 一般變更受到原始程式碼控制（基礎結構即程式碼）的支援
- 接收自訂映射的 DevTest Labs 可能不在相同的 Azure 訂用帳戶中（實驗室 span 訂閱）
- PowerShell 腳本必須可重複使用，因此我們可以視需要啟動其他工廠

## <a name="next-steps"></a>後續步驟
繼續閱讀本節的下一篇文章：[從 Azure DevOps 執行映射 factory](image-factory-set-up-devops-lab.md)
