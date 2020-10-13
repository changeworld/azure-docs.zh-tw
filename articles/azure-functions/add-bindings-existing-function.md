---
title: 在 Azure Functions 中將系結新增至現有的函式
description: 瞭解如何將系結新增至 Azure Functions 專案中的現有函式。
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 23b7b05f9a9f9da5a48511ee555e3b6184a74179
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654105"
---
# <a name="add-bindings-to-an-existing-function-in-azure-functions"></a>在 Azure Functions 中將系結新增至現有的函式

當您建立函式時，會從一組觸發程式範本將特定語言的觸發程式程式碼加入至您的專案。 如果您想要使用輸入或輸出系結將函數連接至其他服務，則必須在函式中新增特定的系結定義。 若要深入了解繫結，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

## <a name="local-development"></a>本機開發       

當您在本機開發函式時，您需要更新函式程式碼以新增系結。 使用 Visual Studio Code 可讓您更輕鬆地將系結新增至函式。  

### <a name="visual-studio-code"></a>Visual Studio Code

當您使用 Visual Studio Code 開發函式，而且您的函式在檔案上使用 function.js時，Azure Functions 擴充功能可以自動將系結新增至檔案上的現有 function.js。 若要深入瞭解，請參閱 [加入輸入和輸出](functions-develop-vs-code.md#add-input-and-output-bindings)系結。   

### <a name="manually-add-bindings-based-on-examples"></a>根據範例手動新增系結

將系結新增至現有的函式時，您需要更新函式程式碼和設定檔上的 function.js（如果您的語言使用的話）。 .NET 類別庫和 JAVA 函數都會使用屬性，而不是 function.json，所以您必須改為更新。

使用下表來尋找特定系結類型的範例，您可以使用這些類型來引導您更新現有的函式。 首先，選擇對應至您專案的 [語言] 索引標籤。 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure 入口網站

當您在 [Azure 入口網站](https://portal.azure.com)中開發函式時，您會在指定函式的 [ **整合** ] 索引標籤中新增輸入和輸出系結。 新的系結會新增至檔案 function.js或方法屬性（視您的語言而定）。 下列文章說明如何在入口網站中將系結新增至現有函式的範例：

+ [佇列儲存體輸出繫結](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB 輸出系結](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>後續步驟

+ [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)
