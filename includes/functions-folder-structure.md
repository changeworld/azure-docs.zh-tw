---
title: 包含檔案
description: 包含檔案
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77205691"
---
特定函式應用程式中所有函式的程式碼，都位於包含主機設定檔和一或多個子資料夾的根專案資料夾中。 每個子資料夾都包含個別函式的程式碼。 資料夾結構會以下列表示法顯示：

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

在 2.x 版和更高版本的 Functions 執行階段中，函式應用程式中所有的應用程式必須共用相同的語言堆疊。  

[host.json](../articles/azure-functions/functions-host-json.md) 檔案包含執行階段特有的組態，且位於函式應用程式的根資料夾中。 bin 資料夾包含函式應用程式需要的封裝和其他程式庫檔案。 請參閱函式應用程式專案以了解特定語言的需求：

* [C# 類別庫 (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 指令碼 (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 指令碼](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
