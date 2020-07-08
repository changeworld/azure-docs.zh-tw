---
title: 在 Azure DevTest Labs 中儲存和散發影像 |Microsoft Docs
description: 本文提供的步驟可讓您從 Azure DevTest Labs 中已建立的虛擬機器（Vm）儲存自訂映射。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476235"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>儲存自訂映像並散發至多個實驗室
本文提供的步驟可讓您從已建立的虛擬機器（Vm）儲存自訂映射。 其中也涵蓋如何將這些自訂映射散發給組織中的其他 DevTest Labs。

## <a name="prerequisites"></a>必要條件
下列專案應該已經準備就緒：

- Azure DevTest Labs 中映射 Factory 的實驗室。
- 用來自動執行映射處理站的 Azure DevOps 專案。
- 包含腳本和設定的原始程式碼位置（在我們的範例中，在上一個步驟所述的相同 DevOps 專案中）。
- 用來協調 Azure Powershell 工作的組建定義。

如有需要，請遵循[從 Azure DevOps 執行映射 factory](image-factory-set-up-devops-lab.md)中的步驟來建立或設定這些專案。 

## <a name="save-vms-as-generalized-vhds"></a>將 Vm 儲存為一般化 Vhd
將現有的 Vm 儲存為一般化 Vhd。  有一個範例 PowerShell 腳本，可將現有的 Vm 儲存為一般化 Vhd。 若要使用它，請先將另一個**Azure Powershell**工作新增至組建定義，如下圖所示：

![新增 Azure PowerShell 步驟](./media/save-distribute-custom-images/powershell-step.png)

一旦您在清單中有新的工作，請選取該專案，讓我們可以填入所有詳細資料，如下圖所示： 

![PowerShell 設定](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>一般化與特製化的自訂映射
在[Azure 入口網站](https://portal.azure.com)中，從虛擬機器建立自訂映射時，您可以選擇擁有一般化或特製化的自訂映射。

- **特製化自訂映射：** 未在電腦上執行 Sysprep/取消布建。 這表示映射是現有虛擬機器上的完整 OS 磁片複本（快照集）。  當我們從這個自訂映射建立新機器時，就會有相同的檔案、應用程式、使用者帳戶、電腦名稱稱等等。
- **一般化的自訂映射：** 已在電腦上執行 Sysprep/取消布建。  當此程式執行時，它會移除使用者帳戶、移除電腦名稱稱、去除使用者登錄 hive 等等，其目標是將映射一般化，以便在建立另一部虛擬機器時進行自訂。  當您將虛擬機器一般化（藉由執行 sysprep）時，該處理常式會終結目前的虛擬機器，而它將無法再正常運作。

在映射處理站中貼齊自訂映射的腳本會儲存在先前步驟中建立之任何虛擬機器的 Vhd （根據 Azure 資源上的標籤來識別）。

## <a name="update-configuration-for-distributing-images"></a>更新發佈映射的設定
此程式的下一個步驟是將自訂映射從「映射工廠」實驗室推播到任何其他需要它們的實驗室。 此程式的核心部分是設定檔**上的labs.js** 。 您可以在映射處理站所包含**的 [設定**] 資料夾中找到此檔案。

設定檔的 labs.js中列出兩個重要事項：

- 使用訂用帳戶識別碼和實驗室名稱來唯一識別特定的目的地實驗室。
- 應推送至實驗室的一組特定映射，做為設定根目錄的相對路徑。 您也可以指定整個資料夾（以取得該資料夾中的所有映射）。

以下是已列出兩個實驗室的檔案範例 labs.js。 在此情況下，您會將映射散發給兩個不同的實驗室。

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>建立建置工作
使用您稍早在本文中所見的相同步驟，將額外的**Azure Powershell**組建工作新增至您的組建定義。 填入詳細資料，如下圖所示： 

![用來散發映射的組建工作](./media/save-distribute-custom-images/second-build-task-powershell.png)

參數為：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

此工作會使用映射處理站中的任何自訂映射，並將其推送至檔案中的 Labs.js所定義的任何實驗室。

## <a name="queue-the-build"></a>將組建排到佇列
完成「散發組建」工作之後，將新的組建排入佇列，以確保所有專案都能正常運作。 成功完成組建之後，新的自訂映射就會顯示在目的地實驗室中，並在設定檔上輸入 Labs.js。

## <a name="next-steps"></a>後續步驟
在系列的下一篇文章中，您會使用保留原則和清除步驟來更新映射 factory：[設定保留原則和執行清除腳本](image-factory-set-retention-policy-cleanup.md)。
