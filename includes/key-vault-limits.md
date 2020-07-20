---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 01b3c9584f3ecddbcdcc6938f5eb469510a47a4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85838694"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>金鑰交易（每個區域的每個保存庫在10秒內允許的最大交易數<sup>1</sup>）：

|金鑰類型|HSM 金鑰<br>建立金鑰|HSM 金鑰<br>所有其他交易|軟體金鑰<br>建立金鑰|軟體金鑰<br>所有其他交易|
|:---|---:|---:|---:|---:|
|RSA 2048 位|5|1,000|10|2,000|
|RSA 3072 位|5|250|10|500|
|RSA 4096 位|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> 在上表中，我們看到 RSA 2048 位軟體金鑰的2000取得每10秒的交易數。 針對 RSA 2048 位 HSM 金鑰1000，允許每10秒取得筆交易。
>
> 節流臨界值為加權，強制執行于其總和。 例如，如上表所示，當您在 RSA HSM 金鑰上執行 GET 作業時，使用4096位金鑰比2048位金鑰更昂貴。 這是因為 1000/125 = 8。
>
> 在指定的10秒間隔內，Azure Key Vault 用戶端只能在遇到節流 HTTP 狀態碼之前，執行下列*其中一項*作業 `429` ：
> - 2000 RSA 2048 位軟體-金鑰取得交易
> - 1000 RSA 2048-位 HSM-金鑰取得交易
> - 125 RSA 4096-位 HSM-金鑰取得交易
> - 124 RSA 4096 位 HSM-金鑰取得交易和 8 RSA 2048-位 HSM-金鑰取得交易

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>秘密、受控儲存體帳戶金鑰和保存庫交易：

| 交易類型 | 每個區域的每個保存庫10秒內允許的最大交易數<sup>1</sup> |
| --- | --- |
| 所有交易 |2,000 |

如需如何在超過這些限制時處理節流的資訊，請參閱[Azure Key Vault 節流指導](../articles/key-vault/key-vault-ovw-throttling.md)方針。

<sup>1</sup>所有交易類型的訂用帳戶範圍限制為每個金鑰保存庫限制的五次。 例如，每個訂用帳戶的 HSM-其他交易限制為每個訂用帳戶10秒內的5000筆交易。

### <a name="azure-private-link-integration"></a>Azure 私人連結整合

> [!NOTE]
> 每個訂閱所啟用私人端點的金鑰保存庫數目屬於可調整的限制。 以下顯示的限制為預設限制。 如果您想要求增加服務的限制，請傳送電子郵件至 akv-privatelink@microsoft.com。 我們會根據個案核准這些要求。

| 資源 | 限制 |
| -------- | ----- |
| 每個金鑰保存庫的私人端點 | 64 |
| 每個訂用帳戶具有私人端點的金鑰保存庫 | 400 |
