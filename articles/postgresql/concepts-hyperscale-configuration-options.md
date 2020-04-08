---
title: 設定選項 • 超大規模(Citus) - 用於後格雷SQL的 Azure 資料庫
description: 超大規模(Citus)伺服器組的選項,包括節點計算、存儲和區域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804583"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>在後格雷SQL的 Azure 資料庫 – 超大規模(Citus) 設定選項

## <a name="compute-and-storage"></a>計算和儲存體
 
您可以為超大規模 (Citus) 伺服器群組中的工作節點和協調器節點單獨選擇計算和儲存設定。  計算資源會以 vCore 的形式提供，vCore 代表了基礎硬體的邏輯 CPU。 用於預先使用的儲存大小是指超大規模 (Citus) 伺服器組中的協調員和輔助節點可用的容量。 儲存包括資料庫檔案、暫存檔、事務日誌和 Postgres 伺服器日誌。
 
|                       | 背景工作節點           | 協調員節點      |
|-----------------------|-----------------------|-----------------------|
| 計算,vCore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| 每個 vCore 的記憶體,GiB | 8                     | 4                     |
| 儲存大小,TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 儲存體類型          | 一般(SSD) | 一般(SSD) |
| IOPS                  | 最多 3 個 IOPS/GiB      | 最多 3 個 IOPS/GiB      |

單個超大規模 (Citus) 節點中的 RAM 總量基於所選 vCore 的數量。

| 虛擬核心 | 一個工作節點,GiB RAM | 協調器節點,GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

預配的儲存總量還定義了每個輔助角色和協調器節點可用的 I/O 容量。

| 儲存大小,TiB | IOPS 上限 |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

對於整個超量程 (Citus) 叢集,聚合 IOPS 可按以下值計算:

| 背景工作節點 | 0.5 TiB,總 IOPS | 1 TiB,總 IOPS | 2 TiB,總 IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

## <a name="regions"></a>區域
超大規模 (Citus) 伺服器群組在以下 Azure 區域中可用:

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
* 歐洲:
    * 北歐
    * 英國南部
    * 西歐

某些區域最初可能無法在所有 Azure 訂閱上啟動。 如果要使用上面清單中的區域,並且在訂閱中看不到它,或者要使用此清單中未的區域,請打開[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="pricing"></a>定價
如需最新的定價資訊，請參閱服務的[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/)。
要查看所需配置的成本[,Azure 門戶](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)會根據您選擇的選項在「**設定」** 選項卡上顯示每月成本。 如果您沒有 Azure 訂用帳戶，則可以使用 Azure 價格計算機來取得估計的價格。 在[Azure 定價計算器](https://azure.microsoft.com/pricing/calculator/)網站上,選擇 **「新增專案**」,展開**資料庫**類別,然後選擇 **「後格雷SQL」 - 超縮放(Citus) 的 Azure 資料庫**以自訂選項。
 
## <a name="next-steps"></a>後續步驟
瞭解如何[在門戶中建立超大規模 (Citus) 伺服器群組](quickstart-create-hyperscale-portal.md)。
