---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949967"
---
## <a name="create-the-local-function-app-project"></a>建立本機函式應用程式專案

從命令列執行下列命令，以在目前本機目錄的 `MyFunctionProj` 資料夾中建立函式應用程式專案。 系統也會在 `MyFunctionProj` 中建立 GitHub 存放庫。

```command
func init MyFunctionProj
```

當出現提示時，請從下列語言選項中選取背景工作角色執行階段：

+ `dotnet`：建立 .NET 類別庫專案 (.csproj)。
+ `node`：建立以 Node.js 為基礎的專案。 選擇 `javascript` 或 `typescript`。 
+ `python`：針對 Python 專案，請改為完成[在 Azure 中建立 HTTP 觸發的函式](../articles/azure-functions/functions-create-first-function-python.md)。
+ `powershell`：針對 PowerShell 專案，請改為完成[在 Azure 中建立您的第一個 PowerShell 函式](../articles/azure-functions/functions-create-first-function-powershell.md)。 


專案建立後，請使用下列命令瀏覽至新的 `MyFunctionProj` 專案資料夾。

```command
cd MyFunctionProj
```
