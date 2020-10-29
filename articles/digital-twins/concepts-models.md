---
title: 自訂模型
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 如何使用使用者定義的模型來描述您環境中的實體。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: fecadf3cd6fd0d654315038680b9aa3fa2b71782
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913903"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>瞭解 Azure 數位 Twins 中的對應項模型

Azure 數位 Twins 的主要特性是能夠定義您自己的詞彙，並以您企業內建的條款建立對應項圖表。 這項功能是透過使用者定義的 **模型** 來提供。 您可以將模型視為您世界描述中的名詞。 

模型類似于物件導向程式設計語言中的 **類別** ，在您的實際工作環境中定義一個特定概念的資料圖形。 模型的名稱 (例如 *會議室* 或 *溫度感應器* ) ，而且包含屬性、遙測/事件和命令等專案，以描述您的環境中這種類型的實體可以做什麼。 稍後，您將使用這些模型來建立 [**數位 twins**](concepts-twins-graph.md) ，以代表符合此類型描述的特定實體。

Azure 數位 Twins 模型以以 JSON-LD 為基礎的數位對應項 **定義語言表示 (DTDL)** 。  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>適用于模型的數位對應項定義語言 (DTDL) 

您可以使用數位 Twins 定義語言 (DTDL) 來定義 Azure 數位 Twins 的模型。 DTDL 是以 JSON-LD 為基礎，而且與程式設計語言無關。 DTDL 不是 Azure 數位 Twins 專屬的，但也可用來代表其他 IoT 服務（例如 [IoT 隨插即用](../iot-pnp/overview-iot-plug-and-play.md)）中的裝置資料。 

