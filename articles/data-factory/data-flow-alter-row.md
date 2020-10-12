---
title: 改變對應資料流程中的資料列轉換
description: 如何使用對應資料流程中的 alter row 轉換來更新資料庫目標
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82982644"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>改變對應資料流程中的資料列轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用 Alter Row 轉換來設定資料列的 insert、delete、update 和 upsert 原則。 您可以新增一對多條件做為運算式。 這些條件應該依照優先順序來指定，因為每個資料列都會以對應至第一個相符運算式的原則來標記。 上述每一種情況都會導致插入、更新、刪除或 upserted) 資料列 (或資料列。 Alter Row 可針對您的資料庫產生 DDL & DML 動作。

![改變數據列設定](media/data-flow/alter-row1.png "改變數據列設定")

Alter Row 轉換只會在資料流程中的資料庫或 CosmosDB 接收上運作。 您指派給資料列 (insert、update、delete、upsert) 的動作不會在 debug 會話期間發生。 在管線中執行「執行資料流程」活動，以在資料庫資料表上制定 alter row 原則。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>指定預設的資料列原則

建立 Alter Row 轉換，並指定條件為的資料列原則 `true()` 。 不符合任何先前定義之運算式的每個資料列，都會標示為指定的資料列原則。 依預設，不符合任何條件運算式的每個資料列都會標示為 `Insert` 。

![Alter row 原則](media/data-flow/alter-row4.png "Alter row 原則")

> [!NOTE]
> 若要將所有資料列標示為一個原則，您可以建立該原則的條件，並將條件指定為 `true()` 。

## <a name="view-policies-in-data-preview"></a>查看資料預覽中的原則

在 [資料預覽] 窗格中，使用 [ [debug] 模式](concepts-data-flow-debug-mode.md) 來查看 alter row 原則的結果。 Alter row 轉換的資料預覽不會針對您的目標產生 DDL 或 DML 動作。

![改變數據列原則](media/data-flow/alter-row3.png "改變數據列原則")

每個 alter row 原則都會以圖示表示，指出是否會發生插入、更新、upsert 或刪除的動作。 頂端標頭會顯示預覽中每個原則所影響的資料列數目。

## <a name="allow-alter-row-policies-in-sink"></a>允許接收器中的 alter row 原則

若要讓 alter row 原則運作，資料流程必須寫入至資料庫或 Cosmos 接收。 在您接收器的 [ **設定** ] 索引標籤中，啟用該接收所允許的 alter row 原則。

![改變數據列接收](media/data-flow/alter-row2.png "改變數據列接收")

預設行為是只允許插入。 若要允許更新、upsert 或刪除，請核取 [接收器] 中對應至該條件的方塊。 如果已啟用更新、upsert 或刪除，則您必須指定要比對之接收中的索引鍵資料行。

> [!NOTE]
> 如果您的插入、更新或 upsert 修改接收中目標資料表的架構，資料流程將會失敗。 若要修改資料庫中的目標架構，請選擇 [ **重新建立資料表** ] 作為 [資料表] 動作。 這會捨棄並重新建立具有新架構定義的資料表。

接收轉換需要單一索引鍵或一系列索引鍵，才能在目標資料庫中使用唯一的資料列識別。 若為 SQL 接收器，請在 [接收設定] 索引標籤中設定索引鍵。若為 CosmosDB，請在設定中設定分割區索引鍵，同時在接收對應中設定 CosmosDB 系統欄位 "id"。 若為 CosmosDB，則必須針對更新、upsert 和刪除包含系統資料行 "id"。

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>Azure SQL Database 和 Synapse 的合併和 upsert

ADF 資料流程支援使用 upsert 選項來合併 Azure SQL Database 和 Synapse 資料庫集區 (資料倉儲) 。

不過，您可能會遇到目標資料庫架構使用索引鍵資料行的 identity 屬性的情況。 ADF 會要求您找出要用來比對更新和 upsert 之資料列值的金鑰。 但是，如果目標資料行已設定 identity 屬性，而且您使用 upsert 原則，目標資料庫將不允許您寫入資料行。 當您嘗試針對分散式資料表的散發資料行 upsert 時，可能也會遇到錯誤。

以下是修正此問題的方法：

1. 移至 [接收] 轉換設定，並設定 [略過寫入索引鍵資料行]。 這會告知 ADF 不要寫入您所選取的資料行做為對應的索引鍵值。

2. 如果該索引鍵資料行不是造成識別欄位問題的資料行，則您可以使用接收轉換前置處理 SQL 選項： ```SET IDENTITY_INSERT tbl_content ON``` 。 然後，使用後置處理 SQL 屬性將其關閉： ```SET IDENTITY_INSERT tbl_content OFF``` 。

3. 針對身分識別案例和散發資料行大小寫，您可以使用條件式分割轉換，將邏輯從 Upsert 切換為使用個別的更新條件和個別的插入條件。 如此一來，您就可以在更新路徑上設定對應，以忽略索引鍵資料行對應。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>範例

下列範例是名為的 alter row 轉換 `CleanData` ，它會採用傳入的資料流程 `SpecifyUpsertConditions` 並建立三個改變數據列的條件。 在先前的轉換中， `alterRowCondition` 會計算名為的資料行，以決定是否要在資料庫中插入、更新或刪除資料列。 如果資料行的值具有符合 alter row 規則的字串值，就會將該原則指派給該原則。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![Alter row 範例](media/data-flow/alter-row4.png "Alter row 範例")

此轉換的資料流指令碼位於下列程式碼片段中：

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>後續步驟

在 Alter Row 轉換之後，您可能會想要將 [資料接收到目的地資料存放區](data-flow-sink.md)。
