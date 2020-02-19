---
title: 效能選項–超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 超大規模資料庫（Citus）伺服器群組的選項，包括節點計算、儲存體和區域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462406"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）效能選項

## <a name="compute-and-storage"></a>計算和儲存體
 
您可以針對背景工作節點和超大規模資料庫（Citus）伺服器群組中的協調器節點，獨立選取計算和儲存體設定。  計算資源會以 vCore 的形式提供，vCore 代表了基礎硬體的邏輯 CPU。 布建的儲存體大小是指超大規模資料庫（Citus）伺服器群組中協調器和背景工作角色節點可用的容量。 儲存體包括資料庫檔案、暫存檔案、交易記錄和 Postgres 伺服器記錄。 您布建的儲存體總量也會定義每個背景工作角色和協調器節點可用的 i/o 容量。
 
|                       | 背景工作節點           | 協調器節點      |
|-----------------------|-----------------------|-----------------------|
| Compute、虛擬核心       | 4、8、16、32、64      | 4、8、16、32、64      |
| 記憶體：每個 vCore、GiB | 8                     | 4                     |
| 儲存體大小，TiB     | 0.5、1、2             | 0.5、1、2             |
| 儲存體類型          | 一般用途（SSD） | 一般用途（SSD） |
| IOPS                  | 最多 3 IOPS/GiB      | 最多 3 IOPS/GiB      |


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

## <a name="pricing"></a>價格
如需最新的定價資訊，請參閱服務的[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看您所需的設定成本， [Azure 入口網站](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)會根據您選取的選項，在 [**設定**] 索引標籤上顯示每月成本。 如果您沒有 Azure 訂用帳戶，則可以使用 Azure 價格計算機來取得估計的價格。 在[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)網站上，選取 **[新增專案**]，展開 [**資料庫**] 類別，然後選擇 [**適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）** ] 以自訂選項。
 
## <a name="next-steps"></a>後續步驟
瞭解如何[在入口網站中建立超大規模資料庫（Citus）伺服器群組](quickstart-create-hyperscale-portal.md)。
