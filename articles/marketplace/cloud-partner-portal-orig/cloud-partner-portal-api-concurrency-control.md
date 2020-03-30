---
title: 併發控制 |Azure 應用商店
description: Cloud Partner 入口網站發佈 API 的並行控制策略。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d0f035d77e74f157b793b9edf3ab5d3494096d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288643"
---
# <a name="concurrency-control"></a>並行控制

對 Cloud Partner 入口網站發佈 API 的每個呼叫都必須明確地指定要使用的並行控制策略。 如果無法提供 **If-Match** 標頭，將會導致 HTTP 400 錯誤回應。 我們針對並行控制提供兩種策略。

-   **開放式** - 執行更新的用戶端會確認自上次讀取資料以來，資料是否已變更。
-   **最後一個優先** - 用戶端會直接更新資料，而不論自上次讀取時間後另一個應用程式是否修改過資料。

<a name="optimistic-concurrency-workflow"></a>開放式並行工作流程
-------------------------------

建議使用開放式並行策略搭配下列工作流程，以確保不會非預期地對您的資源進行編輯。

1.  使用 API 擷取實體。 此回應包含可識別目前儲存之實體版本 (在回應時) 的 ETag 值。
2.  在更新時，將這個相同的 ETag 值包含在必要的 **If-Match** 要求標頭中。
3.  API 會比較要求中收到的 ETag 值與不可部分完成交易中實體目前的 ETag 值。
    *   如果 ETag 值不同，則 API 會傳回 `412 Precondition Failed` HTTP 回應。 此錯誤表示另一個處理序自用戶端上次擷取實體後已更新實體，或者在要求中指定的 ETag 值不正確。
    *  如果 ETag 值相同，或 **If-Match** 標頭包含星號萬用字元 (`*`)，則 API 會執行要求的作業。 此 API 作業也會更新實體的預存 ETag 值。


> [!NOTE]
> 在 **If-Match** 標頭中指定萬用字元 (*) 會導致 API 使用最後一個優先並行策略。 在此情況下，不會進行 ETag 比較，而不需要任何檢查就會更新資源。 
