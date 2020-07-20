---
title: 在以檔案為基礎的存放區之間移動檔案
description: 瞭解如何使用解決方案範本，在以檔案為基礎的存放裝置之間移動檔案，方法是使用 Azure Data Factory。
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
ms.openlocfilehash: 81f072822226e4a573cf0086cac7e64ca1cfe45f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82628158"
---
# <a name="move-files-with-azure-data-factory"></a>使用 Azure Data Factory 移動檔案

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明一個解決方案範本，您可以用來在檔案型存放區之間將檔案從一個資料夾移到另一個。 使用此範本的其中一個常見案例：檔案會持續卸載至來源存放區的登陸資料夾。 藉由建立排程觸發程式，ADF 管線可以定期將這些檔案從來源移至目的地存放區。  ADF 管線達到「移動檔案」的方式是從登陸資料夾取得檔案、將每個檔案複製到目的地存放區上的另一個資料夾，然後從來源存放區的登陸資料夾中刪除相同的檔案。

> [!NOTE]
> 請注意，此範本的設計目的是要移動檔案，而不是移動資料夾。  如果您想要藉由變更資料集來移動資料夾，使其只包含資料夾路徑，然後使用「複製活動」和「刪除活動」來參考代表資料夾的相同資料集，您必須非常小心。 這是因為您必須確定在複製作業與刪除作業之間，不會有新檔案抵達資料夾。 如果在您的複製活動剛剛完成複製作業但是 Delete 活動尚未開始時就有新檔案抵達資料夾，Delete 活動可能會刪除這個新抵達檔案，系統未透過刪除整個資料夾將該檔案複製到目的地。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會從以檔案為基礎的存放區取得檔案。 然後，它會將每個專案移至目的地存放區。

此範本包含五個活動：
- **GetMetadata**會從來源存放區上的資料夾取得物件清單，包括檔案和子資料夾。 它不會以遞迴方式抓取物件。 
- **篩選**從**GetMetadata**活動篩選物件清單，只選取檔案。 
- **ForEach**會從**篩選**活動取得檔案清單，然後逐一查看清單，並將每個檔案傳遞給複製活動和刪除活動。
- [**複製**] 會將一個檔案從來源複製到目的地存放區。
- [**刪除**] 會從來源存放區刪除相同的一個檔案。

範本會定義四個參數：
- *SourceStore_Location*是您想要從中移動檔案之來源存放區的資料夾路徑。 
- *SourceStore_Directory*是您想要從中移動檔案之來源存放區的子資料夾路徑。
- *DestinationStore_Location*是您想要將檔案移至其中的目的地存放區的資料夾路徑。 
- *DestinationStore_Directory*是您想要將檔案移至其中的目的地存放區的子資料夾路徑。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至 [**移動**檔案] 範本。 選取 [現有連線]，或建立您想要從中移動檔案之來源檔案存放區的**新**連接。 請注意， **DataSource_Folder**和**DataSource_File**會參考您的來源檔案存放區的相同連接。

    ![建立與來源的新連線](media/solution-template-move-files/move-files1.png)

2. 選取 [現有連線]，或建立您想要將檔案移至目的地檔案存放區的**新**連接。

    ![建立與目的地的新連線](media/solution-template-move-files/move-files2.png)

3. 選取 [**使用此範本**] 索引標籤。
    
4. 您會看到管線，如下列範例所示：

    ![顯示管線](media/solution-template-move-files/move-files4.png)

5. 選取 [偵錯]，輸入 [參數]，然後選取 [完成]。   參數是您想要從中移動檔案的資料夾路徑，以及您要將檔案移至其中的資料夾路徑。 

    ![執行管道](media/solution-template-move-files/move-files5.png)

6. 檢閱結果。

    ![檢閱結果](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 複製新的和已變更的檔案](solution-template-copy-new-files-lastmodifieddate.md)

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)