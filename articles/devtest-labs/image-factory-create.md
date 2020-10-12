---
title: 在 Azure DevTest Labs 中建立映射 factory |Microsoft Docs
description: 本文說明如何使用 Git 存放庫中提供的範例腳本 (Azure DevTest Labs) 來設定自訂映射 factory。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482066"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中建立自訂映射 factory
本文說明如何使用 [Git 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中提供的範例腳本，來設定自訂映射 factory。

## <a name="whats-an-image-factory"></a>什麼是映射處理站？
映射處理站是一種設定即程式碼解決方案，會定期以所有所需的設定來自動建立和發佈映射。 映射處理站中的影像一律為最新狀態，而且在整個程式自動化之後，進行中的維護幾乎為零。 而且，因為所有必要的設定都已在映射中，所以在使用基底 OS 建立 VM 之後，就可以節省手動設定系統的時間。

在 DevTest Labs 中讓開發人員桌面進入就緒狀態的重要加速器是使用自訂映射。 自訂映射的缺點是在實驗室中有一些額外的維護。 例如，產品的試用版會在一段時間後過期 (或) 未套用新發行的安全性更新，這會強制我們定期重新整理自訂映射。 使用映射處理站時，您會有已簽入原始程式碼控制的影像定義，並且具有自動化的程式，可根據定義產生自訂映射。

此解決方案可讓您從自訂映射建立虛擬機器，同時消除額外的持續維護成本。 透過此解決方案，您可以自動建立自訂映射、將其散發給其他 DevTest 實驗室，以及淘汰舊的映射。 在下列影片中，您將瞭解映射處理站，以及它如何使用 DevTest Labs 來執行。  所有 Azure Powershell 腳本都可免費使用，位於：  [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) 。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>解決方案的高階觀點
此解決方案可讓您從自訂映射建立虛擬機器，同時消除額外的持續維護成本。 透過此解決方案，您可以自動建立自訂映射，並將其散發給其他 DevTest Labs。 您可以使用 Azure DevOps (先前 Visual Studio Team Services) 做為協調流程引擎，以將 DevTest Labs 中的所有作業自動化。

![解決方案的高階觀點](./media/create-image-factory/high-level-view-of-solution.png)

[DevTest Labs 有一個 VSTS 延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)模組，可讓您執行下列個別步驟：

- 建立自訂影像
- 建立 VM
- 刪除 VM
- 建立環境
- 刪除環境
- 填入環境

使用 DevTest Labs 擴充功能可讓您輕鬆地開始在 DevTest Labs 中自動建立自訂映射。

針對更複雜的案例，使用 PowerShell 腳本有替代的執行。 您可以使用 PowerShell，根據可在持續整合和持續傳遞 (CI/CD) 工具鏈中使用的 DevTest Labs，將映射 factory 完全自動化。 遵循此替代解決方案的原則如下：

- 一般更新不需要變更映射 factory。  (例如，加入新的自訂映射類型、自動淘汰舊映射、新增 ' endpoint ' DevTest Labs 以接收自訂映射等等。 ) 
-  (基礎結構即程式碼的原始程式碼控制支援常見的變更) 
- 接收自訂映射的 DevTest Labs 可能不在相同的 Azure 訂用帳戶中 (labs 跨訂用帳戶) 
- PowerShell 腳本必須可重複使用，因此我們可以視需要啟動額外的 factory

## <a name="next-steps"></a>接下來的步驟
移至本節的下一篇文章： [從 Azure DevOps 執行映射 factory](image-factory-set-up-devops-lab.md)
