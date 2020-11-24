---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555018"
---
| 屬性 | 描述 |
|:--- |:---|
|**accountName** | 儲存體帳戶的名稱。 例如： `mystorageaccount` 。  |
|**requestUrl** | 要求的 URL。 |
|**userAgentHeader** | **使用者代理程式標頭** 值，以引號括住。 例如： `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)` 。|
|**referrerHeader** | **查閱者** 標頭值。 例如： `http://contoso.com/about.html` 。|
|**clientRequestId** | 要求的 **x-ms-client-request-id** 標頭值。 例如： `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6` 。 |
|**etag** | 所傳回物件的 ETag 識別碼 (以引號括住)。 例如： `0x8D101F7E4B662C4` 。  |
|**serverLatencyMs** | 執行要求作業的總時間 (以毫秒表示)。 此值不包括網路延遲 (讀取連入要求和傳送回應給要求者的時間)。 例如： `22` 。 |
|**serviceType** | 與此要求相關聯的服務。 例如：`blob`、`table`、`files` 或 `queue`。 |
|**operationCount** | 要求中牽涉到的每個已記錄作業的數目。 此計數會從 `0` 的索引開始。 某些要求需要一個以上的作業。 大部分的要求只會執行一項作業。 例如： `1` 。 |
|**requestHeaderSize** | 要求標頭的大小 (以位元組表示)。 例如： `578` 。 <br>如果要求不成功，這個值可能是空值。 |
|**requestBodySize** | 儲存體服務讀取的要求封包大小 (以位元組表示)。 <br> 例如： `0` 。 <br>如果要求不成功，這個值可能是空值。  |
|**responseHeaderSize** | 回應標頭的大小 (以位元組表示)。 例如： `216` 。 <br>如果要求不成功，這個值可能是空值。  |
|**responseBodySize** | 儲存體服務寫入的回應封包大小 (以位元組為單位)。 如果要求不成功，這個值可能是空值。 例如： `216` 。  |
|**requestMd5** | 要求中 **Content-MD5** 標頭或 **x-ms-content-md5** 標頭的值。 在此欄位中指定的 MD5 雜湊值代表要求中的內容。 例如： `788815fd0198be0d275ad329cafd1830` 。 <br>這個欄位可以是空白的。  |
|**serverMd5** | 儲存體服務計算的 MD5 雜湊值。 例如： `3228b3cf1069a5489b298446321f8521` 。 <br>這個欄位可以是空白的。  |
|**lastModifiedTime** | 所傳回物件的上次修改時間 (LMT)。  例如： `Tuesday, 09-Aug-11 21:13:26 GMT` 。 <br>對於可傳回多個物件的作業而言，此欄位是空白的。 |
|**conditionsUsed** | 代表條件的索引鍵/值組分號分隔清單。 條件可以是下列任何一項： <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> 例如： `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT` 。 |
|**contentLengthHeader** | 傳送至儲存體服務的要求內容長度標頭值。 如果要求成功，則此值等於 requestBodySize。 如果要求不成功，則此值可能不等於 requestBodySize，或可能是空值。 |
|**tlsVersion** | 要求連線中使用的 TLS 版本。 例如： `TLS 1.2` 。 |
|**smbTreeConnectID** | 伺服器訊息區 (SMB) **treeConnectId** 會在樹狀結構連線時間建立。 例如： `0x3` |
|**smbPersistentHandleID** | 網路重新連線後持續存在的 SMB2 CREATE 要求，其中的持續控點識別碼。  在 [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 中參考為 **SMB2_FILEID.Persistent**。 例如： `0x6003f` |
|**smbVolatileHandleID** | 在網路重新連線時回收的 SMB2 CREATE 要求，其中的變動性控點識別碼。  在 [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 中參考為 **SMB2_FILEID.Volatile**。 例如： `0xFFFFFFFF00000065` |
|**smbMessageID** | 連線相對 **MessageId**。 例如： `0x3b165` |
|**smbCreditsConsumed** | 要求所使用的輸入或輸出 (單位為 64k)。 例如： `0x3` |
|**smbCommandDetail** | 此特定要求的詳細資訊，而不是一般的要求類型。 例如： `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | 與檔案或目錄相關聯的 **FileId**。  大致類似於 NTFS 欄位。 例如： `0x9223442405598953` |
|**smbSessionID** | 在工作階段設定期間建立的 SMB2 **SessionId**。 例如： `0x8530280128000049` |
|**smbCommandMajor  uint32** | **SMB2_HEADER.Command** 中的值。 目前，這是介於 0 到 18 (含) 之間的數字。 例如： `0x6` |
|**smbCommandMinor** | 在適當的情況下，**SmbCommandMajor** 的子類別。 例如： `DirectoryCloseAndDelete` |