---
title: " (預覽) 的容器虛刪除"
titleSuffix: Azure Storage
description: 適用于容器的虛刪除 (預覽) 可保護您的資料，以便在應用程式或其他儲存體帳戶使用者錯誤地修改或刪除您的資料時，更容易進行復原。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 15e0312fab4558b9aeea1748c6ff70cf77973fb5
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057155"
---
# <a name="soft-delete-for-containers-preview"></a> (預覽) 的容器虛刪除

 (preview 的容器虛刪除) 保護您的資料不小心或錯誤地修改或刪除。 針對儲存體帳戶啟用容器虛刪除時，任何已刪除的容器和其內容都會保留在您指定之期間的 Azure 儲存體中。 在保留期限內，您可以還原先前刪除的容器和其中的任何 blob。

對於 blob 資料的端對端保護，Microsoft 建議啟用下列資料保護功能：

- 容器虛刪除，以防止意外刪除或覆寫容器。 若要瞭解如何啟用容器虛刪除，請參閱[啟用和管理容器的虛刪除](soft-delete-container-enable.md)。
- Blob 虛刪除，以防止意外刪除或覆寫個別 blob。 若要瞭解如何啟用 blob 虛刪除，請參閱[適用于 blob 的虛刪除](soft-delete-blob-overview.md)。
- Blob 版本設定 (預覽) ，以自動維護舊版的 blob。 啟用 blob 版本設定時，您可以還原較舊版本的 blob，以在錯誤地修改或刪除資料時加以復原。 若要瞭解如何啟用 blob 版本設定，請參閱[啟用和管理 blob 版本](versioning-enable.md)設定。

> [!IMPORTANT]
> 若要防止意外刪除儲存體帳戶，請在儲存體帳戶資源上設定**CannotDelete**鎖定。 如需有關鎖定 Azure 資源的詳細資訊，請參閱[鎖定資源以防止非預期的變更](../../azure-resource-manager/management/lock-resources.md)。

## <a name="how-container-soft-delete-works"></a>容器虛刪除如何運作

當您啟用容器虛刪除時，可以為已刪除的容器指定介於1到365天之間的保留期限。 預設的保留期限為7天。 在保留期間，您可以藉由呼叫取消刪除**容器**作業來復原已刪除的容器。

當您還原容器時，如果該名稱尚未重複使用，您可以將它還原成其原始名稱。 如果已使用原始容器名稱，則您可以使用新的名稱來還原容器。

保留期限到期後，容器會從 Azure 儲存體中永久刪除，而且無法復原。 時鐘會在容器刪除的時間點開始。 您可以隨時變更保留期限，但請記住，已更新的保留期限僅適用于新刪除的容器。 先前已刪除的容器將會根據容器刪除時生效的保留期限而永久刪除。

停用容器虛刪除並不會導致永久刪除先前已虛刪除的容器。 任何已虛刪除的容器將會在容器刪除時生效的保留期限到期時永久刪除。

## <a name="about-the-preview"></a>關於預覽

容器虛刪除在下欄區域提供預覽：

- 法國中部
- 加拿大東部
- 加拿大中部

> [!IMPORTANT]
> 容器虛刪除預覽僅適用于非生產環境的使用。 生產環境的服務等級協定 (SLA) 目前無法使用。

2019-12-12 版和更新版本的 Azure 儲存體 REST API 支援容器虛刪除。

### <a name="storage-account-support"></a>儲存體帳戶支援

容器虛刪除適用于下列類型的儲存體帳戶：

- 一般用途 v2 儲存體帳戶
- 區塊 blob 儲存體帳戶
- Blob 儲存體帳戶

如果您的儲存體帳戶是一般用途 v1 帳戶，請使用 Azure 入口網站升級至一般用途 v2 帳戶。 如需儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶總覽](../common/storage-account-overview.md)。

也支援已啟用階層命名空間以與 Azure Data Lake Storage Gen2 搭配使用的儲存體帳戶。

### <a name="register-for-the-preview"></a>註冊預覽

若要註冊容器虛刪除的預覽，請使用 PowerShell 或 Azure CLI 來提交要求，以向您的訂用帳戶註冊此功能。 核准您的要求之後，您可以使用任何新的或現有的一般用途 v2、Blob 儲存體或高階區塊 Blob 儲存體帳戶來啟用容器虛刪除。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 註冊，請呼叫[AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature)命令。

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要向 Azure CLI 註冊，請呼叫[az feature register](/cli/azure/feature#az-feature-register)命令。

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>檢查註冊的狀態

若要檢查註冊的狀態，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 檢查註冊的狀態，請呼叫[AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 檢查註冊的狀態，請呼叫[az feature](/cli/azure/feature#az-feature-show)命令。

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>價格和計費

啟用容器虛刪除不需額外付費。 虛刪除容器中的資料會依照使用中資料的相同費率計費。

## <a name="next-steps"></a>後續步驟

- [設定容器虛刪除](soft-delete-container-enable.md)
- [Blob 的虛刪除](soft-delete-blob-overview.md)
- [Blob 版本設定 (預覽)](versioning-overview.md)
