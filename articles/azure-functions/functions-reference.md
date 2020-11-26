---
title: 開發 Azure Functions 的指引
description: 了解在 Azure 中開發函式所需的 Azure Functions 概念與技術，其中包含所有的程式設計語言和繫結。
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 54bfd770fba9a1766396d66c0c263111c233c9c2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96167874"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions 開發人員指南
在 Azure Functions 中，不論您使用何種語言或繫結，特定函式都會共用一些核心技術概念和元件。 閱讀指定語言或繫結特有的詳細資料之前，請務必詳閱這份適用於所有語言或繫結的概觀。

本文假設您已閱讀過 [Azure Functions 開發人員概觀](functions-overview.md)。

## <a name="function-code"></a>函式程式碼
「函數」  是 Azure Functions 中的主要概念。 函式包含兩個重要的部分，一個部分是可用各種語言撰寫的程式碼，另一則是一些組態，亦即 function.json 檔案。 對於已編譯的語言，系統會自動從您程式碼中的註解產生此組態檔。 對於指令碼語言，您必須自行提供組態檔。

function.json 檔案會定義函式的觸發程序、繫結和其他組態設定。 每個函式會有一個且只能有一個觸發程序。 執行階段使用此組態檔來判斷要監視的事件，以及如何傳入資料並從函式執行傳回資料。 以下是範例 function.json 檔案。

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

如需詳細資訊，請參閱 [Azure Functions 觸發程式和](functions-triggers-bindings.md)系結概念。

`bindings` 屬性可讓您設定觸發程序和繫結。 每個繫結都共用一些共用設定，以及特定類別的繫結特有的一些設定。 每個繫結都需要下列設定︰

| 屬性 | 值/類型 | 註解 |
| --- | --- | --- |
| `type` |字串 |繫結類型。 例如 `queueTrigger`。 |
| `direction` |'in'、'out' |表示繫結用於將資料接收到函數，還是從函數傳送資料。 |
| `name` |字串 |用於函式中所繫結資料的名稱。 在 C# 中，這是引數名稱；在 JavaScript 中，這是索引鍵/值清單中的索引鍵。 |

## <a name="function-app"></a>函式應用程式
函數應用程式在 Azure 中提供您的函式可在其中執行的執行內容。 因此，它是您的函式部署和管理的單位。 函式應用程式是由一或多個個別函式所組成，這些函式可一起管理、部署與調整。 函數應用程式中的所有函式都會共用相同的定價方案、部署方法和執行階段版本。 請將函式應用程式視為用來組織及集體管理函式的方式。 若要深入瞭解，請參閱 [如何管理函數應用程式](functions-how-to-use-azure-function-app-settings.md)。 

> [!NOTE]
> 函式應用程式中的所有函式都必須以相同的語言撰寫。 在[舊版](functions-versions.md)的 Azure Functions 執行階段中，這不是必要的。

## <a name="folder-structure"></a>資料夾結構
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

以上是 Function 應用程式的預設 (且建議的) 資料夾結構。 如果您想要變更函式程式碼的檔案位置，請修改 _function.json_ 檔案的 `scriptFile` 區段。 我們也建議您使用[套件部署](deployment-zip-push.md)來將您的專案部署到 Azure 中的函數應用程式中。 您也可以使用諸如[持續整合與部署](functions-continuous-deployment.md)和 Azure DevOps 之類的現有工具。

> [!NOTE]
> 如果手動部署封裝，請務必將您的 _host.json_ 檔案和函式資料夾直接部署至 `wwwroot` 資料夾。 您的部署中請勿包含 `wwwroot` 資料夾。 否則，您會得到 `wwwroot\wwwroot` 資料夾。

#### <a name="use-local-tools-and-publishing"></a>使用本機工具與發佈
您可以使用各種工具來製作及發佈函數應用程式，包括 [Visual Studio](./functions-develop-vs.md)、[Visual Studio Code](./create-first-function-vs-code-csharp.md)、[IntelliJ](./functions-create-maven-intellij.md)、[Eclipse](./functions-create-maven-eclipse.md) 與 [Azure Functions Core Tools](./functions-develop-local.md)。 如需詳細資訊，請參閱[如何在本機撰寫 Azure Functions 程式碼並進行測試](./functions-develop-local.md)。

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> 如何在 Azure 入口網站中編輯函式
Azure 入口網站內建的函式編輯器可讓您更新您直接內嵌的程式碼和 *function.json* 檔案。 這僅建議用於概念的小型變更或證明 - 最佳作用是使用如 VS Code 等本機開發工具。

## <a name="parallel-execution"></a>平行執行
發生速度比單一執行緒函數執行階段快的多個觸發事件可以處理它們時，執行階段可能會平行多次叫用函數。  如果函式應用程式使用[取用主控方案](functions-scale.md#how-the-consumption-and-premium-plans-work)，則函式應用程式可以自動相應放大。  不論應用程式是在取用主控方案還是一般 [App Service 主控方案](../app-service/overview-hosting-plans.md)上執行，函式應用程式的每個執行個體可能都會使用多個執行緒平行處理並行函式引動。  每個函式應用程式執行個體中的並行函式叫用次數上限，根據使用的觸發程序類型及函式應用程式內其他函式所使用的資源而有所不同。

## <a name="functions-runtime-versioning"></a>Functions 執行階段版本設定

您可以使用 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定來設定 Functions 執行階段的版本。 例如，值 "~ 3" 表示您的函數應用程式會使用3.x 作為其主要版本。 函數應用程式會在發行時升級為每個新的次要版本。 如需詳細資訊 (包括如何檢視函式應用程式的完全版本)，請參閱[如何設定 Azure Functions 的執行階段目標版本](set-runtime-version.md)。

## <a name="repositories"></a>儲存機制
Azure Functions 的程式碼是開放原始碼，儲存於 GitHub 儲存機制中︰

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions 主機](https://github.com/Azure/azure-functions-host/)
* [Azure Functions 入口網站](https://github.com/azure/azure-functions-ux)
* [Azure Functions 範本](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 延伸模組](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>繫結
以下是所有已支援繫結的表格。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

遇到繫結發生錯誤的問題嗎？ 請檢閱 [Azure Functions 繫結錯誤碼](functions-bindings-error-pages.md)文件。

## <a name="reporting-issues"></a>報告問題
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱下列資源：

* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* [撰寫 Azure Functions 並在本機進行測試](./functions-develop-local.md)
* [Azure Functions 的最佳作法](functions-best-practices.md)
* [Azure Functions C# 開發人員參考](functions-dotnet-class-library.md)
* [Azure Functions Node.js 開發人員參考](functions-reference-node.md)