---
title: 在 Azure Cosmos DB 中使用 JavaScript 查詢 API 撰寫預存程式和觸發程式
description: 了解如何使用 JavaScript 查詢 API 在 Azure Cosmos DB 中撰寫預存程序和觸發程序
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 4251a9ec0766f6e1956e4885b58dc874e01b0960
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253066"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>如何使用 JavaScript 查詢 API 在 Azure Cosmos DB 中撰寫預存程序和觸發程序

Azure Cosmos DB 可讓您使用流暢的 JavaScript 介面來執行最佳化查詢，而無須具備任何可用來撰寫預存程序或觸發程序的 SQL 語言知識。 若要深入了解 Azure Cosmos DB 中的 JavaScript 查詢 API 支援，請參閱[在 Azure Cosmos DB 中使用 JavaScript 語言整合式查詢 API](javascript-query-api.md) 一文。

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>使用 JavaScript 查詢 API 撰寫預存程序

下列程式碼範例說明如何在預存程序的內容中使用 JavaScript 查詢 API。 預存程序會插入輸入參數所指定的 Azure Cosmos 項目，然後使用 `__.filter()` 方法，根據輸入項目的大小屬性，以 minSize、maxSize 和 totalSize 來更新中繼資料文件。

> [!NOTE]
> 使用 JavaScript 查詢 API 時，`__` (雙底線) 是 `getContext().getCollection()` 的別名。

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>後續步驟

參閱下列文章以了解 Azure Cosmos DB 中的預存程序、觸發程序和使用者定義函式：

* [如何在 Azure Cosmos DB 中使用預存程序、觸發程序、使用者定義函式](how-to-use-stored-procedures-triggers-udfs.md)

* [如何在 Azure Cosmos DB 中註冊和使用預存程序](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* 如何在 Azure Cosmos DB 中註冊和使用[預先觸發程序](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)和[後續觸發程序](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)

* [如何在 Azure Cosmos DB 中註冊和使用使用者定義函式](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Azure Cosmos DB 中的綜合分割區索引鍵](synthetic-partition-keys.md)
