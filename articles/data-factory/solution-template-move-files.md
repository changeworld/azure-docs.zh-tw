---
title: 在以檔案為基礎的儲存體之間移動檔案
description: 瞭解如何使用解決方案範本，在以檔案為基礎的儲存體之間移動檔案（使用 Azure Data Factory）。
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
ms.openlocfilehash: f6baea73c0c4964bb3937304603a2a92a13d52b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86522715"
---
# <a name="move-files-with-azure-data-factory"></a>使用 Azure Data Factory 移動檔案

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

ADF 複製活動內建在儲存體存放區間複製二進位檔案時的「移動」案例支援。  啟用此功能的方式是在複製活動中將 "deleteFilesAfterCompletion" 設為 true。 如此一來，複製活動將會在作業完成之後，從資料來源存放區中刪除檔案。 

本文說明解決方案範本的另一種方法，是利用 ADF 彈性控制流程加上複製活動和刪除活動來達成相同的案例。 使用此範本的其中一個常見案例：檔案會持續放在來源存放區的登陸資料夾中。 藉由建立排程觸發程式，ADF 管線可以定期將這些檔案從來源移到目的地存放區。  ADF 管線達成「移動檔案」的方式是從登陸資料夾取得檔案，將每個檔案複製到目的地存放區上的另一個資料夾，然後從來源存放區上的登陸資料夾刪除相同的檔案。

> [!NOTE]
> 請注意，此範本是設計來移動檔案，而不是移動資料夾。  如果您想要藉由變更資料集使其只包含資料夾路徑，然後使用複製活動和刪除活動來參考代表資料夾的相同資料集來移動資料夾，您必須非常小心。 這是因為您必須確定在複製作業與刪除作業之間，不會有新檔案抵達資料夾。 如果在您的複製活動剛剛完成複製作業但是 Delete 活動尚未開始時就有新檔案抵達資料夾，Delete 活動可能會刪除這個新抵達檔案，系統未透過刪除整個資料夾將該檔案複製到目的地。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會從您的來源檔案型存放區取得檔案。 然後，它會將每個專案移到目的地存放區。

範本包含五個活動：
- **GetMetadata** 會取得物件清單，包括來源存放區上資料夾中的檔案和子資料夾。 它不會以遞迴方式取得物件。 
- **篩選** 篩選 **GetMetadata** 活動中的 [物件] 清單，只選取 [檔案]。 
- **ForEach** 會從 **篩選** 活動取得檔案清單，然後逐一查看清單，並將每個檔案傳遞至複製活動和刪除活動。
- **Copy** 會將一個檔案從來源複製到目的地存放區。
- **Delete** 會刪除來源存放區中的相同檔案。

此範本會定義四個參數：
- *SourceStore_Location* 是您想要從中移動檔案的來源存放區的資料夾路徑。 
- *SourceStore_Directory* 是您想要從中移動檔案的來源存放區的子資料夾路徑。
- *DestinationStore_Location* 是您想要將檔案移至其中的目的地存放區的資料夾路徑。 
- *DestinationStore_Directory* 是您想要將檔案移至其中的目的地存放區的子資料夾路徑。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至 [ **移動** 檔案] 範本。 選取現有的連接，或建立您要從中移動檔案之來源檔案存放區的 **新** 連線。 請注意， **DataSource_Folder** 和 **DataSource_File** 參考到您的原始程式檔存放區的相同連接。

    ![建立與來源的新連線](media/solution-template-move-files/move-files1.png)

2. 選取現有的連接，或建立您要將檔案移至其中的目的地檔案存放區的 **新** 連線。

    ![建立與目的地的新連線](media/solution-template-move-files/move-files2.png)

3. 選取 [ **使用此範本** ] 索引標籤。
    
4. 您會看到管線，如下列範例所示：

    ![顯示管線](media/solution-template-move-files/move-files4.png)

5. 選取 [偵錯]，輸入 [參數]，然後選取 [完成]。   這些參數是您想要在其中移動檔案的資料夾路徑，以及您想要將檔案移至其中的資料夾路徑。 

    ![執行管道](media/solution-template-move-files/move-files5.png)

6. 檢閱結果。

    ![檢閱結果](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 複製新的和變更的檔案（依 LastModifiedDate）](solution-template-copy-new-files-lastmodifieddate.md)

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)