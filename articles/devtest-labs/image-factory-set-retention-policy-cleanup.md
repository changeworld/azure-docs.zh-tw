---
title: 在 Azure DevTest Labs 中設定保留原則 |Microsoft Docs
description: 瞭解如何設定保留原則、清除工廠，以及淘汰 DevTest Labs 的舊映射。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 115fdff215399a9a51171161191ecf5009e8e20e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476048"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定保留原則
本文涵蓋設定保留原則、清除工廠，以及淘汰組織中所有其他 DevTest Labs 的舊映射。 

## <a name="prerequisites"></a>Prerequisites
繼續進行之前，請先確定您已遵循這些文章：

- [建立映像處理站](image-factory-create.md)
- [從 Azure DevOps 執行映像處理站](image-factory-set-up-devops-lab.md)
- [儲存自訂映像並散發至多個實驗室](image-factory-save-distribute-custom-images.md)

下列專案應該已準備就緒：

- Azure DevTest Labs 中映射 factory 的實驗室
- 一或多個目標 Azure DevTest Labs，其會將黃金映射散發給工廠
- 用來自動化映射 factory 的 Azure DevOps 專案。
- 在我們的範例中，包含腳本和設定 (的原始程式碼位置，在上述的相同 DevOps 專案中) 
- 用來協調 Azure Powershell 工作的組建定義
 
## <a name="setting-the-retention-policy"></a>設定保留原則
設定清除步驟之前，請先定義您想要保留在 DevTest Labs 中的歷史映射數目。 當您遵循 [Azure DevOps 文章執行映射](image-factory-set-up-devops-lab.md) 處理站時，就會設定各種組建變數。 其中一個是 **ImageRetention**。 您可以將此變數設為 `1` ，這表示 DevTest Labs 不會維護自訂映射的歷程記錄。 只有最新的分散式映射可供使用。 如果您將此變數變更為 `2` ，則會保留最新的分散式映射加上先前的映射。 您可以設定此值，以定義您想要在 DevTest Labs 中維護的歷史映射數目。

## <a name="cleaning-up-the-factory"></a>清除工廠
清除 factory 的第一個步驟是從映射處理站移除黃金映射 Vm。 您可以使用腳本來完成這項工作，就像先前的腳本一樣。 第一個步驟是將另一個 **Azure Powershell** 工作新增至組建定義，如下圖所示：

![PowerShell 步驟](./media/set-retention-policy-cleanup/powershell-step.png)

當您在清單中有新的工作之後，請選取該專案，然後填入所有詳細資料，如下圖所示：

![清除舊映射 PowerShell 工作](./media/set-retention-policy-cleanup/configure-powershell-task.png)

腳本參數為： `-DevTestLabName $(devTestLabName)` 。

## <a name="retire-old-images"></a>淘汰舊映射 
此工作會移除任何舊的映射，只保留符合 **ImageRetention** 組建變數的歷程記錄。 將額外的 **Azure Powershell** 組建工作新增至組建定義。 新增之後，請選取該工作，然後填入詳細資料，如下圖所示： 

![淘汰舊映射 PowerShell 工作](./media/set-retention-policy-cleanup/retire-old-image-task.png)

腳本參數為： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>將組建排到佇列
既然您已完成組建定義，請將新的組建排入佇列，以確定一切都正常運作。 組建順利完成後，新的自訂映射會顯示在目的地實驗室中，如果您檢查映射處理站實驗室，就會看到沒有任何已布建的 Vm。 此外，如果您將進一步的組建排入佇列，您會看到清除工作會根據組建變數中設定的保留值，從 DevTest Labs 淘汰舊的自訂映射。

> [!NOTE]
> 如果您已在系列的最後一篇文章結尾執行組建管線，請在將新組建排入佇列之前，手動刪除在映射處理站實驗室中建立的虛擬機器。  只有在設定所有專案並確認其正常運作時，才需要手動清除步驟。



## <a name="summary"></a>摘要
現在您已有執行中的映射 factory，可依需求產生自訂映射，並將其散發給您的實驗室。 到目前為止，只是讓您的映射正確設定並識別目標實驗室。 如前一篇文章所述，位於 [設定 **] 資料夾中**的檔案**Labs.js**會指定每個目標實驗室中應提供哪些映射。 當您將其他 DevTest Labs 新增至組織時，您只需要在的 Labs.js中加入新實驗室的專案。

將新的映射新增至您的處理站也很簡單。 當您想要在您的工廠中包含新的映射時，請開啟 [Azure 入口網站](https://portal.azure.com)、流覽至您的工廠 DevTest 實驗室、選取要新增 VM 的按鈕，然後選擇所需的 marketplace 映射和成品。 不要選取 [ **建立** ] 按鈕來建立新的 VM，而是選取 [ **View Azure Resource Manager template**]，然後將範本儲存為存放庫中 **GoldenImages** 資料夾內的 json 檔案。 下次您執行映射處理站時，就會建立您的自訂映射。


## <a name="next-steps"></a>接下來的步驟
1. [排程您的組建/發行](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) 以定期執行映射 factory。 它會定期重新整理您的 factory 產生映射。
2. 為您的工廠製作更多黃金映射。 您也可以考慮 [建立](devtest-lab-artifact-author.md) 成品，以編寫 VM 設定工作的其他部分腳本，並將構件包含在原廠映射中。
4. 建立 [個別的組建/版本](/azure/devops/pipelines/overview?view=azure-devops-2019) ，以分別執行 **DistributeImages** 腳本。 當您進行 Labs.js的變更，並將影像複製到目標實驗室時，您可以執行此腳本，而不需要重新建立所有映射。

