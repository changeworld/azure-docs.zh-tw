---
title: 啟用和管理 Blob 版本設定
titleSuffix: Azure Storage
description: 瞭解如何在 Azure 入口網站中或使用 Azure Resource Manager 範本啟用 blob 版本設定。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5802070bf9b495c0e866d160d6661349369a444e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95993734"
---
# <a name="enable-and-manage-blob-versioning"></a>啟用和管理 Blob 版本設定

您可以啟用 Blob 儲存體版本設定，以自動維護舊版的物件。  啟用 blob 版本設定時，您可以還原舊版的 blob 來復原您的資料（如果錯誤遭到修改或刪除）。

本文說明如何使用 Azure 入口網站或 Azure Resource Manager 範本，啟用或停用儲存體帳戶的 blob 版本設定。 若要深入瞭解 blob 版本設定，請參閱 [blob 版本](versioning-overview.md)設定。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>啟用 Blob 版本設定

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要在 Azure 入口網站中啟用 blob 版本設定：

1. 在入口網站中流覽至您的儲存體帳戶。
1. 在 [ **Blob 服務**] 底下，選擇 [ **資料保護**]。
1. 在 [ **版本控制** ] 區段中，選取 [ **已啟用**]。

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="顯示如何在 Azure 入口網站中啟用 blob 版本設定的螢幕擷取畫面":::

# <a name="template"></a>[範本](#tab/template)

若要使用範本啟用 blob 版本設定，請建立一個範本，並將 **IsVersioningEnabled** 屬性設定為 **true**。 下列步驟說明如何在 Azure 入口網站中建立範本。

1. 在 [Azure 入口網站中，選擇 [ **建立資源**]。
1. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後按 **enter**。
1. 選擇 [ **範本部署**]，選擇 [ **建立**]，然後 **在編輯器中選擇 [建立您自己的範本**]。
1. 在範本編輯器中，貼上下列 JSON。 使用您的儲存體帳戶名稱取代 `<accountName>` 預留位置。
1. 儲存範本。
1. 指定帳戶的資源群組，然後選擇 [ **購買** ] 按鈕以部署範本，並啟用 blob 版本設定。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

如需使用 Azure 入口網站中的範本部署資源的詳細資訊，請參閱 [使用 Azure 入口網站部署資源](../../azure-resource-manager/templates/deploy-portal.md)。

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>修改 blob 以觸發新的版本

下列程式碼範例示範如何使用適用于 .NET 的 Azure 儲存體用戶端程式庫（ [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 版或更新版本）來觸發新版本的建立。 執行此範例之前，請確定您已啟用儲存體帳戶的版本設定。

此範例會建立區塊 blob，然後更新 blob 的中繼資料。 更新 blob 的中繼資料會觸發新版本的建立。 此範例會抓取初始版本和目前的版本，並顯示只有目前的版本包含中繼資料。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>列出 blob 版本

若要使用 .NET v12 用戶端程式庫列出 blob 版本或快照集，請使用 [**版本**] 欄位指定 [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates)參數。

下列程式碼範例示範如何使用適用于 .NET 的 Azure 儲存體用戶端程式庫（ [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 版或更新版本）來列出 blob 版本。 執行此範例之前，請確定您已啟用儲存體帳戶的版本設定。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>後續步驟

- [Blob 版本設定](versioning-overview.md)
- [Azure 儲存體 Blob 的虛刪除](./soft-delete-blob-overview.md)