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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188726"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB 中的存留時間 (TTL) 

使用 **"存留時間"** 或"TTL"時，Azure Cosmos DB 提供了在特定時間段後自動從容器中刪除專案的能力。 根據預設，您可以在容器層級設定存留時間，且覆寫每個項目的值。 在容器或項目層級設定 TTL 之後，Azure Cosmos DB 會在自從上次修改以來的時間週期後自動移除這些項目。 存留時間值會以秒設定。 配置 TTL 時，系統將根據 TTL 值自動刪除過期專案，而無需用戶端應用程式顯式發出的刪除操作。

刪除過期專案是使用剩餘[請求單元](request-units.md)（即使用者請求未使用的請求單位）的背景工作。 即使在 TTL 過期後，如果容器已滿裝請求，並且沒有足夠的 RU 可用，則資料刪除也會延遲。 一旦有足夠的 R，可以執行刪除操作，就會刪除資料。 儘管資料刪除延遲，但 TTL 過期後任何查詢（通過任何 API）不會返回資料。

## <a name="time-to-live-for-containers-and-items"></a>容器和項目的存留時間

即時值的時間以秒為單位設置，並且從上次修改項時起，它被解釋為增量。 您可以設定容器或容器內項目的存留時間：

1. **容器的存留時間** (使用 `DefaultTimeToLive` 設定)：

   - 如果遺失 (或設為 null)，項目便不會自動過期。

   - 如果存在，並且該值設置為"-1"，則等於無窮大，並且預設情況下專案不會過期。

   - 如果存在且值設置為某個數位 *"n"，* 則專案將在上次修改時間後 *"n"* 秒過期。

2. **項目的存留時間** (使用 `ttl` 設定)：

   - 僅在父容器有 `DefaultTimeToLive` 且未設定為 DefaultTTL 時，才適用此屬性。

   - 如果有的話，它會覆寫父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>存留時間組態

* 如果 TTL 在容器上設置為 *"n"，* 則該容器中的項將在*n*秒後過期。  如果同一容器中的某個項有自己的存留時間，設置為 -1（指示它們不會過期），或者如果某些專案已用其他編號重寫即時設置的時間，則這些專案將基於它們自己配置的 TTL 值過期。 

* 如果未設定容器的 TTL，則此容器中項目的存留時間沒有任何作用。 

* 如果容器的 TTL 設定為 -1，則此容器中存留時間設為 n 的項目會在 n 秒後過期，而其餘的項目則不會過期。

## <a name="examples"></a>範例

本節顯示一些分配給容器和物料的即時值的時間不同的示例：

### <a name="example-1"></a>範例 1

容器上的 TTL 設置為 null（預設時間到即時 = null）

|專案上的 TTL| 結果|
|---|---|
|ttl = 空|    TTL 已禁用。 專案永遠不會過期（預設）。|
|ttl = -1   |TTL 已禁用。 專案永遠不會過期。|
|ttl = 2000 |TTL 已禁用。 專案永遠不會過期。|


### <a name="example-2"></a>範例 2

容器上的 TTL 設置為 -1（預設時間到即時 = -1）

|專案上的 TTL| 結果|
|---|---|
|ttl = 空 |已啟用 TTL。 專案永遠不會過期（預設）。|
|ttl = -1   |已啟用 TTL。 專案永遠不會過期。|
|ttl = 2000 |已啟用 TTL。 專案將在 2000 秒後過期。|


### <a name="example-3"></a>範例 3

容器上的 TTL 設置為 1000（預設時間到 Live = 1000）

|專案上的 TTL| 結果|
|---|---|
|ttl = 空|    已啟用 TTL。 專案將在 1000 秒（預設）後過期。|
|ttl = -1   |已啟用 TTL。 專案永遠不會過期。|
|ttl = 2000 |已啟用 TTL。 專案將在 2000 秒後過期。|

## <a name="next-steps"></a>後續步驟

瞭解如何在以下文章中配置"存留時間"：

* [如何設定存留時間](how-to-time-to-live.md)
