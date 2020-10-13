---
title: 資料儲存體-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 會將資料以加密方式儲存在與金鑰所指定區域對應的 Azure 資料存放區中。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b74621f357613bc60457ab4846b58f6f85009401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541878"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Language Understanding (LUIS) 認知服務中的資料儲存和移除
LUIS 會將資料以加密方式儲存在與金鑰所指定區域對應的 Azure 資料存放區中。 此資料會儲存 30 天。 

## <a name="export-and-delete-app"></a>匯出及刪除應用程式
使用者可以完全控制應用程式的[匯出](luis-how-to-start-new-app.md#export-app)和[刪除](luis-how-to-start-new-app.md#delete-app)。 

## <a name="utterances"></a>表達方式

語句可以儲存在兩個不同的位置。 

* **撰寫過程**中會建立語句，並將其儲存在意圖中。 成功的 LUIS 應用程式需要語句意圖。 一旦發行應用程式並接收端點的查詢之後，端點要求的查詢字串 `log=false` 就會判斷是否儲存端點語句。 如果端點已儲存，它就會成為入口網站的 [ **建立** ] 區段中，在 [ **審核端點語句** ] 區段中找到之主動式學習語句的一部分。 
* 當您 **檢查端點語句**並將語句新增至意圖時，語句不會再儲存為要檢查的端點語句的一部分。 它會新增至應用程式意圖。 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>從意圖刪除範例語句

刪除用於進行 [LUIS](luis-reference-regions.md) 定型的範例語句。 若您從 LUIS 應用程式刪除範例語句，則會從 LUIS Web 服務中予以移除，而且無法用於匯出。

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>從主動式學習刪除語句的評論

您可以在 [[Review endpoint utterances](luis-how-to-review-endpoint-utterances.md)****] \(檢閱端點語句\) 頁面中，從 LUIS 所建議的使用者語句清單中刪除語句。 從此清單中刪除語句可防止系統建議這些語句，但並不會從記錄中刪除它們。

如果您不想要主動式學習語句，您可以 [停用主動式學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 停用主動式學習也會停用記錄功能。

### <a name="disable-logging-utterances"></a>停用記錄語句
[停用主動學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning) 會停用記錄功能。


<a name="accounts"></a>

## <a name="delete-an-account"></a>刪除帳戶
如果您刪除帳戶，系統就會刪除所有應用程式及其範例語句和記錄。 資料會保留 60 天，之後系統就會將帳戶和資料永久刪除。

刪除帳戶時，是從 [Settings] \(設定\)**** 頁面執行刪除。 請選取右上方導覽列中您的帳戶名稱，以移至 [Settings] \(設定\)**** 頁面。

## <a name="data-inactivity-as-an-expired-subscription"></a>資料非作用中狀態視為過期訂用帳戶
基於資料保留和刪除的目的，非作用中 LUIS 應用程式可能「由 Microsoft 斟酌決定」__ 視為過期的訂用帳戶。 應用程式如果在過去 90 天符合下列條件，即可視為非作用中： 

* **沒有**任何對它發出的呼叫。
* 尚未經過修改。
* 沒有已指派的目前金鑰。
* 沒有已指派的使用者。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何匯出及刪除應用程式](luis-how-to-start-new-app.md)
