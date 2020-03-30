---
title: 包含檔案
description: 包含檔案
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780230"
---
若要閱讀封存儲存體中的資料，您必須先將 blob 層變更為經常性存取或非經常性存取。 此過程稱為補液，可能需要數小時才能完成。 我們建議使用大 Blob 大小，以實現最佳的補液性能。 同時解除凍結數個小型 blob 可能會增加額外時間。 目前有兩個重新水合優先順序，即"高（預覽"）和"標準"，可通過[設置 Blob 層](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)或複製[Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作上的可選*x-ms-rehydrate 優先屬性*進行設置。

* **標準優先順序**：補液請求按收到的連續處理，可能需要長達 15 小時。
* **高優先順序（預覽）：** 補液請求將優先于標準請求，並可能在 1 小時內完成。 高優先順序可能需要超過 1 小時，具體取決於 blob 大小和當前需求。 高優先順序請求保證優先于標準優先順序請求。

> [!NOTE]
> 標準優先順序是存檔的預設補液選項。 高優先順序是一個比標準優先順序補液成本更高、通常保留用於緊急資料恢復情況的更快選項。

一旦啟動補液請求，則無法取消。 在解除凍結期間，您可以查看 [封存狀態]** blob 屬性，以確認是儲存層否已變更。 視目的地層而定，狀態會顯示為 "rehydrate-pending-to-hot" 或 "rehydrate-pending-to-cool"。 完成後，將刪除存檔狀態屬性，並且 Access *Tier* blob 屬性反映新的熱層或冷層。
