---
title: 包含檔案
description: 包含檔案
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684094"
---
若要閱讀封存儲存體中的資料，您必須先將 blob 層變更為經常性存取或非經常性存取。 此過程稱為補液,可能需要數小時才能完成。 我們建議使用大 Blob 大小,以實現最佳的補液性能。 同時解除凍結數個小型 blob 可能會增加額外時間。 目前有兩個重新水合優先順序,即"高"和"標準",可通過[設置 Blob 層](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)或複製[Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作上的可選*x-ms-rehydrate 優先*屬性進行設置。

* **標準優先順序**:補液請求按收到的順序處理,可能需要長達 15 小時。
* **高優先順序**:補液請求優先於標準請求,並可能在 1 小時內完成。 高優先順序可能需要超過 1 小時,具體取決於 blob 大小和當前需求。 高優先順序請求保證優先於標準優先順序請求。

> [!NOTE]
> 標準優先順序是存檔的預設補液選項。 高優先順序是一個比標準優先順序補液成本更高、通常保留用於緊急數據恢復情況的更快選項。

一旦啟動補液請求,則無法取消。 在補液過程中 *,x-ms-access 層*blob 屬性將繼續作為存檔顯示,直到將補液完成到聯機層。 要確認補液狀態和進度,可以調用[Get Blob 屬性](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)來檢查 x ms*存檔狀態*和*x-ms-rehyd-rehyd 優先*blob 屬性。 存檔狀態可以讀取「補水待定到熱」或「補水待定到冷卻」,具體取決於補水目標層。 補水優先順序將指示"高"或"標準"的速度。 完成後,將刪除存檔狀態和補水優先屬性,並且訪問層 blob 屬性將更新以反映所選的熱層或冷層。