Azure 數位 Twins 使用 **DTDL _第2版_** 。 如需此 DTDL 版本的詳細資訊，請參閱 GitHub 中的規格檔： [*數位 Twins 定義語言 (DTDL) -第2版*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 使用 DTDL _第1版_ 搭配 Azure 數位 Twins 現在已被取代。

> [!NOTE] 
> 並非所有使用 DTDL 的服務都會執行完全相同的 DTDL 功能。 例如，IoT 隨插即用不會使用適用于圖形的 DTDL 功能，而 Azure 數位 Twins 目前不會執行 DTDL 命令。
>
> 如需有關 Azure 數位 Twins 專屬 DTDL 功能的詳細資訊，請參閱本文稍後有關 [Azure 數位 TWINS DTDL 實行細節](#azure-digital-twins-dtdl-implementation-specifics)的章節。

## <a name="elements-of-a-model"></a>模型的元素

在模型定義中，最上層程式碼專案是 **介面** 。 這會封裝整個模型，而且模型的其餘部分會在介面中定義。 

DTDL 模型介面可能包含下列每個欄位中的零個、一個或多個欄位：
* **屬性（Property** ）屬性（Property）是代表實體之狀態的資料欄位 (例如許多物件導向程式設計語言中的屬性) 。 屬性具有支援儲存體，而且可以隨時讀取。
* **遙測** -遙測欄位代表測量或事件，通常用來描述裝置感應器讀數。 不同于屬性，遙測不會儲存在數位對應項上;這是一系列的時間綁定資料事件，需要在發生時加以處理。 如需屬性和遙測之間差異的詳細資訊，請參閱下方的 [*屬性與遙測*](#properties-vs-telemetry) 一節。
* **元件** 元件可讓您視需要將模型介面建立為其他介面的元件。 元件的其中一個範例是 *frontCamera* 介面 (以及另一個元件介面 *backCamera* ) ，用來定義 *手機* 的模型。 您必須先定義 *frontCamera* 的介面，如同它本身的模型一樣，然後您可以在定義 *電話* 時參考它。

    您可以使用元件來描述屬於解決方案不可或缺的部分，但不需要個別的身分識別，也不需要個別在對應項圖形中建立、刪除或重新排列。 如果您想要讓實體在對應項圖形中有獨立的 existences，請將它們表示為不同模型的不同數位 twins、依 *關聯* 性連接 (請參閱下一個專案符號) 。
    
    >[!TIP] 
    >元件也可以用於組織，以將模型介面內的相關屬性集合分組。 在此情況下，您可以將每個元件視為介面內的命名空間或「資料夾」。
* **關聯** 性-關聯性可讓您表示數位對應項如何與其他數位 twins 有關。 關聯性可以表示不同的語義意義，例如 *包含* ( 「樓層包含房間」 ) 、 *冷卻* ( 「hvac 冷卻室」 ) 、 *isBilledTo* ( 「壓縮程式會向使用者收費」 ) 等等。關聯性可讓解決方案提供相互關聯實體的圖形。

> [!NOTE]
> [DTDL 的規格](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)也會定義 **命令** ，也就是可以在數位對應項上執行的方法， (例如重設命令，或是用來切換風扇) 的命令。 不過， *Azure 數位 Twins 目前不支援命令。*

### <a name="properties-vs-telemetry"></a>屬性與遙測

以下是一些在 Azure 數位 Twins 中區分 DTDL **屬性** 和 **遙測** 欄位的額外指引。

Azure 數位 Twins 模型的屬性和遙測之間的差異如下：
* **屬性** 預期會有支援儲存體。 這表示您可以隨時讀取屬性，並取得其值。 如果屬性是可寫入的，您也可以將值儲存在屬性中。  
* **遙測** 更像是事件串流;它是一組具有簡短壽命的資料訊息。 如果您未設定接聽事件，以及在事件發生時所採取的動作，就不會在稍後追蹤事件。 您無法再回來閱讀。 
  - 就 c # 而言，遙測就像是 c # 事件。 
  - 在 IoT 方面，遙測通常是裝置所傳送的單一度量。

**遙測** 通常會與 IoT 裝置搭配使用，因為許多裝置無法（或有興趣）儲存其產生的測量值。 它們只會將它們當作「遙測」事件的資料流程來傳送。 在此情況下，您不能隨時針對遙測欄位的最新值查詢裝置。 相反地，您必須接聽來自裝置的訊息，並在訊息抵達時採取動作。 

因此，在 Azure 數位 Twins 中設計模型時，您可能會在大部分情況下使用 **屬性** 來建立 Twins 模型。 這可讓您擁有支援儲存體，以及讀取和查詢資料欄位的能力。

遙測和屬性通常會一起運作，以處理來自裝置的資料輸入。 由於所有對 Azure 數位 Twins 的輸入都是透過 [api](how-to-use-apis-sdks.md)，因此您通常會使用輸入函式來讀取裝置的遙測或屬性事件，並在 ADT 中設定屬性以回應。 

您也可以從 Azure 數位 Twins API 發佈遙測事件。 就像其他遙測一樣，這是需要接聽程式才能處理的短暫事件。

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure 數位 Twins DTDL 的執行細節

若要讓 DTDL 模型與 Azure 數位 Twins 相容，必須符合這些需求。

* 模型中的所有最上層 DTDL 元素都必須是 *介面* 類型。 這是因為 Azure 數位 Twins 模型 Api 可以接收代表介面或介面陣列的 JSON 物件。 因此，最上層不允許其他 DTDL 元素類型。
* DTDL for Azure 數位 Twins 不得定義任何 *命令* 。
* Azure 數位 Twins 僅允許單一層級的元件嵌套。 這表示做為元件使用的介面不能有任何元件本身。 
* 無法在其他 DTDL 介面內以內嵌方式定義介面;它們必須定義為不同的最上層實體及其本身的識別碼。 然後，當另一個介面想要將該介面包含為元件或透過繼承時，它就可以參考它的識別碼。

Azure 數位 Twins 也不會觀察 `writable` 屬性或關聯性的屬性。 雖然可以根據 DTDL 規格設定此值，但 Azure 數位 Twins 不會使用此值。 相反地，對於 Azure 數位 Twins 服務具有一般寫入權限的外部用戶端，這些一律會被視為可寫入的。

## <a name="example-model-code"></a>範例模型程式碼

可以在任何文字編輯器中撰寫對應項類型模型。 DTDL 語言會遵循 JSON 語法，因此您應該儲存副檔名為 *JSON* 的模型。 使用 JSON 擴充功能可讓許多程式設計文字編輯器為您的 DTDL 檔提供基本語法檢查和反白顯示。 另外還有 [DTDL 延伸](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) 模組可用於 [Visual Studio Code](https://code.visualstudio.com/)。

本節包含撰寫為 DTDL 介面的一般模型範例。 此模型會描述 **行星** ，每個都有名稱、品質和溫度。
 
請考慮行星也 **可以與衛星本身互動，而且** 可能包含 **craters** 。 在下列範例中， `Planet` 模型藉由參考兩個外部模型（和）來表示與這些其他實體的連接 `Moon` `Crater` 。 這些模型也會在下列範例程式碼中定義，但會保持不變，因此不會降低主要 `Planet` 範例。

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

| 欄位 | 描述 |
| --- | --- |
| `@id` | 模型的識別碼。 的格式必須是 `dtmi:<domain>:<unique model identifier>;<model version number>` 。 |
| `@type` | 識別所描述的資訊種類。 若為介面，此類型為 *介面* 。 |
| `@context` | 設定 JSON 檔的 [內容](https://niem.github.io/json/reference/json-ld/context/) 。 模型應該使用 `dtmi:dtdl:context;2` 。 |
| `displayName` | 參數可讓您視需要為模型提供易記名稱。 |
| `contents` | 所有其餘的介面資料都放在這裡，作為屬性定義的陣列。 每個屬性都必須提供 `@type` (的 *屬性* 、 *遙測* 、 *命令* 、 *關聯* 性或 *元件* ) 來識別它所描述的介面資訊，然後是定義實際屬性的一組屬性 (例如， `name` 以及 `schema` 定義) *屬性* 。 |

> [!NOTE]
> 請注意，在此範例中 *火山* 的元件介面 (，) 是定義在與使用它 ( *行星* ) 之介面相同的陣列中。 您必須在 API 呼叫中以這種方式定義元件，才能找到介面。

### <a name="possible-schemas"></a>可能的架構

根據 DTDL， *屬性* 和 *遙測* 屬性的架構可以是標準基本類型（ `integer` 、 `double` 、 `string` 和 `Boolean` ），以及其他類型，例如 `DateTime` 和 `Duration` 。 

除了基本型別之外， *屬性* 和 *遙測* 欄位也可以具有下列複雜類型：
* `Object`
* `Map`
* `Enum`

*遙測* 欄位也支援 `Array` 。

### <a name="model-inheritance"></a>模型繼承

有時，您可能會想要進一步將模型特殊化。 例如，具有一般模型 *室* 和特製化變異（ *conferenceroom 等等* 和 *Gym* ）可能會很有用。 為了表達特製化，DTDL 支援繼承：介面可以繼承自一或多個其他介面。 

下列範例會從先前的 DTDL 範例重新 imagines *地球* 模型，作為較大 *CelestialBody* 模型的子類型。 「父」模型會先定義，然後「子」模型會使用欄位來建立 `extends` 。

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

在此範例中， *CelestialBody* 會為 *地球* 提供名稱、品質和溫度。 `extends`區段是介面名稱或介面名稱陣列 (如果需要) ，可讓擴充介面繼承多個父模型。

套用繼承之後，擴充介面會公開整個繼承鏈的所有屬性。

擴充介面無法變更父介面的任何定義;它只能新增至其中。 此外，它也無法重新定義其父介面中已定義的功能 (即使這些功能已定義為相同的) 。 例如，如果父介面定義 `double` 屬性 *品質* ，則擴充介面不能包含 *大量* 的宣告，即使它也是 `double` 。

## <a name="validating-models"></a>驗證模型

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="converting-industry-standard-models"></a>轉換產業標準模型

如果您的現有模型是以產業標準（例如 RDF 或 OWL）為基礎的 Azure 數位 Twins，您必須 **將它們轉換成 DTDL** ，以搭配 Azure 數位 Twins 使用。 DTDL 版本接著會成為 Azure 數位 Twins 內模型的真實來源。

如需此程式的詳細資訊，請參閱 [*如何：轉換產業標準模型*](how-to-convert-models.md)。

## <a name="next-steps"></a>下一步

請參閱如何使用 DigitalTwinModels Api 管理模型：
* [操作說明：管理自訂模型](how-to-manage-model.md)

或者，您也可以瞭解如何根據模型建立數位 twins：
* [*概念：數位 twins 和對應項圖表*](concepts-twins-graph.md)

