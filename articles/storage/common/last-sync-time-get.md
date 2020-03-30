---
title: 檢查存儲帳戶的"上次同步時間"屬性
titleSuffix: Azure Storage
description: 瞭解如何檢查異地複製存儲帳戶的 **"上次同步時間"** 屬性。 "**上次同步時間"** 屬性指示從主區域成功寫入次要區域的所有寫入的最後一次。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165484"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>檢查存儲帳戶的"上次同步時間"屬性

配置存儲帳戶時，可以指定將資料複製到距主區域數百英里的次要區域。 在主區域發生重大中斷（如自然災害）時，異地複製可為數據提供持久性。 如果另外啟用對次要區域的讀取存取，則如果主區域不可用，您的資料仍可用於讀取操作。 如果主區域無回應，則可以設計應用程式以無縫切換到從次要區域讀取。

異地冗余存儲 （GRS） 和地理區域冗余存儲 （GZRS） （預覽）都非同步地將資料複製到次要區域。 對於對次要區域的讀取存取，啟用讀取存取異地冗余存儲 （RA-GRS） 或讀取存取地理區域冗余存儲 （RA-GZRS）。 有關 Azure 存儲提供的冗余的各種選項的詳細資訊，請參閱 Azure[存儲冗余](storage-redundancy.md)。

本文介紹如何檢查存儲帳戶**的"上次同步時間"** 屬性，以便可以評估主區域和次要區域之間的任何差異。

## <a name="about-the-last-sync-time-property"></a>關於上次同步時間屬性

由於異地複製是非同步，因此在發生中斷時，可能尚未將寫入主區域的資料寫入次要區域。 "**上次同步時間"** 屬性指示上次成功將主區域的資料寫入次要區域。 在最後一個同步時間之前對主區域進行的所有寫入都可以從輔助位置讀取。 上次同步時間之後對主區域進行的寫入屬性可能還不能讀取，也可能不可用。

**"上次同步時間"** 屬性是 GMT 日期/時間值。

## <a name="get-the-last-sync-time-property"></a>獲取上次同步時間屬性

可以使用 PowerShell 或 Azure CLI 檢索 **"上次同步時間"** 屬性的值。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

要獲取使用 PowerShell 的存儲帳戶的最後一次同步時間，請安裝支援獲取異地複製統計資訊的 Azure 存儲預覽模組。例如：

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

然後檢查存儲帳戶的**地理複製統計.LastSyncTime屬性**。 請記住將預留位置值替換為您自己的值：

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要獲取使用 Azure CLI 的存儲帳戶的最後一次同步時間，請檢查存儲帳戶的**地理複製Stats.lastSyncTime**屬性。 使用`--expand`參數返回嵌套在**地理複製統計**下的屬性的值。 請記住將預留位置值替換為您自己的值：

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

- [Azure 存儲冗余](storage-redundancy.md)
- [更改存儲帳戶的冗余選項](redundancy-migration.md)
- [使用讀取存取異地冗余存儲設計高可用應用程式](storage-designing-ha-apps-with-ragrs.md)