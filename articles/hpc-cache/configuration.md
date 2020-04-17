---
title: 設定 Azure HPC 快取設定
description: 說明如何為緩存配置其他設置(如 MTU 和非根壁切,以及如何從 Azure Blob 儲存目標存取快速快照)。
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538813"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>設定其他 Azure HPC 快取設定

Azure 門戶中的 **「配置」** 頁具有用於自訂多個設置的選項。 大多數使用者不需要從預設值更改這些值。

本文還介紹如何使用快照功能實現 Azure Blob 存儲目標。 快照功能沒有可配置設置。

要查看這些設置,請參閱 Azure 門戶中的緩存 **「配置」** 頁。

![Azure 門戶中設定頁的螢幕截圖](media/configuration.png)

## <a name="adjust-mtu-value"></a>調整 MTU 值
<!-- linked from troubleshoot-nas article -->

您可以使用標記為**MTU 大小的**下拉功能表為快取選擇最大傳輸單元大小。

默認值為 1500 位元組,但您可以將其更改為 1400 位元組。

> [!NOTE]
> 如果降低快取的 MTU 大小,請確保與快取通訊的用戶端和儲存系統具有相同的 MTU 設定或較低的值。

降低快取 MTU 值可以説明您解決快取網路其餘部分的資料包大小限制。 例如,某些 VPN 無法成功傳輸全尺寸 1500 位元組數據包。 減小通過 VPN 發送的數據包的大小可能會消除此問題。 但是,請注意,較低的緩存 MTU 設置意味著與快取通訊的任何其他元件(包括用戶端和儲存系統)也必須具有較低的設置,以避免與緩存的通訊問題。

如果不想更改其他系統元件上的 MTU 設定,則不應降低快取的 MTU 設定。 還有其他解決方案需要解決 VPN 資料包大小限制。 閱讀 NAS 故障排除文章中[的「調整 VPN 資料包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)」,瞭解有關診斷和解決此問題的更多內容。

通過讀取[Azure VM 的 TCP/IP 性能調優](../virtual-network/virtual-network-tcpip-performance-tuning.md),瞭解有關 Azure 虛擬網路中 MTU 設置的更多內容。

## <a name="configure-root-squash"></a>設定根壁
<!-- linked from troubleshoot -->

**啟用根壁擠壓**設置控制 Azure HPC 緩存允許根訪問的方式。 根壁球有助於防止根級訪問來自未經授權的用戶端。

此設置允許使用者在緩存級別控制根訪問,這有助於補償用作存儲目標的 NAS``no_root_squash``系統所需的設置。 (閱讀有關[NFS 存儲目標先決條件的更多](hpc-cache-prereqs.md#nfs-storage-requirements)內容。當與 Azure Blob 儲存目標一起使用時,它還可以提高安全性。

默認設置為 **"是**"。 (2020 年 4 月之前建立的快取可能具有預設設定 **"否**")啟用此功能後,此功能還防止在用戶端請求快取中使用 set-UID 許可權位。

## <a name="view-snapshots-for-blob-storage-targets"></a>檢視 Blob 儲存目標的快照

Azure HPC 快取會自動為 Azure Blob 儲存目標儲存儲存快照。 快照為後端存儲容器的內容提供了一個快速參考點。 快照不能替代數據備份,並且不包含有關緩存數據狀態的任何資訊。

> [!NOTE]
> 此快照功能不同於 NetApp、Isilon 或 ZFS 存儲軟體中包含的快照功能。 這些快照實現在拍攝快照之前將更改從緩存刷新到後端存儲系統。
>
> 為提高效率,Azure HPC Cache 快照不會先刷新更改,而只記錄已寫入 Blob 容器的數據。 此快照不表示緩存數據的狀態,因此可能會排除最近的更改。

此功能僅適用於 Azure Blob 儲存目標,並且無法更改其配置。

快照每八小時拍攝一次,在 UTC 0:00、08:00 和 16:00。

Azure HPC 快取存儲每日、每周和每月快照,直到它們被新快照替換。 這些限制是：

* 每日最多 20 張快照
* 每週最多 8 次快照
* 最多 3 個每月快照

從 Blob`.snapshot`儲存目標 命名空間中的目錄中存取快照。
