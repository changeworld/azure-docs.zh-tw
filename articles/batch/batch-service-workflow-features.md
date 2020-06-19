---
title: Batch 服務工作流程和資源
description: 從開發觀點了解 Batch 服務的功能及其高階工作流程。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 5084ae222d0a9da0d8aa171bc89dba48377a9dd4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835915"
---
# <a name="batch-service-workflow-and-resources"></a>Batch 服務工作流程和資源

在 Azure Batch 服務的核心元件概觀中，我們會討論 Batch 開發人員可用來建置大規模平行計算解決方案的高階工作流程，以及所使用的主要服務資源。

不論您正在開發可發出直接 [REST API](https://docs.microsoft.com/rest/api/batchservice/) 呼叫的分散式計算應用程式或服務，還是正在使用另外一個 [Batch SDK](batch-apis-tools.md#batch-service-apis)，都會使用此處所討論的眾多資源和功能。

> [!TIP]
> 如需更高層級的 Batch 服務簡介，請參閱 [Azure Batch 的基本概念](batch-technical-overview.md)。 另請參閱最新的 [Batch 服務更新](https://azure.microsoft.com/updates/?product=batch)。

## <a name="basic-workflow"></a>基本工作流程

下列高階工作流程是幾乎所有使用 Batch 服務處理平行工作負載的應用程式和服務典型︰

1. 將您要處理的**資料檔案**上傳至 [Azure 儲存體](../storage/index.yml)帳戶。 Batch 包含可供存取 Azure Blob 儲存體的內建支援，而在執行工作時，您的工作可以將這些檔案下載至 [計算節點](nodes-and-pools.md#nodes) 。
2. 上傳您的工作將要執行的 **應用程式檔案** 。 這些檔案可以是二進位檔或指令碼及其相依項目，並由您作業中的工作執行。 您的工作可以從儲存體帳戶下載這些檔案，或者您可以使用 Batch 的 [應用程式套件](nodes-and-pools.md#application-packages) 功能來管理和部署應用程式。
3. 建立計算節點的 [集區](nodes-and-pools.md#pools) 。 當您建立集區時，會指定集區的計算節點數目、其大小和作業系統。 當您作業中的每個工作執行時，會指派其在您集區的其中一個節點上執行。
4. 建立 [作業](jobs-and-tasks.md#jobs)。 作業可管理一群工作。 您可以將每項作業關聯至將執行該作業之工作的特定集區。
5. 將 [工作](jobs-and-tasks.md#tasks) 加入至作業。 每個工作會執行您上傳的應用程式或指令碼，以處理它從您的儲存體帳戶下載的資料檔案。 當每個工作完成時，即可將其輸出上傳至 Azure 儲存體。
6. 監視作業進度並從 Azure 儲存體擷取工作輸出。

> [!NOTE]
> 您需要有 [Batch 帳戶](accounts.md)才能使用 Batch 服務。 大部分 Batch 解決方案也會使用相關聯的 [Azure 儲存體](../storage/index.yml)帳戶來儲存及擷取檔案。

## <a name="batch-service-resources"></a>Batch 服務資源

下列主題討論可達成分散式計算案例的 Batch 資源。

- [Batch 帳戶和儲存體帳戶](accounts.md)
- [節點和集區](nodes-and-pools.md)
- [作業和工作](jobs-and-tasks.md)
- [檔案和目錄](files-and-directories.md)

## <a name="next-steps"></a>後續步驟

- 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。
- 了解使用 [Batch .NET 用戶端程式庫](quick-run-dotnet.md)或 [Python](quick-run-python.md) 開發啟用 Batch 之應用程式的基本概念。 這些快速入門會介紹使用 Batch 服務在多個計算節點上執行工作負載的範例應用程式，並說明如何使用 Azure 儲存體進行工作負載檔案的預備和擷取。
- 下載並安裝 [Batch Explorer](https://azure.github.io/BatchExplorer/)，以在開發 Batch 解決方案時使用。 使用 Batch Explorer 有助於建立、偵錯和監視 Azure Batch 應用程式。
- 請參閱社群資源，包括 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch)、[Batch 社群存放庫](https://github.com/Azure/Batch)以及 [Azure Batch 論壇](https://docs.microsoft.com/answers/topics/azure-batch.html)。
