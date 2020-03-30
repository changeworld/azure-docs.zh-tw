---
title: 瞭解 Azure 應用配置鍵值存儲
description: 瞭解配置資料如何在 Azure 應用配置中存儲。
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523599"
---
# <a name="keys-and-values"></a>索引鍵和值

Azure 應用程式設定會將設定資料儲存為索引鍵/值組。 鍵值對是開發人員使用的應用程式設定的簡單和靈活的表示形式。

## <a name="keys"></a>索引鍵

鍵用作鍵值對的識別碼，用於存儲和檢索相應的值。 常見的方法是使用字元分隔符號 (例如 `/` 或 `:`)，來將索引鍵組織到階層命名空間。 使用最適合您的應用程式的約定。 應用程式設定會將索引鍵視為一個整體。 其不會剖析索引鍵，以找出索引鍵名稱的結構，或對索引鍵強制執行任何規則。

下面是結構化到層次結構中的兩個鍵名稱示例：

* 以元件服務為基礎

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* 以部署區域為基礎

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

在應用程式框架中使用配置資料可能規定鍵值的特定命名方案。 例如，JAVA 的 Spring Cloud 框架`Environment`定義為 Spring 應用程式提供設置的資源。  這些變數由包含*應用程式名稱*和*設定檔*的變數參數化。 Spring Cloud 相關設定資料的索引鍵通常會以這兩個元素開頭，並以分隔符號隔開。

儲存在應用程式設定中的索引鍵是 Unicode 字串 (需區分大小寫)。 在應用程式組態存放區中，索引鍵 app1** 和 App1** 是不同的。 當您在應用程式內使用組態設定時，務必記得這一點，因為部分架構會以不區分大小寫的方式處理設定索引鍵。 我們不建議使用 case 來區分鍵。

您可以在鍵名中使用除`*`和`,`之外的任何單碼字元。 `\`  如果需要包含這些保留字元之一，請使用`\{Reserved Character}`轉義它。 

鍵值對的組合大小限制為 10 KB。 此限制包括索引鍵中的所有字元、索引鍵的值及所有相關聯的選擇性屬性。 在此限制中，您可以有許多索引鍵階層層級。

### <a name="design-key-namespaces"></a>設計索引鍵命名空間

命名設定資料所使用的索引鍵一般有兩種方法：平面式或階層式。 從應用程式使用方式的角度來看，這些方法會很類似，但階層式命名可提供許多優點：

* 易於閱讀。 分層鍵名稱中的分隔符號充當句子中的空格。 還會在文字之間提供自然的中斷點。
* 易於管理。 索引鍵名稱的階層表示設定資料的邏輯群組。
* 易於使用。 您可以更輕鬆地撰寫查詢，以透過模式來比對階層式結構中的索引鍵，並只擷取一部分的設定資料。 此外，許多較新的程式設計架構都有階層式設定資料的原生支援，可讓您的應用程式使用特定設定集。

您可以使用許多方式來將應用程式設定中的索引鍵組織成階層。 這類索引鍵可作為 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)。 每個階層式索引鍵都是「路徑」** 資源，由一個或多個以分隔符號聯結的元件組成。 請根據應用程式、程式設計語言或架構需求，來選擇要當作分隔符號使用的字元。 針對應用程式設定中的不同索引鍵，使用多個分隔符號。

### <a name="label-keys"></a>標籤索引鍵

應用程式設定中的索引鍵值可選擇性地使用標籤屬性。 標籤會用來區分具有相同索引鍵的索引鍵值。 具有標籤 A** 和 B** 的索引鍵 app1**，會在應用程式組態存放區中形成兩個個別的索引鍵。 預設情況下，鍵值沒有標籤。 要顯式引用沒有標籤的鍵值，請使用`\0`（URL 編碼為`%00`）。

標籤可提供便利的方式來建立索引鍵變體。 標籤的常見用法是為相同索引鍵指定多個環境：

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>版本索引鍵值

應用配置不會自動對鍵值進行版本。 請使用標籤作為建立多個索引鍵值版本的方式。 例如，您可以在標籤內輸入應用程式版本號碼或 Git 認可識別碼，以識別與特定軟體組建相關聯的索引鍵值。

您可以在標籤中使用任何 Unicode 字元，但 `*`、`,` 及 `\` 除外。 這些都是保留字元。 若要包含保留字元，您必須使用 `\{Reserved Character}` 來將其逸出。

### <a name="query-key-values"></a>查詢索引鍵值

透過索引鍵和標籤 (可以是 `null`)，即可識別每個唯一的索引鍵值。 您可以藉由指定模式來查詢應用程式組態存放區的索引鍵值。 應用程式組態存放區會傳回符合模式的索引鍵值，以及其對應的值和屬性。 在對應用程式設定發出的 REST API 呼叫中，請使用下列索引鍵模式：

| Key | |
|---|---|
| 省略 `key` 或使用 `key=*` | 符合所有索引鍵 |
| `key=abc` | 完全符合索引鍵名稱 **abc** |
| `key=abc*` | 符合以 **abc** 開頭的索引鍵名稱 |
| `key=abc,xyz` | 匹配鍵名稱**abc**或**xyz**。 限於五個 CSV |

您也可以包含下列標籤模式：

| 標籤 | |
|---|---|
| 省略 `label` 或使用 `label=*` | 符合任何標籤，包括 `null` 在內 |
| `label=%00` | 符合 `null` 標籤 |
| `label=1.0.0` | 完全符合標籤 **1.0.0** |
| `label=1.0.*` | 符合以 **1.0.** 開頭的標籤 |
| `label=%00,1.0.0` | 符合標籤 `null` 或 **1.0.0** (限制為五個 CSV) |

## <a name="values"></a>值

指派給索引鍵的值也是 Unicode 字串。 您可以使用所有 Unicode 字元來作為值。 每個值都可以與選擇性的使用者定義內容類型建立關聯。 使用此屬性可以存儲有關輔助應用程式正確處理該值的資訊。

存儲在應用配置存儲中的配置資料在靜態和傳輸過程中進行加密。 金鑰未在靜態時加密。 應用程式設定並不是 Azure Key Vault 的替代方案。 請勿在其中儲存應用程式祕密。

## <a name="next-steps"></a>後續步驟

* [時間點快照集](./concept-point-time-snapshot.md)  
* [功能管理](./concept-feature-management.md)  
