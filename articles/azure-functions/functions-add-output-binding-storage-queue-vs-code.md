---
title: 使用 Visual Studio Code 將 Azure Functions 連線至 Azure 儲存體
description: 了解如何將輸出繫結新增至您的 Visual Studio Code 專案，進而將 Azure Functions 連線至 Azure 儲存體佇列。
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e280fddbe83da2a7ee89185046883f6c2c77167a
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739805"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>使用 Visual Studio Code 將 Azure Functions 連線至 Azure 儲存體

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

此文章說明如何使用 Visual Studio Code 將 Azure 儲存體連線至您在上一篇快速入門文章中建立的函式。 您新增至此函式的輸出繫結，會將資料從 HTTP 要求寫入至位於 Azure 佇列儲存體佇列中的訊息。 

大部分的繫結都需要函式用來存取繫結服務的預存連接字串。 為了方便作業，您可以使用您以函式應用程式建立的儲存體帳戶。 此帳戶的連線已儲存在名為 `AzureWebJobsStorage` 的應用程式設定中。  

## <a name="configure-your-local-environment"></a>設定您的本機環境

開始進行此文章內容之前，您必須符合下列需求：

* 安裝[適用於 Visual Studio Code 的 Azure 儲存體擴充](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) \(英文\)。

* 安裝 [Azure 儲存體總管](https://storageexplorer.com/)。 儲存體總管是您將用來檢查由您的輸出繫結所產生之佇列訊息的工具。 儲存體總管支援安裝在以 macOS、Windows 和 Linux 為基礎的作業系統上。

::: zone pivot="programming-language-csharp"
* 安裝 [.NET Core CLI 工具](/dotnet/core/tools/?tabs=netcore2x)。
::: zone-end

::: zone pivot="programming-language-csharp"  
* 完成 [Visual Studio Code 快速入門第 1 部分](create-first-function-vs-code-csharp.md)中的步驟。 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* 完成 [Visual Studio Code 快速入門第 1 部分](create-first-function-vs-code-node.md)中的步驟。 
::: zone-end   
::: zone pivot="programming-language-java"  
* 完成 [Visual Studio Code 快速入門第 1 部分](create-first-function-vs-code-java.md)中的步驟。 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* 完成 [Visual Studio Code 快速入門第 1 部分](create-first-function-vs-code-typescript.md)中的步驟。 
::: zone-end   
::: zone pivot="programming-language-python"  
* 完成 [Visual Studio Code 快速入門第 1 部分](create-first-function-vs-code-python.md)中的步驟。 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* 完成 [Visual Studio Code 快速入門第 1 部分](create-first-function-vs-code-powershell.md)中的步驟。 
::: zone-end   

此文章假設您已從 Visual Studio Code 登入您的 Azure 訂用帳戶。 您可以從命令選擇區執行 `Azure: Sign In` 來登入。 

## <a name="download-the-function-app-settings"></a>下載函式應用程式設定

在[先前的快速入門文章](./create-first-function-vs-code-csharp.md)中，您已在 Azure 中建立函式應用程式與必要的儲存體帳戶。 此帳戶的連接字串會安全地儲存在 Azure 的應用程式設定中。 在本文中，您會將訊息寫入至相同帳戶中的儲存體佇列。 在本機執行函式時若要連線至儲存體帳戶，您必須將應用程式設定下載到 local.settings.json 檔案。 

1. 按 F1 鍵來開啟命令選擇區，然後搜尋並執行 `Azure Functions: Download Remote Settings....` 命令。 

1. 選擇您在先前的文章中所建立的函式應用程式。 選取 [全部皆是] 來覆寫現有的本機設定。 

    > [!IMPORTANT]  
    > 由於 local.settings.json 檔案中包含祕密，因此一律不會被發行，且會從原始檔控制中排除。

1. 複製 `AzureWebJobsStorage` 值，其為儲存體帳戶連接字串值的金鑰。 您將使用此連線來確認輸出繫結會如預期般運作。

## <a name="register-binding-extensions"></a>註冊繫結延伸模組

由於您是使用佇列儲存體輸出繫結，您必須先安裝儲存體繫結擴充才能執行專案。 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

您的專案已設定為使用[擴充功能配套](functions-bindings-register.md#extension-bundles)，這會自動安裝一組預先定義的擴充功能套件。 

系統會在專案根目錄的 host. json 檔案中啟用擴充功能套件組合，如下所示：

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

除了 HTTP 和計時器觸發程序之外，繫結皆會以擴充套件的形式實作。 在終端機視窗中執行下列 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令來將儲存體套件新增至您的專案。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

現在，您可以將儲存體輸出繫結新增至您的專案。

## <a name="add-an-output-binding"></a>新增輸出繫結

在函式中，每一種繫結都需要在 function.json 檔案中定義 `direction`、`type` 和唯一的 `name`。 定義這些屬性的方式會取決於您函式應用程式的語言。

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>新增會使用輸出繫結的程式碼

在定義繫結之後，您即可使用繫結的 `name` 來以函式簽章中屬性的形式存取它。 藉由使用輸出繫結，您無須使用 Azure 儲存體 SDK 程式碼來進行驗證、取得佇列參考或寫入資料。 Functions 執行階段和佇列輸出繫結會為您進行這些工作。

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

## <a name="update-the-test-set"></a>更新測試集

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

您第一次使用輸出繫結時，Functions 執行階段會在儲存體帳戶中建立名為 **outqueue** 的新佇列。 您將會使用儲存體總管來確認佇列已連同新訊息一起被建立。

::: zone pivot="programming-language-java"  

## <a name="update-the-tests"></a>更新測試

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>將儲存體總管連線到您的帳戶

如果您已經安裝 Azure 儲存體總管並將它連線至您的 Azure 帳戶，請略過此節內容。

1. 執行 Azure 儲存體總管工具，選取左側的連線圖示，然後選取 [新增帳戶]。

    ![將 Azure 帳戶新增至 Microsoft Azure 儲存體總管](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. 在 [連線] 對話方塊中，選擇 [新增 Azure 帳戶]，選擇您的 [Azure 環境]，然後選取 [登入]。 

    ![登入您的 Azure 帳戶](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

在您成功登入帳戶之後，您會看見與您的帳戶相關聯的所有 Azure 訂用帳戶。

### <a name="examine-the-output-queue"></a>檢查輸出佇列

1. 在 Visual Studio Code 中，按 F1 鍵來開啟命令選擇區，然後搜尋並執行 `Azure Storage: Open in Storage Explorer` 命令，並選擇您的儲存體帳戶名稱。 您的儲存體帳戶會在 Azure 儲存體總管中開啟。  

1. 展開 [佇列] 節點，然後選取名為 **outqueue** 的佇列。 

   佇列包含訊息，該訊息將您執行 HTTP 觸發程序函式時建立的輸出繫結排入佇列。 如果您已叫用預設 `name` 值為 *Azure* 的函式，則佇列訊息是 *Name passed to the function:Azure*。

    ![Azure 儲存體總管中顯示的佇列訊息](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. 再次執行函式並傳送另一個要求，您將會看到新的訊息出現在佇列中。  

現在，您可以將更新的函式應用程式重新發行至 Azure。

## <a name="redeploy-and-verify-the-updated-app"></a>重新部署並驗證更新的應用程式

1. 在 Visual Studio Code 中按 F1 以開啟命令選擇區。 在命令選擇區中，搜尋並選取 `Azure Functions: Deploy to function app...`。

1. 選擇您在第一篇文章中所建立的函式應用程式。 由於您是要將專案部署至相同的應用程式，請選取 [部署] 來關閉關於覆寫檔案的警告。

1. 部署完成後，您便可以再次使用 cURL 或瀏覽器來測試重新部署的函式。 如同以往，將查詢字串 `&name=<yourname>` 附加至 URL，如下列範例所示：

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. 再次[檢視儲存體佇列中的訊息](#examine-the-output-queue)，以確認輸出繫結會再次在佇列中產生新訊息。

## <a name="clean-up-resources"></a>清除資源

在 Azure 中，「資源」是指函式應用程式、函式、儲存體帳戶等等。 其會分組為「資源群組」，您可以藉由刪除群組來刪除群組中的所有項目。

您已建立資源來完成這些快速入門。 您可能必須支付這些資源，取決於您的[帳戶狀態](https://azure.microsoft.com/account/)和[服務定價](https://azure.microsoft.com/pricing/)。 如果您不再需要資源，刪除方式如下：

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>後續步驟

您已更新 HTTP 觸發的函式，以將資料寫入至儲存體佇列。 現在，您可以深入了解如何使用 Visual Studio Code 來開發 Functions：

+ [使用 Visual Studio Code 來開發 Azure Functions](functions-develop-vs-code.md)

+ [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)。
::: zone pivot="programming-language-csharp"  
+ [C# 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=csharp)。

+ [Azure Functions C# 開發人員參考](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=javascript)。

+ [Azure Functions JavaScript 開發人員指南](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Java 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=java)。

+ [Azure Functions Java 開發人員指南](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=typescript)。

+ [Azure Functions TypeScript 開發人員指南](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=python)。

+ [Azure Functions Python 開發人員指南](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell 中完整函式專案的例](/samples/browse/?products=azure-functions&languages=azurepowershell)。

+ [Azure Functions PowerShell 開發人員指南](functions-reference-powershell.md) 
::: zone-end