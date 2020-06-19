---
title: 適用於開發人員的 API 和工具
description: 了解可搭配 Azure Batch 服務用來開發解決方案的 API 和工具。
ms.topic: conceptual
ms.date: 05/22/2010
ms.custom: seodec18
ms.openlocfilehash: cd87419cd8c389be861cb88071884f8da1c3c1a3
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870587"
---
# <a name="overview-of-batch-apis-and-tools"></a>Batch API 和工具的概觀

使用 Azure Batch 處理平行工作負載通常會透過其中一個 Batch API，以程式設計方式進行。 用戶端應用程式或服務可使用 Batch API 來與 Batch 服務進行通訊。 Batch API 可讓您建立和管理計算節點 (虛擬機器或雲端服務) 集區。 您可以接著排程要在這些節點上執行的作業及工作。

您可以為組織有效率地處理大量工作負載，或提供前端服務給客戶，讓他們可以在一個、數百個或甚至數千個節點上，依需要或依排程執行作業和工作。 您也可以在 [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json)之類的工具所管理的大型工作流程中使用 Azure Batch。

> [!TIP]
> 若要深入了解 Azure Batch 中使用的功能和工作流程，請參閱 [Batch 服務工作流程和資源](batch-service-workflow-features.md)。

## <a name="azure-accounts-for-batch-development"></a>用於 Batch 開發的 Azure 帳戶

當您開發 Batch 解決方案時，您將在 Azure 訂用帳戶中使用下列帳戶：

- **Batch 帳戶** - Azure Batch 資源 (包括集區、計算節點、作業和工作) 都與 Azure [Batch 帳戶](accounts.md)相關聯。 當您的應用程式對 Batch 服務提出要求時，可以使用 Azure Batch 帳戶名稱、帳戶的 URL 及存取金鑰或 Azure Active Directory 權杖，來驗證此要求。 您可以在 Azure 入口網站中或以程式設計方式[建立 Batch 帳戶](batch-account-create-portal.md)。
- **儲存體帳戶**：Batch 包含內建支援，可處理 [Azure 儲存體](../storage/index.yml)中的檔案。 幾乎每個 Batch 案例都會使用 Azure Blob 儲存體，來預備工作執行的程式及其處理的資料，以及儲存其產生的輸出資料。 每個 Batch 帳戶通常都會與對應的儲存體帳戶相關聯。

## <a name="service-level-and-management-level-apis"></a>服務等級和管理等級 API

Azure Batch 有兩組 API，一個用於服務等級，另一個用於管理等級。 其命名通常相似，但會傳回不同的結果。

只有來自管理 API 的動作會在活動記錄中進行追蹤。 服務等級 API 會略過 Azure 資源管理層 (management.azure.com) 且不會記錄。

