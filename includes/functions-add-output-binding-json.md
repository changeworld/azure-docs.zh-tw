---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9b3859d854b3900cc30eda6a95b8425da6763e59
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340943"
---
繫結屬性是直接定義在 function.json 檔案中。 根據繫結類型，可能需要額外的屬性。 [佇列輸出組態](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration)會說明 Azure 儲存體佇列繫結所需的欄位。 擴充可讓您輕鬆地將繫結加入至 function.json 檔案。 

若要建立繫結，請以滑鼠右鍵按一下 (在 macOS 上則為 Ctrl+按一下) HttpTrigger 資料夾中的 `function.json` 檔案，然後選擇 [新增繫結]  。遵循提示來針對新繫結定義下列繫結屬性：

| Prompt | 值 | 描述 |
| -------- | ----- | ----------- |
| **選取繫結方向** | `out` | 此繫結為輸出繫結。 |
| **選取具有下列方向的繫結** | `Azure Queue Storage` | 此繫結是 Azure 儲存體佇列繫結。 |
| **用以在程式碼中識別此繫結的名稱** | `msg` | 識別您的程式碼中參考之繫結參數的名稱。 |
| **要接收訊息的佇列** | `outqueue` | 作為繫結寫入目標的佇列名稱。 當 *queueName* 不存在，繫結會在第一次使用時加以建立。 |
| **"local.setting.json" 選取設定** | `AzureWebJobsStorage` | 包含儲存體帳戶連接字串之應用程式設定的名稱。 `AzureWebJobsStorage` 設定會包含您以函式應用程式建立之儲存體帳戶的連接字串。 |

繫結會新增至 function.json 中的 `bindings` 陣列，此時應該會顯示如下：

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::
