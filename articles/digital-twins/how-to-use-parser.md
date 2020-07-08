---
title: 剖析和驗證模型
titleSuffix: Azure Digital Twins
description: 瞭解如何使用解析程式程式庫來剖析 DTDL 模型。
author: cschormann
ms.author: cschorm
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b6d6e16b079f1423fd1ea812e384546ae5d84067
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392194"
---
# <a name="dtdl-client-side-parser-library"></a>DTDL 用戶端剖析器程式庫

Azure 數位 Twins 中的[模型](concepts-models.md)是使用以 JSON-LD 為基礎的數位 Twins 定義語言（DTDL）來定義。 對於剖析模型很有用的情況，NuGet.org 上會以用戶端程式庫的形式提供 DTDL 剖析程式庫：[選取](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。

此程式庫提供 DTDL 定義的模型存取，基本上是作為 DTDL 的 c # 反映的對應項。 此程式庫可獨立于任何[Azure 數位 TWINS SDK](how-to-use-apis-sdks.md)使用，特別是針對視覺效果或文字編輯器中的 DTDL 驗證。 在您嘗試將模型定義檔案上傳至服務之前，它會很有説明。

若要使用剖析器程式庫，您可以提供一組 DTDL 檔。 一般來說，您會從服務中抓取這些模型檔，但如果您的用戶端負責在一開始就將它們上傳到服務，則您可能也會在本機提供它們。 

以下是使用剖析器的一般工作流程：
1. 從服務中取出部分或所有的 DTDL 檔。
2. 將傳回的記憶體內部 DTDL 檔傳遞給剖析器。
3. 剖析器會驗證傳遞給它的檔集，並傳回詳細錯誤資訊。 這項功能在編輯器案例中很有用。
4. 使用剖析器 Api 繼續分析包含在檔集內的模型。 

剖析器的功能包括：
* 取得所有實作為模型介面（介面之區段的內容 `extends` ）。
* 取得模型中宣告的所有屬性、遙測、命令、元件和關聯性。 此命令也會取得包含在這些定義中的所有中繼資料，並將繼承（ `extends` 區段）納入考慮。
* 取得所有複雜的模型定義。
* 判斷模型是否可從另一個模型指派。

> [!NOTE]
> [IoT 隨插即用（PnP）](../iot-pnp/overview-iot-plug-and-play.md)裝置會使用小型的語法變異來描述其功能。 這個語法變異是 Azure 數位 Twins 中所使用之 DTDL 的語義相容子集。 使用剖析器程式庫時，您不需要知道使用哪個語法 variant 來建立數位對應項的 DTDL。 根據預設，剖析器一律會針對 PnP 和 Azure 數位 Twins 語法傳回相同的模型。

## <a name="use-the-dtdl-validator-sample"></a>使用 DTDL 驗證程式範例

有可用的範例程式碼可驗證模型檔，以確保 DTDL 有效。 它是建置於 DTDL 剖析器程式庫上，並與語言無關。 請在此尋找： [DTDL 驗證程式範例](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)。

驗證程式範例可做為命令列公用程式，用來驗證 DTDL 檔案的目錄樹狀結構。 它也會提供互動模式。 原始程式碼會顯示如何使用剖析器程式庫的範例。

在 DTDL 驗證程式範例的資料夾中，請參閱*readme.md*檔案，以取得如何將範例封裝到獨立可執行檔的指示。

在您建立獨立套件並將可執行檔新增至您的路徑之後，您可以在電腦的主控台中使用此命令來執行驗證程式：

```cmd/sh
DTDLValidator
```

使用預設選項時，此範例會搜尋 `*.json` 目前目錄和所有子目錄中的檔案。 您也可以新增下列選項，讓範例搜尋在指定的目錄中，並將所有子目錄用於副檔名為 *. dtdl*的檔案：

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

您可以新增 `-i` 範例的選項，以進入互動模式：

```cmd/sh
DTDLValidator -i
```

如需此範例的詳細資訊，請參閱原始程式碼或執行 `DTDLValidator --help` 。

## <a name="use-the-parser-library-in-code"></a>在程式碼中使用剖析器程式庫

您也可以直接使用剖析器程式庫，針對在自己的應用程式中驗證模型，或產生動態、模型驅動 UI、儀表板和報表等專案。

若要支援下列剖析器程式碼範例，請考慮在 Azure 數位 Twins 實例中定義的數個模型：

> [!TIP] 
> 此 `dtmi:com:contoso:coffeeMaker` 模型使用*功能模型*語法，這表示它是透過連接會公開該模型的 PnP 裝置，安裝在服務中。

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

下列程式碼顯示如何使用剖析器程式庫，以在 c # 中反映這些定義的範例：

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

當您完成撰寫模型之後，請參閱如何使用 DigitalTwinsModels Api 上傳它們（並執行其他管理作業）：
* [如何：管理自訂模型](how-to-manage-model.md)