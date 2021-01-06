---
title: 可用 Azure 檔案儲存體通訊協定-NFS 和 SMB
description: 在建立 Azure 檔案共用之前，請先瞭解可用的通訊協定，包括伺服器訊息區 (SMB) 和網路檔案系統 (NFS) 。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: e914bcece9a30270d12d5c2cc09d3fd1014783c6
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916502"
---
# <a name="azure-file-share-protocols"></a>Azure 檔案共用通訊協定

Azure 檔案儲存體提供兩種通訊協定來連線及掛接您的 Azure 檔案共用。 [伺服器訊息區 (SMB) 通訊協定](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 和 [網路檔案系統 (NFS) protocol](https://en.wikipedia.org/wiki/Network_File_System) (preview) 。 Azure 檔案儲存體目前不支援多重通訊協定存取，因此共用只能是 NFS 共用或 SMB 共用。 基於這個原因，建議您在建立 Azure 檔案共用之前，判斷哪一個通訊協定最適合您的需求。

## <a name="differences-at-a-glance"></a>差異一覽

|功能  |NFS (預覽)   |SMB  |
|---------|---------|---------|
|存取通訊協定     |NFS 4。1         |SMB 2.1、SMB 3。0         |
|建議的作業系統     |Linux 核心4.3 版 +         |Windows 2008 R2 +、Linux 核心版本 4.11 +         |
|[可用層](storage-files-planning.md#storage-tiers)     |進階儲存體         |Premium 儲存體、交易優化、經常性存取、非經常性存取         |
|[備援性](storage-files-planning.md#redundancy)     |LRS、ZRS         |LRS、ZRS、GRS         |
|驗證     |僅限以主機為基礎的驗證        |以身分識別為基礎的驗證，以使用者為基礎的驗證         |
|權限     |UNIX 樣式許可權         |NTFS 樣式許可權         |
|檔案系統語義     |POSIX 相容         |不符合 POSIX 規範         |
|區分大小寫     |區分大小寫         |不區分大小寫         |
|硬連結支援     |支援         |不支援         |
|符號連結支援     |支援         |不支援         |
|刪除或修改開啟的檔案     |支援         |不支援         |
|鎖定     |位元組範圍諮詢網路鎖定管理員         |支援         |
|公用 IP 安全清單 | 不支援 | 支援|
|通訊協定 interop| 不支援 | FileREST|

## <a name="nfs-shares-preview"></a>NFS 共用 (預覽) 

使用 NFS 4.1 裝載 Azure 檔案共用目前為預覽狀態。 它提供與 Linux 的緊密整合。 這是完全符合 POSIX 規範的供應專案，其為 Unix 和其他 * nix 型作業系統各式的標準。 這個企業級的檔案儲存體服務會相應增加，以符合您的儲存體需求，而且可以由數千個計算實例同時存取。

### <a name="limitations"></a>限制

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>區域可用性

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>最適合

具有 Azure 檔案儲存體的 NFS 適用于：

- 需要符合 POSIX 規範的檔案共用、區分大小寫或 Unix 樣式許可權 (UID/GID) 的工作負載。
- 不需要 Windows 存取的 Linux 中心工作負載。

### <a name="security"></a>安全性

所有 Azure 檔案儲存體資料都會在待用時加密。 針對傳輸中加密，Azure 會使用 [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE)為 Azure 資料中心之間傳輸中的所有資料提供加密層級。 如此一來，在 Azure 資料中心之間傳輸資料時，就會有加密存在。 不同于使用 SMB 通訊協定的 Azure 檔案儲存體，使用 NFS 通訊協定的檔案共用不會提供以使用者為基礎的驗證。 NFS 共用的驗證是以設定的網路安全性規則為基礎。 基於這個原因，為了確保只會對您的 NFS 共用建立安全的連接，您必須使用服務端點或私人端點。 如果您想要從內部部署存取共用，則除了私人端點之外，您還必須設定 VPN 或 ExpressRoute。 不是源自下列來源的要求將會遭到拒絕：

- [私人端點](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [點對站 (P2S) VPN](../../vpn-gateway/point-to-site-about.md)
    - [站對站](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [受限制的公用端點](storage-files-networking-overview.md#storage-account-firewall-settings)

如需可用網路功能選項的詳細資訊，請參閱 [Azure 檔案儲存體網路功能考慮](storage-files-networking-overview.md)。

## <a name="smb-shares"></a>SMB 共用

使用 SMB 掛接的 Azure 檔案共用提供更 Azure 檔案儲存體的功能，且沒有 Azure 檔案儲存體功能限制，因為它已正式推出。

### <a name="features"></a>功能

- Azure 檔案同步
- 以身分識別為基礎的驗證
- Azure 備份支援
- 快照集
- 虛刪除
- 加密傳輸和待用加密

### <a name="best-suited"></a>最適合

具有 Azure 檔案儲存體的 SMB 適用于：

- 生產環境
- 需要[功能](#features)中列出的任何功能的客戶

## <a name="next-steps"></a>後續步驟

- [建立 NFS 檔案共用](storage-files-how-to-create-nfs-shares.md)
- [建立 SMB 檔案共用](storage-how-to-create-file-share.md)
