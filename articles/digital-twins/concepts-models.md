---
title: 自訂模型
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 如何使用使用者定義的模型來描述您環境中的實體。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 56ebb32e2d1c2a9bab9592da63e1ada7130bb7ff
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131628"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>瞭解 Azure 數位 Twins 中的對應項模型

Azure 數位 Twins 的主要特性是能夠定義您自己的詞彙，並以您商務的自我定義詞彙來建立對應項圖表。 這項功能是透過使用者定義的**模型**來提供。 您可以將模型視為您世界的描述中的名詞。 

模型類似于物件導向程式設計語言中的**類別**，會在您的實際工作環境中定義一個特定概念的資料圖形。 模型具有名稱（例如*房間*或*溫度感應器*），而且包含屬性、遙測/事件和命令等專案，這些專案會描述您的環境中此類型實體的功能。 稍後，您將使用這些模型來建立[**數位 twins**](concepts-twins-graph.md) ，以代表符合此類型描述的特定實體。

模型是使用以 JSON-LD 為基礎的**數位對應項定義語言（DTDL）** 來撰寫。  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>用於寫入模型的數位對應項定義語言（DTDL）

Azure 數位 Twins 的模型是使用數位 Twins 定義語言（DTDL）來定義。 DTDL 是以 JSON-LD 為基礎，而且與程式設計語言無關。 DTDL 不是 Azure 數位 Twins 專屬的，但也可用來代表其他 IoT 服務中的裝置資料，例如[iot 隨插即用](../iot-pnp/overview-iot-plug-and-play.md)。 

