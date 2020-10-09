---
title: 在對應資料流程中壓平合併轉換
description: 使用壓平合併轉換反正規化階層式資料
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81413683"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>在對應資料流程中壓平合併轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用「壓平合併」轉換，在階層式結構（例如 JSON）內採用陣列值，並將它們展開成個別的資料列。 此處理程式稱為反正規化。

## <a name="configuration"></a>設定

壓平合併轉換包含下列設定

![簡維設定](media/data-flow/flatten1.png "簡維設定")

### <a name="unroll-by"></a>展開者

選取要展開的陣列。 輸出資料會在每個陣列中的每個專案各有一個資料列。 如果輸入資料列中的展開 by 陣列是 null 或空白，則會有一個輸出資料列的展開值為 null。

### <a name="unroll-root"></a>展開根目錄

依預設，「簡維」轉換會將陣列展開至其所在階層的最上層。 您可以選擇性地選取陣列作為展開根。 展開根目錄必須是複雜物件的陣列，也就是或包含展開 by 陣列。 如果選取了展開根目錄，則輸出資料將會在展開根目錄中的每個專案都包含至少一個資料列。 如果輸入資料列沒有展開根目錄中的任何專案，則會從輸出資料中卸載它。 選擇展開根目錄時，一律會輸出小於或等於預設行為的資料列數目。

### <a name="flatten-mapping"></a>壓平合併對應

類似于「選取」轉換，從傳入的欄位和反正規化的陣列選擇新結構的投射。 如果已對應反正規化的陣列，則輸出資料行的資料類型會與陣列相同。 如果展開 by 陣列是包含 subarrays 的複雜物件陣列，則對應該 subarry 的專案將會輸出陣列。

請參閱 [檢查] 索引標籤和資料預覽，以驗證您的對應輸出。

## <a name="examples"></a>範例

如需簡維轉換的下列範例，請參閱下列 JSON 物件

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

### <a name="no-unroll-root-with-string-array"></a>沒有具有字串陣列的展開根目錄

| 展開者 | 展開根目錄 | Projection |
| --------- | ----------- | ---------- |
| 貨物。客戶 | 無 | NAME <br> customer = 貨物。客戶 |

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

### <a name="no-unroll-root-with-complex-array"></a>沒有具有複雜陣列的展開根目錄

| 展開者 | 展開根目錄 | Projection |
| --------- | ----------- | ---------- |
| 貨品。 orderItems | 無 | NAME <br> 訂單 Id = 貨物。訂單 <br> [orderItems] < 貨品 = [貨品] <br> itemQty = 貨品. orderItems. itemQty <br> 位置 = 位置 |

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

### <a name="same-root-as-unroll-array"></a>與展開陣列相同的根

| 展開者 | 展開根目錄 | Projection |
| --------- | ----------- | ---------- |
| 貨物。訂單 | 貨物。訂單 | NAME <br> 貨品。 orderItems。 <br> 貨物。客戶 <br> location |

#### <a name="output"></a>輸出

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>具有複雜陣列的展開根目錄

| 展開者 | 展開根目錄 | Projection |
| --------- | ----------- | ---------- |
| 貨品。 orderItem | 貨物。訂單 |NAME <br> 訂單 Id = 貨物。訂單 <br> [orderItems] < 貨品 = [貨品] <br> itemQty = 貨品. orderItems. itemQty <br> 位置 = 位置 |

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

* 使用 [pivot 轉換](data-flow-pivot.md) 將資料列樞紐分析表資料行。
* 使用 [Unpivot 轉換](data-flow-unpivot.md) 將資料行樞紐分析表至資料列。
