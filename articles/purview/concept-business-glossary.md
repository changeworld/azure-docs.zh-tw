---
title: '瞭解 Azure 範疇中的商務詞彙功能 (預覽版) '
description: 本文說明 Azure 範疇中的商務詞彙。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552280"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>瞭解 Azure 範疇中的商務詞彙功能

本文概要說明 Azure 範疇中的商務詞彙功能。 

## <a name="business-glossary"></a>商務詞彙

詞彙供應商務使用者的詞彙。  它是由彼此相關的商務詞彙所組成，可讓他們進行分類，以便在不同的內容中瞭解。 這些詞彙接著可以對應到資料庫、資料表、資料行等資產。這有助於抽象化與資料存放庫相關聯的技術術語，並可讓商務使用者探索及使用更熟悉的詞彙中的資料。


商務詞彙是詞彙的集合。 每個詞彙都代表組織中的物件，而且很可能會有多個詞彙代表相同的物件。 客戶也可以稱為用戶端、買方或買方。 這兩個詞彙彼此之間有關聯性。 這些詞彙之間的關聯性可以是下列其中一項：

- 同義字-具有相同定義的不同詞彙
- 相關-具有類似定義的不同名稱

相同的詞彙也可以代表多個商務物件。 請務必妥善定義每個詞彙，並清楚瞭解組織內的每個詞彙。

## <a name="custom-attributes"></a>自訂屬性

Azure 範疇支援任何商務詞彙條款的八個現成屬性：
- Name
- 定義
- 資料負責人
- 資料專家
- 縮略字
- 同義字
- 相關詞彙
- 資源

這些屬性無法編輯或刪除。 不過，這些屬性並不足以完全定義組織中的詞彙。 為了解決這個問題，範疇提供了一個功能，您可以在其中定義詞彙的自訂屬性。

## <a name="term-templates"></a>詞彙範本

詞彙範本可讓詞彙自訂屬性在目錄中以邏輯方式分組在一起。 這項功能可讓您在範本中將所有相關的自訂屬性群組在一起，然後在建立詞彙期間套用範本。 例如，所有與財務相關的自訂屬性（例如成本中心、利潤中心、會計程式碼）都可以分組在詞彙範本財務範本中，而財務範本可用來建立財務詞彙條款。

所有標準屬性都會分組為系統預設範本。 您所建立的任何字詞範本都將包含這些屬性，以及建立為範本建立過程中所建立的任何其他自訂屬性。

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>詞彙 vs 分類與敏感度標籤

雖然詞彙、分類和標籤是資料資產的附注，但每一個都在目錄的內容中各有不同的意義。 

### <a name="glossary"></a>詞彙

如上所述，商務詞彙詞彙會定義組織的商務詞彙，並協助您在公司中的不同部門之間銜接橋樑。

### <a name="classifications"></a>分類

分類是可指派給實體的附注。 分類的彈性可讓您在多個案例中使用，例如：

- 瞭解資料資產中儲存的資料本質
- 定義存取控制原則

範疇目前有超過100的系統分類器，而且您可以在目錄中定義您自己的分類器。 在掃描過程中，我們會自動偵測這些分類，並將其套用至資料資產和架構。 不過，您可以在任何時間點覆寫它們。 人工覆寫絕對不會被自動掃描取代。

### <a name="sensitivity-labels"></a>敏感度標籤

敏感度標籤是一種批註類型，可讓您分類和保護組織的資料，而不會阻礙生產力和共同作業。 敏感度標籤可用來識別組織資料內的分類類型類別，並將您想要套用至每個類別的原則分組。 範疇會使用與 Microsoft 365 相同的機密資訊類型，可讓您在整個內容和資料資產之間延展現有的安全性原則和保護。 您可以在範疇中的 Microsoft Office 產品和資料資產之間共用相同的標籤。

## <a name="next-steps"></a>後續步驟

- [管理字詞範本](how-to-manage-term-templates.md)
- [流覽 Azure 範疇中的資料目錄](how-to-browse-catalog.md)