Azure 數位 Twins 使用 DTDL*第2版*。 如需此版本 DTDL 的詳細資訊，請參閱 GitHub 中的規格檔：[*數位 Twins 定義語言（DTDL）-第2版*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。

> [!TIP] 
> 並非所有使用 DTDL 的服務都會執行 DTDL 的完全相同功能。 例如，IoT 隨插即用不會使用適用于圖形的 DTDL 功能，而 Azure 數位 Twins 目前不會執行 DTDL 命令。 如需有關 Azure 數位 Twins 專屬 DTDL 功能的詳細資訊，請參閱本文稍後關於[Azure 數位 TWINS DTDL 的實做細節](#azure-digital-twins-dtdl-implementation-specifics)一節。

## <a name="elements-of-a-model"></a>模型的元素

在模型定義中，最上層程式碼專案是**介面**。 這會封裝整個模型，並在介面中定義模型的其餘部分。 

DTDL 模型介面可以包含零個、一個或多個下欄欄位：
* **屬性**屬性是代表實體狀態的資料欄位（例如許多物件導向程式設計語言中的屬性）。 不同于遙測（也就是時間系結資料事件），屬性具有支援的儲存體，而且可以隨時讀取。
* **遙測**-遙測欄位代表測量或事件，通常用來描述裝置感應器讀數。 遙測不會儲存在數位對應項上;它比較像是準備傳送到某處的資料事件資料流程。 
* **元件**元件可讓您建立模型介面，做為其他介面的元件（如果您想要的話）。 元件的範例是*frontCamera*介面（以及另一個元件介面*backCamera*），可用來定義*手機*的模型。 您必須先定義*frontCamera*的介面，就好像它是它自己的模型一樣，然後在定義*電話*時就可以參考它。

    使用元件來描述屬於解決方案不可或缺的部分，但不需要個別的身分識別，也不需要在對應項圖形中個別建立、刪除或重新排列。 如果您想要讓實體在對應項圖形中具有獨立的 existences，請將它們表示為不同模型的個別數位 twins，並以*關聯*性連接（請參閱下一個專案符號）。
    
    >[!TIP] 
    >元件也可以用於組織，以便將模型介面中的相關屬性集群組在一起。 在此情況下，您可以將每個元件視為命名空間或介面中的「資料夾」。
* **關聯**性-關聯性可讓您表示數位對應項如何與其他數位 twins 相關聯。 關聯性可以代表不同的語義意義，例如*contains* （「樓層包含房間」）、*冷卻*（「hvac 冷卻室」）、 *isBilledTo* （"將壓縮給使用者計費"）等等。關聯性可讓解決方案提供相互關聯實體的圖形。

> [!NOTE]
> DTDL 的規格也會定義**命令**，這是可在數位對應項上執行的方法（例如重設命令，或用來開啟或關閉風扇的命令）。 不過， *Azure 數位 Twins 目前不支援命令。*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure 數位 Twins DTDL 的執行細節

若要讓 DTDL 模型與 Azure 數位 Twins 相容，它必須符合這些需求。

* 模型中的所有最上層 DTDL 元素都必須是*介面*類型。 這是因為 Azure 數位 Twins 模型 Api 可以接收代表介面或介面陣列的 JSON 物件。 因此，最上層不允許其他 DTDL 元素類型。
* 適用于 Azure 數位 Twins 的 DTDL 不得定義任何*命令*。
* Azure 數位 Twins 僅允許單一層級的元件嵌套。 這表示當做元件使用的介面不能有任何元件本身。 
* 介面無法在其他 DTDL 介面內以內嵌方式定義;它們必須定義為個別的最上層實體，並具有自己的識別碼。 然後，當另一個介面想要包含該介面做為元件或透過繼承時，它可以參考其識別碼。

## <a name="example-model-code"></a>範例模型程式碼

可以在任何文字編輯器中寫入對應項類型模型。 DTDL 語言遵循 JSON 語法，因此您應該儲存副檔名為*json*的模型。 使用 JSON 延伸模組，可讓許多程式設計文字編輯器為您的 DTDL 檔提供基本語法檢查和反白顯示。 另外還有[DTDL 擴充](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)功能可用於[Visual Studio Code](https://code.visualstudio.com/)。

此章節包含一般模型的範例，其撰寫為 DTDL 介面。 此模型會描述**行星**，每個都有一個名稱、一個品質和一個溫度。
 
請考慮行星也**可以與衛星本身互動，而且**可能包含**craters**。 在下列範例中，模型會藉 `Planet` 由參考兩個外部模型（和）來表示這些其他實體的連接 `Moon` `Crater` 。 這些模型也會在下列範例程式碼中定義，但會保持非常簡單，以免受到主要範例的降低 `Planet` 。

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

模型的欄位如下：

| 欄位 | 說明 |
| --- | --- |
| `@id` | 模型的識別碼。 的格式必須是 `dtmi:<domain>:<unique model identifier>;<model version number>` 。 |
| `@type` | 識別所描述的資訊類型。 若為介面，類型為*介面*。 |
| `@context` | 設定 JSON 檔的[內容](https://niem.github.io/json/reference/json-ld/context/)。 模型應該使用 `dtmi:dtdl:context;2` 。 |
| `displayName` | 選擇性可讓您視需要為模型提供易記名稱。 |
| `contents` | 所有其餘的介面資料會放在這裡，做為屬性定義的陣列。 每個屬性都必須 `@type` 提供 *（屬性*、*遙測*、*命令*、*關聯*性或*元件*），以識別它所描述的介面資訊的類型，然後是定義實際屬性的一組屬性（例如， `name` 和 `schema` 來定義*屬性*）。 |

> [!NOTE]
> 請注意，元件介面（在此範例中為*火山*）會定義在與使用它（*地球*）之介面相同的陣列中。 元件必須以這種方式在 API 呼叫中定義，才能找到介面。

### <a name="possible-schemas"></a>可能的架構

根據 DTDL，*屬性*和*遙測*屬性的架構可以是標準基本類型（ `integer` 、 `double` 、 `string` 和 `Boolean` ），以及其他類型，例如 `DateTime` 和 `Duration` 。 

除了基本類型，*屬性*和*遙測*欄位可以具有下列複雜類型：
* `Object`
* `Map`
* `Enum`

*遙測*欄位也支援 `Array` 。

### <a name="model-inheritance"></a>模型繼承

有時候，您可能會想要進一步特殊化模型。 例如，具有一般模型*空間*，以及特製化變體*conferenceroom 等等*和*Gym*可能會很有用。 若要表示特製化，DTDL 支援繼承：介面可以繼承自一或多個其他介面。 

下列範例會從先前的 DTDL 範例重新想像*地球*模型，做為較大*CelestialBody*模型的子類型。 「父」模型會先定義，然後「子系」模型會使用欄位在其上建立 `extends` 。

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

在此範例中， *CelestialBody*會將名稱、大量和溫度提供給*地球*。 `extends`區段是介面名稱或介面名稱的陣列（如有需要，可讓擴充介面繼承自多個父模型）。

套用繼承之後，擴充介面會公開整個繼承鏈的所有屬性。

擴充介面無法變更父介面的任何定義;它只能新增到其中。 它也無法重新定義其任何父介面中已定義的功能（即使功能已定義為相同）。 例如，如果父介面定義的屬性為 `double` [*大量*]，則擴充介面不能包含 [*大量*] 的宣告，即使它也是 `double` 。

## <a name="validating-models"></a>驗證模型

> [!TIP]
> 建議您先離線驗證您的模型，再將其上傳至您的 Azure 數位 Twins 實例。

有一種與語言無關的範例可用來驗證模型檔，以確保 DTDL 正確無誤。 其位於這裡： [**DTDL 驗證程式範例**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)。

DTDL 驗證程式範例是以 .NET DTDL 剖析器程式庫為基礎，可在 NuGet 上作為用戶端程式庫：[**選取**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。 您也可以直接使用程式庫來設計自己的驗證解決方案。 使用剖析器程式庫時，請務必使用與 Azure 數位 Twins 正在執行之版本相容的版本。 在預覽期間，這是版本*3.7.0*。

若要深入瞭解剖析器程式庫，包括使用範例，請閱讀[*如何：剖析和驗證模型*](how-to-use-parser.md)。

## <a name="next-steps"></a>後續步驟

瞭解如何使用 DigitalTwinsModels Api 來管理模型：
* [*操作說明：管理自訂模型*](how-to-manage-model.md)

或者，深入瞭解如何根據模型建立數位 twins：
* [*概念：數位 twins 和對應項圖形*](concepts-twins-graph.md)

