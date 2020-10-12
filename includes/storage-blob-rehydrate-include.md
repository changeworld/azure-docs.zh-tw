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
ms.openlocfilehash: 53d2d47143c5a2cefbd50faca9a02af18ffae315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84754655"
---
若要閱讀封存儲存體中的資料，您必須先將 blob 層變更為經常性存取或非經常性存取。 此程式稱為解除凍結，可能需要數小時才能完成。 我們建議大型 blob 大小，以獲得最佳的解除凍結效能。 同時解除凍結數個小型 blob 可能會增加額外時間。 目前有兩個解除凍結優先順序：高和標準，可透過[設定 Blob 層](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)或[複製 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)作業上的選擇性*x-解除凍結優先順序*屬性來設定。

* **標準優先順序**：解除凍結要求會依照收到的順序進行處理，最多可能需要15小時的時間。
* **高優先順序**：解除凍結要求會優先于標準要求，並可在1小時內針對大小低於 10 GB 的物件完成。 

> [!NOTE]
> 標準優先權是封存的預設解除凍結選項。 高優先順序是較快的選項，其成本高於標準優先順序解除凍結，通常會保留供緊急資料還原情況使用。
>
> 高優先順序可能需要超過1小時的時間，視 blob 大小和目前的需求而定。 高優先順序的要求保證優先于標準優先權要求。

一旦起始解除凍結要求，便無法取消。 在解除凍結程式期間， *x-ms 存取層* blob 屬性會繼續顯示為封存，直到解除凍結完成至線上層為止。 若要確認解除凍結狀態和進度，您可以呼叫 [取得 Blob 屬性](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) 來檢查 *x ms-封存狀態* 和 *x 毫秒-解除凍結優先順序* 的 blob 屬性。 封存狀態可以讀取「解除凍結-擱置」或「解除凍結-暫止」，取決於解除凍結目的地層。 解除凍結優先權會指出「高」或「標準」的速度。 完成時，會移除封存狀態和解除凍結優先順序屬性，並會更新存取層 blob 屬性以反映選取的經常性存取層或非經常性存取層。
