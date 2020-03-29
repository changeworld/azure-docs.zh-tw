---
title: 在 Azure 開發人員測試實驗室中設置保留原則 |微軟文檔
description: 瞭解如何配置保留原則、清理工廠並從 DevTest 實驗室停用舊映射。
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
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759409"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>在 Azure 開發人員測試實驗室中設置保留原則
本文介紹設置保留原則、清理工廠以及從組織中的所有其他 DevTest 實驗室停用舊映射。 

## <a name="prerequisites"></a>Prerequisites
在繼續之前，請確保您已遵循了這些文章：

- [建立映像處理站](image-factory-create.md)
- [從 Azure DevOps 執行映像處理站](image-factory-set-up-devops-lab.md)
- [保存自訂映射並分發到多個實驗室](image-factory-save-distribute-custom-images.md)

以下專案應已經到位：

- Azure 開發人員測試實驗室中映射工廠的實驗室
- 一個或多個目標 Azure 開發人員測試實驗室，工廠將在其中分發金色圖像
- 用於自動執行映射工廠的 Azure DevOps 專案。
- 包含腳本和配置的原始程式碼位置（在我們的示例中，在上面使用的同一 DevOps 專案中）
- 用於協調 Azure Powershell 任務組建定義
 
## <a name="setting-the-retention-policy"></a>設置保留原則
在配置清理步驟之前，請定義要在 DevTest 實驗室中保留多少歷史圖像。 當您遵循 Azure DevOps 一文中的["運行映射工廠"](image-factory-set-up-devops-lab.md)時，將配置各種生成變數。 其中之一是**圖像保留**。 將此變數設置為`1`，這意味著 DevTest 實驗室不會維護自訂映射的歷史記錄。 只有最新的分散式圖像可用。 如果將此變數更改為`2`，將保留最新的分散式圖像以及以前的映射。 您可以設置此值以定義您希望在 DevTest Labs 中維護的歷史圖像數。

## <a name="cleaning-up-the-factory"></a>清理工廠
清理工廠的第一步是從映射工廠中刪除金色圖像 VM。 有一個腳本可以像我們以前的腳本一樣執行此任務。 第一步是向組建定義添加另一個**Azure Powershell**任務，如下圖所示：

![電源外殼步驟](./media/set-retention-policy-cleanup/powershell-step.png)

在清單中有新任務後，選擇該專案並填寫如下圖像所示的所有詳細資訊：

![清理舊映射 PowerShell 任務](./media/set-retention-policy-cleanup/configure-powershell-task.png)

腳本參數為： `-DevTestLabName $(devTestLabName)`。

## <a name="retire-old-images"></a>停用舊圖像 
此任務刪除任何舊映射，僅保留與 Image**保留**生成變數匹配的歷史記錄。 向組建定義添加其他**Azure Powershell**生成任務。 添加任務後，選擇任務並填寫下圖所示的詳細資訊： 

![停用舊映射 PowerShell 任務](./media/set-retention-policy-cleanup/retire-old-image-task.png)

腳本參數為：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>對生成進行排隊
現在已完成組建定義，可以排隊生成，以確保一切正常工作。 生成成功完成後，新的自訂映射顯示在目標實驗室中，如果檢查映射工廠實驗室，則看不到預配的 VM。 此外，如果進一步排隊生成，您將看到清理任務根據生成變數中設置的保留值從 DevTest Labs 中刪除舊的自訂映射。

> [!NOTE]
> 如果在本系列最後一篇文章的末尾執行了生成管道，請手動刪除在映射工廠實驗室中創建的虛擬機器，然後再排隊生成。  僅在我們設置所有內容並驗證其工作時，才需要手動清理步驟。



## <a name="summary"></a>總結
現在，您擁有了一個正在運行的映射工廠，可按需生成自訂映射並將其分發到實驗室。 此時，只需正確設置圖像並識別目標實驗室。 如上一篇文章所述，位於 **"配置"** 資料夾中的**Labs.json**檔指定每個目標實驗室都應提供哪些圖像。 當您向組織添加其他 DevTest 實驗室時，只需在新實驗室的 Labs.json 中添加一個條目。

向工廠添加新映射也很簡單。 如果要在工廠中包含新映射，請打開[Azure 門戶](https://portal.azure.com)，導航到工廠 DevTest Labs，選擇按鈕添加 VM，然後選擇所需的市場映射和專案。 選擇 **"查看 Azure 資源管理器"範本**而不是選擇 **"創建**"按鈕，然後將範本另存為存儲庫中**GoldenImages**資料夾中的 .json 檔。 下次運行映射工廠時，它將創建自訂映射。


## <a name="next-steps"></a>後續步驟
1. [計畫生成/發佈](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer)定期運行映射工廠。 它定期刷新工廠生成的映射。
2. 為您的工廠製作更多金色圖像。 您還可以考慮[創建專案](devtest-lab-artifact-author.md)來編寫 VM 設置任務的其他部分，並在原廠映像中包括工件。
4. 創建[單獨的生成/版本](/azure/devops/pipelines/overview?view=azure-devops-2019)以單獨運行**分發圖像**腳本。 當您對 Labs.json 進行更改時，可以運行此腳本，並將圖像複製到目標實驗室，而無需再次重新創建所有圖像。

