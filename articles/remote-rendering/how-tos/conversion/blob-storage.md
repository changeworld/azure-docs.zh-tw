---
title: 使用 Azure Blob 儲存體進行模型轉換
description: 描述設定和使用 blob 儲存體進行模型轉換的一般步驟。
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80681645"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>使用 Azure Blob 儲存體進行模型轉換

[模型轉換](model-conversion.md)服務需要存取 Azure blob 儲存體，才能取得輸入資料並儲存輸出資料。 本文說明如何執行最常見的步驟。

## <a name="prepare-azure-storage-accounts"></a>準備 Azure 儲存體帳戶

- 建立儲存體帳戶 (StorageV2) 
- 在儲存體帳戶中建立輸入 blob 容器 (例如，名為 "arrinput" ) 
- 在儲存體帳戶中建立輸出 blob 容器 (例如，名為 "arroutput" ) 

> [!TIP]
> 如需如何設定儲存體帳戶的逐步指示，請參閱[快速入門：轉換模型以進行](../../quickstarts/convert-model.md)轉譯

您可以使用下列其中一種工具來建立儲存體帳戶和 blob 容器：

- [Azure 入口網站](https://portal.azure.com)
- [az 命令列](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
- Sdk (c #、Python ... ) 

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>確定 Azure 遠端轉譯可以存取您的儲存體帳戶

Azure 遠端轉譯需要從您的儲存體帳戶取出模型資料，並將資料寫回給它。

您可以透過下列兩種方式，授與儲存體帳戶 Azure 遠端轉譯存取權：

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>將您的 Azure 儲存體帳戶與您的 Azure 遠端轉譯帳戶連線

遵循 [建立帳戶](../create-an-account.md#link-storage-accounts) 一節中提供的步驟。

### <a name="retrieve-sas-for-the-storage-containers"></a>取得儲存體容器的 SAS

儲存的存取簽章 (SAS) 用來授與讀取權限給輸入，以及寫入輸出的存取權。 每次轉換模型時，建議您產生新的 Uri。 因為 Uri 會在一段時間後到期，所以保存它們以持續較長的時間，可能會造成意外中斷應用程式。

Sas [檔](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)中可找到 sas 的詳細資料。

您可以使用下列其中一種來產生 SAS URI：

- az PowerShell 模組
  - 請參閱 [範例 PowerShell 腳本](../../samples/powershell-example-scripts.md)
- [az 命令列](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
  - 以滑鼠右鍵按一下 [取得共用存取簽章] 容器 (讀取、列出輸入容器的存取權、輸出容器的寫入存取權) 
- Sdk (c #、Python ... ) 

在「資產」轉換中使用共用存取簽章的範例，會顯示在 [Powershell 範例腳本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1 中。

## <a name="upload-an-input-model"></a>上傳輸入模型

若要開始轉換模型，您必須使用下列其中一個選項來上傳模型：

- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/) -可在 Azure blob 儲存體上上傳/下載/管理檔案的方便 UI
- [Azure 命令列](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - 請參閱 [範例 PowerShell 腳本](../../samples/powershell-example-scripts.md)
- [使用儲存體 SDK (Python、c # ... ) ](https://docs.microsoft.com/azure/storage/)
- [使用 Azure 儲存體 REST Api](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

如需如何上傳資料以進行轉換的範例，請參閱 [Powershell 範例腳本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1。

## <a name="get-a-sas-uri-for-the-converted-model"></a>取得已轉換模型的 SAS URI

此步驟類似于為 [儲存體容器取出 SAS](#retrieve-sas-for-the-storage-containers)。 不過，這次您需要取出模型檔案的 SAS URI，並將其寫入至輸出容器。

例如，若要透過 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)取出 SAS URI，請以滑鼠右鍵按一下模型檔案，然後選取 [取得共用存取簽章]。

如果您尚未將儲存體帳戶連接到您的 Azure 遠端轉譯帳戶，則需要 (SAS) 的共用存取簽章來載入模型。 您可以在「 [建立帳戶](../create-an-account.md#link-storage-accounts)」中瞭解如何連接您的帳戶。

## <a name="next-steps"></a>後續步驟

- [設定模型轉換](configure-model-conversion.md)
- [模型轉換 REST API](conversion-rest-api.md)
