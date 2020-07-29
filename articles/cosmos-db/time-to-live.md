---
title: 利用存留時間讓 Azure Cosmos DB 中的資料過期
description: Microsoft Azure Cosmos DB 可讓您利用 TTL 在一段時間後自動從系統清除文件。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77188726"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB 中的存留時間 (TTL) 

有了**存留時間**或 TTL，Azure Cosmos DB 提供在一段時間後自動從容器中刪除專案的功能。 根據預設，您可以在容器層級設定存留時間，且覆寫每個項目的值。 在容器或項目層級設定 TTL 之後，Azure Cosmos DB 會在自從上次修改以來的時間週期後自動移除這些項目。 存留時間值會以秒設定。 當您設定 TTL 時，系統會根據 TTL 值自動刪除過期的專案，而不需要由用戶端應用程式明確發出的刪除作業。

刪除過期的專案是一種背景工作，它會取用由使用者要求未取用的要求單位，而不會耗用由左至右的[要求單位](request-units.md)。 即使在 TTL 過期之後，如果容器是以要求多載，而且沒有足夠的 RU 可用，則資料刪除會延遲。 一旦有足夠的 ru 可執行刪除作業，就會刪除資料。 雖然已延遲刪除資料，但在 TTL 過期之後，任何查詢（由任何 API）都不會傳回資料。

## <a name="time-to-live-for-containers-and-items"></a>容器和項目的存留時間

[存留時間] 值是以秒為單位設定，而且會從上次修改專案的時間中解釋為差異。 您可以設定容器或容器內項目的存留時間：

1. **容器的存留時間** (使用 `DefaultTimeToLive` 設定)：

   - 如果遺失 (或設為 null)，項目便不會自動過期。

   - 如果存在且值設定為 "-1"，則等於無限大，而專案預設不會過期。

   - 如果存在且值設為某個數位 *"n"* –專案會在其上次修改時間後的 *"n"* 秒到期。

2. **項目的存留時間** (使用 `ttl` 設定)：

   - 僅在父容器有 `DefaultTimeToLive` 且未設定為 DefaultTTL 時，才適用此屬性。

   - 如果有的話，它會覆寫父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>存留時間組態

* 如果容器上的 TTL 設定為 *"n"* ，則該容器中的專案會在*n*秒後過期。  如果相同容器中的專案有自己的存留時間，請將設定為-1 （表示它們不會過期），或如果某些專案已使用不同的數位覆寫存留時間設定，這些專案會根據自己設定的 TTL 值而過期。 

* 如果未設定容器的 TTL，則此容器中項目的存留時間沒有任何作用。 

* 如果容器的 TTL 設定為 -1，則此容器中存留時間設為 n 的項目會在 n 秒後過期，而其餘的項目則不會過期。

## <a name="examples"></a>範例

本節顯示指派給容器和專案之存留時間值不同的一些範例：

### <a name="example-1"></a>範例 1

容器上的 TTL 設定為 null （Defaulttimetolive 還長 = null）

|專案上的 TTL| 結果|
|---|---|
|ttl = null|    已停用 TTL。 專案永遠不會過期（預設值）。|
|ttl =-1   |已停用 TTL。 專案永遠不會過期。|
|ttl = 2000 |已停用 TTL。 專案永遠不會過期。|


### <a name="example-2"></a>範例 2

容器上的 TTL 設定為-1 （Defaulttimetolive 還長 =-1）

|專案上的 TTL| 結果|
|---|---|
|ttl = null |已啟用 TTL。 專案永遠不會過期（預設值）。|
|ttl =-1   |已啟用 TTL。 專案永遠不會過期。|
|ttl = 2000 |已啟用 TTL。 此專案會在2000秒後過期。|


### <a name="example-3"></a>範例 3

容器上的 TTL 設定為1000（Defaulttimetolive 還長 = 1000）

|專案上的 TTL| 結果|
|---|---|
|ttl = null|    已啟用 TTL。 此專案會在1000秒後過期（預設值）。|
|ttl =-1   |已啟用 TTL。 專案永遠不會過期。|
|ttl = 2000 |已啟用 TTL。 此專案會在2000秒後過期。|

## <a name="next-steps"></a>後續步驟

在下列文章中瞭解如何設定存留時間：

* [如何設定存留時間](how-to-time-to-live.md)
