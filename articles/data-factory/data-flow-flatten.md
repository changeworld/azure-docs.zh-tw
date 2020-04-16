---
title: 映射資料串圖串的扁平轉換
description: 使用拼合轉換並轉換層級資料非規範化
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413683"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>映射資料串圖串的扁平轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用拼合變換在分層結構(如 JSON)中獲取陣列,並將其展開到單獨的行中。 此過程稱為非規範化。

## <a name="configuration"></a>組態

扁平轉換包含以下設定設定

![拼合設定](media/data-flow/flatten1.png "拼合設定")

### <a name="unroll-by"></a>展開者

選擇要展開的陣列。 每個陣列中每個項的輸出數據將具有一行。 如果輸入行中的按陣列展開為空,則將有一個輸出行,其中未滾動的值為空。

### <a name="unroll-root"></a>展開根

默認情況下,拼合轉換將陣列解壓到它存在的層次結構的頂部。 您可以選擇陣列作為展開根。 展開根必須是複雜物件的陣列,這些對像是或包含按陣列展開的。 如果選擇了未卷根,則輸出數據將包含卷根中每個項至少包含一行。 如果輸入行在未滾動根中沒有任何項,它將從輸出數據中刪除。 選擇未卷根始終輸出的行數小於或等於默認行為。

### <a name="flatten-mapping"></a>拼合對應

與選擇轉換類似,從傳入欄位和非規範化陣組中選擇新結構的投影。 如果映射了非規範化陣列,則輸出列將與陣列的數據類型相同。 如果按陣列展開是包含子陣列的複雜物件的陣列,則映射該 subarry 的項將輸出陣列。

請參閱檢查選項卡和數據預覽以驗證映射輸出。

## <a name="examples"></a>範例

有關壓平變換的以下示例,請參閱以下 JSON 物件

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>沒有帶字串陣列的解卷根

| 展開者 | 展開根 | 投射 |
| --------- | ----------- | ---------- |
| 貨物.客戶 | None | NAME <br> 客戶 = 貨物。 |

#### <a name="output"></a>輸出

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>沒有具有複雜陣列的卷根

| 展開者 | 展開根 | 投射 |
| --------- | ----------- | ---------- |
| 貨物訂單訂單訂單訂單訂單訂單訂單 | None | NAME <br> 訂單 Id = 貨物.訂單.訂單Id <br> 專案名稱 = 貨物.訂單.發貨.訂單專案.專案名稱 <br> 物料數量 = 貨物.訂單.發貨.訂單專案.專案數量 <br> 位置 = 位置 |

#### <a name="output"></a>輸出

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>與捲外捲陣列相同的根

| 展開者 | 展開根 | 投射 |
| --------- | ----------- | ---------- |
| 貨物.訂單 | 貨物.訂單 | NAME <br> 貨物.訂單.發貨.訂單專案.專案名稱 <br> 貨物.客戶 <br> location |

#### <a name="output"></a>輸出

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>使用複雜陣列展開根

| 展開者 | 展開根 | 投射 |
| --------- | ----------- | ---------- |
| 貨物訂單訂單訂單訂單訂單訂單訂單 | 貨物.訂單 |NAME <br> 訂單 Id = 貨物.訂單.訂單Id <br> 專案名稱 = 貨物.訂單.發貨.訂單專案.專案名稱 <br> 物料數量 = 貨物.訂單.發貨.訂單專案.專案數量 <br> 位置 = 位置 |

#### <a name="output"></a>輸出

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>範例

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>後續步驟

* 使用[數據透視轉換](data-flow-pivot.md)將行透視到列。
* 使用[「取消透視」轉換](data-flow-unpivot.md)將列透視到行。
