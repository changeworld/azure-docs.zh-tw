---
title: 對應資料流程中的 Alter row 轉換
description: 如何使用對應資料流程中的 alter row 轉換來更新資料庫目標
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/20/2020
ms.openlocfilehash: 6b353967c9b9c7517f1a42581717c6394c0e6374
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729130"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>對應資料流程中的 Alter row 轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用 Alter Row 轉換來設定資料列的 insert、delete、update 和 upsert 原則。 您可以新增一到多個條件做為運算式。 這些條件應依照優先順序來指定，因為每個資料列會以對應至第一個相符運算式的原則來標示。 每一個條件都會導致插入、更新、刪除或 upserted 資料列（或資料列）。 Alter Row 可以針對您的資料庫產生 DDL & DML 動作。

![改變數據列設定](media/data-flow/alter-row1.png "改變數據列設定")

Alter Row 轉換只會在資料流程中的資料庫或 CosmosDB 接收上運作。 您指派給資料列的動作（insert、update、delete、upsert）不會在 debug 會話期間發生。 在管線中執行「執行資料流程」活動，以制定資料庫資料表的 alter row 原則。

## <a name="specify-a-default-row-policy"></a>指定預設資料列原則

建立 Alter Row 轉換，並指定條件為的`true()`資料列原則。 不符合任何先前定義之運算式的每個資料列都會標示為指定的資料列原則。 根據預設，每個不符合任何條件運算式的資料列都會標示`Insert`為。

![改變數據列原則](media/data-flow/alter-row4.png "改變數據列原則")

> [!NOTE]
> 若要使用一個原則標記所有資料列，您可以建立該原則的條件，並將條件`true()`指定為。

## <a name="view-policies-in-data-preview"></a>在資料預覽中查看原則

在 [資料預覽] 窗格中，使用 [檢查[模式](concepts-data-flow-debug-mode.md)] 來查看 alter row 原則的結果。 Alter row 轉換的資料預覽不會針對您的目標產生 DDL 或 DML 動作。

![改變數據列原則](media/data-flow/alter-row3.png "改變數據列原則")

每個改變數據列原則都是以表示插入、更新、upsert 或刪除動作是否會發生的圖示來表示。 頂端標頭會顯示預覽中每個原則所影響的資料列數目。

## <a name="allow-alter-row-policies-in-sink"></a>允許變更接收中的資料列原則

若要讓 alter row 原則能夠正常執行，資料流程必須寫入資料庫或 Cosmos 接收。 在接收的 [**設定**] 索引標籤中，啟用該接收允許的 alter row 原則。

![改變數據列接收](media/data-flow/alter-row2.png "改變數據列接收")

預設行為是只允許插入。 若要允許更新、更新插入或刪除，請核取對應于該條件之接收器中的方塊。 如果已啟用更新、更新插入或、刪除，您必須指定要比對之接收中的索引鍵資料行。

> [!NOTE]
> 如果您的插入、更新或更新插入修改了接收中目標資料表的架構，資料流程將會失敗。 若要修改資料庫中的目標架構，請選擇 [**重新建立資料表**] 做為資料表動作。 這會卸載並重新建立您的資料表，並使用新的架構定義。

在目標資料庫中，接收轉換需要單一索引鍵或一系列金鑰來進行唯一的資料列識別。 若是 SQL 接收器，請在 [接收設定] 索引標籤中設定索引鍵。若為 CosmosDB，請在設定中設定分割區索引鍵，並在您的接收對應中設定 CosmosDB 系統欄位 "id"。 針對 CosmosDB，必須包含 system 資料行 "id" 來進行更新、更新插入和刪除。

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

下列範例是名為`CleanData`的 alter row 轉換，它會接受傳入`SpecifyUpsertConditions`資料流程，並建立三個 alter row 條件。 在先前的轉換中，會計算`alterRowCondition`名為的資料行，以決定是否要在資料庫中插入、更新或刪除資料列。 如果資料行的值具有符合 alter row 規則的字串值，則會將該原則指派給該原則。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![Alter row 範例](media/data-flow/alter-row4.png "Alter row 範例")

此轉換的資料流程腳本位於下列程式碼片段中：

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>後續步驟

在 Alter Row 轉換之後，您可能會想要將[資料接收到目的地資料存放區](data-flow-sink.md)。
