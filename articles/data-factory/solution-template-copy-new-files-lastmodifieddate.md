---
title: 依 LastModifiedDate 複製新的和已變更的檔案
description: 瞭解如何使用解決方案範本，以 Azure Data Factory LastModifiedDate 來複製新的和變更的檔案。
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
ms.openlocfilehash: 363af1012b327a55703cc6e35a02dc1f27869bd0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629060"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>使用 Azure Data Factory 複製新的和已變更的檔案

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明一個解決方案範本，您可以用來將新的和變更的檔案，從以檔案為基礎的存放區 LastModifiedDate 到目的地存放區。 

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會先選取新的和已變更的檔案，而僅由其屬性**LastModifiedDate**，然後將這些選取的檔案從資料來源存放區複製到資料目的地存放區。

此範本包含一個活動：
- [**複製**]，只會從檔案存放區 LastModifiedDate 至目的地存放區，以複製新的和已變更的檔案。

範本會定義六個參數：
-  *FolderPath_Source*是您可以從來源存放區讀取檔案的資料夾路徑。 您必須將預設值取代為您自己的資料夾路徑。
-  *Directory_Source*是您可以從來源存放區讀取檔案的子資料夾路徑。 您必須將預設值取代為您自己的子資料夾路徑。
-  *FolderPath_Destination*是您要將檔案複製到目的地存放區的資料夾路徑。 您必須將預設值取代為您自己的資料夾路徑。
-  *Directory_Destination*是您要將檔案複製到目的地存放區的子資料夾路徑。 您必須將預設值取代為您自己的子資料夾路徑。
-  *LastModified_From*可用來選取其 LastModifiedDate 屬性已晚于或等於此日期時間值的檔案。  為了只選取最近未複製的新檔案，此日期時間值可以是上次觸發管線的時間。 您可以在 UTC 時區中，將預設值 ' 2019-02-01T00：00： 00Z ' 取代為您預期的 LastModifiedDate。 
-  *LastModified_To*可用來選取其 LastModifiedDate 屬性早于此日期時間值的檔案。 為了只選取最近尚未複製的新檔案，此日期時間值可以是目前的時間。  您可以在 UTC 時區中，將預設值 ' 2019-02-01T00：00： 00Z ' 取代為您預期的 LastModifiedDate。 

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至範本**只複製 LastModifiedDate 的新**檔案。 建立與您的來源儲存體存放區的**新**連線。 來源儲存體存放區是您要從中複製檔案的位置。

    ![建立與來源的新連線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. 建立連至目的地存放區的**新**連線。 目的地存放區是您要將檔案複製到其中的位置。 

    ![建立與目的地的新連線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. 選取 [**使用此範本**]。

    ![使用此範本](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. 您會在面板中看見可用的管線，如下列範例所示：

    ![顯示管線](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. 選取 [ **Debug**]，寫入**參數**的值，然後選取 **[完成]**。  在下圖中，我們會設定參數，如下所示。
   - **FolderPath_Source** = sourcefolder
   - **Directory_Source** = 子資料夾
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = 子資料夾
   - **LastModified_From** = 2019-02-01T00：00：00Z
   - **LastModified_To** = 2019-03-01T00：00：00Z
    
    此範例指出已在時間範圍內（**2019-02-01T00：00： 00Z**到**2019-03-01T00：00： 00Z**）最後修改過的檔案，將從來源路徑**sourcefolder/子資料夾**複製到目的地路徑**destinationfolder/子資料夾**。  您可以使用自己的參數來取代這些。

    ![執行管道](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. 檢閱結果。 您只會看到在設定的 timespan 內最後修改的檔案已複製到目的地存放區。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. 現在您可以新增輪轉 windows 觸發程式來自動化此管線，讓管線一律只會定期 LastModifiedDate 複製新的和已變更的檔案。  選取 [**加入觸發**程式]，然後選取 [**新增/編輯**]。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. 在 [新增觸發程序]**** 視窗中，選取 [+ 新增]****。

9. 選取觸發程式類型的 [**輪轉視窗]** ，設定**每隔15分鐘**做為週期（您可以變更為任何間隔時間）。 針對 [已啟用] 方塊選取 **[是**]，然後選取 **[確定]**。

    ![建立觸發程序](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. 設定**觸發程式執行參數**的值，如下所示，然後選取 **[完成]**。
    - **FolderPath_Source** = **sourcefolder**。  您可以將取代為您在來源資料存放區中的資料夾。
    - **Directory_Source** = **子資料夾**。  您可以將取代為來源資料存放區中的子資料夾。
    - **FolderPath_Destination** = **destinationfolder**。  您可以將取代為目的地資料存放區中的資料夾。
    - **Directory_Destination** = **子資料夾**。  您可以將取代為目的地資料存放區中的子資料夾。
    - **LastModified_From**  =   **LastModified_From\@觸發程式（）。 windowStartTime**。  它是來自觸發程式的系統變數，用以判斷上次觸發管線的時間。
    - **LastModified_To**  =  **LastModified_To\@觸發程式（）。 .windowendtime**。  這是來自觸發程式的系統變數，可決定這次觸發管線的時間。
    
    ![輸入參數](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. 選取 [全部發佈]****。
    
    ![全部發佈](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. 在資料來源存放區的來源資料夾中建立新檔案。  您現在正在等候管線自動觸發，而且只有新的檔案會複製到目的地存放區。

13. 選取左側流覽窗格中的 [**監視**] 索引標籤，如果觸發程式的週期已設定為每隔15分鐘，請等候約15分鐘。 

14. 檢閱結果。 您會看到每隔15分鐘會自動觸發管線，而且只有來源存放區中的新檔案或變更的檔案會在每個管線執行中複製到目的地存放區。

    ![檢閱結果](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>後續步驟

- [Azure Data Factory 簡介](introduction.md)
