---
title: 針對 Blob 儲存體的要求提供加密金鑰
titleSuffix: Azure Storage
description: 對 Azure Blob 儲存體提出要求的用戶端，可以選擇以每個要求為基礎（預覽）提供加密金鑰。 在要求中包含加密金鑰，可讓您更精確地控制 Blob 儲存體作業的加密設定。
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0e5a85bcc4ded3b4bf3fcbcaf095d7c8ef01c458
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84805308"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>在 Blob 儲存體的要求上提供加密金鑰（預覽）

對 Azure Blob 儲存體提出要求的用戶端，可以選擇以每個要求為基礎（預覽）提供加密金鑰。 在要求中包含加密金鑰，可讓您更精確地控制 Blob 儲存體作業的加密設定。 客戶提供的金鑰可以儲存在 Azure Key Vault 或另一個金鑰存放區中。

## <a name="encrypting-read-and-write-operations"></a>加密讀取和寫入作業

當用戶端應用程式在要求上提供加密金鑰時，Azure 儲存體會在讀取和寫入 blob 資料時，以透明的方式執行加密和解密。 Azure 儲存體會將加密金鑰的 SHA-256 雜湊連同 blob 的內容一起寫入。 雜湊是用來確認所有對 blob 的後續作業都使用相同的加密金鑰。

Azure 儲存體不會儲存或管理用戶端與要求一起傳送的加密金鑰。 加密或解密程序完成後，就會安全地捨棄金鑰。

當用戶端在要求上使用客戶提供的金鑰來建立或更新 blob 時，該 blob 的後續讀取和寫入要求也必須提供金鑰。 如果未在已使用客戶提供的金鑰加密之 blob 的要求上提供金鑰，則要求會失敗，錯誤碼為409（衝突）。

如果用戶端應用程式在要求上傳送加密金鑰，而且也使用 Microsoft 管理的金鑰或客戶管理的金鑰來加密儲存體帳戶，則 Azure 儲存體會使用要求中提供的金鑰進行加密和解密。

若要傳送加密金鑰做為要求的一部分，用戶端必須使用 HTTPS 建立與 Azure 儲存體的安全連線。

每個 blob 快照集都可以有自己的加密金鑰。

## <a name="request-headers-for-specifying-customer-provided-keys"></a>要求標頭，用於指定客戶提供的金鑰

針對 REST 呼叫，用戶端可以使用下列標頭，安全地將要求的加密金鑰資訊傳遞至 Blob 儲存體：

|要求標頭 | Description |
|---------------|-------------|
|`x-ms-encryption-key` |寫入和讀取要求都需要。 Base64 編碼的 AES-256 加密金鑰值。 |
|`x-ms-encryption-key-sha256`| 寫入和讀取要求都需要。 加密金鑰的 Base64 編碼 SHA256。 |
|`x-ms-encryption-algorithm` | 寫入要求的必要，對於讀取要求則為選擇性。 指定使用指定的索引鍵來加密資料時，所要使用的演算法。 必須是 AES256。 |

在要求上指定加密金鑰是選擇性的。 不過，如果您指定上面列出的其中一個標頭來進行寫入作業，則必須指定它們的全部。

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>支援客戶提供之金鑰的 Blob 儲存體作業

下列 Blob 儲存體作業支援在要求上傳送客戶提供的加密金鑰：

- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置區塊清單](/rest/api/storageservices/put-block-list)
- [放置區塊](/rest/api/storageservices/put-block)
- [將區塊從 URL 放入](/rest/api/storageservices/put-block-from-url)
- [放置頁面](/rest/api/storageservices/put-page)
- [從 URL 放置頁面](/rest/api/storageservices/put-page-from-url) (機器翻譯)
- [附加區塊](/rest/api/storageservices/append-block)
- [設定 Blob 屬性](/rest/api/storageservices/set-blob-properties)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [取得 Blob](/rest/api/storageservices/get-blob)
- [取得 Blob 屬性](/rest/api/storageservices/get-blob-properties)
- [取得 Blob 中繼資料](/rest/api/storageservices/get-blob-metadata)
- [快照 Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>輪替客戶提供的金鑰

若要輪替用來加密 blob 的加密金鑰，請下載 blob，然後使用新的加密金鑰重新上傳它。

> [!IMPORTANT]
> Azure 入口網站不能用來讀取或寫入使用要求所提供之金鑰加密的容器或 blob。
>
> 請務必保護您在 Azure Key Vault 的安全金鑰存放區中，對 Blob 儲存體的要求所提供的加密金鑰。 如果您嘗試在沒有加密金鑰的容器或 blob 上進行寫入作業，此操作將會失敗，而且您將無法存取物件。

## <a name="next-steps"></a>後續步驟

- [使用 .NET 在 Blob 儲存體的要求上指定客戶提供的金鑰](../blobs/storage-blob-customer-provided-key.md)
- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
