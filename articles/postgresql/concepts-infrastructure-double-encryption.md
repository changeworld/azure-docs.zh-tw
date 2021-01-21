---
title: 基礎結構雙重加密-適用於 PostgreSQL 的 Azure 資料庫
description: 瞭解如何使用基礎結構雙重加密，以服務管理的金鑰來新增第二層的加密。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 83635b732318a4ada76d1d71c1ce419cae8b35e9
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630136"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>適用於 PostgreSQL 的 Azure 資料庫基礎結構雙重加密

適用於 PostgreSQL 的 Azure 資料庫針對使用 Microsoft 管理的金鑰的資料使用待用資料的儲存體 [加密](concepts-security.md#at-rest) 。 資料（包括備份）會在磁片上加密，且此加密一律為開啟狀態，且無法停用。 加密使用 FIPS 140-2 驗證的密碼編譯模組，以及適用于 Azure 儲存體加密的 AES 256 位加密。

基礎結構雙重加密會使用服務管理的金鑰來新增第二層的加密。 它使用 FIPS 140-2 驗證的密碼編譯模組，但使用不同的加密演算法。 這可為您的待用資料提供額外一層保護。 基礎結構雙重加密中使用的金鑰也是由適用於 PostgreSQL 的 Azure 資料庫服務所管理。 預設不會啟用基礎結構雙重加密，因為額外的加密層級可能會對效能造成影響。

> [!NOTE]
> 只有適用於 PostgreSQL 的 Azure 資料庫中的「一般用途」和「記憶體優化」定價層才支援這項功能。

基礎結構層加密具有在最接近存放裝置或網路線路的層級上執行的優點。 適用於 PostgreSQL 的 Azure 資料庫使用服務管理的金鑰來執行兩層加密。 雖然在技術上仍是服務層級，但很接近儲存待用資料的硬體。 您仍然可以選擇使用 [客戶管理的金鑰](concepts-data-encryption-postgresql.md) ，為布建的于 postgresql 伺服器啟用資料加密。  

基礎結構層的執行也支援多樣性的金鑰。 基礎結構必須留意不同的電腦和網路叢集。 因此，會使用不同的金鑰來將基礎結構攻擊的群發半徑降至最低，並將各種硬體和網路故障降至最低。 

> [!NOTE]
> 由於額外的加密程式，使用基礎結構雙重加密將對適用於 PostgreSQL 的 Azure 資料庫伺服器造成效能上的影響。

## <a name="benefits"></a>優點

適用於 PostgreSQL 的 Azure 資料庫的基礎結構雙重加密提供下列優點：

1. **額外的** 密碼編譯採用多樣性-規劃移至硬體式加密，除了以軟體為基礎的執行之外，還提供硬體式的執行，進一步 [多樣化了實現。
2. **執行錯誤** -在基礎結構層的兩層加密可防止在更高層級內公開純文字資料的任何錯誤。 此外，這兩個層級也可確保在一般情況下執行加密時的錯誤。

這些組合提供強大的保護，以防範用來攻擊密碼編譯的常見威脅和弱點。

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>使用基礎結構雙重加密的支援案例

適用於 PostgreSQL 的 Azure 資料庫所提供的加密功能可以一起使用。 以下是您可以使用的各種案例摘要：

|  ##   | 預設加密 | 基礎結構雙重加密 | 使用客戶管理的金鑰進行資料加密  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *是*              | *否*                             | *否*                                         |
| 2     | *是*              | *是*                            | *否*                                         |
| 3     | *是*              | *否*                             | *是*                                        |
| 4     | *是*              | *是*                            | *是*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - 案例2和4將對適用於 PostgreSQL 的 Azure 資料庫伺服器造成效能影響，因為額外的基礎結構加密層級。
> - 只有在伺服器建立期間，才允許為適用於 PostgreSQL 的 Azure 資料庫設定基礎結構雙精確度加密。 布建伺服器之後，您就無法變更儲存體加密。 不過，您仍然可以使用客戶管理的金鑰來啟用使用客戶管理的金鑰來建立資料加密，而不需要基礎結構雙重加密。

## <a name="limitations"></a>限制

針對適用於 PostgreSQL 的 Azure 資料庫，使用服務管理的金鑰的基礎結構雙重加密支援有下列限制：

* 這項功能的支援僅限於 **一般用途** 和 **記憶體優化** 定價層。
* 這項功能只在支援儲存體最多 16 TB 的區域和伺服器上受到支援。 如需支援儲存體最多 16 TB 的 Azure 區域清單，請參閱 [儲存體檔](concepts-pricing-tiers.md#storage)。

    > [!NOTE]
    > - 上述區域中所建立的所有 **新** 于 postgresql 伺服器也支援使用客戶管理金鑰進行資料加密。 在此情況下，透過時間點還原所建立的伺服器 (PITR) 或讀取複本不符合「新增」的資格。
    > - 若要驗證您布建的伺服器是否支援最多 16 TB 的功能，您可以移至入口網站中的 [定價層] 分頁，並查看儲存體滑杆是否可移至 16 TB。 如果您只能將滑杆移至 4 TB，則您的伺服器可能不支援使用客戶管理的金鑰進行加密;不過，資料會隨時使用服務管理的金鑰進行加密。 AskAzureDBforPostgreSQL@service.microsoft.com如果您有任何問題，請與您聯繫。

## <a name="next-steps"></a>下一步

瞭解如何 [設定適用于于 postgresql 的 Azure 資料庫的基礎結構雙重加密](howto-double-encryption.md)。
