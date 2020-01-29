---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c81aaa02af0895e6c3a851fe15273d85de9b55bc
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262738"
---
用戶端應用程式必須知道語句是否對應用程式沒有意義或不適當。 **None** 意圖會在建立過程中新增至每個應用程式，以判斷用戶端應用程式是否不應回答語句。

如果 LUIS 針對語句傳回 [None]  意圖時，用戶端應用程式可以詢問使用者是否想要結束交談，或提供更多有關繼續交談的指示。 

如果您將 [無]  意圖保留空白，則會在其中一個現有的主體網域意圖中預測應在主體網域外部預測的表達。 結果就是用戶端應用程式 (例如聊天機器人) 將根據不正確的預測來執行錯誤的作業。 

1. 選取左面板中的 [意圖]  。

1. 選取 [無]  意圖。 新增您的使用者可能輸入、但與您的比薩訂購應用程式無關的三個語句。

    |`None` 範例語句|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    這些範例不應使用您在主題領域中預期的文字，例如 `pizza`、`cheese`、`crust`、`pickup`、`deliver`。