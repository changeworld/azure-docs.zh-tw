---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.openlocfilehash: 1509d175979bb65c467424db5de967f56825a3f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545575"
---
用戶端應用程式必須知道語句是否對應用程式沒有意義或不適當。 **None** 意圖會在建立過程中新增至每個應用程式，以判斷用戶端應用程式是否不應回答語句。

如果 LUIS 針對語句傳回 [None] 意圖時，用戶端應用程式可以詢問使用者是否想要結束交談，或提供更多有關繼續交談的指示。

如果您將 [無] 意圖保留空白，則會在其中一個現有的主體網域意圖中預測應在主體網域外部預測的表達。 結果就是用戶端應用程式 (例如聊天機器人) 將根據不正確的預測來執行錯誤的作業。

1. 選取左面板中的 [意圖]。

1. 選取 [無] 意圖。 新增您的使用者可能輸入、但與您的比薩訂購應用程式無關的三個語句。

    |`None` 範例語句|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    這些範例不應使用您在主題領域中預期的文字，例如 `pizza`、`cheese`、`crust`、`pickup`、`deliver`。