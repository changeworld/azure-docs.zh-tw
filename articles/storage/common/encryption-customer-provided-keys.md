---
title: 在請求 Blob 存儲時提供加密金鑰
titleSuffix: Azure Storage
description: 針對 Azure Blob 存儲發出請求的用戶端可以選擇根據請求（預覽）提供加密金鑰。 在請求中包括加密金鑰可對 Blob 存儲操作的加密設定進行精細控制。
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410060"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>在請求 Blob 存儲時提供加密金鑰（預覽）

針對 Azure Blob 存儲發出請求的用戶端可以選擇根據請求（預覽）提供加密金鑰。 在請求中包括加密金鑰可對 Blob 存儲操作的加密設定進行精細控制。 客戶提供的金鑰可以存儲在 Azure 金鑰保存庫或其他金鑰存儲中。

## <a name="encrypting-read-and-write-operations"></a>加密讀取和寫入操作

當用戶端應用程式在請求上提供加密金鑰時，Azure 存儲在讀取和寫入 Blob 資料時透明地執行加密和解密。 Azure 存儲在 blob 的內容旁邊寫入加密金鑰的 SHA-256 雜湊。 雜湊用於驗證針對 Blob 的所有後續操作是否使用相同的加密金鑰。

Azure 存儲不存儲或管理用戶端隨請求一起發送的加密金鑰。 加密或解密過程完成後，金鑰將立即安全丟棄。

當用戶端使用客戶提供的金鑰在請求上創建或更新 Blob 時，該 blob 的後續讀取和寫入請求也必須提供該金鑰。 如果尚未在請求已使用客戶提供的金鑰加密的 Blob 上提供金鑰，則請求將失敗，錯誤代碼 409（衝突）。

如果用戶端應用程式在請求上發送加密金鑰，並且存儲帳戶也使用 Microsoft 管理的金鑰或客戶管理的金鑰進行加密，則 Azure 存儲將使用加密和解密請求上提供的金鑰。

要將加密金鑰作為請求的一部分發送，用戶端必須使用 HTTPS 建立與 Azure 存儲的安全連線。

每個 Blob 快照可以有自己的加密金鑰。

## <a name="request-headers-for-specifying-customer-provided-keys"></a>請求指定客戶提供的金鑰的標頭

對於 REST 調用，用戶端可以使用以下標頭在請求上安全地將加密金鑰資訊傳遞給 Blob 存儲：

|要求標頭 | 描述 |
|---------------|-------------|
|`x-ms-encryption-key` |寫入和讀取請求都是必需的。 Base64 編碼的 AES-256 加密金鑰值。 |
|`x-ms-encryption-key-sha256`| 寫入和讀取請求都是必需的。 加密金鑰的 Base64 編碼 SHA256。 |
|`x-ms-encryption-algorithm` | 寫入請求是必需的，可為讀取請求提供。 指定使用給定金鑰加密資料時要使用的演算法。 必須為 AES256。 |

在請求上指定加密金鑰是可選的。 但是，如果為寫入操作指定上面列出的標頭之一，則必須指定所有這些標頭。

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>支援客戶提供金鑰的 Blob 存儲操作

以下 Blob 存儲操作支援在請求上發送客戶提供的加密金鑰：

- [Put Blob](/rest/api/storageservices/put-blob) \(英文\)
- [Put Block List](/rest/api/storageservices/put-block-list) \(英文\)
- [放置區塊](/rest/api/storageservices/put-block)
- [從 URL 放置塊](/rest/api/storageservices/put-block-from-url)
- [放置頁面](/rest/api/storageservices/put-page)
- [從 URL 中放置頁面](/rest/api/storageservices/put-page-from-url)
- [附加區塊](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties) \(英文\)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [取得 Blob](/rest/api/storageservices/get-blob)
- [獲取 Blob 屬性](/rest/api/storageservices/get-blob-properties)
- [取得 Blob 中繼資料](/rest/api/storageservices/get-blob-metadata)
- [快照 Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>旋轉客戶提供的金鑰

要旋轉用於加密 Blob 的加密金鑰，請下載 Blob，然後使用新的加密金鑰重新上載它。

> [!IMPORTANT]
> Azure 門戶不能用於讀取或寫入使用請求上提供的金鑰加密的容器或 Blob。
>
> 請確保保護在安全金鑰存儲（如 Azure 金鑰保存庫）中請求 Blob 存儲時提供的加密金鑰。 如果嘗試在沒有加密金鑰的容器或 Blob 上寫入操作，該操作將失敗，並且您將無法訪問該物件。

## <a name="next-steps"></a>後續步驟

- [在請求使用 .NET 的 Blob 存儲時指定客戶提供的金鑰](../blobs/storage-blob-customer-provided-key.md)
- [靜態資料的 Azure 存儲加密](storage-service-encryption.md)
