---
title: 使用 Visual Studio Code 在 Azure 中建立第一個函式
description: 使用 Visual Studio Code 中的 Azure Functions 擴充功能建立簡單 HTTP 觸發函式並發佈至 Azure。
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842109"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>快速入門：在 Visual Studio Code 中建立 Azure Functions 專案

在本文中，您會使用 Visual Studio Code 建立可回應 HTTP 要求的函式。 在本機測試程式碼之後，您可以將其部署到 Azure Functions 的無伺服器環境。 完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。 

本文還有 [CLI 型版本](functions-create-first-azure-function-azure-cli.md)。

## <a name="prerequisites"></a>Prerequisites

+ 其中一個[支援的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上有 [Visual Studio Code](https://code.visualstudio.com/)。 
::: zone pivot="programming-language-csharp"
+ 適用於 Visual Studio Code 的 [ 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/)、作用中 LTS 和維修 LTS 版本 (建議使用 8.11.1 和 10.14.1)。
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) 或 [Python 3.6](https://www.python.org/downloads/release/python-368/)(如Azure Functions 支援)。 尚不支援 Python 3.8。 

+ 適用於 Visual Studio Code 的 [Python 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)

+ [適用於 Visual Studio Code 的 PowerShell 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)。 
::: zone-end

+ 適用於 Visual Studio Code 的 [Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 

+ 具有有效訂用帳戶的 [Azure 帳戶](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-an-azure-functions-project"></a>建立本機專案 

在這一節中，您會使用 Visual Studio Code 在您所選的語言中建立本機 Azure Functions 專案。 稍後在本文中，您會將函式程式碼發佈至 Azure。 

1. 選擇活動列中的 Azure 圖示，然後在 [Azure：  函式] 區域，選取 [建立新專案...]  圖示。

    ![選擇建立新專案](media/functions-create-first-function-vs-code/create-new-project.png)

1. 選擇您專案工作區的目錄位置，然後選擇 [選取]  。

    > [!NOTE]
    > 這些步驟主要設計為在工作區以外的地方完成。 在此案例中，請勿選取屬於工作區的專案資料夾。

1. 依照提示提供下列資訊：

    ::: zone pivot="programming-language-csharp"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 為您的函式專案選取語言 | C# |
    | 選取版本 | Azure Functions v2 | 
    | 選取您專案第一個函式的範本 | HTTP 觸發程序 | 
    | 提供函式名稱 | HttpExample | 
    | 提供命名空間 | My.Functions | 
    | 授權層級 | 匿名 | 
    | 選取您要如何開啟專案 | 新增到工作區 |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 為您的函式專案選取語言 | JavaScript | 
    | 選取版本 | Azure Functions v2 | 
    | 選取您專案第一個函式的範本 | HTTP 觸發程序 | 
    | 提供函式名稱 | HttpExample | 
    | 授權層級 | 匿名 | 
    | 選取您要如何開啟專案 | 新增到工作區 |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 為您的函式專案選取語言 | TypeScript | 
    | 選取版本 | Azure Functions v2 | 
    | 選取您專案第一個函式的範本 | HTTP 觸發程序 | 
    | 提供函式名稱 | HttpExample | 
    | 授權層級 | 匿名 | 
    | 選取您要如何開啟專案 | 新增到工作區 |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 為您的函式專案選取語言 | PowerShell | 
    | 選取版本 | Azure Functions v2 | 
    | 選取您專案第一個函式的範本 | HTTP 觸發程序 | 
    | 提供函式名稱 | HttpExample | 
    | 授權層級 | 匿名 | 
    | 選取您要如何開啟專案 | 新增到工作區 |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 為您的函式專案選取語言 | Python | 
    | 選取版本 | Azure Functions v2 | 
    | 選取 Python 別名以建立虛擬環境 | Python 別名 | 
    | 選取您專案第一個函式的範本 | HTTP 觸發程序 | 
    | 提供函式名稱 | HttpExample | 
    | 授權層級 | 匿名 | 
    | 選取您要如何開啟專案 | 新增到工作區 | 

    ::: zone-end

1. Visual Studio Code 會執行以下動作：

    + 在新的工作區中建立 Azure Functions 專案，其包含 [host.json](functions-host-json.md) 和 [local.settings.json](functions-run-local.md#local-settings-file) 組態檔。 

    ::: zone pivot="programming-language-csharp"

    + 建立可執行函式的 [HttpExample.cs 類別庫檔案](functions-dotnet-class-library.md#functions-class-library-project)。

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + 在根資料夾中建立 package.json 檔案。

    + 建立 HttpExample 資料夾，其中包含 [function.json 定義檔](functions-reference-node.md#folder-structure)和 [index.js 檔案](functions-reference-node.md#exporting-a-function) (這是包含函式程式碼的 Node.js 檔案)。
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + 在根資料夾中建立 package.json 檔案和 tsconfig.json 檔案。

    + 建立 HttpExample 資料夾，其中包含 [function.json 定義檔](functions-reference-node.md#folder-structure)和 [index.js 檔案](functions-reference-node.md#typescript) (這是包含函式程式碼的 TypeScript 檔案)。
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + 建立 HttpExample 資料夾，其中包含 [function.json 定義檔](functions-reference-python.md#programming-model)和 run.ps1 檔案 (其包含函式程式碼)。
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + 建立專案層級 requirements.txt 檔案，該檔案會列出 Functions 所需的套件。
    
    + 建立 HttpExample 資料夾，其中包含 [function.json 定義檔](functions-reference-python.md#programming-model)和 \_\_init\_\_.py 檔案 (其包含函式程式碼)。
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

確認函式在本機電腦上正確執行之後，就可以使用 Visual Studio Code 將專案直接發佈到 Azure。 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>在 Azure 中執行函式

1. 從 [輸出]  面板中複製 HTTP 觸發程序的 URL。 同樣地，將 `name` 查詢字串當作 `?name=<yourname>` 新增至此 URL 的結尾並執行要求。

    呼叫 HTTP URL 觸發函式的 URL 應採用下列格式：

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. 將 HTTP 要求的新 URL 貼到瀏覽器的網址列。 下列範例顯示瀏覽器中對於函式所傳回遠端 GET 要求所做出的回應︰ 

    ![瀏覽器中的函式回應](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>清除資源

當您繼續進行下一個步驟[將 Azure 儲存體佇列繫結新增至您的函式](functions-add-output-binding-storage-queue-vs-code.md)時，您必須保留所有資源，以您所完成的工作為基礎。

否則，您可以使用下列步驟來刪除函式應用程式及其相關資源，以避免產生任何額外的成本。

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

若要深入了解 Functions 成本，請參閱[估計使用量方案成本](functions-consumption-costs.md)。

## <a name="next-steps"></a>後續步驟

您已透過 Visual Studio Code，使用簡單的 HTTP 觸發函式建立函式應用程式。 在下一篇文章中，您可以藉由新增輸出繫結來展開該函式。 這個繫結會從 HTTP 要求將字串寫入 Azure 佇列儲存體佇列中的訊息。 

> [!div class="nextstepaction"]
> [將 Azure 儲存體佇列繫結新增至您的函式](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
