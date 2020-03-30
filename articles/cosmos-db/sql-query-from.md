---
title: Azure 宇宙 DB 中的 FROM 子句
description: 瞭解 SQL 語法，以及 Azure Cosmos DB 的 FROM 子句示例。 本文還演示了用於範圍結果的示例，並使用 FROM 子句獲取子項。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587680"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的 FROM 子句

FROM （`FROM <from_specification>`） 子句是可選的，除非在查詢中稍後篩選或投影源。 像這樣的查詢，`SELECT * FROM Families`如枚舉整個`Families`容器。 還可以對容器使用特殊識別碼 ROOT，而不是使用容器名稱。

FROM 子句強制每個查詢執行以下規則：

* 您可以為容器設定別名，例如 `SELECT f.id FROM Families AS f` 或只是 `SELECT f.id FROM Families f`。 下面是`f``Families`的別名。 AS 是[別名](sql-query-aliasing.md)識別碼的可選關鍵字。  

* 一旦別名化，無法綁定原始源名稱。 例如，`SELECT Families.id FROM Families f`在語法上無效，因為識別碼`Families`已別名，無法再解析。  

* 所有引用的屬性都必須完全限定，以避免在未嚴格遵循架構的情況下進行任何不明確的綁定。 例如，`SELECT id FROM Families f`在語法上無效，因為屬性`id`不受約束。

## <a name="syntax"></a>語法
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>引數
  
- `<from_source>`  
  
  指定包含或不包含別名的資料來源。 若未指定別名，則會使用下列規則從 `<container_expression>` 加以推斷：  
  
  -  如果運算式為 container_name，則會使用 container_name 作為別名。  
  
  -  如果運算式為 `<container_expression>`，則會使用 property_name、 then property_name 作為別名。 如果運算式為 container_name，則會使用 container_name 作為別名。  
  
- AS `input_alias`  
  
  指定 `input_alias` 為一組由基礎容器運算式傳回的值。  
 
- `input_alias` IN  
  
  指定 `input_alias` 應該代表一組透過反覆計算所有陣列元素所取得的值，其中會依基礎容器運算式傳回每個陣列。 會忽略任何由基礎容器運算式傳回的任何非陣列的值。  
  
- `<container_expression>`  
  
  指定要用來擷取文件的容器運算式。  
  
- `ROOT`  
  
  指定應該從預設、目前的連線容器中擷取文件。  
  
- `container_name`  
  
  指定應該從提供的容器中擷取文件。 容器名稱必須與目前所連線容器的名稱相符。  
  
- `input_alias`  
  
  指定應該從由提供的別名定義的其他來源擷取文件。  
  
- `<container_expression> '.' property_name`  
  
  指定應通過訪問`property_name`該屬性檢索文檔。  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  指定文件應該透過存取 `property_name` 屬性或 array_index 陣列元素加以擷取，其中所文件均依指定的容器運算式擷取。  
  
## <a name="remarks"></a>備註
  
`<from_source>(`中提供的所有別名或推斷) 必須是唯一的。 語法 `<container_expression>.`property_name 與 `<container_expression>' ['"property_name"']'` 相同。 不過，若屬性名稱包含非識別碼字元，則可以使用後者的語法。  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>處理遺漏的屬性、遺漏的陣列元素及未定義的值
  
若容器運算式存取屬性或陣列元素，且該值不存在，則會忽略該值且不會進一步處理。  
  
### <a name="container-expression-context-scoping"></a>容器運算式內容範圍  
  
容器運算式可以是容器範圍或文件範圍的運算式：  
  
-   若容器運算式的基礎來源為 ROOT 或 `container_name`，則運算式為容器範圍的運算式。 這類運算式代表一組直接從容器中擷取的文件，並不會相依於其他容器運算式的處理。  
  
-   若容器運算式的基礎來源為之前在查詢中導入的 `input_alias`，則運算式為文件範圍的運算式。 這類運算式代表一組文件，這組文件是透過評估屬於與別名容器相關聯之集的每個文件範圍的容器運算式而取得。  結果集會是透過評估基礎集中每個文件的容器運算式所獲得的聯集。 

## <a name="examples"></a>範例

### <a name="get-subitems-by-using-the-from-clause"></a>使用 FROM 子句獲取子項

FROM 子句可以將源減少到較小的子集。 要枚舉每個專案中的子樹，子根可以成為源，如以下示例所示：

```sql
    SELECT *
    FROM Families.children
```

結果如下：

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

前面的查詢使用陣列作為源，但也可以使用物件作為源。 查詢考慮源中的任何有效定義的 JSON 值，以便包含在結果中。 下面的示例將排除`Families`沒有`address.state`值的值。

```sql
    SELECT *
    FROM Families.address.state
```

結果如下：

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [選擇子句](sql-query-select.md)
- [WHERE 條款](sql-query-where.md)
