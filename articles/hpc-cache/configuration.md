---
title: 設定 Azure HPC Cache 設定
description: 說明如何設定快取的其他設定，例如 MTU 和無根目錄 squash，以及如何從 Azure Blob 儲存體目標存取快速快照集。
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/06/2020
ms.author: v-erkel
ms.openlocfilehash: b01c4d896d5ec600e0fe22e3ca7b7816141776a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86497194"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>設定其他 Azure HPC Cache 設定

Azure 入口網站中的 [設定] 頁面有自訂數個 **設定的選項** 。 大部分的使用者都不需要從其預設值變更這些設定。

本文也會說明如何使用 Azure Blob 儲存體目標的快照集功能。 快照集功能沒有可設定的設定。

若要查看設定，請在 Azure 入口網站中開啟快取的 [設定 **] 頁面。**

![Azure 入口網站中設定頁面的螢幕擷取畫面](media/configuration.png)

> [!TIP]
> [ [管理 Azure HPC Cache] 影片](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) 會顯示 [設定] 頁面及其設定。

## <a name="adjust-mtu-value"></a>調整 MTU 值
<!-- linked from troubleshoot-nas article -->

您可以使用標示為 [ **MTU 大小**] 的下拉式功能表，來選取快取的最大傳輸單位大小。

預設值為1500個位元組，但您可以將它變更為1400。

> [!NOTE]
> 如果您降低快取的 MTU 大小，請確定與快取通訊的用戶端和儲存系統具有相同的 MTU 設定或較低的值。

降低快取 MTU 值可協助您在快取網路的其餘部分解決封包大小的限制。 例如，某些 Vpn 無法成功傳輸全大小的1500位元組封包。 減少透過 VPN 傳送的封包大小可能會排除該問題。 不過，請注意，較低的快取 MTU 設定表示與快取通訊的任何其他元件（包括用戶端和儲存系統）也必須有較低的 MTU 設定，以避免通訊問題。

如果您不想變更其他系統元件上的 MTU 設定，則不應降低快取的 MTU 設定。 還有其他解決 VPN 封包大小限制的解決方案。 若要深入瞭解診斷和解決此問題，請參閱 NAS 疑難排解文章中的「 [調整 VPN 封包大小」限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) 。

藉由讀取 [Azure vm 的 tcp/ip 效能微調](../virtual-network/virtual-network-tcpip-performance-tuning.md)，深入瞭解 azure 虛擬網路中的 MTU 設定。

## <a name="configure-root-squash"></a>設定根 squash
<!-- linked from troubleshoot -->

**啟用根 squash**設定可控制 Azure HPC Cache 如何在用戶端電腦上處理來自根使用者的要求。

當根 squash 啟用時，用戶端的根使用者會在透過 Azure HPC Cache 傳送要求時，自動對應至使用者「沒有人」。 它也會防止用戶端要求使用設定 UID 許可權位。

如果已停用根 squash，用戶端根使用者 (UID 0) 的要求會傳遞至後端 NFS 儲存體系統作為根。 此設定可能會允許不當的檔案存取。

在快取上設定根 squash 有助於彌補 ``no_root_squash`` 用作為儲存體目標的 NAS 系統上的必要設定。  (深入瞭解 [NFS 儲存體目標必要條件](hpc-cache-prerequisites.md#nfs-storage-requirements)。在搭配 Azure Blob 儲存體目標使用時，) 它也可以改善安全性。

預設值為 [是]。 在2020年4月之前建立的 (快取可能預設設定為 [ **否**]。 ) 

## <a name="view-snapshots-for-blob-storage-targets"></a>查看 blob 儲存體目標的快照集

Azure HPC Cache 會自動儲存 Azure Blob 儲存體目標的儲存體快照集。 快照集提供後端儲存體容器內容的快速參考點。

快照不是資料備份的取代，而且不會包含快取資料狀態的任何相關資訊。

> [!NOTE]
> 此快照集功能不同于 NetApp 或 Isilon 儲存體軟體中包含的快照集功能。 這些快照集會在取得快照集之前，先將快取的變更排清到後端儲存體系統。
>
> 為了提高效率，Azure HPC Cache 快照集不會先排清變更，而只會記錄已寫入 Blob 容器的資料。 此快照集不代表快取資料的狀態，因此可能不包含最近的變更。

這項功能僅適用于 Azure Blob 儲存體目標，無法變更其設定。

快照集會每隔八小時執行，UTC 0:00、08:00 和16:00。

Azure HPC Cache 儲存每日、每週和每月快照，直到以新的快照集取代它們為止。 這些限制是：

* 最多20個每日快照集
* 最多8個每週快照
* 最多3個月快照

從 `.snapshot` blob 儲存體目標命名空間中的目錄存取快照集。
