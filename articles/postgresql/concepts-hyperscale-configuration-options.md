---
title: 設定選項–超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 超大規模資料庫（Citus）伺服器群組的選項，包括節點計算、儲存體和區域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804583"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）設定選項

## <a name="compute-and-storage"></a>計算和儲存體
 
您可以針對背景工作節點和超大規模資料庫（Citus）伺服器群組中的協調器節點，獨立選取計算和儲存體設定。  計算資源會以 vCore 的形式提供，vCore 代表了基礎硬體的邏輯 CPU。 布建的儲存體大小是指超大規模資料庫（Citus）伺服器群組中協調器和背景工作角色節點可用的容量。 儲存體包括資料庫檔案、暫存檔案、交易記錄和 Postgres 伺服器記錄。
 
|                       | 背景工作節點           | 協調器節點      |
|-----------------------|-----------------------|-----------------------|
| Compute、虛擬核心       | 4、8、16、32、64      | 4、8、16、32、64      |
| 記憶體：每個 vCore、GiB | 8                     | 4                     |
| 儲存體大小，TiB     | 0.5、1、2             | 0.5、1、2             |
| 儲存體類型          | 一般用途（SSD） | 一般用途（SSD） |
| IOPS                  | 最多 3 IOPS/GiB      | 最多 3 IOPS/GiB      |

單一超大規模資料庫（Citus）節點中的 RAM 總量是根據選取的虛擬核心數目。

| 虛擬核心 | 一個背景工作節點，GiB RAM | 協調器節點、GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

您布建的儲存體總量也會定義每個背景工作角色和協調器節點可用的 i/o 容量。

| 儲存體大小，TiB | IOPS 上限 |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6148        |

對於整個超大規模資料庫（Citus）叢集，匯總的 IOPS 會使用下列值：

| 背景工作節點 | 0.5 TiB，總 IOPS | 1個 TiB，IOPS 總數 | 2 TiB，總 IOPS |
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
超大規模資料庫（Citus）伺服器群組適用于下列 Azure 區域：

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
* 歐洲
    * 北歐
    * 英國南部
    * 西歐

某些區域可能一開始不會在所有 Azure 訂用帳戶上啟用。 如果您想要使用上述清單中的區域，且在您的訂用帳戶中看不到它，或如果您想要使用不在此清單上的區域，請開啟[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="pricing"></a>定價
如需最新的定價資訊，請參閱服務的[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看您所需的設定成本， [Azure 入口網站](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)會根據您選取的選項，在 [**設定**] 索引標籤上顯示每月成本。 如果您沒有 Azure 訂用帳戶，則可以使用 Azure 價格計算機來取得估計的價格。 在[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)網站上，選取 **[新增專案**]，展開 [**資料庫**] 類別，然後選擇 [**適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）** ] 以自訂選項。
 
## <a name="next-steps"></a>後續步驟
瞭解如何[在入口網站中建立超大規模資料庫（Citus）伺服器群組](quickstart-create-hyperscale-portal.md)。
