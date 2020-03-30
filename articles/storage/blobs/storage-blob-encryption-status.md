---
title: 檢查 Blob 的加密狀態 - Azure 存儲
description: 瞭解如何使用 Azure 門戶、PowerShell 或 Azure CLI 檢查給定 Blob 是否已加密。 如果 Blob 未加密，請瞭解如何使用 AzCopy 通過下載和重新上載 Blob 來強制加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707587"
---
# <a name="check-the-encryption-status-of-a-blob"></a>檢查 Blob 的加密狀態

2017 年 10 月 20 日之後寫入 Azure 存儲的每個塊 Blob、追加 Blob 或頁面 Blob 都使用 Azure 存儲加密進行加密。 在此日期之前創建的 Blob 將繼續由後臺進程加密。

本文演示如何確定給定 Blob 是否已加密。

## <a name="check-a-blobs-encryption-status"></a>檢查 Blob 的加密狀態

使用 Azure 門戶、PowerShell 或 Azure CLI 確定 Blob 是否在沒有代碼的情況下進行加密。

### <a name="azure-portal"></a>[Azure 門戶](#tab/portal)

要使用 Azure 門戶檢查 Blob 是否已加密，請按照以下步驟操作：

1. 在 Azure 入口網站中，瀏覽至您的儲存體帳戶。
1. 選擇 **"容器"** 以導航到帳戶中的容器清單。
1. 找到 Blob 並顯示其 **"概述"** 選項卡。
1. 查看**伺服器加密**屬性。 如果**為 True，** 如下圖所示，則 blob 將被加密。 請注意，Blob 的屬性還包括創建 Blob 的日期和時間。

    ![顯示如何在 Azure 門戶中檢查伺服器加密屬性的螢幕截圖](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[電源外殼](#tab/powershell)

要使用 PowerShell 檢查 Blob 是否已加密，請檢查 Blob 的**IsServer 加密**屬性。 請記得以您自己的值取代角括號中的預留位置值：

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

要確定創建 Blob 時，請檢查 **"已創建"** 屬性的值：

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

要使用 Azure CLI 檢查 Blob 是否已加密，請檢查 Blob 的**IsServer 加密**屬性。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

要確定創建 Blob 時，請檢查**創建**屬性的值。

---

## <a name="force-encryption-of-a-blob"></a>強制加密 Blob

如果 2017 年 10 月 20 日之前創建的 Blob 尚未由後臺進程加密，則可以通過下載並重新上載 Blob 強制立即進行加密。 一個簡單的方法是使用 AzCopy。

要使用 AzCopy 將 blob 下載到本地檔案系統，請使用以下語法：

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

要使用 AzCopy 將 Blob 重新上載到 Azure 存儲，請使用以下語法：

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

有關使用 AzCopy 複製 Blob 資料的詳細資訊，請參閱[使用 AzCopy 和 Blob 存儲傳輸資料](../common/storage-use-azcopy-blobs.md)。

## <a name="next-steps"></a>後續步驟

[靜態資料的 Azure 存儲加密](../common/storage-service-encryption.md)
