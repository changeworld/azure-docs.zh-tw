---
title: 在 Azure 開發人員測試實驗室中保存和分發映射 |微軟文檔
description: 本文提供了從 Azure DevTest Labs 中已創建的虛擬機器 （VM） 中保存自訂映射的步驟。
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
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759426"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>儲存自訂映像並散發至多個實驗室
本文提供了從已創建的虛擬機器 （VM） 中保存自訂映射的步驟。 它還介紹了如何將這些自訂映射分發到組織中的其他 DevTest 實驗室。

## <a name="prerequisites"></a>Prerequisites
以下專案應已經到位：

- Azure 開發人員測試實驗室中映射工廠的實驗室。
- 用於自動執行映射工廠的 Azure DevOps 專案。
- 包含腳本和配置的原始程式碼位置（在我們的示例中，在上一步驟中提到的同一 DevOps 專案中）。
- 組建定義以協調 Azure Powershell 任務。

如果需要，請按照 Azure [DevOps 運行映射工廠](image-factory-set-up-devops-lab.md)中的步驟創建或設置這些專案。 

## <a name="save-vms-as-generalized-vhds"></a>將 VM 保存為通用 VHD
將現有 VM 保存為通用 VHD。  有一個示例 PowerShell 腳本，用於將現有 VM 保存為通用 VHD。 要使用它，首先，將另一個**Azure Powershell**任務添加到組建定義中，如下圖所示：

![添加 Azure 電源外殼步驟](./media/save-distribute-custom-images/powershell-step.png)

在清單中有新任務後，選擇該專案，以便我們可以填寫如下圖像所示的所有詳細資訊： 

![電源殼設置](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>通用映射與專用自訂映射
在[Azure 門戶](https://portal.azure.com)中，從虛擬機器創建自訂映射時，可以選擇具有通用映射或專用自訂映射。

- **專用自訂映射：** 未在電腦上運行 Sysprep/取消預配。 這意味著映射是現有虛擬機器（快照）上的 OS 磁片的精確副本。  當我們從這個自訂映射創建新電腦時，都存在相同的檔、應用程式、使用者帳戶、電腦名稱稱等。
- **通用自訂映射：** 在電腦上運行了 Sysprep/取消預配。  此過程運行時，它會刪除使用者帳戶、刪除電腦名稱稱、剝離使用者註冊表配置單元等，目的是對映射進行一般化，以便在創建另一個虛擬機器時對其進行自訂。  當您對虛擬機器進行通用化（通過運行 sysprep）時，該過程將銷毀當前的虛擬機器 - 它將不再正常工作。

在映射工廠中捕捉自訂映射的腳本將為在上一步中創建的任何虛擬機器（根據 Azure 中資源上的標記標識）保存 VD。

## <a name="update-configuration-for-distributing-images"></a>用於分發映射的更新配置
該過程的下一步是將自訂映射從映射工廠實驗室推送到任何其他需要它們的實驗室。 此過程的核心部分是**實驗室.json**設定檔。 您可以在映射工廠中包含的 **"配置"** 資料夾中找到此檔。

實驗室.json 設定檔中列出了兩個關鍵內容：

- 使用訂閱 ID 和實驗室名稱唯一標識特定目標實驗室。
- 應作為配置根的相對路徑推送到實驗室的特定圖像集。 也可以指定整個資料夾（獲取該資料夾中的所有圖像）。

下面是一個示例實驗室.json 檔，其中列出了兩個實驗室。 在這種情況下，您將圖像分發到兩個不同的實驗室。

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
使用本文前面看到的相同步驟，向組建定義添加其他 Azure **Powershell**生成任務。 填寫下圖所示的詳細資訊： 

![生成任務以分發圖像](./media/save-distribute-custom-images/second-build-task-powershell.png)

參數為：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

此任務獲取映射工廠中存在的任何自訂映射，並將它們推送到 Labs.json 檔中定義的任何實驗室。

## <a name="queue-the-build"></a>對生成進行排隊
分發生成任務完成後，將重新生成排隊以確保一切正常工作。 生成成功完成後，新的自訂映射將顯示在輸入到 Labs.json 設定檔的目標實驗室中。

## <a name="next-steps"></a>後續步驟
在本系列的下一篇文章中，您將使用保留原則和清理步驟更新映射工廠：[設置保留原則並運行清理腳本](image-factory-set-retention-policy-cleanup.md)。