例如，[刪除集區的 Batch 服務 API](https://docs.microsoft.com/rest/api/batchservice/pool/delete) \(部分機器翻譯\) 會直接以批次帳戶為目標：`DELETE {batchUrl}/pools/{poolId}`

而[刪除集區的 Batch 管理 API](https://docs.microsoft.com/rest/api/batchmanagement/pool/delete) \(部分機器翻譯\) 會以 management.azure.com 層為目標：`DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>Batch 服務 API

應用程式和服務可以發出直接的 REST API 呼叫，或使用一或多個下列用戶端程式庫來執行和管理 Azure Batch 工作負載。

| API | API 參考資料 | 下載 | 教學課程 | 程式碼範例 | 其他資訊 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com](https://docs.microsoft.com/rest/api/batchservice/) |N/A |- |- | [支援的版本](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[教學課程](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [版本資訊](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com](https://docs.microsoft.com/python/api/overview/azure/batch/client?view=azure-python) |[PyPI](https://pypi.org/project/azure-batch/) |[教學課程](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [讀我檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[docs.microsoft.com](https://docs.microsoft.com/javascript/api/overview/azure/batch/client?view=azure-node-latest) |[npm](https://www.npmjs.com/package/azure-batch) |[教學課程](batch-nodejs-get-started.md) |- | [讀我檔案](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com](https://docs.microsoft.com/java/api/overview/azure/batch?view=azure-java-stable) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [讀我檔案](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch 管理 API

適用於 Batch 的 Azure Resource Manager API 會提供 Batch 帳戶的程式設計存取方式。 使用這些 API，您可以透過 Microsoft.Batch 提供者，以程式設計方式管理 Batch 帳戶、配額、應用程式套件及其他資雲。  

| API | API 參考資料 | 下載 | 教學課程 | 程式碼範例 |
| --- | --- | --- | --- | --- |
| **Batch 管理 REST** |[docs.microsoft.com](https://docs.microsoft.com/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com](https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [教學課程](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Batch 管理 Python** |[docs.microsoft.com](https://docs.microsoft.com/python/api/overview/azure/batch/management?view=azure-python) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Batch 管理 Node.js** |[docs.microsoft.com](https://docs.microsoft.com/javascript/api/overview/azure/batch/management?view=azure-node-latest) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Batch 管理 Java** |[docs.microsoft.com](https://docs.microsoft.com/java/api/overview/azure/batch/management?view=azure-java-stable) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Batch 命令列工具

這些命令列工具可提供與 Batch 服務和 Batch 管理 API 相同的功能︰ 

- [Batch PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.batch/)：[Azure PowerShell](/powershell/azure/overview) 模組中的 Azure Batch Cmdlet 可讓您使用 PowerShell 來管理 Batch 資源。
- [Azure CLI](/cli/azure)：Azure CLI 是一個跨平台工具組，可提供用來與許多 Azure 服務 (包括 Batch 服務和 Batch Management 服務) 互動的殼層命令。 如需搭配使用 Azure CLI 與 Batch 的詳細資訊，請參閱[使用 Azure CLI 管理 Batch 資源](batch-cli-get-started.md)。

## <a name="other-tools-for-application-development"></a>其他用於應用程式開發的工具

這些其他工具可能有助於建置及偵錯您的 Batch 應用程式與服務。

- [Azure 入口網站](https://portal.azure.com/)︰您可以在 Azure 入口網站建立、監視和刪除 Batch 集區、作業和工作。 您可以在執行作業時檢視上述和其他資源的狀態資訊，甚至從您集區中的計算節點下載檔案。 例如，您可以在進行疑難排解時下載失敗的工作 `stderr.txt`。 您也可以下載可用來登入計算節點的遠端桌面 (RDP) 檔案。
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/)：Batch Explorer (先前稱為 BatchLabs) 是一個免費且功能豐富的獨立用戶端工具，可協助您建立、偵錯及監視 Azure Batch 應用程式。 下載適用於 Mac、Linux 或 Windows 的[安裝套件](https://azure.github.io/BatchExplorer/)。
- [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard)：Batch Shipyard 這項工具可協助您在 Azure Batch 上佈建、執行和監視以容器為基礎的批次處理與 HPC 工作負載。
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)：嚴格來說，雖然儲存體總管不算是 Azure Batch 工具，但卻是您開發和偵錯 Batch 解決方案時另一個很實用的工具。

## <a name="additional-resources"></a>其他資源

- 若要了解如何記錄來自 Batch 應用程式的事件，請參閱[用於診斷評估和監視的 Batch 計量、警示和記錄](batch-diagnostics.md)。
- 如需 Batch 服務所引發之事件的參考資訊，請參閱 [Batch 分析](batch-analytics.md)。
- 如需計算節點環境變數的相關資訊，請參閱 [Azure Batch 執行階段環境變數](batch-compute-node-environment-variables.md)。

## <a name="next-steps"></a>後續步驟

- 了解 [Batch 服務工作流程和主要資源](batch-service-workflow-features.md)，例如集區、節點、作業和工作。
- [開始使用適用於 .NET 的 Azure Batch 程式庫](tutorial-parallel-dotnet.md) ，了解如何使用 C# 和 Batch .NET 程式庫，透過一般的批次工作流程來執行簡單的工作負載。 也會提供 [Python 版本](tutorial-parallel-python.md)和 [Node.js 教學課程](batch-nodejs-get-started.md)。
- 下載 [GitHub 上的程式碼範例](https://github.com/Azure-Samples/azure-batch-samples)，查看 C# 和 Python 如何與 Batch 相互作用，以排程和處理範例工作負載。
