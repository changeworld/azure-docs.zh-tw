---
title: 設定選項-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 超大規模 (Citus) 伺服器群組的選項，包括節點計算、儲存體和區域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165606"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>適用於 PostgreSQL 的 Azure 資料庫–超大規模 (Citus) 設定選項

## <a name="compute-and-storage"></a>計算和儲存
 
您可以針對背景工作節點和超大規模 (Citus) 伺服器群組中的協調器節點，分別選取計算和儲存體設定。  計算資源會以 vCore 的形式提供，vCore 代表了基礎硬體的邏輯 CPU。 布建的儲存體大小是指超大規模 (Citus) 伺服器群組中的協調器和背景工作節點可用的容量。 儲存體包括資料庫檔案、暫存檔案、交易記錄和 Postgres 伺服器記錄。
 
| 資源              | 背景工作節點           | 協調器節點      |
|-----------------------|-----------------------|-----------------------|
| Compute、虛擬核心       | 4、8、16、32、64      | 4、8、16、32、64      |
| 每個 vCore 的記憶體，GiB | 8                     | 4                     |
| 儲存體大小，TiB     | 0.5、1、2             | 0.5、1、2             |
| 儲存體類型          | 一般用途 (SSD)  | 一般用途 (SSD)  |
| IOPS                  | 最多 3 IOPS/GiB      | 最多 3 IOPS/GiB      |

單一超大規模 (Citus) 節點的 RAM 總量是根據選取的虛擬核心數目。

| 虛擬核心 | 一個背景工作節點，GiB RAM | 協調器節點，GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

您布建的儲存體總數量也會定義每個背景工作角色和協調器節點可用的 i/o 容量。

| 儲存體大小，TiB | IOPS 上限 |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6148        |

針對整個超大規模 (Citus) 叢集，匯總的 IOPS 會以下列值運作：

| 背景工作節點 | 0.5 TiB，總 IOPS | 1 TiB，總 IOPS | 2 TiB，總 IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12296            |
| 3            | 4608               | 9216             | 18444            |
| 4            | 6,144               | 12,288            | 24592            |
| 5            | 7680               | 15,360            | 30740            |
| 6            | 9216               | 18432            | 36888            |
| 7            | 10752              | 21504            | 43036            |
| 8            | 12,288              | 24576            | 49184            |
| 9            | 13824              | 27648            | 55332            |
| 10           | 15,360              | 30720            | 61480            |
| 11           | 16896              | 33792            | 67628            |
| 12           | 18432              | 36864            | 73776            |
| 13           | 19968              | 39936            | 79924            |
| 14           | 21504              | 43008            | 86072            |
| 15           | 23040              | 46080            | 92220            |
| 16           | 24576              | 49152            | 98368            |
| 17           | 26112              | 52224            | 104516           |
| 18           | 27648              | 55296            | 110664           |
| 19           | 29184              | 58368            | 116812           |
| 20           | 30720              | 61440            | 122960           |

## <a name="regions"></a>區域
超大規模 (Citus) 伺服器群組適用于下列 Azure 區域：

* 美洲：
    * 加拿大中部
    * 美國中部
    * 美國東部
    * 美國東部 2
    * 美國中北部
    * 美國西部 2
* 亞太地區：
    * 澳大利亞東部
    * 日本東部
    * 南韓中部
    * 東南亞
* 歐洲：
    * 北歐
    * 英國南部
    * 西歐

某些區域可能不會在所有 Azure 訂用帳戶上一開始啟用。 如果您想要使用上述清單中的區域，但在您的訂用帳戶中看不到該區域，或您想要使用不在此清單上的區域，請開啟 [支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="limits-and-limitations"></a>限制

下一節說明超大規模 (Citus) 服務中的容量和功能限制。

### <a name="maximum-connections"></a>連線數目上限

每個于 postgresql 連接 (甚至是閒置的連接) 使用至少 10 MB 的記憶體，因此請務必限制同時連接。 以下是我們選擇讓節點保持狀況良好的限制：

* 協調器節點
   * 最大連線數：300
   * 最大使用者連接數：297
* 背景工作節點
   * 最大連線數：600
   * 最大使用者連接數：597

超過這些限制的嘗試連接將會失敗，並出現錯誤。 系統會保留三個用於監視節點的連線，這就是為什麼使用者查詢有三個較少的連接可供使用者查詢使用。

建立新的連接需要一些時間。 這適用于大部分的應用程式，這會要求許多短期的連接。 建議您使用連接共用器，以減少閒置交易並重複使用現有的連接。 若要深入瞭解，請造訪我們的 [blog 文章](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)。

### <a name="storage-scaling"></a>儲存體調整

協調器與背景工作節點上的儲存空間可以擴大 (增加) 但無法相應縮小 (減少) 。

### <a name="storage-size"></a>儲存體大小

協調器與背景工作節點上支援最多 2 TiB 的儲存體。 如需節點和叢集大小，請參閱 [上面](#compute-and-storage) 的可用儲存體選項和 IOPS 計算。

### <a name="database-creation"></a>建立資料庫

Azure 入口網站會提供認證，以連線至每個超大規模 (Citus) 伺服器群組、資料庫的單一資料庫 `citus` 。 目前不允許建立另一個資料庫，且 CREATE DATABASE 命令將會失敗並出現錯誤。

## <a name="pricing"></a>定價
如需最新的定價資訊，請參閱服務的[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看您想要的設定成本， [Azure 入口網站](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) 會根據您選取的選項，在 [ **設定** ] 索引標籤上顯示每月成本。 如果您沒有 Azure 訂用帳戶，則可以使用 Azure 價格計算機來取得估計的價格。 在 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/) 網站上，選取 **[新增專案**]，展開 [ **資料庫** ] 類別，然後選擇 [ **適用於 PostgreSQL 的 Azure 資料庫–超大規模 (Citus)** 自訂選項。
 
## <a name="next-steps"></a>後續步驟
瞭解如何 [在入口網站中建立超大規模 (Citus) server 群組](quickstart-create-hyperscale-portal.md)。
