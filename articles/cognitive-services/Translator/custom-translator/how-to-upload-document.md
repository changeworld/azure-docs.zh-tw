---
title: 如何上傳文件 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 檔上傳功能會將平行檔上傳 (兩份檔，其中一個是來源，另一個則是轉譯) 至服務中。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 83cce31d3eee9b5cca7b54e996e41a270fec0030
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896389"
---
# <a name="upload-a-document"></a>上傳文件

在[自訂翻譯工具](https://portal.customtranslator.azure.ai)中，您可以上傳平行文件來訓練您的翻譯模型。 [平行文件](what-are-parallel-documents.md)是配對文件，其中的一份文件是另一份文件的翻譯。 該配對中的一份文件會包含原始語言的句子，而另一分文件會包含已翻譯成目標語言的這些句子。

上傳文件之前，請先檢閱[文件格式和命名慣例指引](document-formats-naming-convention.md)，確保自訂翻譯工具支援您的檔案格式。

## <a name="how-to-upload-document"></a>如何上傳文件？

從[自訂翻譯工具](https://portal.customtranslator.azure.ai)入口網站中，按一下 [文件] 索引標籤，即可移至文件頁面。

![文件上傳連結](media/how-to/how-to-upload-1.png)


1.  按一下文件頁面上的 [上傳檔案] 按鈕。

    ![上傳文件頁面](media/how-to/how-to-upload-2.png)

2.  在對話方塊中填寫下列資訊︰

    a.  文件類型：

    -  訓練：這些文件將用於訓練集。
    -  調整：這些文件將用於調整集。
    -  測試：這些文件將用於測試集。
    -  片語字典：這些文件將用於片語字典。
    -  句子字典：這些文件將用於句子字典

    b.  語言組

    c.  覆寫已存在的文件：如果您想使用相同名稱覆寫任何現有的文件，請選取此核取方塊。

    d.  填寫平行資料或組合資料的相關區段。

    -  平行資料：
        -  原始檔案：從本機電腦選取原始語言檔案。
        -  目標檔案：從本機電腦選取目標語言檔案。
        -  文件名稱：只在您上傳平行檔案時才會用到。

    - 組合資料：
        -  組合檔案：從本機電腦選取組合檔案。 組合檔案中會有原始語言和目標語言的句子。 [命名慣例](document-formats-naming-convention.md)是組合檔案很重要的部分。

    e.  按一下 [上傳]。

    ![上傳文件對話方塊](media/how-to/how-to-upload-dialog.png)

3.  此時，我們會處理您的文件，並嘗試擷取句子。 您可以按一下 [檢視上傳進度] 來檢查文件的處理狀態。

    ![上傳文件的處理對話方塊](media/how-to/how-to-upload-processing-dialog.png)

4.  此頁面會顯示上傳期間每個檔案的狀態及任何錯誤。 按一下 [上傳記錄] 索引標籤，即可檢視過去任何時間的上傳狀態。

    ![上傳文件記錄對話方塊](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>檢視上傳記錄

在上傳記錄頁面中，您可以檢視所有文件上傳的詳細記錄，例如文件類型、語言組和上傳狀態等。

1. 從[自訂翻譯工具](https://portal.customtranslator.azure.ai)入口網站中，按一下 [上傳記錄] 索引標籤來檢視記錄。

    ![上傳記錄索引標籤](media/how-to/how-to-upload-history-1.png)

2. 此頁面會顯示所有過去的上傳狀態。 以最新到最舊的順序顯示上傳項目。 其中會顯示每次上傳的文件名稱、上傳狀態、上傳日期、上傳的檔案數目、上傳的檔案類型和檔案的語言組。

    ![上傳記錄頁面](media/how-to/how-to-document-history-2.png)

3. 按一下任何上傳記錄。 在上傳記錄的詳細資料頁面中，您可以檢視該上傳作業中的上傳檔案、檔案上傳狀態、檔案語言及錯誤訊息 (如果上傳中有發生錯誤的話)。

## <a name="next-steps"></a>後續步驟

- 使用[文件詳細資料頁面](how-to-view-document-details.md)來檢閱擷取的句子清單。
- [如何訓練模型](how-to-train-model.md)。
