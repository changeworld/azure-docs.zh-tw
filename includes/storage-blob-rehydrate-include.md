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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684094"
---
若要閱讀封存儲存體中的資料，您必須先將 blob 層變更為經常性存取或非經常性存取。 此程式稱為解除凍結，可能需要數小時才能完成。 我們建議大型 blob 大小以獲得最佳的解除凍結效能。 同時解除凍結數個小型 blob 可能會增加額外時間。 目前有兩個解除凍結優先順序：高和標準，可以透過[設定 Blob 層](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)或[複製 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)作業上的選擇性 [ *x-解除凍結-優先順序*] 屬性來設定。

* **標準優先順序**：解除凍結要求會依照收到的連續處理，最多可能需要15小時。
* **高優先順序**：解除凍結要求的優先順序高於標準要求，而且可能會在1小時內完成。 高優先順序可能需要超過1小時的時間，視 blob 大小和目前的需求而定。 高優先順序的要求保證優先于標準優先順序要求。

> [!NOTE]
> [標準優先權] 是 [封存] 的預設解除凍結選項。 高優先順序是較快速的選項，其成本高於標準的優先順序解除凍結，而且通常保留供緊急資料還原情況使用。

一旦起始解除凍結要求，就無法取消。 在解除凍結程式期間， *x 毫秒-存取層*blob 屬性會繼續顯示為封存，直到解除凍結完成到線上層為止。 若要確認解除凍結狀態和進度，您可以呼叫 [[取得 Blob 屬性](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)] 來檢查 [ *x 毫秒-封存狀態*] 和 [ *x-解除凍結-優先順序*] Blob 屬性。 根據解除凍結目的地層而定，封存狀態可以讀取 "解除凍結-pending to 經常性" 或 "解除凍結-pending"。 解除凍結優先權會指出「高」或「標準」的速度。 完成時，會移除封存狀態和解除凍結優先順序屬性，而 [存取層] blob 屬性會更新，以反映選取的經常性存取或非經常性存取層。
