---
title: 瞭解 Azure 應用程式組態索引鍵/值存放區
description: 瞭解 Azure 應用程式組態中的索引鍵/值存放區，其會將設定資料儲存為索引鍵/值。 索引鍵/值是應用程式設定的標記法。
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: b1998532c3d9e4272d91280d57d9ea2f6e7a262c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88586387"
---
# <a name="keys-and-values"></a>索引鍵和值

Azure 應用程式組態會將設定資料儲存為索引鍵/值。 索引鍵/值是開發人員所使用之應用程式設定的簡單且有彈性的標記法。

## <a name="keys"></a>索引鍵

索引鍵會作為索引鍵/值的識別碼，可用來儲存和取出對應的值。 常見的方法是使用字元分隔符號 (例如 `/` 或 `:`)，來將索引鍵組織到階層命名空間。 使用最適合您應用程式的慣例。 應用程式設定會將索引鍵視為一個整體。 其不會剖析索引鍵，以找出索引鍵名稱的結構，或對索引鍵強制執行任何規則。

以下是以元件服務為基礎之階層結構化的索引鍵名稱範例：

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

在應用程式架構內使用設定資料可能會針對索引鍵/值指定特定的命名配置。 例如，JAVA 的春季 Cloud framework 會定義 `Environment` 將設定提供給春季應用程式的資源。  這些是由包含 *應用程式名稱* 和 *設定檔*的變數所參數化。 Spring Cloud 相關設定資料的索引鍵通常會以這兩個元素開頭，並以分隔符號隔開。

儲存在應用程式設定中的索引鍵是 Unicode 字串 (需區分大小寫)。 在應用程式組態存放區中，索引鍵 app1** 和 App1** 是不同的。 當您在應用程式內使用組態設定時，務必記得這一點，因為部分架構會以不區分大小寫的方式處理設定索引鍵。 我們不建議使用 case 來區分金鑰。

除了之外，您還可以在索引鍵名稱中使用任何 unicode 字元 `%` 。 索引鍵名稱不可以是 `.` 或 `..` 。 在索引鍵/值上有 10 KB 的合併大小限制。 此限制包括索引鍵中的所有字元、索引鍵的值及所有相關聯的選擇性屬性。 在此限制中，您可以有許多索引鍵階層層級。

### <a name="design-key-namespaces"></a>設計索引鍵命名空間

命名設定資料所使用的索引鍵一般有兩種方法：平面式或階層式。 從應用程式使用方式的角度來看，這些方法會很類似，但階層式命名可提供許多優點：

* 易於閱讀。 階層式索引鍵名稱中的分隔符號作為句子中的空格。 還會在文字之間提供自然的中斷點。
* 易於管理。 索引鍵名稱的階層表示設定資料的邏輯群組。
* 易於使用。 您可以更輕鬆地撰寫查詢，以透過模式來比對階層式結構中的索引鍵，並只擷取一部分的設定資料。 此外，許多較新的程式設計架構都有階層式設定資料的原生支援，可讓您的應用程式使用特定設定集。

您可以使用許多方式來將應用程式設定中的索引鍵組織成階層。 這類索引鍵可作為 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)。 每個階層式索引鍵都是「路徑」** 資源，由一個或多個以分隔符號聯結的元件組成。 請根據應用程式、程式設計語言或架構需求，來選擇要當作分隔符號使用的字元。 針對應用程式設定中的不同索引鍵，使用多個分隔符號。

### <a name="label-keys"></a>標籤索引鍵

應用程式設定中的索引鍵/值可選擇性地使用標籤屬性。 標籤會用來區分具有相同索引鍵的索引鍵值。 具有標籤 A** 和 B** 的索引鍵 app1**，會在應用程式組態存放區中形成兩個個別的索引鍵。 依預設，索引鍵/值沒有標籤。 若要明確地參考沒有標籤的索引鍵/值，請使用 `\0` 編碼為) 的 (URL `%00` 。

標籤可提供便利的方式來建立索引鍵變體。 標籤的常見用法是為相同索引鍵指定多個環境：

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>版本索引鍵-值

使用標籤作為建立多個索引鍵/值版本的方式。 例如，您可以在標籤內輸入應用程式版本號碼或 Git 認可識別碼，以識別與特定軟體組建相關聯的索引鍵/值。

> [!NOTE]
> 如果您要尋找變更版本，應用程式設定會保留在過去特定一段時間內發生的索引鍵/值變更。 如需詳細資訊，請參閱 [時間點快照](./concept-point-time-snapshot.md) 集。

### <a name="query-key-values"></a>查詢索引鍵/值

透過索引鍵和標籤 (可以是 `\0`)，即可識別每個唯一的索引鍵/值。 您可以藉由指定模式來查詢應用程式設定存放區中的索引鍵/值。 應用程式設定存放區會傳回符合模式的所有索引鍵/值，包括其對應的值和屬性。 在對應用程式設定發出的 REST API 呼叫中，請使用下列索引鍵模式：

| Key | 描述 |
|---|---|
| 省略 `key` 或使用 `key=*` | 符合所有索引鍵 |
| `key=abc` | 完全符合索引鍵名稱 **abc** |
| `key=abc*` | 符合以 **abc** 開頭的索引鍵名稱 |
| `key=abc,xyz` | 符合索引鍵名稱 **abc** 或 **xyz**。 限制為五個 Csv |

您也可以包含下列標籤模式：

| 標籤 | 描述 |
|---|---|
| 省略 `label` 或使用 `label=*` | 符合任何標籤，包括 `\0` 在內 |
| `label=%00` | 符合 `\0` 標籤 |
| `label=1.0.0` | 完全符合標籤 **1.0.0** |
| `label=1.0.*` | 符合以 **1.0.** 開頭的標籤 |
| `label=%00,1.0.0` | 符合標籤 `\0` 或 **1.0.0** (限制為五個 CSV) |

> [!NOTE]
> `*`、 `,` 和 `\` 是查詢中的保留字元。 如果您的索引鍵名稱或標籤中使用了保留字元，您必須在查詢中使用來將它換用 `\{Reserved Character}` 。

## <a name="values"></a>值

指派給索引鍵的值也是 Unicode 字串。 您可以使用所有 Unicode 字元來作為值。

### <a name="use-content-type"></a>使用 Content-type
應用程式設定中的每個索引鍵/值都有 content-type 屬性。 您可以選擇性地使用此屬性，將數值型別的相關資訊儲存在索引鍵/值中，以協助應用程式正確地進行處理。 您可以使用任何格式的內容類型。 應用程式設定使用 [媒體類型]( https://www.iana.org/assignments/media-types/media-types.xhtml) (也稱為 MIME 類型) 適用于內建資料類型，例如功能旗標、Key Vault 參考和 JSON 索引鍵/值。

## <a name="next-steps"></a>後續步驟

* [時間點快照集](./concept-point-time-snapshot.md)
* [功能管理](./concept-feature-management.md)
* [事件處理](./concept-app-configuration-event.md)
