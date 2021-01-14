---
title: 範圍 SDK
titleSuffix: Azure Defender for IoT
description: 範圍 SDK 可讓適用于 IoT 的 Azure Defender 開發人員設計 dissector 外掛程式，以將網路流量解碼，讓 IoT 網路分析程式的自動化 Defender 可以進行處理。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: d6105f65508eff59164246020d9a3f286b68c5a1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210521"
---
# <a name="horizon-proprietary-protocol-dissector"></a>範圍專屬通訊協定 dissector

範圍是開放式開發環境 (NODE.JS) 用來保護執行專屬通訊協定的 IoT 和 ICS 裝置。

此環境為客戶和技術合作夥伴提供下列解決方案：

- 不受限制、完整支援常見、專屬的自訂通訊協定，或與任何標準的通訊協定。 

- 適用于 DPI 開發的新彈性和範圍層級。

- 以指數方式展開可見度和控制項的工具，而不需要升級適用于 IoT 平臺版本的 Defender。

- 在不 divulging 機密資訊的情況下允許專屬開發的安全性。

範圍 SDK 可讓適用于 IoT 的 Azure Defender 開發人員設計 dissector 外掛程式，以將網路流量解碼，讓 IoT 網路分析程式的自動化 Defender 可以進行處理。

通訊協定 dissectors 是以外部外掛程式的形式開發，並且與廣泛的適用于 IoT 服務的 Defender 範圍整合。 例如，提供監視、警示和報告功能的服務。

## <a name="secure-development-environment"></a>安全的開發環境 

水準 NODE.JS 可讓您開發無法在組織外共用的自訂或專屬通訊協定。 例如，因為法律規定或公司原則。

開發 dissector 外掛程式而不需要： 

- 公開有關如何定義您的通訊協定的任何專屬資訊。

- 共用任何您的機密 PCAPs。

- 違反合規性法規。

## <a name="customization-and-localization"></a>自訂和當地語系化  

