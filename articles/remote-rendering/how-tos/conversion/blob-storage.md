---
title: 使用 Azure Blob 儲存的模型轉換
description: 描述設定和使用 blob 儲存進行模型轉換的常見步驟。
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681645"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>使用 Azure Blob 儲存的模型轉換

[模型轉換](model-conversion.md)服務需要存取 Azure Blob 儲存,以便它可以檢索輸入數據和儲存輸出數據。 本文介紹如何執行最常見的步驟。

## <a name="prepare-azure-storage-accounts"></a>準備 Azure 儲存帳戶

- 建立儲存帳戶 (儲存 V2)
- 在儲存帳戶中建立輸入 blob 容器(例如,名為"arrinput")
- 在儲存帳戶中建立輸出 Blob 容器(例如,名為"arr輸出")

> [!TIP]
> 有關如何設置儲存帳戶的分步說明,請查看[快速入門:轉換用於渲染的模型](../../quickstarts/convert-model.md)

儲存帳號與 blob 容器的建立可以使用以下工具之一完成:

- [Azure 入口網站](https://portal.azure.com)
- [az 指令列](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
- SDK(C#,Python ...

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>確保 Azure 遠端呈現可以存取儲存帳戶

Azure 遠端 Rending 需要從存儲帳戶檢索模型數據,並將數據寫回存儲帳戶。

您可以透過以下兩種方式授予 Azure 遠端呈現對儲存帳戶的存取權限:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>將 Azure 儲存帳戶與 Azure 遠端呈現帳戶連線

按照"[創建帳戶](../create-an-account.md#link-storage-accounts)"部分中給出的步驟操作。

### <a name="retrieve-sas-for-the-storage-containers"></a>取索儲存容器的 SAS

儲存的訪問簽名 (SAS) 用於授予輸入的讀取存取許可權和輸出的寫入存取許可權。 我們建議在每次轉換模型時生成新的 URI。 由於URI會在一段時間後過期,因此將其保留更長時間可能會意外中斷應用程式。

有關 SAS 的詳細資訊,請參閱[SAS 文件](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

可以使用:

- az PowerShell 模組
  - 請參考[範例 PowerShell 文稿](../../samples/powershell-example-scripts.md)
- [az 指令列](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)
  - 右鍵按下容器「取得分享存取簽名」(讀取、清單存取輸入容器,寫入輸出容器的存取)
- SDK(C#,Python ...

在資產轉換中使用共享存取簽名的範例顯示在[Powershell 示例文本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1 中。

## <a name="upload-an-input-model"></a>上傳輸入模型

要開始轉換模型,您需要使用以下選項之一上載模型:

- [Azure 儲存資源管理員](https://azure.microsoft.com/features/storage-explorer/)-在 Azure Blob 儲存上上傳/下載/管理檔案的便捷 UI
- [Azure 指令列](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - 請參考[範例電源外殼文稿](../../samples/powershell-example-scripts.md)
- [使用儲存 SDK(Python、C# ...](https://docs.microsoft.com/azure/storage/)
- [使用 Azure 儲存 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

有關如何上傳數據進行轉換的範例,請參閱[Powershell 示例文本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1。

## <a name="get-a-sas-uri-for-the-converted-model"></a>取得轉換後的 SAS URI

此步驟類似於[檢索存儲容器的 SAS。](#retrieve-sas-for-the-storage-containers) 但是,這一次您需要檢索已寫入輸出容器的模型檔的 SAS URI。

例如,要通過[Azure 儲存資源管理器](https://azure.microsoft.com/features/storage-explorer/)檢索 SAS URI,請右鍵單擊模型檔並選擇"獲取共用訪問簽名」。

如果尚未將儲存帳戶連接到 Azure 遠端呈現帳戶,則需要共用訪問簽名 (SAS) 來載入模型。 您可以在[「創建帳戶](../create-an-account.md#link-storage-accounts)」中瞭解如何連接您的帳戶。

## <a name="next-steps"></a>後續步驟

- [設定模型轉換](configure-model-conversion.md)
- [模型轉換 REST API](conversion-rest-api.md)
