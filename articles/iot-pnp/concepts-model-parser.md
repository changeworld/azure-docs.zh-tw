---
title: 瞭解數位 Twins 模型剖析器 |Microsoft Docs
description: 身為開發人員，瞭解如何使用 DTDL 剖析器來驗證模型
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352187"
---
# <a name="understand-the-digital-twins-model-parser"></a>瞭解數位 twins 模型剖析器

數位 Twins 定義語言（DTDL）會在[DTDL 規格](https://github.com/Azure/opendigitaltwins-dtdl)中說明。 使用者可以使用_數位 Twins 模型_剖析器 NuGet 封裝來驗證及查詢多個檔案中定義的模型。

## <a name="install-the-dtdl-model-parser"></a>安裝 DTDL 模型剖析器

剖析器可在 NuGet.org 中使用，識別碼為：[選取。](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser) 若要安裝剖析器，請使用任何相容的 NuGet 套件管理員，例如 Visual Studio 或 CLI 中的任何一個 `dotnet` 。

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>使用剖析器來驗證模型

您想要驗證的模型可能是由 JSON 檔案中所述的一或多個介面所組成。 您可以使用剖析器來載入指定資料夾中的所有檔案，並使用剖析器來驗證整個檔案，包括檔案之間的任何參考：

1. 建立 `IEnumerable<string>` 包含所有模型內容清單的：

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. 具現化 `ModelParser` 並呼叫 `ParseAsync` ：

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. 檢查驗證錯誤。 如果剖析器發現任何錯誤，則會擲回， `AggregateException` 其中會列出詳細的錯誤訊息：

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. 檢查 `Model` 。 如果驗證成功，您可以使用模型剖析器 API 來檢查模型。 下列程式碼片段示範如何反復查看所有剖析的模型，並顯示現有的屬性：

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>後續步驟

本文中所審查的模型剖析器 API 可讓許多案例自動化或驗證相依于 DTDL 模型的工作。 例如，您可以從模型中的資訊動態地建立 UI。
