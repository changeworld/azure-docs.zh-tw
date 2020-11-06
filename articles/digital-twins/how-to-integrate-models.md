---
title: 整合產業標準模型
titleSuffix: Azure Digital Twins
description: 瞭解如何使用特殊的 DTDL 本體或轉換現有的本體，將產業標準模型整合至 DTDL for Azure 數位 Twins
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1866e9b233a1379474c061779ada09fd6d119107
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338389"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>使用 DTDL for Azure 數位 Twins 整合產業標準模型

使用以產業標準為基礎或使用標準本體標記法的模型（例如 RDF 或 OWL），可在設計 Azure 數位 Twins 模型時提供豐富的起點。 使用產業模型也有助於標準化與資訊共用。

若要搭配 Azure 數位 Twins 使用，模型必須以以 JSON-LD 為基礎的 [**數位 Twins 定義語言表示， (DTDL)**](concepts-models.md)。 因此，本文說明如何在 DTDL 中呈現您的產業標準模型，並將現有的產業概念與 DTDL 語義整合，讓 Azure 數位 Twins 可以使用它們。 然後，DTDL 模型會作為 Azure 數位 Twins 內模型的真實來源。

整合產業標準模型與 DTDL 有三個可能的途徑：
* **採用** ：您可以使用已建基於廣泛採用產業標準的開放原始碼 DTDL 本體來啟動您的解決方案。 
* **轉換** ：如果您已經有現有的模型，您必須將它們轉換成 DTDL。
* **作者** ：您一律可以從頭開始開發自己的自訂 DTDL 模型，如 [*如何：管理自訂模型*](how-to-manage-model.md)中所述。

## <a name="adopt-an-open-source-dtdl-ontology"></a>採用開放原始碼 DTDL 本體

從空白頁面開始，通常可以更輕鬆地開始使用開放原始碼 DTDL 本體。 

例如，智慧建築解決方案可利用開放原始碼 DTDL 為 [**基礎的 RealEstateCore 本體**](https://github.com/Azure/opendigitaltwins-building)，此可提供模型化智慧建築的共同基礎，同時運用業界標準來預防我們重新改造。 

這些開放原始碼 DTDL 本體也提供如何取用和適當擴充模型的最佳作法。 

## <a name="convert-existing-models-to-dtdl"></a>將現有模型轉換成 DTDL

大部分的產業模型 (也稱為 **本體** ) 是以語義 web 標準（例如 [OWL](https://www.w3.org/OWL/)、 [RDF](https://www.w3.org/2001/sw/wiki/RDF)和 [RDFS](https://www.w3.org/2001/sw/wiki/RDFS)）為基礎。 

若要搭配使用模型與 Azure 數位 Twins，它必須是 DTDL 格式。 本節說明將以 RDF 為基礎的模型轉換成 DTDL，以將其用於 Azure 數位 Twins 的 **轉換模式** 一般設計指引。 

它也包含 [RDF 轉換器的 **範例轉換**](#sample-converter-application)程式程式碼 [，此程式](https://brickschema.org/ontology/)代碼已針對基礎構件架構進行驗證，並且可延伸至建築產業中的其他架構。

### <a name="conversion-pattern"></a>轉換模式

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

### <a name="sample-converter-application"></a>範例轉換器應用程式 

有一個可用的範例應用程式，可將以 RDF 為基礎的模型檔案轉換成 [DTDL (第2版) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 供 Azure 數位 Twins 服務使用。 它已針對基礎構件架構進行驗證，並且可延伸至建築產業 ([中的其他](https://brickschema.org/ontology/) 架構，例如 [建立拓撲本體 (BOT) ](https://w3c-lbd-cg.github.io/bot/)、 [語義感應器網路](https://www.w3.org/TR/vocab-ssn/)或 [buildingSmart 產業基礎類別 (IFC) ](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)) 。

此範例是名為 **RdfToDtdlConverter** 的 .net Core 命令列應用程式。

您可以在這裡取得範例： [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

若要將程式碼下載到您的電腦，請按範例登陸頁面標題底下的 [ *下載 ZIP* ] 按鈕。 這會下載名稱 *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* 下的 *ZIP* 檔案，然後您可以解壓縮和探索。

您可以使用此範例來查看內容中的轉換模式，並根據您自己的特定需求，將其做為您自己應用程式的建立區塊，以執行模型轉換。

## <a name="validate-and-upload-dtdl-models"></a>驗證和上傳 DTDL 模型

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

一旦模型轉換和驗證之後，您就可以將 **它上傳至您的 Azure 數位 Twins 實例** 。 如需此程式的詳細資訊，請參閱 how *to：管理自訂模型* 的「 [*上傳模型*](how-to-manage-model.md#upload-models)」一節。

## <a name="next-steps"></a>後續步驟 

深入瞭解如何設計和管理數位對應項模型：
 
* [概念：自訂模型](concepts-models.md)
* [操作說明：管理自訂模型](how-to-manage-model.md)
* [*How to：剖析和驗證模型*](how-to-parse-models.md)
