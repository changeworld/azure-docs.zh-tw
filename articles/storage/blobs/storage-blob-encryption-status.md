---
title: 檢查 blob 的加密狀態-Azure 儲存體
description: 瞭解如何使用 Azure 入口網站、PowerShell 或 Azure CLI 來檢查指定的 blob 是否已加密。 如果 blob 未加密，請瞭解如何使用 AzCopy 來強制加密，方法是下載並重新上傳 blob。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 6d0392cd8e94ba8a9026f557b90e740fbed7f50c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84809081"
---
# <a name="check-the-encryption-status-of-a-blob"></a>檢查 blob 的加密狀態

2017年10月20日之後，寫入至 Azure 儲存體的每個區塊 blob、附加 blob 或分頁 blob 都會使用 Azure 儲存體加密進行加密。 在此日期之前建立的 blob 將繼續由背景進程加密。

本文說明如何判斷指定的 blob 是否已加密。

## <a name="check-a-blobs-encryption-status"></a>檢查 blob 的加密狀態

使用 Azure 入口網站、PowerShell 或 Azure CLI 來判斷是否要在沒有程式碼的情況下加密 blob。

### <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站檢查 blob 是否已加密，請遵循下列步驟：

1. 在 Azure 入口網站中，瀏覽至您的儲存體帳戶。
1. 選取 **容器** 以流覽至帳戶中的容器清單。
1. 找出 blob，並顯示其 **[總覽** ] 索引標籤。
1. 請參閱 **伺服器加密** 屬性。 如果為 **True**（如下圖所示），則表示 blob 已加密。 請注意，blob 的屬性也包含建立 blob 的日期和時間。

    ![顯示如何在 Azure 入口網站中檢查伺服器加密屬性的螢幕擷取畫面](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 來檢查 blob 是否已加密，請檢查 blob 的 **IsServerEncrypted** 屬性。 請記得以您自己的值取代角括號中的預留位置值：

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

若要判斷 blob 的建立時間，請檢查所 **建立** 屬性的值：

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 檢查 blob 是否已加密，請檢查 blob 的 **IsServerEncrypted** 屬性。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

若要判斷 blob 的建立時間，請檢查所 **建立** 屬性的值。

---

## <a name="force-encryption-of-a-blob"></a>強制加密 blob

如果在2017年10月20日之前建立的 blob 尚未由背景進程加密，您可以透過下載並重新上傳 blob，來強制立即進行加密。 簡單的方法是使用 AzCopy。

若要使用 AzCopy 將 blob 下載至本機檔案系統，請使用下列語法：

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

若要使用 AzCopy 將 blob 重新上傳至 Azure 儲存體，請使用下列語法：

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

如需使用 AzCopy 複製 blob 資料的詳細資訊，請參閱使用 [AzCopy 和 blob 儲存體傳輸資料](../common/storage-use-azcopy-blobs.md)。

## <a name="next-steps"></a>接下來的步驟

[待用資料的 Azure 儲存體加密](../common/storage-service-encryption.md)
