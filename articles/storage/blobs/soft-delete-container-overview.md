---
title: '適用于容器的虛刪除 (預覽) '
titleSuffix: Azure Storage
description: " (預覽版的容器虛刪除) 可保護您的資料，讓您可以在應用程式或其他儲存體帳戶使用者錯誤地修改或刪除資料時，更輕鬆地復原資料。"
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 3c6aa408ab3e89c367fca0701773b35fb9b41bb8
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460793"
---
# <a name="soft-delete-for-containers-preview"></a>適用于容器的虛刪除 (預覽) 

 (預覽版的容器虛刪除) 可保護您的資料免于不慎或錯誤地修改或刪除。 針對儲存體帳戶啟用容器虛刪除時，任何已刪除的容器及其內容都會保留在 Azure 儲存體中指定的期間內。 在保留期限內，您可以還原先前刪除的容器和其中的任何 blob。

針對 blob 資料的端對端保護，Microsoft 建議啟用下列資料保護功能：

- 容器虛刪除，以防止意外刪除或覆寫容器。 若要瞭解如何啟用容器虛刪除，請參閱 [啟用及管理容器的虛刪除](soft-delete-container-enable.md)。
- Blob 虛刪除，以防止意外刪除或覆寫個別的 blob。 若要瞭解如何啟用 blob 虛刪除，請參閱 [適用于 blob 的虛刪除](soft-delete-blob-overview.md)。
- Blob 版本設定，以自動維護舊版的 blob。 啟用 blob 版本設定時，您可以還原舊版的 blob 來復原您的資料（如果錯誤遭到修改或刪除）。 若要瞭解如何啟用 blob 版本設定，請參閱 [啟用和管理 blob 版本](versioning-enable.md)設定。

> [!WARNING]
> 無法復原刪除儲存體帳戶。 虛刪除無法防止刪除儲存體帳戶。 若要防止意外刪除儲存體帳戶，請在儲存體帳戶資源上設定 **CannotDelete** 鎖定。 如需有關鎖定 Azure 資源的詳細資訊，請參閱 [鎖定資源以防止非預期的變更](../../azure-resource-manager/management/lock-resources.md)。

## <a name="how-container-soft-delete-works"></a>容器虛刪除的運作方式

當您啟用容器虛刪除時，可以指定介於1到365天的已刪除容器的保留期限。 預設保留期間為7天。 在保留期限內，您可以藉由呼叫取消刪除 **容器** 作業來復原已刪除的容器。

當您還原容器時，如果該名稱尚未重複使用，您可以將它還原為其原始名稱。 如果已使用原始容器名稱，則您可以使用新名稱來還原容器。

保留期限到期後，容器會從 Azure 儲存體中永久刪除，而且無法復原。 時鐘會在容器刪除的時間點開始保留期限。 您可以隨時變更保留期限，但請記住，更新的保留期限僅適用于新刪除的容器。 先前刪除的容器將會根據容器刪除時生效的保留期限永久刪除。

停用容器虛刪除並不會永久刪除先前已虛刪除的容器。 任何虛刪除的容器將會在容器刪除時生效的保留期限到期時永久刪除。

## <a name="about-the-preview"></a>關於預覽

容器虛刪除在所有公用 Azure 區域中都可供預覽。

> [!IMPORTANT]
> 容器虛刪除預覽版僅供非生產環境使用。 生產環境的服務等級協定 (SLA) 目前無法使用。

版本2019-12-12 和更新版本的 Azure 儲存體 REST API 支援容器虛刪除。

### <a name="storage-account-support"></a>儲存體帳戶支援

容器虛刪除適用于下列類型的儲存體帳戶：

- 一般用途 v2 儲存體帳戶
- 區塊 blob 儲存體帳戶
- Blob 儲存體帳戶

如果您的儲存體帳戶是一般用途 v1 帳戶，請使用 Azure 入口網站升級至一般用途 v2 帳戶。 如需儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶總覽](../common/storage-account-overview.md)。

也支援啟用階層命名空間的儲存體帳戶，以搭配 Azure Data Lake Storage Gen2 使用。

### <a name="register-for-the-preview"></a>註冊預覽

若要註冊容器虛刪除的預覽，請使用 PowerShell 或 Azure CLI 提交要求，以向您的訂用帳戶註冊此功能。 核准您的要求之後，您可以使用任何新的或現有的一般用途 v2、Blob 儲存體或 premium 區塊 Blob 儲存體帳戶來啟用容器虛刪除。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 註冊，請呼叫 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 命令。

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要向 Azure CLI 註冊，請呼叫 [az feature register](/cli/azure/feature#az-feature-register) 命令。

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>檢查您的註冊狀態

若要檢查您的註冊狀態，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 檢查註冊的狀態，請呼叫 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要檢查 Azure CLI 的註冊狀態，請呼叫 [az feature](/cli/azure/feature#az-feature-show) 命令。

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>價格和計費

啟用容器虛刪除沒有額外的費用。 虛刪除容器中的資料會以與活動資料相同的費率計費。

## <a name="next-steps"></a>後續步驟

- [設定容器虛刪除](soft-delete-container-enable.md)
- [Blob 的虛刪除](soft-delete-blob-overview.md)
- [Blob 版本設定](versioning-overview.md)
