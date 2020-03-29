---
title: 管理版本 - LUIS
titleSuffix: Azure Cognitive Services
description: 版本可讓您建置及發佈不同的模型。 理想的做法是先將目前作用中的模型複製到不同的應用程式版本，再對模型進行變更。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221493"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>使用版本進行編輯和測試而不影響預備或生產應用程式

版本可讓您建置及發佈不同的模型。 理想的做法是先將目前作用中的模型複製到不同的應用程式[版本](luis-concept-version.md)，再對模型進行變更。 

若要使用版本，請在 [我的應用程式]**** 頁面上選取應用程式的名稱來開啟應用程式，接著選取頂端列中的 [管理]****，然後選取左側導覽中的 [版本]****。 

版本清單顯示發佈的版本、發佈版本的位置以及當前處於活動狀態的版本。 

> [!div class="mx-imgBorder"]
> [![[版本] 頁面的 [管理] 區段](./media/luis-how-to-manage-versions/versions-import.png "[版本] 頁面的 [管理] 區段")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>複製版本

1. 選取您要複製的版本，然後從工具列中選取 [複製]****。 

2. 在 [複製版本]**** 對話方塊中，輸入新版本的名稱，例如 "0.2"。

   ![[複製版本] 對話方塊](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > 版本識別碼只能包含字元、數字或 '.'，且長度不能超過 10 個字元。
 
   隨即會建立具有指定名稱的新版本，並會設定為使用中版本。

## <a name="set-active-version"></a>設定使用中版本

從清單中選擇一個版本，然後從工具列中選擇 **"啟動**"。 

> [!div class="mx-imgBorder"]
> [![管理部分、版本頁、制定版本操作](./media/luis-how-to-manage-versions/versions-other.png "管理部分、版本頁、制定版本操作")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>匯入版本

您可以導入應用程式的`.json`或`.lu`版本。

1. 從工具列中選擇 **"導入**"，然後選擇格式。 

2. 在 [匯入新版本]**** 快顯視窗中，輸入新的版本名稱 (10 個字元)。 僅當應用中已存在檔中的版本時，才需要設置版本 ID。

    ![管理區段, 版本頁面, 匯入新版本](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    匯入版本之後，新版本就會變成使用中的版本。

### <a name="import-errors"></a>導入錯誤

* 標記器錯誤：如果在導入時收到**權杖器錯誤**，則嘗試導入使用與應用當前使用的不同[標記器](luis-language-support.md#custom-tokenizer-versions)的版本。 要解決此問題，請參閱[在標記器版本之間遷移](luis-language-support.md#migrating-between-tokenizer-versions)。

<a name = "export-version"></a>

## <a name="other-actions"></a>其他動作

* 若要**刪除**版本，請從清單中選取版本，然後從工具列中選取 [刪除]****。 選擇 **"確定**"。 
* 若要**重新命名**版本，請從清單中選取版本，然後從工具列中選取 [重新命名]****。 輸入新的名稱，然後選取 [完成]****。 
* 若要**匯出**版本，請從清單中選取版本，然後從工具列中選取 [匯出應用程式]****。 選擇要匯出的 JSON 進行備份，選擇 **"匯出容器"** 以[在 LUIS 容器中使用此應用](luis-container-howto.md)。  

