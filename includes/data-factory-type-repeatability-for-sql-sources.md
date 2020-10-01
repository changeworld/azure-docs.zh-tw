---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 9447cec55c53861ca57d5416a91ffefd35fdd20b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571860"
---
## <a name="repeatability-during-copy"></a>在複製期間的重複性
從其他資料存放區複製資料到 Azure SQL/SQL Server 時，需要記住重複性以避免非預期的結果。 

將資料複製到 Azure SQL/SQL Server Database 時，複製活動預設會將資料集附加至 sink 資料表的尾端。 例如，從包含兩筆記錄的 CSV (逗點分隔值) 檔案來源複製資料到 Azure SQL/SQL Server 資料庫時，資料表看起來像這樣：

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

假設您在來源檔案中找到錯誤，並將來源檔案中 Down Tube 的數量 (Quantity) 從 2 更新為 4。 如果您重新執行該期間的資料配量，會發現有兩筆新記錄附加到 Azure SQL/SQL Server 資料庫。 以下假設資料表中的資料行都沒有主索引鍵條件約束。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

若要避免這個問題，您必須利用下述 2 個機制之一來指定 UPSERT 語意。

> [!NOTE]
> 可以根據指定的重試原則在 Azure Data Factory 中自動重新執行配量。
> 
> 

### <a name="mechanism-1"></a>機制 1
您可以利用 **sqlWriterCleanupScript** 屬性在執行配量時先進行清理動作。 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

在複製指定的配量時會先執行清除指令碼，這會刪除 SQL 資料表中對應至該配量的資料。 活動接著會將資料插入 SQL 資料表。 

如果在此時重新執行配量，則會發現數量已如您所需更新了。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

假設原始 csv 中的 Flat Washer 記錄被移除。 則重新執行配量會產生以下結果： 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
不需要再做什麼別的動作。 複製活動執行清除指令碼來刪除該配量的對應資料。 然後它會從 csv (只包含 1 筆記錄) 讀取輸入，並將其插入資料表。 

### <a name="mechanism-2"></a>機制 2
> [!IMPORTANT]
> 目前 Azure Synapse Analytics 不支援 sliceIdentifierColumnName。 time. 

達成重複性的另一個機制是在目標資料表中使用專用資料行 (**sliceIdentifierColumnName**)。 Azure Data Factory 會使用這個資料行以確保來源和目的地保持同步。 當目的地 SQL 資料表結構描述可彈性變更或定義，就可以使用這種方法。 

基於重複性的目的，Azure Data Factory 會使用此資料行，且在程序中 Azure Data Factory 將不會對資料表進行任何結構描述變更。 如何使用這個方法：

1. 在目的地 SQL 資料表中定義二進位 (32) 類型的資料行。 此資料行不應該有任何條件約束。 此範例中我們將這個資料行命名為 'ColumnForADFuseOnly'。
2. 在複製活動中使用它，如下所示：
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory 會根據此資料行的需求填入資料，以確保來源和目的地保持同步。 使用者不應在此內容之外使用此資料行的值。 

和機制 1 類似，複製活動會自動先從目的地 SQL 資料表中清除指定配量的資料，然後正常執行複製活動將資料從來源插入至該配量的目的地。 

