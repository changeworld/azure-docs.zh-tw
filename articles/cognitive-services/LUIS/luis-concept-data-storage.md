---
title: 資料存儲 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 會將資料以加密方式儲存在與金鑰所指定區域對應的 Azure 資料存放區中。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221333"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Language Understanding (LUIS) 認知服務中的資料儲存和移除
LUIS 會將資料以加密方式儲存在與金鑰所指定區域對應的 Azure 資料存放區中。 此資料會儲存 30 天。 

## <a name="export-and-delete-app"></a>匯出及刪除應用程式
使用者可以完全控制應用程式的[匯出](luis-how-to-start-new-app.md#export-app)和[刪除](luis-how-to-start-new-app.md#delete-app)。 

## <a name="utterances"></a>表達方式

可存儲在兩個不同的位置。 

* **在創作過程中**，話語被創建並存儲在意圖中。 成功的 LUIS 應用需要意圖中的意圖。 發佈應用並在終結點接收查詢後，終結點請求的查詢字串`log=false`將確定終結點是否發出。 如果存儲終結點，它將成為門戶**的"生成**"部分（**在"審閱終結點陳述"** 部分中找到的活動學習話語）的一部分。 
* 當您**查看終結點陳述**，並將陳述添加到意圖時，該陳述不再存儲為要審閱的終結點陳述的一部分。 它添加到應用的意圖。 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>從意圖中刪除示例陳述

刪除用於進行 [LUIS](luis-reference-regions.md) 定型的範例語句。 若您從 LUIS 應用程式刪除範例語句，則會從 LUIS Web 服務中予以移除，而且無法用於匯出。

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>從活動學習中刪除審閱中的話語

您可以在 [[Review endpoint utterances](luis-how-to-review-endpoint-utterances.md)****] \(檢閱端點語句\) 頁面中，從 LUIS 所建議的使用者語句清單中刪除語句。 從此清單中刪除語句可防止系統建議這些語句，但並不會從記錄中刪除它們。

如果您不希望主動學習話語，可以[禁用活動學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 禁用活動學習也會禁用日誌記錄。

### <a name="disable-logging-utterances"></a>禁用日誌記錄陳述
[禁用活動學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)將禁用日誌記錄。


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
