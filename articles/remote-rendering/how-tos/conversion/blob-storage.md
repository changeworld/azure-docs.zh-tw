---
title: 使用 Azure Blob 儲存體進行模型轉換
description: 說明設定和使用 blob 儲存體進行模型轉換的一般步驟。
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681645"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>使用 Azure Blob 儲存體進行模型轉換

[模型轉換](model-conversion.md)服務需要存取 Azure blob 儲存體，才能取得輸入資料並儲存輸出資料。 本文說明如何執行最常見的步驟。

## <a name="prepare-azure-storage-accounts"></a>準備 Azure 儲存體帳戶

- 建立儲存體帳戶（StorageV2）
- 在儲存體帳戶（例如，名為 "arrinput"）中建立輸入 blob 容器
- 在儲存體帳戶（例如，名為 "arroutput"）中建立輸出 blob 容器

> [!TIP]
> 如需如何設定儲存體帳戶的逐步指示，請參閱[快速入門：轉換模型以進行](../../quickstarts/convert-model.md)轉譯

您可以使用下列其中一項工具來建立儲存體帳戶和 blob 容器：

- [Azure 入口網站](https://portal.azure.com)
- [az 命令列](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
- Sdk （c #、Python ...）

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>確保 Azure 遠端呈現可以存取您的儲存體帳戶

Azure 遠端轉譯需要從您的儲存體帳戶中取出模型資料，並將資料寫回其中。

您可以透過下列兩種方式，將 Azure 遠端轉譯存取權授與您的儲存體帳戶：

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>使用您的 Azure 遠端轉譯帳戶來連接您的 Azure 儲存體帳戶

請遵循[建立帳戶](../create-an-account.md#link-storage-accounts)一節中提供的步驟。

### <a name="retrieve-sas-for-the-storage-containers"></a>取得儲存體容器的 SAS

預存存取簽章（SAS）可用來授與讀取存取權，以及輸出的寫入權限。 建議您在每次轉換模型時產生新的 Uri。 因為 Uri 會在一段時間後過期，所以將它們保存較長的持續時間可能會造成意外中斷應用程式的風險。

如需 SAS 的詳細資料，請參閱[sas 檔](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

您可以使用下列其中一種來產生 SAS URI：

- az PowerShell module
  - 請參閱[PowerShell 腳本範例](../../samples/powershell-example-scripts.md)
- [az 命令列](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
  - 以滑鼠右鍵按一下容器「取得共用存取簽章」（讀取、列出輸入容器的存取權、輸出容器的寫入權限）
- Sdk （c #、Python ...）

如需在資產轉換中使用共用存取簽章的範例，請 Conversion.ps1 [Powershell 範例腳本](../../samples/powershell-example-scripts.md#script-conversionps1)中顯示。

## <a name="upload-an-input-model"></a>上傳輸入模型

若要開始轉換模型，您必須使用下列其中一個選項來上傳它：

- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)-在 Azure blob 儲存體上傳/下載/管理檔案的方便 UI
- [Azure 命令列](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - 請參閱[PowerShell 腳本範例](../../samples/powershell-example-scripts.md)
- [使用儲存體 SDK （Python、c # ...）](https://docs.microsoft.com/azure/storage/)
- [使用 Azure 儲存體 REST Api](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

如需如何上傳資料以進行轉換的範例，請參閱[Powershell 範例腳本](../../samples/powershell-example-scripts.md#script-conversionps1)Conversion.ps1。

## <a name="get-a-sas-uri-for-the-converted-model"></a>取得已轉換模型的 SAS URI

此步驟類似于抓取[儲存體容器的 SAS](#retrieve-sas-for-the-storage-containers)。 不過，這次您需要取得模型檔案的 SAS URI，而該檔案已寫入輸出容器。

例如，若要透過[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)取出 SAS URI，請以滑鼠右鍵按一下模型檔案，然後選取 [取得共用存取簽章]。

如果您尚未將您的儲存體帳戶連接到 Azure 遠端轉譯帳戶，則需要共用存取簽章（SAS）來載入模型。 您可以在[建立帳戶](../create-an-account.md#link-storage-accounts)中瞭解如何連接您的帳戶。

## <a name="next-steps"></a>後續步驟

- [設定模型轉換](configure-model-conversion.md)
- [模型轉換 REST API](conversion-rest-api.md)
