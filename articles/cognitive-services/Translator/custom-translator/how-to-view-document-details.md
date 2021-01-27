---
title: 文件詳細資料 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 文件清單頁面會顯示您的工作區中的前 10 個文件。 對於每個文件，將會顯示其名稱、配對、類型、語言、上傳時間戳記，以及文件者的使用者電子郵件地址。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 56093204516e156d670097c22b4edab42db54bde
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895606"
---
# <a name="view-document-details"></a>檢視文件詳細資料

文件清單頁面會顯示您的工作區中的前 10 個文件。 對於每個文件，將會顯示其名稱、配對、類型、語言、上傳時間戳記，以及文件者的使用者電子郵件地址。

按一下個別文件，可檢視文件的詳細資料頁面。 文件的詳細資料頁面會顯示從文件中擷取的句子清單。

- 依預設，會在下拉式欄位中選取 [並存] 來源和目的語言顯示，但是您可以切換以在來源或目的語言中看到句子。
- 根據預設，每頁會顯示 20 個句子。 您可以使用分頁控制項在頁面之間瀏覽。

![文件詳細資料](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>刪除文件

使用者必須是工作區擁有者才能刪除文件。 此外，如果文件由某個模型使用中，且該模型屬於定型程序或部署程序的一部分，文件即無法刪除。

1. 移至文件頁面
2. 將滑鼠停留在任何文件記錄上方，然後按一下垃圾桶圖示。

    ![刪除文件](media/how-to/how-to-delete-document-1.png)

3. 確認刪除。

    ![刪除確認](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>後續步驟

- 了解[如何定型模型](how-to-train-model.md)。
