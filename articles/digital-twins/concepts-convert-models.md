---
title: 轉換產業標準模型
titleSuffix: Azure Digital Twins
description: 瞭解將產業標準 (RDF/OWL) 模型轉換成 DTDL 的模式
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2d30410cf201475b69adce39eb6a66a7831cad8d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503244"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>將產業標準模型轉換為 DTDL for Azure 數位 Twins

Azure 數位 Twins 中的模型以以 JSON-LD 為基礎的 [**數位 Twins 定義語言表示 (DTDL) **](concepts-models.md)。 如果您的現有模型是以產業標準（例如 RDF 或 OWL）為基礎的 Azure 數位 Twins，您必須 **將它們轉換成 DTDL** ，以搭配 Azure 數位 Twins 使用。 DTDL 版本接著會成為 Azure 數位 Twins 內模型的真實來源。

本文描述將以 RDF 為基礎的產業或自訂模型轉換為 DTDL 的模式。 其中包括：
* 適用于各種標準和模型類型的**策略層級指導方針**
* [RDF 專用轉換器的**範例程式碼**](#sample-converter-application)

## <a name="industry-models"></a>產業模型  

使用產業模型可在設計 Azure 數位 Twins 模型時，提供豐富的起點。 使用產業模型也有助於標準化與資訊共用。 

一些常見的產業模型包括：  

| 產業垂直 | 型號 |
| --- | --- | 
| 建築物/裝置管理 | [RealEstateCore](https://www.realestatecore.io/)<br>[基礎構件架構](https://brickschema.org/ontology/1.1/)<br>[ (BOT) 建立拓撲本體 ](https://w3c-lbd-cg.github.io/bot/)<br>[語義感應器網路](https://www.w3.org/TR/vocab-ssn/)<br>[buildingSmart 產業基礎類別 (IFC) ](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| 智慧城市 | [ETSI NGSI-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[智慧型應用程式參考 (SAREF) ](https://saref.etsi.org/)<br>[Fiware](https://www.fiware.org/)<br>[開啟 & Agile 智慧城市 (OASC) ](https://oascities.org/) |
| 能源方格 | [CIM](https://cimug.ucaiug.org/) /[IEC 61968](https://en.wikipedia.org/wiki/IEC_61968)<br>[ADRM](http://www.adrm.com/) 能源和商用交易的模型 |
| 汽車 | [車輛信號規格](https://github.com/GENIVI/vehicle_signal_specification/tree/master/spec) |

視您的需求而定，您也可以使用 DTDL 自訂或擴充產業模型，或從頭開始開發自訂模型。 

## <a name="create-and-edit-models"></a>建立和編輯模型

模型化的第一個步驟是建立您的模型。 您可以先建立並完成產業標準模型的編輯，再將其轉換為 DTDL，或者您可以將它們轉換成早期 DTDL，然後繼續將其編輯為 DTDL 檔。

### <a name="with-industry-standards"></a>使用業界標準

大部分的產業模型 (也稱為 **本體**) 是以語義 web 標準（例如 [OWL](https://www.w3.org/OWL/[)、 [RDF](https://www.w3.org/2001/sw/wiki/RDF)和 [RDFS](https://www.w3.org/2001/sw/wiki/RDFS)）為基礎。 

在某些情況下，您可能會想要使用以 OWL 為基礎的模型工具來建立或編輯模型。 您可以使用任意數目的模型撰寫工具來設計以 OWL 為基礎的模型，包括下列各項。
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) 和 [Protégé Desktop](https://protege.stanford.edu/products.php#desktop-protege) 是熱門的範例。 您可以匯入多種格式的產業模型、編輯或擴充模型，以及匯出模型。 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) 是另一個用來視覺化模型的常用工具。 

如果您使用未 DTDL 的某些產業標準來建立模型，則需要將其轉換為 DTDL，並將其上傳至 Azure 數位 Twins。 

#### <a name="common-model-file-formats"></a>一般模型檔案格式 

RDF、OWL 和 RDFS 是語義 web 的基本構成要素。 

**RDF** 提供用來描述專案的概念結構（以 **三**合一的形式）。 三個部分包含三個部分： **subject** **、述詞和****物件**。 可以建立 Uri 的物件。 

以下是 RDF 三合一的一些範例：

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

這些範例全都是有效的 RDF，但最後一個語句在語義上無效。 這種情況是 OWL 規格進入圖片的位置。 **OWL** 會定義您可以使用 RDF 撰寫的內容，以便有有效的本體。

以下是使用 OWL 的範例： 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**RDFS** 提供可協助您定義和描述類別的其他詞彙語法。 例如，其中一個類別是 `rdfs:subClassOf` ：

```
<Equipment> <subClassOf> <Asset>
```

您可以透過許多檔案格式來儲存、匯入和匯出模型，包括：  
* RDF/XML 
* 龜標 (TTL)  
* OWL/XML 

### <a name="with-dtdl"></a>使用 DTDL

Azure 數位 Twins 使用以 JSON-LD 為基礎的數位對應項 **定義語言 (DTDL) ** 來進行模型化。 任何將與 Azure 數位 Twins 搭配使用的模型，都必須是原始寫入或轉換成 DTDL。

在 DTDL 中使用模型時，您可以使用適用于 Visual Studio Code 的[**DTDL 延伸**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)模組   ，以提供語法驗證和其他功能來協助撰寫 DTDL 模型。 [ ](https://code.visualstudio.com/)

您可以閱讀更多有關 Azure 數位 Twins 模型及其元件的 [*概念：自訂模型*](concepts-models.md) 和作法 [*：管理自訂模型*](how-to-manage-model.md)。

## <a name="convert-models-to-dtdl"></a>將模型轉換成 DTDL

若要搭配使用模型與 Azure 數位 Twins，它必須是 DTDL 格式。 本節說明如何將以 RDF 為基礎的模型轉換為 DTDL，讓它們可以與 Azure 數位 Twins 搭配使用。

您可以使用數個協力廠商程式庫，將以 RDF 為基礎的模型轉換為 DTDL。 其中一些程式庫可讓您將模型檔案載入圖形中。 您可以在圖表中迴圈查看特定的 RDFS 和 OWL 結構，並將其轉換為 DTDL。   

下表是 RDFS 和 OWL 結構如何對應至 DTDL 的範例。 

| RDFS/OWL 概念 | RDFS/OWL 結構 | DTDL 概念 | DTDL 結構 |
| --- | --- | --- | --- |
| 類別 | `owl:Class`<br>IRI 尾碼<br>``rdfs:label``<br>``rdfs:comment`` | 介面 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| 子 | `owl:Class`<br>IRI 尾碼<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | 介面 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Datatype 屬性 | `owl:DatatypeProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:label`<br>`rdfs:range` | 介面屬性 | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| 物件屬性 | `owl:ObjectProperty`<br>`rdfs:label` 或 `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | 關聯性 | `type:Relationship`<br>`name`<br>`target`如果沒有) ，則 (或省略 `rdfs:range`<br>`comment`<br>`displayName`<br>

下列 c # 程式碼片段顯示如何使用 [**dotNetRDF**](https://www.dotnetrdf.org/) 程式庫，將 RDF 模型檔案載入至圖形並轉換成 DTDL。 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>驗證和上傳 DTDL 模型

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

一旦模型轉換和驗證之後，您就可以將 **它上傳至您的 Azure 數位 Twins 實例**。 如需此程式的詳細資訊，請參閱 how *to：管理自訂模型*的「[*上傳模型*](how-to-manage-model.md#upload-models)」一節。

## <a name="sample-converter-application"></a>範例轉換器應用程式 

有一個可用的範例應用程式，可將以 RDF 為基礎的模型檔案轉換成 [DTDL (第2版) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 供 Azure 數位 Twins 服務使用。 此範例是名為 **RdfToDtdlConverter**的 .net Core 命令列應用程式。

您可以在這裡取得範例： [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

若要將程式碼下載到您的電腦，請按範例登陸頁面標題底下的 [ *下載 ZIP* ] 按鈕。 這會下載名稱*RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*下的*ZIP*檔案，然後您可以解壓縮和探索。

您可以使用此範例來查看內容中的轉換模式，並根據您自己的特定需求，將其做為您自己應用程式的建立區塊，以執行模型轉換。

## <a name="next-steps"></a>後續步驟 

深入瞭解如何設計和管理數位對應項模型：
 
* [概念：自訂模型](concepts-models.md)
* [操作說明：管理自訂模型](how-to-manage-model.md)
* [*How to：剖析和驗證模型*](how-to-parse-models.md)