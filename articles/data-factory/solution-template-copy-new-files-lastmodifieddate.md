---
title: 按上次修改日期複製新檔和更改的檔
description: 瞭解如何使用解決方案範本通過使用 Azure 資料工廠複製新檔和更改的檔。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 971d311dfb54d417a8f66f504d01f08f8bcfc48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942004"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>使用 Azure 資料工廠按上次修改日期複製新檔和已更改檔

本文介紹一個解決方案範本，它只能通過 Last修改日期從檔案為儲存基礎到目標存儲複製新的和更改的檔。 

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本首先僅根據它們的屬性 **"上次修改日期**"選擇新的和已更改的檔，然後將所選檔從資料來源存儲複製到資料目標存儲。

該範本包含一個活動：
- **僅**通過"上次修改日期"從檔存儲複製到目標存儲複製以複製新檔和更改的檔。

範本定義了六個參數：
-  *FolderPath_Source*是從源存儲讀取檔的資料夾路徑。 您需要將預設值替換為自己的資料夾路徑。
-  *Directory_Source*是從源存儲中讀取檔的子資料夾路徑。 您需要將預設值替換為自己的子資料夾路徑。
-  *FolderPath_Destination*是要將檔案複製到目標存儲的資料夾路徑。 您需要將預設值替換為自己的資料夾路徑。
-  *Directory_Destination*是要將檔案複製到目標存儲的子資料夾路徑。 您需要將預設值替換為自己的子資料夾路徑。
-  *LastModified_From*用於選擇其上次修改日期屬性位於此日期時間值之後或等於的檔。  為了僅選擇上次未複製的新檔，此日期時間值可以是上次觸發管道的時間。 您可以將預設值"2019-02-01T00：00：00：00Z"替換為 UTC 時區中的預期上次修改日期。 
-  *LastModified_To*用於選擇其上次修改日期屬性早于此日期時間值的檔。 為了僅選擇上次未複製的新檔，此日期時間值可以是目前時間。  您可以將預設值"2019-02-01T00：00：00：00Z"替換為 UTC 時區中的預期上次修改日期。 

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 轉到範本**僅按上次修改日期複製新檔**。 創建到源存儲存儲**的新**連接。 源存儲存儲是要從中複製檔的位置。

    ![建立與來源的新連線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 創建到目標存儲**的新**連接。 目標存儲是要將檔案複製到的位置。 

    ![建立與目的地的新連線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. 選擇**使用此範本**。

    ![使用此範本](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. 您會在面板中看見可用的管線，如下列範例所示：

    ![顯示管線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. 選擇**調試**，為**參數**寫入值，然後選擇 **"完成**"。  在下圖中，我們將參數設置為以下內容。
   - **FolderPath_Source** = 源資料夾
   - **Directory_Source** = 子資料夾
   - **FolderPath_Destination** = 目的檔案夾
   - **Directory_Destination** = 子資料夾
   - **LastModified_From** = 2019-02-01T00：00：00Z
   - **LastModified_To** = 2019-03-01T00：00：00Z
    
    該示例指示在時間跨度 **（2019-02-01T00：00：00：00Z**到**2019-03-01T00：00：00：00：00Z）** 內最後修改的檔將從源路徑**源資料夾/子**資料夾複製到目標路徑**目的檔案夾/子資料夾**。  您可以將這些參數替換為您自己的參數。

    ![執行管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. 檢閱結果。 您將看到在配置的時間跨度內上次修改的檔已複製到目標存儲區。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. 現在，您可以添加翻倒視窗觸發器以自動執行此管道，以便管道始終只能按 Last 修改日期複製新檔並更改。  選擇 **"添加觸發器**"，然後選擇 **"新建/編輯**"。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. 在 [新增觸發程序]**** 視窗中，選取 [+ 新增]****。

9. 為觸發器類型選擇 **"翻滾視窗**"，將**每 15 分鐘（秒）** 設置為定期（可以更改為任何間隔時間）。 為"已啟動"框選擇 **"是**"，然後選擇 **"確定**"。

    ![建立觸發程序](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. 將**觸發器運行參數**的值設置為以下內容，然後選擇 **"完成**"。
    - **FolderPath_Source** = **源資料夾**。  您可以在來源資料存儲中替換為資料夾。
    - **Directory_Source** = **子資料夾**。  您可以在來源資料存儲中替換為子資料夾。
    - **FolderPath_Destination** = **目的檔案夾**。  您可以在目標資料存儲中替換為資料夾。
    - **Directory_Destination** = **子資料夾**。  您可以在目標資料存儲中替換為子資料夾。
    - **LastModified_From**  =   **LastModified_From\@觸發器（）輸出.window 開始時間**。  它是觸發器中的系統變數，用於確定上次觸發管道的時間。
    - **LastModified_To**  =  **LastModified_To\@觸發器（））輸出.window 結束時間**。  它是觸發器中的系統變數，用於確定此時觸發管道的時間。
    
    ![輸入參數](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. 選取 [全部發佈]****。
    
    ![全部發佈](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. 在資料來源存儲的源資料夾中創建新檔。  您現在正在等待自動觸發管道，並且只有新檔將複製到目標存儲。

13. 在左側導航面板中選擇 **"監視器"** 選項卡，如果觸發器的定期設置為每 15 分鐘，則等待大約 15 分鐘。 

14. 檢閱結果。 您將看到管道將每隔 15 分鐘自動觸發一次，並且在每個管道運行中，只有源存儲中的新檔或更改的檔才會複製到目標存儲。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
