---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224447"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>關鍵事務（每個區域<sup>1</sup>每個保存庫在 10 秒內允許的最大事務）：

|金鑰類型|HSM 金鑰<br>創建金鑰|HSM 金鑰<br>所有其他交易|軟體金鑰<br>創建金鑰|軟體金鑰<br>所有其他交易|
|:---|---:|---:|---:|---:|
|RSA 2，048 位|5|1,000|10|2,000|
|RSA 3，072 位|5|250|10|500|
|RSA 4，096 位|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 在上表中，我們看到，對於 RSA 2，048 位軟體金鑰，每 10 秒允許 2，000 個 GET 事務。 對於 RSA 2，048 位 HSM 金鑰，每 10 秒允許 1，000 個 GET 事務。
>
> 限制閾值是加權的，並且強制執行在其總和上。 例如，如上表所示，當您對 RSA HSM 鍵執行 GET 操作時，使用 4，096 位金鑰的成本是 2，048 位金鑰的八倍。 這是因為 1，000/125 = 8。
>
> 在給定的 10 秒間隔內，Azure 金鑰保存庫用戶端在遇到限制 HTTP 狀態碼之前`429`只能執行以下*操作之*一：
> - 2，000 RSA 2，048 位軟體金鑰 GET 事務
> - 1，000 RSA 2，048 位 HSM 金鑰 GET 事務
> - 125 RSA 4，096 位 HSM 金鑰 GET 事務
> - 124 RSA 4，096 位 HSM 金鑰 GET 事務和 8 個 RSA 2，048 位 HSM 金鑰 GET 事務

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>機密、託管存儲帳戶金鑰和保存庫事務：
| 交易類型 | 每個<sup>區域</sup>1 的保存庫在 10 秒內允許的最大事務 |
| --- | --- |
| 所有交易 |2,000 |

有關如何在超出這些限制時處理限制的資訊，請參閱[Azure 金鑰保存庫限制指南](../articles/key-vault/key-vault-ovw-throttling.md)。

<sup>1</sup>所有事務類型的訂閱範圍限制是每個金鑰保存庫限制的五倍。 例如，每次訂閱的 HSM 其他事務限制為每個訂閱在 10 秒內進行 5，000 個事務。
