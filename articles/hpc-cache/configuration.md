---
title: 設定 Azure HPC 快取設定
description: 說明如何設定快取的其他設定，例如 MTU 和無根目錄 squash，以及如何從 Azure Blob 儲存體目標存取快速快照集。
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/06/2020
ms.author: v-erkel
ms.openlocfilehash: 88aea7e58aacd9a630771948c6dbc6ed5712a674
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85505302"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>設定其他 Azure HPC 快取設定

[Azure 入口網站中的 [設定] 頁面具有自訂數個**設定的選項**。 大部分的使用者都不需要從預設值變更這些設定。

本文也說明如何使用 Azure Blob 儲存體目標的快照集功能。 快照集功能沒有可設定的設定。

若要查看設定，請在 [Azure 入口網站中開啟快取的 [設定 **] 頁面。**

![Azure 入口網站中設定頁面的螢幕擷取畫面](media/configuration.png)

> [!TIP]
> [管理 AZURE HPC](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)快取影片會顯示 [設定] 頁面及其設定。

## <a name="adjust-mtu-value"></a>調整 MTU 值
<!-- linked from troubleshoot-nas article -->

您可以使用標示為 [ **MTU 大小**] 的下拉式功能表，來選取快取的傳輸單位大小上限。

預設值是1500個位元組，但是您可以將它變更為1400。

> [!NOTE]
> 如果您降低快取的 MTU 大小，請確定與快取通訊的用戶端和儲存系統具有相同的 MTU 設定或較低的值。

降低快取 MTU 值可協助您解決其餘快取網路的封包大小限制。 例如，某些 Vpn 無法成功傳輸完整大小的1500位元組封包。 減少透過 VPN 傳送的封包大小可能會消除該問題。 不過，請注意，較低的快取 MTU 設定表示與快取通訊的任何其他元件（包括用戶端和儲存系統）也必須有較低的 MTU 設定，以避免通訊問題。

如果您不想變更其他系統元件上的 MTU 設定，則不應該降低快取的 MTU 設定。 還有其他解決 VPN 封包大小限制的解決方案。 如需深入瞭解診斷和解決此問題的詳細資訊，請參閱在 NAS 疑難排解文章中[調整 VPN 封包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)。

若要深入瞭解 Azure 虛擬網路中的 MTU 設定，請閱讀[Azure vm 的 tcp/ip 效能微調](../virtual-network/virtual-network-tcpip-performance-tuning.md)。

## <a name="configure-root-squash"></a>設定根 squash
<!-- linked from troubleshoot -->

[**啟用根 squash** ] 設定可控制 Azure HPC 快取如何在用戶端電腦上處理來自 root 使用者的要求。

啟用根 squash 時，來自用戶端的根使用者會在透過 Azure HPC 快取傳送要求時，自動對應至使用者「無人」。 它也會防止用戶端要求使用設定 UID 許可權位。

如果已停用根 squash，用戶端根使用者（UID 0）的要求會傳遞至後端 NFS 儲存體系統作為根。 此設定可能會允許不適當的檔案存取。

在快取上設定 root squash 有助於補償 ``no_root_squash`` 做為儲存體目標的 NAS 系統上所需的設定。 （深入瞭解[NFS 儲存體目標必要條件](hpc-cache-prereqs.md#nfs-storage-requirements)）。當搭配 Azure Blob 儲存體目標使用時，它也可以改善安全性。

預設值為 [是]。 （2020年4月之前建立的快取可能預設設定為 [**否**]）。

## <a name="view-snapshots-for-blob-storage-targets"></a>查看 blob 儲存體目標的快照集

Azure HPC Cache 會自動儲存 Azure Blob 儲存體目標的儲存體快照集。 快照集會提供後端儲存體容器內容的快速參考點。

快照集不是資料備份的取代，而且它們不包含任何有關快取資料狀態的資訊。

> [!NOTE]
> 此快照集功能與 NetApp 或 Isilon 儲存體軟體中包含的快照集功能不同。 這些快照集執行會在製作快照集之前，將快取中的變更排清到後端儲存體系統。
>
> 為了提高效率，Azure HPC 快取快照集不會先排清變更，而且只會記錄已寫入 Blob 容器的資料。 此快照集不代表快取資料的狀態，因此可能不會包含最近的變更。

這項功能僅適用于 Azure Blob 儲存體目標，無法變更其設定。

每隔8小時會取得快照集，UTC 0:00、08:00 和16:00。

Azure HPC 快取會儲存每日、每週和每月快照集，直到它們被新的快照集取代為止。 這些限制是：

* 最多20個每日快照集
* 最多8個每週快照集
* 最多3個月快照

從 `.snapshot` blob 儲存體目標命名空間中的目錄存取快照集。
