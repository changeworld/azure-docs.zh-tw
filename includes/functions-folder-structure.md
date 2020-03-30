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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205691"
---
特定函式應用程式中所有函式的程式碼，都位於包含主機設定檔和一或多個子資料夾的根專案資料夾中。 每個子資料夾都包含單獨函數的代碼。 資料夾結構顯示在以下表示形式中：

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

在函數運行時的版本 2.x 和更高版本中，函數應用中的所有函數都必須共用相同的語言堆疊。  

[host.json](../articles/azure-functions/functions-host-json.md)檔包含特定于運行時的配置，位於函數應用的根資料夾中。 *bin*資料夾包含函數應用所需的包和其他庫檔。 請參閱函式應用程式專案以了解特定語言的需求：

* [C# 類別庫 (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 指令碼 (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 指令碼](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [JAVA](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JAVAscript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