SDK 支援各種自訂選項，包括：

  - 函數代碼的文字。 

  - 警示、事件和通訊協定參數的完整當地語系化文字。 如需詳細資訊，請參閱 [ (JSON) 建立對應 ](#create-mapping-files-json)檔。

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="查看完全當地語系化的警示。":::

## <a name="horizon-architecture"></a>範圍架構

架構模型包含三個產品層。

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>適用于 IoT 的 Defender 平台層

可即時監視 Defender for IoT 平臺中的自訂 dissector 外掛程式，而不需要升級適用于 IoT 的 Defender 平臺版本。

## <a name="defender-for-iot-services-layer"></a>適用于 IoT 的 Defender 服務層

每項服務都設計為管線，與特定的通訊協定分離，以提供更有效率的獨立開發。

每項服務都設計成與特定通訊協定分離的管線。 服務會接聽管線上的流量。 它們會與外掛程式資料和感應器所捕獲的流量互動，以編制部署的通訊協定索引並分析流量承載，並啟用更有效率且獨立的開發。

## <a name="custom-dissector-layer"></a>自訂 dissector 層 

使用 Defender for IoT 專屬 SDK 來建立外掛程式， (包括 c + + 的執行和 JSON 設定) 到： 

- 定義如何識別通訊協定

- 定義如何對應您想要從流量中解壓縮的欄位，並將其解壓縮 

- 定義如何與 Defender for IoT 服務整合

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="內建層。":::

適用于 IoT 的 Defender 提供一般通訊協定的基本 dissectors。 您可以在這些通訊協定之上建立您的 dissectors。

## <a name="before-you-begin"></a>開始之前

## <a name="what-this-sdk-contains"></a>此 SDK 包含的內容 

此套件包含開發所需的標頭檔。 開發程式需要此 SDK 中所述的基本步驟和選擇性的 advanced 步驟。

<support@cyberx-labs.com>如需接收標頭檔和其他資源的相關資訊，請聯絡。

## <a name="about-the-environment-and-setup"></a>關於環境和設定 

### <a name="requirements"></a>規格需求 

- 慣用的開發環境是 Linux。 如果您是在 Windows 環境中進行開發，請考慮將 VM 與 Linux 系統搭配使用。

- 針對編譯器，請使用 GCC 7.4.0 或更新版本。 使用 c + + 17 所支援之 stdlib.h 中的任何標準類別。

- 適用于 IoT 3.0 版和更新版本的 Defender。

### <a name="process"></a>處理序

1. [下載](https://www.eclipse.org/) 適用于 C/c + + 開發人員的 Eclipse IDE。 您可以使用任何其他慣用的 IDE。 本檔會引導您使用 Eclipse IDE 進行設定。

1. 啟動 Eclipse IDE 並設定工作區 (儲存專案的位置) ，請按 **Ctrl + n**，然後將它建立為 c + + 專案。

1. 在下一個畫面上，將名稱設為您想要開發的通訊協定，並選取 [] 和 [專案類型] `Shared Library` `AND Linux GCC` 。

1. 編輯專案屬性，在 **C/c + + 組建**  >  **設定**  >  **工具設定**  >  **GCC c + + 編譯器**  >  的 **其他**  >  **刻度位置獨立程式碼**。

1. 貼上您使用 SDK 所收到的範例程式碼並加以編譯。

1. 將成品 (程式庫、config.js開啟和中繼資料) 加入至 gz 檔案，並將副檔名變更為 \<XXX> hdp，其中是 \<XXX> 外掛程式的名稱。

### <a name="research"></a>參考 

開始之前，請確認您：

- 如果有的話，請閱讀通訊協定規格。

- 瞭解您打算解壓縮的通訊協定欄位。

- 已規劃您的對應目標。

## <a name="about-plugin-files"></a>關於外掛程式檔案 

開發過程中會定義三個檔案。

### <a name="json-configuration-file-required"></a>需要 (JSON 設定檔)  

此檔案應定義 dissector 識別碼和宣告、相依性、整合需求、驗證參數和對應定義，以將值轉譯為名稱、將數位轉換成文字。 如需詳細資訊，請參閱下列連結：

- [ (JSON) 準備設定檔 ](#prepare-the-configuration-file-json)

- [準備實施程式碼驗證](#prepare-implementation-code-validations)

- [將裝置中繼資料解壓縮](#extract-device-metadata)

- [連接到索引服務 (基準) ](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>執行程式碼： c + + (必要) 

 (CPP 的執行程式碼) 剖析未經處理的流量，並將其對應至服務、類別和函式程式碼之類的值。 它會將圖層欄位解壓縮，並將其對應至 JSON 設定檔中的索引名稱。 要從 CPP 解壓縮的欄位會定義在設定檔中。 如需詳細資訊，請參閱 [準備 (c + +) 的執行程式碼 ](#prepare-the-implementation-code-c)。

### <a name="mapping-files-optional"></a> (選擇性) 對應檔案

您可以自訂外掛程式輸出文字，以符合您企業環境的需求。

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="遷移":::

您可以定義和更新對應檔，以更新文字，而不需要變更程式碼。 每個檔案可以對應一或多個欄位：

  - 將域值對應至名稱，例如1： Reset、2： Start、3： Stop。

  - 對應文字以支援多種語言。

如需詳細資訊，請參閱 [ (JSON) 建立對應 ](#create-mapping-files-json)檔。

## <a name="create-a-dissector-plugin-overview"></a>建立 dissector 外掛程式 (總覽) 

1. 請參閱 [關於環境和設定](#about-the-environment-and-setup) 一節。

2.  [ (c + +) 準備執行程式碼 ](#prepare-the-implementation-code-c)。 複製檔案 **上的template.js** ，並加以編輯以符合您的需求。 請勿變更金鑰。 

3. [ (JSON) 準備設定檔 ](#prepare-the-configuration-file-json)。 複製 **範本 .cpp** 檔案，並執行覆寫方法。 如需詳細資訊，請參閱 [範圍：:p rotocol：： BaseParser](#horizonprotocolbaseparser) ，以取得詳細資料。

4. [準備實行程式碼驗證](#prepare-implementation-code-validations)。

## <a name="prepare-the-implementation-code-c"></a> (c + +) 準備執行程式碼

CPP 檔案是負責下列動作的剖析器：

- 驗證封包標頭和內容 (，例如標頭長度或承載結構) 。

- 將標頭和承載中的資料解壓縮至已定義的欄位。

- 執行 JSON 檔案所提取的設定欄位。

### <a name="what-to-do"></a>解決方式

複製範本 **.cpp** 檔案，並執行覆寫方法。 如需詳細資訊，請參閱 [範圍：:p rotocol：： BaseParser](#horizonprotocolbaseparser)。

### <a name="basic-c-template-sample"></a>基本 c + + 範本範例 

本節提供基本的通訊協定範本，其中包含適用于 IoT 範圍通訊協定的範例 Defender 標準函式。

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>基本 c + + 範本描述  

本節提供基本的通訊協定範本，並說明適用于 IoT 範圍通訊協定的範例 Defender 標準函式。 

### <a name="include-pluginpluginh"></a>#include "外掛程式/外掛程式 .h"

外掛程式所使用的定義。 標頭檔包含完成開發所需的所有專案。

### <a name="horizonprotocolbaseparser"></a>範圍：:p rotocol：： BaseParser

範圍基礎結構和外掛程式層之間的通訊介面。 如需詳細資訊，請參閱「 [範圍架構](#horizon-architecture) 」以瞭解各層的總覽。

ProcessLayer 是用來處理資料的方法。

- 函式程式碼中的第一個參數是處理公用程式，用於抓取先前處理的資料、建立新的欄位和圖層。

- 函數程式碼中的第二個參數是從先前剖析器傳遞的目前資料。

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

使用建立剖析器的實例。

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>通訊協定函式程式碼範例 

本節提供的範例說明如何將程式碼編號 (2 個位元組) ，以及將訊息長度 (4 位元組) 解壓縮。

這會根據 JSON 設定檔中提供的位元組格式來完成，這表示通訊協定不是 *位元組* 程式順序，而感應器會在具有極大位元組順序的電腦上執行，因此會進行轉換。

此外也會建立用來儲存資料的圖層。 使用處理公用程式中的 *fieldsManager* 來建立新的欄位。 欄位只能有下列其中一種類型： *字串*、 *數位*、 *原始資料*、特定類型的 *陣列* () 或 *複雜*。 此層可包含數位、原始或識別碼的字串。

下列範例會將下列兩個欄位解壓縮：

- `function_code_number`

- `headerLength`

建立新的圖層，然後將解壓縮的欄位複製到其中。

下列範例說明特定的函式，這是為了進行外掛程式處理而實行的主要邏輯。

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>相關的 JSON 欄位 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="相關的 json 欄位。":::

## <a name="prepare-the-configuration-file-json"></a> (JSON) 準備設定檔

範圍 SDK 使用標準 JavaScript 物件標記法 (JSON) ，這是儲存和傳輸資料的輕量格式，不需要專屬的指令碼語言。

本節說明最基本的 JSON 設定宣告、相關結構，並提供定義通訊協定的範例設定檔。 此通訊協定會自動與裝置探索服務整合。

## <a name="file-structure"></a>檔案結構

下列範例說明檔案結構。

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="檔案結構的範例。":::

### <a name="what-to-do"></a>解決方式

複製範本檔案 `config.json` 並加以編輯，以符合您的需求。 請勿變更金鑰。 [範例 JSON 設定檔](#sample-json-configuration-file)中的索引鍵會以紅色標示。 

### <a name="file-naming-requirements"></a>檔案命名需求 

JSON 設定檔案必須另存為 `config.json` 。

### <a name="json-configuration-file-fields"></a>JSON 設定檔欄位

本節說明您將定義的 JSON 設定欄位。 請勿變更欄位 *標籤*。

### <a name="basic-parameters"></a>基本參數

本節說明基本參數。

| 參數標籤 | 描述 | 類型 |
|--|--|--|
| **識別碼** | 通訊協定的名稱。 刪除預設值，並在您的通訊協定出現時新增其名稱。 | String |
| **endianess** | 定義多位元組資料的編碼方式。 只使用「小」或「大」一詞。 取自通訊協定規格或交通記錄 | String |
| **sanity_failure_codes** | 當有關于程式碼身分識別的健全衝突時，就會傳回剖析器傳回的程式碼。 請參閱 c + + 區段中的魔術號碼驗證。 | String |
| **malformed_codes** | 這些是已正確識別的程式碼，但偵測到錯誤。 例如，如果欄位長度太短或太長，或值無效。 | String |
| **dissect_as** | 定義特定通訊協定流量應抵達之位置的陣列。 | TCP/UDP、埠等。 |
| **欄位** | 宣告哪些欄位將從流量中解壓縮。 每個欄位都有自己的識別碼 (名稱) ，以及類型 (數值、字串、原始、陣列、複雜) 。 例如，在執行剖析器檔案中解壓縮的欄位 [函數](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) 。 寫入設定檔中的欄位是唯一可新增至圖層的欄位。 |  |

### <a name="other-advanced-fields"></a>其他 advanced 欄位 

本節說明其他欄位。

| 參數標籤 | 描述 |
|-----------------|--------|
| **允許清單** | 您可以為通訊協定值編制索引，並將它們顯示在資料採礦報表中。 這些報表會反映您的網路基準。 :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="資料採礦視圖的範例。"::: <br /> 如需詳細資訊，請參閱 [連接到索引服務 (基準) ](#connect-to-an-indexing-service-baseline) 以取得詳細資料。 |
| **固件** | 您可以解壓縮固件資訊、定義索引值，以及觸發外掛程式通訊協定的固件警示。 如需詳細資訊，請參閱 [解壓縮固件資料](#extract-firmware-data) 以取得詳細資料。 |
| **value_mapping** | 您可以藉由定義和更新對應檔，自訂外掛程式輸出文字以符合您企業環境的需求。 例如，對應至語言檔案。 您可以輕鬆地將變更實作為文字，而不需要變更或影響程式碼。 如需詳細資訊，請參閱 [ (JSON) 建立對應 ](#create-mapping-files-json) 檔，以取得詳細資料。 |

## <a name="sample-json-configuration-file"></a>範例 JSON 設定檔

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>準備實施程式碼驗證

本節說明如何執行 c + + 程式碼驗證函式，並提供範例程式碼。 有兩層驗證可用：

- 理智。

- 程式碼格式不正確。

您不需要建立驗證程式代碼，就能建立可運作的外掛程式。 如果您未準備驗證程式代碼，您可以查看感應器資料採礦報告，以指出是否已成功處理。

域值可以對應至對應檔案中的文字，並在不影響處理的情況下順暢地更新。

## <a name="sanity-code-validations"></a>健全程式碼驗證 

這會驗證傳送的封包符合通訊協定的驗證參數，以協助您識別流量內的通訊協定。

例如，使用前8個位元組作為 *魔術數位*。 如果健全狀況失敗，則會傳回健全狀況失敗回應。

例如：

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

如果已部署其他相關的外掛程式，封包會對它們進行驗證。

## <a name="malformed-code-validations"></a>錯誤的程式碼驗證 

經過正確驗證的通訊協定之後，會使用格式不正確的驗證。

如果無法處理以通訊協定為基礎的封包，則會傳回失敗回應。

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="格式不正確的程式碼":::

## <a name="c-sample-with-validations"></a>具有驗證的 c + + 範例

根據函式，會執行進程，如下列範例所示。

### <a name="function-20"></a>函數20 

- 它會以固件的形式處理。

- 這些欄位會根據函數讀取。

- 這些欄位會加入至圖層。

### <a name="function-10"></a>函數10 

- 函數包含另一個子函式，這是更特定的作業

- 子會讀取並新增至圖層。

完成此作業之後，就會完成處理。 傳回值會指出是否已成功處理 dissector 層。 如果是，則圖層會變成可用。

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>將裝置中繼資料解壓縮

您可以將下列中繼資料解壓縮到資產上：

  - `Is_distributed_control_system` -表示通訊協定是否為分散式控制系統的一部分。  (範例： SCADA 通訊協定應為 false) 

  - `Has_protocol_address` –指出是否有通訊協定位址;目前通訊協定的特定位址，例如 MODBUS 單位識別碼

  - `Is_scada_protocol` -表示通訊協定是否專屬於 OT 網路

  - `Is_router_potential` -表示通訊協定主要是由路由器使用。 例如，LLDP、CDP 或 STP。

為了達到此目的，必須使用 metadata 屬性來更新 JSON 設定檔案。

## <a name="json-sample-with-metadata"></a>具有中繼資料的 JSON 範例 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>解壓縮程式設計程式碼 

當程式設計事件發生時，您可以解壓縮程式代碼內容。 解壓縮的內容可讓您：

- 比較不同程式設計事件中的程式碼檔案內容。

- 觸發未授權程式設計的警示。  

- 觸發用來接收程式設計程式碼檔的事件。

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="程式設計變更記錄檔。":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="按一下按鈕以查看程式設計。":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="未經授權的 PLC 程式設計警示。":::

為了達到此目的，必須使用屬性來更新 JSON 設定檔案 `code_extraction` 。 

### <a name="json-configuration-fields"></a>JSON 設定欄位 

本節說明 JSON 設定欄位。 

- **方法**

  指出程式設計事件檔的接收方式。 

  所有 (的程式設計動作都會導致即使檔案沒有變更) ，也會收到所有程式碼檔案。

- **file_type**  

  表示程式碼內容類型。  

  文字 (每個程式碼檔案都包含) 的文字資訊。

- **code_data_field**
  
  指出要用來提供程式碼內容的執列欄位。

  領域。

- **code_name_field**

  指出要用來提供程式碼撰寫檔名稱的執列欄位。

  領域。

- **size_limit**

  指出每個編碼檔案內容的大小限制（以位元組為單位），如果程式碼檔案超過設定的限制，將會予以捨棄。 如果未指定此欄位，預設值會是 15 MB 的15000000。

  數量。

- **元**

  指出程式碼檔案的其他資訊。

  陣列，包含具有兩個屬性的物件：

    - 名稱 (字串) -表示中繼資料索引鍵 XSense 目前只支援使用者名稱索引鍵。
 
    - 值 (欄位) -表示要用來提供中繼資料資料的執列欄位。

## <a name="json-sample-with-programming-code"></a>具有程式碼的 JSON 範例

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>自訂範圍警示

某些通訊協定函式程式碼可能表示發生錯誤。 例如，如果通訊協定控制具有特定化學的容器，而該化學必須一律儲存在特定溫度。 在此情況下，可能會有函數代碼指出溫度計中的錯誤。 例如，如果函式程式碼為25，您可以在 Web 主控台中觸發指出容器有問題的警示。 在這種情況下，您可以定義深層封包警示。

將 [ **警示** ] 參數新增至 `config.json` 外掛程式的。

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>JSON 設定欄位

本節說明 JSON 設定欄位。 

| 欄位名稱 | 描述 | 可能值 |
|--|--|--|
| **識別碼** | 表示單一警示識別碼。 在此內容中必須是唯一的。 | 數值 0-10000 |
| **message** | 顯示給使用者的資訊。 此欄位可讓您使用不同的欄位。 | 使用您的通訊協定中的任何欄位，或任何較低層的通訊協定。 |
| **title** | 警示標題 |  |
| **expression** | 當您想要讓此警示彈出時。 | 使用位於較低圖層或目前圖層中的任何數值欄位。</br></br> 每個欄位都應該使用包裝函式 `{}` ，以便讓 SDK 偵測為欄位，支援的邏輯運算子為</br> = =-等於</br> <=-小於或等於</br> >=-大於或等於</br> >-超過</br> <-小於</br> ~ =-不等於 |

## <a name="more-about-expressions"></a>關於運算式的詳細資訊

每次當範圍 SDK 叫用運算式，並變成 *true* 時，感應器就會觸發警示。

相同警示中可以包含多個運算式。 例如，

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

此運算式只會在10.10.10.10 封包 ipv4 src 時驗證函式程式碼。 這是數值標記法中 IP 位址的原始標記法。

您可以使用 `and` 或，以便 `or` 連接運算式。

## <a name="json-sample-custom-horizon-alerts"></a>JSON 範例自訂範圍警示

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>連接到索引服務 (基準) 

您可以為通訊協定值編制索引，並將它們顯示在資料採礦報表中。

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="資料採礦選項的視圖。":::

這些值稍後可以對應至特定文字，例如，以任何語言將資料對應至文字或新增資訊。

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="遷移":::

如需詳細資訊，請參閱 [ (JSON) 建立對應 ](#create-mapping-files-json) 檔，以取得詳細資料。

您也可以使用先前剖析的通訊協定值來解壓縮其他資訊。

例如，針對以 TCP 為基礎的值，您可以使用 IPv4 層的值。 您可以從這一層中，將值（例如封包的來源）和目的地等值解壓縮。

為了達到此目的，必須使用屬性來更新 JSON 設定檔案 `whitelist` 。

## <a name="allow-list-data-mining-fields"></a>允許清單 (資料採礦) 欄位

可以使用下列允許清單欄位：

- 名稱–用來編制索引的名稱。

- alert_title –說明事件的簡短、唯一的標題。

- alert_text –其他資訊

您可以新增多個允許清單，以便在編制索引時提供完整的彈性。

## <a name="json-sample-with-indexing"></a>具有索引編制的 JSON 範例 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>將固件資料解壓縮

您可以解壓縮固件資訊、定義索引值，以及觸發外掛程式通訊協定的固件警示。 例如，

- 將固件模型或版本解壓縮。 此資訊可進一步利用來識別 Cve。

- 偵測到新的固件版本時，觸發警示。

為了達到此目的，必須使用 [固件] 屬性來更新 JSON 設定檔案。

## <a name="firmware-fields"></a>固件欄位

本節說明 JSON 固件設定欄位。

- **name**
  
  指出欄位在感應器主控台中的呈現方式。

- **value**

 指出要用來提供資料的執列欄位。 

- **firmware_index：**

  選取一項：  
  - **模型**：裝置型號。 啟用 Cve 的偵測。
  - **序列**：裝置序號。 所有通訊協定都不一定都有序號。 此值在每個裝置上都是唯一的。
  - **機架**：表示機架識別碼（如果裝置是機架的一部分）。
  - 位置 **：如果** 裝置是機架的一部分，則為位置識別碼。  
  - **module_address**：如果模組可以顯示在另一個裝置後方，請使用來呈現階層。 適用于機架位置組合，也就是更簡單的簡報。  
  - **firmware_version**：指出裝置版本。 啟用 Cve 的偵測。
  - **alert_text**：表示描述固件偏差的文字，例如版本變更。  
  - **index_by**：表示用來識別及編制裝置索引的欄位。 在下列範例中，裝置是由其 IP 位址所識別。 在某些通訊協定中，可以使用更複雜的索引。 例如，如果另一個裝置已連線，且您知道如何解壓縮其內部路徑。 例如，MODBUS 單位識別碼可以作為索引的一部分，以不同的 IP 位址和單位識別碼的組合來使用。
  - **firmware_fields**：指出哪些欄位是裝置元資料欄位。 在此範例中，會使用下列各項：模型、修訂和名稱。 每個通訊協定都可以定義自己的固件資料。

## <a name="json-sample-with-firmware"></a>具有固件的 JSON 範例 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>解壓縮裝置屬性 

您可以在清查、資料採礦和其他報表中增強裝置的可用資訊。

- 名稱

- 類型

- 廠商

- 作業系統

為了達到此目的，必須使用 **Properties** 屬性來更新 JSON 設定檔。 

您可以在撰寫基本外掛程式和解壓縮必要欄位之後進行此作業。

## <a name="properties-fields"></a>屬性欄位

本節說明 [JSON 屬性] 設定欄位。 

**config_file** 

包含定義如何處理欄位中每個索引鍵的檔案名 `key` 。 設定檔本身應為 JSON 格式，並納入為外掛程式通訊協定資料夾的一部分。

JSON 中的每個索引鍵都會定義當您從封包解壓縮此金鑰時，應執行的動作集合。

每個金鑰可以有：

- 封 **包資料**-指出將根據從封包解壓縮的資料 (用來提供該資料) 的「實」欄位來更新的屬性。

- **靜態資料** -表示應更新的預先定義 `property-value` 動作集。

可以在此檔案中設定的屬性為： 

- **名稱** -表示裝置名稱。

- **類型** -指出裝置類型。

- **廠商** -表示裝置廠商。

- **描述** -指出裝置固件型號 (低於「模型」 ) 的優先順序。

- **作業系統** -指出裝置作業系統。

### <a name="fields"></a>欄位

| 欄位 | 描述 |
|--|--|
| 索引鍵 | 指出金鑰。 |
| 值 | 指出要用來提供資料的執列欄位。 |
| is_static_key | 指出欄位是否 `key` 衍生為封包中的值，或是否為預先定義的值。 |

### <a name="working-with-static-keys-only"></a>僅使用靜態金鑰

如果您使用的是靜態金鑰，則不需要設定 `config.file` 。 您只能設定 JSON 檔案。

## <a name="json-sample-with-properties"></a>具有屬性的 JSON 範例 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a> (JSON) 建立對應檔

您可以藉由定義和更新對應檔，自訂外掛程式輸出文字以符合您企業環境的需求。 您可以輕鬆地將變更實作為文字，而不需要變更或影響程式碼。 每個檔案都可以對應一或多個欄位。

- 將域值對應至名稱，例如1： Reset、2： Start、3： Stop。

- 對應文字以支援多種語言。

您可以定義兩種類型的對應檔案。

  - [簡易對應](#simple-mapping-file)檔。

  - 相依性[對應](#dependency-mapping-file)檔。

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="乙太幣 net":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="未處理警示的觀點。":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="已知的原則違規清單。":::

## <a name="file-naming-and-storage-requirements"></a>檔案命名和儲存需求 

對應檔案應該儲存在 metadata 資料夾下。

檔案名必須符合 JSON 設定檔案識別碼。

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="JSON 設定檔案的範例。":::

## <a name="simple-mapping-file"></a>簡易對應檔 

下列範例會顯示基本的 JSON 檔案做為索引鍵值。

當您建立允許清單時，它會包含一或多個對應的欄位。 值將會從數位、字串或任何類型轉換成對應檔中所顯示的格式化文字。

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>相依性對應檔 

若要指出檔案是相依性檔案，請將關鍵字加入 `dependency` 至對應設定。

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

檔案包含相依性欄位與函數位段之間的對應。 例如，在函數和 sub 函數之間。 子函式會根據所提供的函式而變更。

在先前設定的允許清單中，沒有相依性設定，如下所示。

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

相依性可以根據特定的值或欄位。 在下列範例中，它是以欄位為基礎。 如果您以值作為基礎，請定義要從對應檔讀取的解壓縮值。

在下列範例中，相依性會遵循相同的欄位值。

例如，在子函式中，會根據函數變更意義。

  - 如果是 read 函式，則五表示讀取記憶體。

  - 如果它是寫入函式，則會使用相同的值從檔案讀取。

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>範例檔案

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>具有對應的 JSON 範例

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>封裝、上傳及監視外掛程式 

本節說明如何

  - 封裝您的外掛程式。

  - 上傳您的外掛程式。

  - 監視和偵測外掛程式，以評估其執行效能。

若要封裝外掛程式：

1. **將成品 (可以** 是、程式庫、config.js，或中繼資料) 新增至檔案 `tar.gz` 。

1. 將副檔名變更為 \<XXX.hdp> ，其中是 \<XXX> 外掛程式的名稱。

若要登入範圍主控台：

1.  以系統管理員、CyberX 或支援使用者身分登入您的感應器 CLI。

2.  在檔案中： `/var/cyberx/properties/horizon.properties` 將 [ **已啟用** ] 屬性變更為 [ **true** ]， (`horizon.properties:ui.enabled=true`) 。

3.  登入感應器主控台。

4.  從主功能表中選取 [ **範圍** ] 選項。

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="從左側窗格中選取 [範圍] 選項。":::

    範圍主控台隨即開啟。

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="範圍主控台及其所有外掛程式的觀點。":::

## <a name="plugins-pane"></a>外掛程式窗格

[外掛程式] 窗格會列出：

  - 基礎結構外掛程式：預設會使用 Defender for IoT 安裝基礎結構外掛程式。

  - 應用程式外掛程式：根據預設，會針對 IoT 和 defender 針對 IoT 或外部開發人員所開發的其他外掛程式安裝應用程式外掛程式。
    
啟用及停用使用切換所上傳的外掛程式。

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CJP 切換。":::

### <a name="uploading-a-plugin"></a>上傳外掛程式

建立並封裝您的外掛程式之後，您可以將其上傳至適用于 IoT 的 Defender 感應器。 若要完全涵蓋您的網路，您應該將外掛程式上傳至您組織中的每個感應器。

若要上傳：

1.  登入您的感應器。


2. 選取 [上傳] 。

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="上傳您的外掛程式。":::

3. 流覽至您的外掛程式，並將其拖曳至 [外掛程式] 對話方塊。 確認首碼為 `.hdp` 。 外掛程式會載入。

## <a name="plugin-status-overview"></a>外掛程式狀態總覽 

範圍主控台 **總覽** 視窗提供您所上傳之外掛程式的相關資訊，並可讓您停用和啟用。

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="範圍主控台的總覽。":::

| 欄位 | 描述 |
|--|--|
| Application | 您上傳之外掛程式的名稱。 |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="On 和 off 參數。"::: | 開啟 **或****關閉** 外掛程式。 當您關閉外掛程式時，Defender for IoT 不會處理外掛程式中定義的通訊協定流量。 |
| Time | 上次分析資料的時間。 每隔5秒更新一次。 |
| Pps | 每秒的封包數目。 |
| 頻寬 | 過去5秒內偵測到的平均頻寬。 |
| Malforms | 經過正確驗證的通訊協定之後，會使用格式不正確的驗證。 如果無法處理以通訊協定為基礎的封包，則會傳回失敗回應。   <br><br>這個資料行會指出過去5秒內的 malform 錯誤數目。 如需詳細資訊，請參閱錯誤的程式 [代碼驗證](#malformed-code-validations) 以取得詳細資料。 |
| 警告 | 封包符合結構和規格，但根據外掛程式警告設定，有非預期的行為。 |
| Errors | 基本通訊協定驗證失敗的封包數目。 驗證封包符合通訊協定定義。 此處顯示的數位指出過去5秒內偵測到的錯誤數目。 如需詳細資訊，請參閱 [健全程式碼驗證](#sanity-code-validations) 以取得詳細資料。 |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="監視器圖示。"::: | 查看針對您的外掛程式偵測到的 malform 和警告的詳細資料。 |

## <a name="plugin-details"></a>外掛程式詳細資料

您可以藉由分析為外掛程式偵測到的 *Malform* 和 *警告* 數目，來監視即時外掛程式行為。 有一個選項可用來凍結畫面並匯出，以供進一步調查

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="SNMP 監視器畫面。":::

若要監視：

從總覽中選取外掛程式的 [監視] 按鈕。

後續步驟

設定您的 [範圍 API](references-horizon-api.md)
