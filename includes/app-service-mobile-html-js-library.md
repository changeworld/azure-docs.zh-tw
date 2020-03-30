---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174489"
---
## <a name="create-a-client-connection"></a><a name="create-client"></a>創建用戶端連接
建立 `WindowsAzure.MobileServiceClient` 物件來建立用戶端連接。  以您行動應用程式的 URL 取代 `appUrl` 。

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="work-with-tables"></a><a name="table-reference"></a>使用表
若要存取或更新資料，請建立後端資料表的參考。 以您的資料表名稱取代 `tableName`

```javascript
var table = client.getTable(tableName);
```

只要有資料表參考，就進一步使用資料表進行下列作業︰

* [查詢表](#querying)
  * [篩選資料](#table-filter)
  * [逐頁查看資料](#table-paging)
  * [排序資料](#sorting-data)
* [插入資料](#inserting)
* [修改資料](#modifying)
* [刪除資料](#deleting)

### <a name="how-to-query-a-table-reference"></a><a name="querying"></a>操作說明 ：查詢資料表參考
取得資料表參考之後，您可以使用它來查詢伺服器上的資料。  您可以使用「類似 LINQ」的語言來撰寫查詢。
若要從資料表傳回所有資料，使用下列程式碼：

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

搭配 results 呼叫 success 函式。  請勿在 success 函式中使用 `for (var i in results)`，因為當使用其他查詢函式時 (例如 `.includeTotalCount()`)，這樣會反覆檢查結果中包含的資訊。

如需 Query 語法的詳細資訊，請參閱 [Query 物件文件]。

#### <a name="filtering-data-on-the-server"></a><a name="table-filter"></a>篩選伺服器上的資料
您可以在資料表參考上使用 `where` 子句：

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

您也可以使用函式來篩選物件。  在此案例中， `this` 變數會指派給目前篩選的物件。  以下程式碼在功能上等同於先前的範例：

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="paging-through-data"></a><a name="table-paging"></a>通過資料分頁
利用 `take()` 和 `skip()` 方法。  例如，如果您想要將資料表分割成 100 列的記錄：

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

`.includeTotalCount()` 方法是用來將 totalCount 欄位加入至 results 物件。  totalCount 欄位中填入未使用分頁時會傳回的記錄總數。

接著，您可以使用 pages 變數和一些 UI 按鈕來提供頁面清單。您可以使用 `loadPage()` 載入每個頁面的新記錄。  實作快取來加速存取已經載入的記錄。

#### <a name="how-to-return-sorted-data"></a><a name="sorting-data"></a>操作說明：傳回已排序的資料
使用 `.orderBy()` 或 `.orderByDescending()` 查詢方法︰

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

如需 Query 物件的詳細資訊，請參閱 [Query 物件文件]。

### <a name="how-to-insert-data"></a><a name="inserting"></a>操作說明：插入資料
以適當的日期建立 JavaScript 物件，並以非同步方式呼叫 `table.insert()`：

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

在成功插入時，插入的項目及同步處理作業所需的其他欄位會一起傳回。  以這項資訊更新您自己的快取，後續更新時才會正確。

Azure Mobile Apps Node.js Server SDK 支援的動態結構描述適用於開發用途。  動態結構描述可讓您在插入或更新作業中指定資料行，以將資料行新增至資料表。  在將應用程式移至生產環境之前，我們建議您關閉動態結構描述。

### <a name="how-to-modify-data"></a><a name="modifying"></a>操作說明：修改資料
類似於 `.insert()` 方法，您應該建立 Update 物件，然後呼叫 `.update()`。  Update 物件必須包含要更新的記錄的識別碼 - 在讀取記錄或呼叫 `.insert()` 時會取得此識別碼。

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="how-to-delete-data"></a><a name="deleting"></a>操作說明：刪除資料
若要刪除記錄時，請呼叫 `.del()` 方法。  將識別碼傳入物件參考中：

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
