---
title: 管理版本-LUIS
titleSuffix: Azure Cognitive Services
description: 版本可讓您建置及發佈不同的模型。 理想的做法是先將目前作用中的模型複製到不同的應用程式版本，再對模型進行變更。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 3aa7958ea4edf6cf3e3c1b170e2351f49f719983
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782835"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>使用版本進行編輯和測試而不影響預備或生產應用程式

版本可讓您建置及發佈不同的模型。 理想的做法是先將目前作用中的模型複製到不同的應用程式[版本](luis-concept-version.md)，再對模型進行變更。

作用中版本是您在 LUIS 入口網站**Build**區段中，使用意圖、實體、功能和模式編輯的版本。 使用撰寫 Api 時，您不需要設定作用中的版本，因為版本特定的 REST API 呼叫會包含路由中的版本。

若要使用版本，請在 [我的應用程式]**** 頁面上選取應用程式的名稱來開啟應用程式，接著選取頂端列中的 [管理]****，然後選取左側導覽中的 [版本]****。

版本清單會顯示發行的版本、發行的位置，以及目前使用中的版本。

## <a name="clone-a-version"></a>複製版本

1. 選取您要複製的版本，然後從工具列中選取 [複製]****。

2. 在 [複製版本]**** 對話方塊中，輸入新版本的名稱，例如 "0.2"。

   ![[複製版本] 對話方塊](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > 版本識別碼只能包含字元、數字或 '.'，且長度不能超過 10 個字元。

   隨即會建立具有指定名稱的新版本，並會設定為使用中版本。

## <a name="set-active-version"></a>設定使用中版本

從清單中選取版本，然後從工具列中選取 [**啟動**]。

## <a name="import-version"></a>匯入版本

您可以匯入 `.json` 或某個 `.lu` 版本的應用程式。

1. 從工具列中選取 [匯**入**]，然後選取格式。

2. 在 [匯入新版本]**** 快顯視窗中，輸入新的版本名稱 (10 個字元)。 如果檔案中的版本已存在於應用程式中，您只需要設定版本識別碼。

    ![管理區段, 版本頁面, 匯入新版本](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    匯入版本之後，新版本就會變成使用中的版本。

### <a name="import-errors"></a>匯入錯誤

* Tokenizer 錯誤：如果您在匯入時收到**Tokenizer 錯誤**，表示您嘗試匯入的版本使用與目前所使用的應用程式不同的[Tokenizer](luis-language-support.md#custom-tokenizer-versions) 。 若要修正此問題，請參閱[在 tokenizer 版本之間進行遷移](luis-language-support.md#migrating-between-tokenizer-versions)。

<a name = "export-version"></a>

## <a name="other-actions"></a>其他動作

* 若要**刪除**版本，請從清單中選取版本，然後從工具列中選取 [刪除]****。 選取 **[確定]**。
* 若要**重新命名**版本，請從清單中選取版本，然後從工具列中選取 [重新命名]****。 輸入新的名稱，然後選取 [完成]****。
* 若要**匯出**版本，請從清單中選取版本，然後從工具列中選取 [匯出應用程式]****。 選擇要匯出以進行備份的 JSON 或 LU，或在原始檔控制中儲存，選擇 [**匯出容器**] 以[在 LUIS 容器中使用此應用程式](luis-container-howto.md)。

