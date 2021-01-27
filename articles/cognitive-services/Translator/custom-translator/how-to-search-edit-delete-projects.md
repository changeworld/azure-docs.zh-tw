---
title: 如何搜尋、編輯和刪除專案 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 自訂翻譯工具可讓您以不同方式有效率地管理專案。 您可以建立多個專案、依條件搜尋，以及編輯專案。 在自訂翻譯工具中也可刪除專案。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896438"
---
# <a name="search-edit-and-delete-projects"></a>搜尋、編輯和刪除專案

自訂翻譯工具可讓您以多種方式有效率地管理專案。 您可以建立許多專案、依條件搜尋，以及編輯專案。 在自訂翻譯工具中也可刪除專案。  

## <a name="search-and-filter-projects"></a>搜尋和篩選專案

篩選工具可讓您以不同的篩選條件來搜尋專案。 它可篩選專案名稱、狀態、來源和目標語言，以及專案的類別。

1. 按一下篩選按鈕。

    ![搜尋專案](media/how-to/how-to-search-project.png)

2. 您可以依下列任何 (或所有) 的欄位進行篩選：專案名稱、來源語言、目的語言、類別和專案可用性。

3. 按一下 [套用]。

    ![搜尋專案篩選選項](media/how-to/how-to-search-project-filters.png)

4. 點選 [清除]，以清除篩選條件並檢視您所有的專案。

## <a name="edit-a-project"></a>編輯專案

自訂翻譯讓您能夠編輯專案的名稱和描述。 其他專案中繼資料（例如類別、來源語言和目的語言）無法進行編輯。 下列步驟說明如何編輯專案。

1. 按一下您將滑鼠停留在專案上方時出現的鉛筆圖示。

    ![編輯專案](media/how-to/how-to-edit-project.png)

2. 在對話方塊中，如果沒有部署模型，您可以修改專案名稱、專案描述、類別描述和專案標籤。 一旦建立專案之後，您就無法修改類別或語言組。

    ![編輯專案對話方塊](media/how-to/how-to-edit-project-dialog.png)

3. 按一下 [儲存] 按鈕。

## <a name="delete-a-project"></a>刪除專案

您可以在不再需要專案時加以刪除。 讓專案沒有處於使用中狀態的模型，例如已部署、定型定型、資料處理、部署等，否則刪除作業將會失敗。 下列步驟說明如何刪除專案。

1. 將滑鼠停留在任何專案記錄上方，然後按一下垃圾桶圖示。

   ![刪除專案](media/how-to/how-to-delete-project.png)

2. 確認刪除。 刪除專案時將一併刪除在該專案內建立的所有模型。 刪除專案並不會影響到您的文件。

   ![刪除確認對話方塊](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>後續步驟

- [上傳文件](how-to-upload-document.md)以開始建置您的自訂翻譯模型。
