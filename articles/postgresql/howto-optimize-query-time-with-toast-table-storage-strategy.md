---
title: 使用適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的快顯資料表儲存體策略來優化查詢時間
description: 本文說明如何使用適用於 PostgreSQL 的 Azure 資料庫單一伺服器上的快顯表儲存策略來優化查詢時間。
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ac1dc43a2b89bc1cc748947ec08e6ada87edbfcb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "65066990"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>使用快顯通知資料表儲存策略最佳化查詢時間 
本文說明如何使用快顯通知資料表儲存策略最佳化查詢時間。

## <a name="toast-table-storage-strategies"></a>快顯通知資料表儲存策略
有四個不同的策略可用來將資料行儲存在可使用快顯通知的磁碟上， 它們分別代表壓縮和非正規儲存之間的各種組合。 此策略可設定於資料類型層級和資料行層級上。
- **一般**可防止壓縮或非正規儲存。 這個策略會停用 varlena 類型的單一位元組標頭。 「一般」是不可使用快顯通知資料類型的資料行唯一可用的策略。
- **擴充**可允許壓縮和非正規儲存。 「擴充」是多數可使用快顯通知資料類型的預設值。 會先嘗試進行壓縮， 如果資料列仍然太大，才會嘗試進行非正規儲存。
- **外部**可允許非正規儲存，但不允許壓縮。 使用「外部」會讓寬文字和 bytea 資料行的子字串作業更為快速， 但快速的代價是佔用較多儲存空間。 這些作業已最佳化，在非正規值未壓縮時僅擷取其必要部分。
- **主要**可允許壓縮，但不允許非正規儲存。 對於這類資料行仍會執行非正規儲存，但僅作為最後手段。 在沒有任何其他方法可縮小資料列使其容納於頁面內時，才會採用這個策略。

## <a name="use-toast-table-storage-strategies"></a>使用快顯通知資料表儲存策略
如果您的查詢會存取可使用快顯通知的資料類型，請考慮使用「主要」策略 (而非預設的「擴充」選項)，以縮短查詢時間。 「主要」不會排除非正規儲存。 如果您的查詢不會存取可使用快顯通知的資料類型，使用預設的「擴充」選項可能會有所助益。 主要資料表中較大的資料列部分會容納於共用的緩衝區快取中，而有助於提升效能。

如果您有工作負載使用具有寬型資料表和高字元計數的結構描述，請考慮使用 PostgreSQL 快顯通知資料表。 範例客戶資料表有超過 350 個資料行，其中有數個資料行超過 255 個字元。 在轉換為快顯通知資料表「主要」策略後，其基準測試查詢時間從 4203 秒降低為 467 秒， 速度提升 89%。

## <a name="next-steps"></a>後續步驟
檢閱您的工作負載是否有前述特性。 

檢閱下列 PostgreSQL 文件： 
- [Chapter 68, Database physical storage](https://www.postgresql.org/docs/current/storage-toast.html) 