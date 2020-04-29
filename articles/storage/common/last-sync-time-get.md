---
title: 檢查儲存體帳戶的上次同步處理時間屬性
titleSuffix: Azure Storage
description: 瞭解如何檢查異地複寫儲存體帳戶的 [**上次同步時間**] 屬性。 [**上次同步時間**] 屬性會指出上次從主要區域寫入到次要區域的時間。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77165484"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>檢查儲存體帳戶的上次同步處理時間屬性

當您設定儲存體帳戶時，您可以指定將資料複製到與主要區域相距數百英里的次要地區。 異地複寫可在主要區域發生重大中斷（例如自然災害）時，為您的資料提供持久性。 如果您另外啟用次要區域的讀取權限，當主要區域變成無法使用時，您的資料仍可供讀取作業使用。 如果主要區域沒有回應，您可以將應用程式設計成從次要區域順暢地切換，以進行讀取。

異地多餘儲存體（GRS）和異地區域冗余儲存體（切換）（預覽）都會以非同步方式將您的資料複寫至次要區域。 如需次要區域的讀取權限，請啟用讀取權限異地多餘儲存體（RA-GRS）或讀取權限異地區域-多餘儲存體（RA-切換）。 如需 Azure 儲存體所提供的各種冗余選項的詳細資訊，請參閱[Azure 儲存體冗余](storage-redundancy.md)。

本文說明如何檢查儲存體帳戶的 [**上次同步時間**] 屬性，讓您可以評估主要和次要區域之間的任何差異。

## <a name="about-the-last-sync-time-property"></a>關於上次同步時間屬性

由於異地複寫是非同步，因此在發生中斷時，寫入主要區域的資料可能尚未寫入次要區域。 [**上次同步時間**] 屬性指出從主要區域成功寫入次要區域的最後一次資料。 在上次同步處理時間之前對主要區域所進行的所有寫入都可以從次要位置讀取。 在 [上次同步時間] 屬性之後寫入主要區域的作業，可能會或可能無法供讀取之用。

[**上次同步時間**] 屬性是 GMT 日期/時間值。

## <a name="get-the-last-sync-time-property"></a>取得上次同步時間屬性

您可以使用 PowerShell 或 Azure CLI 來取出 [**上次同步時間**] 屬性的值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 取得儲存體帳戶的上次同步處理時間，請安裝支援取得異地複寫統計資料的 Azure 儲存體預覽模組。例如：

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

然後檢查儲存體帳戶的**GeoReplicationStats. LastSyncTime**屬性。 請記得使用您自己的值來取代預留位置值：

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要取得具有 Azure CLI 之儲存體帳戶的上次同步處理時間，請檢查儲存體帳戶的**geoReplicationStats. lastSyncTime**屬性。 使用`--expand`參數來傳回在**geoReplicationStats**底下所嵌套屬性的值。 請記得使用您自己的值來取代預留位置值：

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>請參閱

- [Azure 儲存體的冗余](storage-redundancy.md)
- [變更儲存體帳戶的冗余選項](redundancy-migration.md)
- [使用讀取權限異地多餘儲存體設計高可用性應用程式](storage-designing-ha-apps-with-ragrs.md)