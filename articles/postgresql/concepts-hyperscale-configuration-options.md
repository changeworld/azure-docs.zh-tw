---
title: 性能選項 • 超大規模（Citus） - 用於後格雷SQL的 Azure 資料庫
description: 超大規模（Citus）伺服器組的選項，包括節點計算、存儲和區域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462406"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>用於後格雷SQL的 Azure 資料庫 – 超大規模（Citus） 性能選項

## <a name="compute-and-storage"></a>計算和儲存體
 
您可以為超大規模 （Citus） 伺服器組中的工作節點和協調器節點單獨選擇計算和存儲設置。  計算資源會以 vCore 的形式提供，vCore 代表了基礎硬體的邏輯 CPU。 用於預配的存儲大小是指超大規模 （Citus） 伺服器組中的協調員和輔助節點可用的容量。 存儲包括資料庫檔案、暫存檔案、事務日誌和 Postgres 伺服器日誌。 預配的存儲總量還定義了每個輔助角色和協調器節點可用的 I/O 容量。
 
|                       | 背景工作節點           | 協調員節點      |
|-----------------------|-----------------------|-----------------------|
| 計算，vCore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| 每個 vCore 的記憶體，GiB | 8                     | 4                     |
| 存儲大小，TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 儲存體類型          | 通用（SSD） | 通用（SSD） |
| IOPS                  | 最多 3 個 IOPS/GiB      | 最多 3 個 IOPS/GiB      |


## <a name="regions"></a>區域
超大規模 （Citus） 伺服器組在以下 Azure 區域中可用：

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

某些區域最初可能無法在所有 Azure 訂閱上啟動。 如果要使用上面清單中的區域，並且在訂閱中看不到它，或者要使用此清單中未的區域，請打開[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="pricing"></a>定價
如需最新的定價資訊，請參閱服務的[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/)。
要查看所需配置的成本[，Azure 門戶](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)會根據您選擇的選項在"**配置"** 選項卡上顯示每月成本。 如果您沒有 Azure 訂用帳戶，則可以使用 Azure 價格計算機來取得估計的價格。 在[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)網站上，選擇 **"添加專案**"，展開**資料庫**類別，然後選擇 **"後格雷SQL" - 超縮放（Citus） 的 Azure 資料庫**以自訂選項。
 
## <a name="next-steps"></a>後續步驟
瞭解如何[在門戶中創建超大規模 （Citus） 伺服器組](quickstart-create-hyperscale-portal.md)。
