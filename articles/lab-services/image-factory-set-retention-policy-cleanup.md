---
title: 在 Azure DevTest Labs 中設定保留原則 |Microsoft Docs
description: 瞭解如何設定保留原則、清除處理站，以及從 DevTest Labs 淘汰舊的映射。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759409"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定保留原則
本文涵蓋設定保留原則、清除處理站，以及淘汰組織中所有其他 DevTest 實驗室的舊映射。 

## <a name="prerequisites"></a>先決條件
請先確定您已遵循這些文章，再繼續進行：

- [建立映像處理站](image-factory-create.md)
- [從 Azure DevOps 執行映像處理站](image-factory-set-up-devops-lab.md)
- [儲存自訂映像並散發至多個實驗室](image-factory-save-distribute-custom-images.md)

下列專案應該已經準備就緒：

- Azure DevTest Labs 中映射 factory 的實驗室
- 一或多個目標 Azure DevTest Labs，factory 會在其中散發黃金影像
- 用來自動執行映射 factory 的 Azure DevOps 專案。
- 包含腳本和設定的原始程式碼位置（在我們的範例中，位於上面使用的相同 DevOps 專案中）
- 用來協調 Azure Powershell 工作的組建定義
 
## <a name="setting-the-retention-policy"></a>設定保留原則
設定清除步驟之前，請定義您想要保留在 DevTest Labs 中的歷史映射數目。 當您遵循[從 Azure DevOps 執行映射](image-factory-set-up-devops-lab.md)處理站一文時，您已設定了各種組建變數。 其中一個是**ImageRetention**。 您可以將此變數`1`設定為，這表示 DevTest Labs 不會維護自訂映射的歷程記錄。 只會提供最新的分散式映射。 如果您將此變數變更`2`為，則會保留最新的分散式映射加上先前的影像。 您可以設定此值，以定義您想要在 DevTest Labs 中維護的歷史映射數目。

## <a name="cleaning-up-the-factory"></a>清理 factory
清除處理站的第一個步驟是從映射處理站移除黃金映射 Vm。 執行此工作的腳本與先前的腳本一樣。 第一個步驟是將另一個**Azure Powershell**工作新增至組建定義，如下圖所示：

![PowerShell 步驟](./media/set-retention-policy-cleanup/powershell-step.png)

一旦您在清單中有新的工作，請選取該專案，然後填入所有詳細資料，如下圖所示：

![清除舊映射 PowerShell 工作](./media/set-retention-policy-cleanup/configure-powershell-task.png)

腳本參數為： `-DevTestLabName $(devTestLabName)`。

## <a name="retire-old-images"></a>淘汰舊的映射 
此工作會移除任何舊的映射，只保留符合**ImageRetention**組建變數的歷程記錄。 將額外的**Azure Powershell**組建工作新增至我們的組建定義。 新增之後，請選取工作，然後填入詳細資料，如下圖所示： 

![淘汰舊映射 PowerShell 工作](./media/set-retention-policy-cleanup/retire-old-image-task.png)

腳本參數為：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>將組建排到佇列
既然您已完成組建定義，請將新的組建排入佇列，以確保所有專案都能正常運作。 成功完成組建之後，新的自訂映射會顯示在目的地實驗室中，如果您檢查映射工廠實驗室，就會看到沒有任何已布建的 Vm。 此外，如果您將進一步的組建排入佇列，您會看到清除工作根據組建變數中設定的保留值，從 DevTest Labs 淘汰舊的自訂映射。

> [!NOTE]
> 如果您已在系列的最後一篇文章結尾執行組建管線，請在將新組建排入佇列之前，手動刪除在映射工廠實驗室中建立的虛擬機器。  只有在我們設定所有專案並確認其運作正常時，才需要手動清除步驟。



## <a name="summary"></a>[摘要]
現在您有一個執行中的映射處理站，可依需求產生自訂映射並將其散發給您的實驗室。 此時，只是要讓您的映射正確設定，並識別目標實驗室。 如前一篇文章中所述，位於您的**Configuration**資料夾中的**Labs. json**檔案會指定每個目標實驗室中應該提供哪些映射。 當您將其他 DevTest Labs 新增至您的組織時，您只需要在新實驗室的 Labs 中新增一個專案。

將新的影像加入至您的處理站也很簡單。 當您想要在 factory 中包含新的映射時，請開啟 [ [Azure 入口網站](https://portal.azure.com)]，流覽至您的 Factory DevTest 實驗室，選取 [新增 VM] 按鈕，然後選擇所需的 marketplace 映射和構件。 若不選取 [**建立**] 按鈕來建立新的 VM，請選取 [ **View Azure Resource Manager template**]，然後將範本儲存為 json 檔案，並將其儲存在存放庫中**GoldenImages**資料夾內的某個位置。 下一次執行映射處理站時，它會建立您的自訂映射。


## <a name="next-steps"></a>後續步驟
1. [排程組建/發行](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer)以定期執行映射處理站。 它會定期重新整理您的 factory 產生映射。
2. 為您的工廠製作更多黃金映射。 您也可以考慮[建立](devtest-lab-artifact-author.md)成品來編寫 VM 設定工作的其他部分，並在原廠映射中包含構件。
4. 建立[個別的組建/版本](/azure/devops/pipelines/overview?view=azure-devops-2019)以分別執行**DistributeImages**腳本。 當您變更 Labs. json 並取得複製到目標實驗室的影像，而不需要重新建立所有映射時，您可以執行此腳本。

