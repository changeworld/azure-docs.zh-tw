---
title: 啟用和管理容器 (預覽) 的虛刪除
titleSuffix: Azure Storage
description: 啟用容器虛刪除 (預覽) ，以在錯誤地修改或刪除您的資料時更容易復原。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f79b198b512547b1d93fbbd458af8251b109e62f
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066656"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>啟用和管理容器 (預覽) 的虛刪除

容器虛刪除 (預覽) 保護您的資料不會被意外或錯誤地修改或刪除。 針對儲存體帳戶啟用容器虛刪除時，容器和其內容可能會在您指定的保留期間內被刪除之後復原。

如果應用程式或其他儲存體帳戶使用者可能不小心修改或刪除您的資料，Microsoft 建議您開啟容器虛刪除。 本文說明如何啟用容器的虛刪除。 如需容器虛刪除的詳細資料，包括如何註冊預覽版，請參閱[ (預覽) 的容器虛刪除](soft-delete-container-overview.md)。

對於端對端資料保護，Microsoft 建議您也為 blob 和 blob 版本設定啟用虛刪除， (預覽) 。 若要瞭解如何同時啟用 blob 的虛刪除，請參閱[啟用和管理 blob 的虛刪除](soft-delete-blob-enable.md)。 若要瞭解如何啟用 blob 版本設定，請參閱[blob 版本設定 (預覽) ](versioning-overview.md)。

## <a name="enable-container-soft-delete"></a>啟用容器虛刪除

您可以使用 Azure 入口網站或 Azure Resource Manager 範本，隨時啟用或停用儲存體帳戶的容器虛刪除。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要使用 Azure 入口網站為您的儲存體帳戶啟用容器虛刪除，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您的儲存體帳戶。
1. 找出 [ **Blob 服務**] 底下的 [**資料保護**] 設定。
1. 將 [**容器虛刪除**] 屬性設定為 [*已啟用*]。
1. 在 [**保留原則**] 底下，指定 Azure 儲存體保留已刪除之容器的時間長度。
1. 儲存您的變更。

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="螢幕擷取畫面，顯示如何在 Azure 入口網站中啟用容器虛刪除":::

# <a name="template"></a>[範本](#tab/template)

若要使用 Azure Resource Manager 範本啟用容器虛刪除，請建立範本來設定 **containerDeleteRetentionPolicy**屬性。 下列步驟說明如何在 Azure 入口網站中建立範本。

1. 在 [Azure 入口網站中，選擇 [ **建立資源**]。
1. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後按 **enter**。
1. 選擇 [ **範本部署**]，選擇 [ **建立**]，然後 **在編輯器中選擇 [建立您自己的範本**]。
1. 在 [範本編輯器] 中，貼上下列 JSON。 將  `<account-name>`   預留位置取代為您的儲存體帳戶名稱。

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
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. 指定保留週期。 預設值為 7。
1. 儲存範本。
1. 指定帳戶的資源群組，然後選擇 [ **審查 + 建立**]   按鈕以部署範本並啟用容器虛刪除。

## <a name="view-soft-deleted-containers"></a>觀看已虛刪除的容器

啟用虛刪除時，您可以在 Azure 入口網站中查看已虛刪除的容器。 在指定的保留期間內，會顯示虛刪除的容器。 保留期限到期後，已虛刪除的容器會永久刪除，而且不會再顯示。

若要在 Azure 入口網站中查看已虛刪除的容器，請遵循下列步驟：

1. 在 [Azure 入口網站] 中流覽至您的儲存體帳戶，並查看您的容器清單。
1. 切換 [顯示已刪除的容器] 切換，以在清單中包含已刪除的容器。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="螢幕擷取畫面，顯示如何在 Azure 入口網站中查看已虛刪除的容器":::

## <a name="restore-a-soft-deleted-container"></a>還原虛刪除的容器

您可以在保留期限內還原虛刪除的容器和其內容。 若要在 Azure 入口網站中還原已虛刪除的容器，請遵循下列步驟：

1. 在 [Azure 入口網站] 中流覽至您的儲存體帳戶，並查看您的容器清單。
1. 顯示您想要刪除之容器的內容功能表，然後從功能表中選擇 [取消**刪除**]。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="顯示如何在 Azure 入口網站中還原虛刪除容器的螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

- [ (預覽) 的容器虛刪除](soft-delete-container-overview.md)
- [Blob 的虛刪除](soft-delete-blob-overview.md)
- [Blob 版本設定 (預覽)](versioning-overview.md)