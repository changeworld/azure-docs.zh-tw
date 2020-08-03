---
title: 檢查儲存體帳戶的 [上次同步時間] 屬性
titleSuffix: Azure Storage
description: 了解如何檢查異地複寫儲存體帳戶的 [上次同步時間] 屬性。 [上次同步時間] 屬性會指出來自主要區域的所有寫入已成功寫入次要區域的最近時間。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00f6085414e5a48647846830f93ba3fb98c382f2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502506"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>檢查儲存體帳戶的 [上次同步時間] 屬性

當您設定儲存體帳戶時，可以指定將資料複製到與主要區域相距數百英里的次要區域。 異地複寫可在主要區域發生重大中斷 (例如自然災害) 時，為您的資料提供持久性。 如果您另外啟用次要區域的讀取權限，當主要區域變成無法使用時，您的資料仍可供讀取作業使用。 如果主要區域沒有回應，您可以將應用程式設計成從次要區域順暢地切換，以進行讀取。

異地備援儲存體 (GRS) 和異地區域備援儲存體 (GZRS) 都會以非同步方式將您的資料複寫至次要區域。 如需次要區域的讀取存取，請啟用讀取存取異地備援儲存體 (RA-GRS) 或讀取存取異地區域備援儲存體 (RA-GZRS)。 如需 Azure 儲存體所提供的各種備援選項詳細資訊，請參閱 [Azure 儲存體備援](storage-redundancy.md)。

本文說明如何檢查儲存體帳戶的 [上次同步時間] 屬性，讓您可以評估主要與次要區域之間的任何差異。

## <a name="about-the-last-sync-time-property"></a>檢查 [上次同步時間] 屬性

由於異地複寫為非同步，因此在發生中斷時，寫入主要區域的資料可能尚未寫入次要區域。 [上次同步時間] 屬性會指出來自主要區域的資料已成功寫入次要區域的最近時間。 在上次同步時間之前對主要區域進行的所有寫入都可以從次要位置讀取。 在 [上次同步時間] 屬性之後完成的主要區域寫入可能已可讀取，也可能無法讀取。

[上次同步時間] 屬性是 GMT 日期/時間值。

## <a name="get-the-last-sync-time-property"></a>取得 [上次同步時間] 屬性

您可以使用 PowerShell 或 Azure CLI 來擷取 [上次同步時間] 屬性的值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 取得儲存體帳戶的上次同步處理時間，請安裝1.11.0 或更新版本的[Az. storage](https://www.powershellgallery.com/packages/Az.Storage)模組。 然後檢查儲存體帳戶的 **GeoReplicationStats.LastSyncTime** 屬性。 請記得以您自己的值取代預留位置值：

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要取得具有 Azure CLI 之儲存體帳戶的上次同步時間，請檢查儲存體帳戶的 **geoReplicationStats.lastSyncTime** 屬性。 使用 `--expand` 參數來傳回 **geoReplicationStats** 下巢狀的屬性值。 請記得以您自己的值取代預留位置值：

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>另請參閱

- [Azure 儲存體複寫](storage-redundancy.md)
- [變更儲存體帳戶的備援選項](redundancy-migration.md)
- [使用異地備援來設計高可用性的應用程式](geo-redundant-design.md)
