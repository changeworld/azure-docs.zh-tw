---
title: 在檔案儲存的記憶體檔案
description: 瞭解如何使用解決方案範本使用 Azure 數據工廠在基於檔的存儲之間行動檔。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b36eb2615e98ee8ea7751c836fd43e81a5a0f4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414754"
---
# <a name="move-files-with-azure-data-factory"></a>使用 Azure 資料工廠移動檔案
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介紹了一個解決方案範本,可用於在基於檔的存儲之間將檔從一個資料夾移動到另一個資料夾。 使用此範本的常見方案之一:檔會不斷下降到源存儲的著陸資料夾。 通過創建計畫觸發器,ADF 管道可以定期將這些檔從源移動到目標存儲。  ADF 管道實現「移動檔」的方式是從登錄資料夾中獲取檔,將每個檔案複製到目標存儲上的另一個資料夾,然後從源存儲的著陸資料夾中刪除相同的檔。

> [!NOTE]
> 請注意,此範本旨在移動檔,而不是移動資料夾。  如果要透過更改資料集使其僅包含資料夾路徑來移動資料夾,然後使用複製活動和刪除活動來引用表示資料夾的同一資料集,則需要非常小心。 這是因為您必須確定在複製作業與刪除作業之間，不會有新檔案抵達資料夾。 如果在您的複製活動剛剛完成複製作業但是 Delete 活動尚未開始時就有新檔案抵達資料夾，Delete 活動可能會刪除這個新抵達檔案，系統未透過刪除整個資料夾將該檔案複製到目的地。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本從檔案的來源儲存中獲取檔。 然後,它將每個它們移動到目標存儲。

此樣本包含五個活動:
- **GetMetadata**從來源儲存的資料夾中獲取物件清單,包括檔案和子資料夾。 它將不會遞歸地檢索物件。 
- **篩選**從**GetMetadata**活動中篩選物件清單,以僅選擇檔案。 
- **ForEach**從 **「篩選器」** 活動獲取檔案清單,然後遍過清單,並將每個檔傳遞到「複製」活動和「刪除」活動。
- **將**一個檔案從源複製到目標存儲。
- **刪除**從來源儲存中移除相同的一個檔案。

樣本定義了四個參數:
- *SourceStore_Location*是源存儲的資料夾路徑,要從該路徑移動檔。 
- *SourceStore_Directory*是源存儲的子文件路徑,要從中移出檔。
- *DestinationStore_Location*是要將文件移動到的目標存儲的資料夾路徑。 
- *DestinationStore_Directory*是要將文件移動到的目標存儲的子資料夾路徑。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 跳到 **'行動檔案**' 樣本。 選擇現有連接或創建到源檔案儲存**的"新建**連接",以便從中移出檔。 請注意 **,DataSource_Folder**和**DataSource_File**是源檔案存儲的相同連接的引用。

    ![建立與來源的新連線](media/solution-template-move-files/move-files1.png)

2. 選擇現有連接或創建要將檔案移動到的目標檔案儲存**的"新建**連接"

    ![建立與目的地的新連線](media/solution-template-move-files/move-files2.png)

3. 選擇**使用此樣本**選項卡。
    
4. 您將看到導管,例如以下範例所示:

    ![顯示管線](media/solution-template-move-files/move-files4.png)

5. 選擇 **「除錯**」,輸入**參數**,然後選擇 **"完成**"。   參數是要將檔案移離的資料夾路徑,以及要將文件移動到的資料夾路徑。 

    ![執行管道](media/solution-template-move-files/move-files5.png)

6. 檢閱結果。

    ![檢閱結果](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure 資料工廠按上次修改日期複製新檔案和已更改檔案](solution-template-copy-new-files-lastmodifieddate.md)

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)