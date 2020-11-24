---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: e4abbeadb0d30911d99fff57c0e99a3e427a6d8d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563486"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>金鑰交易 (每個區域中的每個保存庫在 10 秒內允許的交易上限<sup>1</sup>)：

|金鑰類型|HSM 金鑰<br>CREATE 金鑰|HSM 金鑰<br>所有其他交易|軟體金鑰<br>CREATE 金鑰|軟體金鑰<br>所有其他交易|
|:---|---:|---:|---:|---:|
|RSA 2,048 位元|5|1,000|10|2,000|
|RSA 3,072 位元|5|250|10|500|
|RSA 4,096 位元|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 在上表中，我們看到 RSA 2,048 位元軟體金鑰，每 10 秒允許的 2,000 個 GET 交易。 對於 RSA 2,048 位元 HSM 金鑰，每 10 秒允許 1,000 個 GET 交易。
>
> 節流閾值會進行加權，並依其總和強制執行。 例如，如上表所示，當您在 RSA HSM 金鑰上執行 GET 作業時，使用 4,096 位元金鑰比起使用 2,048 位元金鑰貴八倍。 這是因為 1,000/125 = 8。
>
> 在指定的 10 秒間隔內，Azure Key Vault 用戶端在遇到 `429` 節流 HTTP 狀態碼之前，只能執行下列其中一項作業：
> - 2,000 個 RSA 2,048 位元軟體金鑰 GET 交易
> - 1,000 個 RSA 2,048 位元 HSM 金鑰 GET 交易
> - 125 個 RSA 4,096 位元 HSM 金鑰 GET 交易
> - 124 RSA 4,096 位元 HSM 金鑰 GET 交易和 8 RSA 2,048 位元 HSM 金鑰 GET 交易

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>祕密、受控儲存體帳戶金鑰和保存庫交易：

| 交易類型 | 每個區域中的每個保存庫在 10 秒內允許的交易上限<sup>1</sup> |
| --- | --- |
| 所有交易 |2,000 |

如需有關如何在超過這些限制時處理節流的相關資訊，請參閱 [Azure Key Vault 節流指引](../articles/key-vault/general/overview-throttling.md)。

<sup>1</sup> 適用於所有交易類型的全訂用帳戶限制，是每個金鑰保存庫限制的五倍。 例如，每個訂用帳戶的 HSM - 其他交易受限於每個訂閱 10 秒內 5,000 筆交易。

### <a name="azure-private-link-integration"></a>Azure 私人連結整合

> [!NOTE]
> 每個訂閱所啟用私人端點的金鑰保存庫數目屬於可調整的限制。 以下顯示的限制為預設限制。 如果您想要求增加服務的限制，請傳送電子郵件至 akv-privatelink@microsoft.com。 我們會根據個案核准這些要求。

| 資源 | 限制 |
| -------- | ----- |
| 每個金鑰保存庫的私人端點 | 64 |
| 每個訂用帳戶具有私人端點的金鑰保存庫 | 400 |