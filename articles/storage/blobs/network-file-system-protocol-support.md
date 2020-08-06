---
title: Azure Blob 儲存體中的網路檔案系統3.0 支援 (預覽) |Microsoft Docs
description: Blob 儲存體現在支援網路檔案系統 (NFS) 3.0 通訊協定。 這項支援可讓 Windows 和 Linux 用戶端在 Blob 儲存體中，從 Azure 虛擬機器 (VM) 或在內部部署環境中執行的電腦掛接容器。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 32eafc41451c42a9c929e94d30450b923e7eae83
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760918"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob 儲存體中的網路檔案系統 (NFS) 3.0 通訊協定支援 (預覽) 

Blob 儲存體現在支援網路檔案系統 (NFS) 3.0 通訊協定。 這項支援可讓 Windows 或 Linux 用戶端在 Blob 儲存體中，從 Azure 虛擬機器 (VM) 或內部部署電腦掛接容器。 

> [!NOTE]
> Azure Blob 儲存體中的 NFS 3.0 通訊協定支援處於公開預覽狀態，並可在下欄區域使用：美國東部、美國中部和加拿大中部。

## <a name="general-workflow-mounting-a-storage-account-container"></a>一般工作流程：裝載儲存體帳戶容器

若要掛接儲存體帳戶容器，您必須執行這些動作。

1. 向您的訂用帳戶註冊 NFS 3.0 通訊協定功能。

2. 確認已註冊此功能。

3. 建立 Azure 虛擬網路 (VNet) 。

4. 設定網路安全性。

5. 建立並設定儲存體帳戶，以接受來自 VNet 的流量。

6. 在儲存體帳戶中建立容器。

7. 裝載容器。

如需逐步指引，請參閱[使用網路檔案系統掛接 Blob 儲存體 (NFS) 3.0 通訊協定 (preview) ](network-file-system-protocol-support-how-to.md)。

> [!IMPORTANT]
> 請務必依序完成這些工作。 在您的帳戶上啟用 NFS 3.0 通訊協定之前，無法掛接您所建立的容器。 此外，在您的帳戶上啟用 NFS 3.0 通訊協定之後，就無法將它停用。

## <a name="network-security"></a>網路安全性

您的儲存體帳戶必須包含在 VNet 中。 VNet 可讓用戶端安全地連接到您的儲存體帳戶。 保護帳戶中資料的唯一方法是使用 VNet 和其他網路安全性設定。 任何其他用來保護資料的工具，包括帳戶金鑰授權、Azure Active Directory (AD) 安全性和存取控制清單 (Acl) 尚未在啟用 NFS 3.0 通訊協定支援的帳戶中受到支援。 

若要深入瞭解，請參閱[Blob 儲存體的網路安全性建議](security-recommendations.md#networking)。

## <a name="supported-network-connections"></a>支援的網路連接

用戶端可以透過公用或[私人端點](../common/storage-private-endpoints.md)進行連線，並可從下列任何網路位置進行連線：

- 您為儲存體帳戶設定的 VNet。 

  基於本文的目的，我們會將該 VNet 稱為*主要 vnet*。 若要深入瞭解，請參閱[授與從虛擬網路存取權](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 與主要 VNet 位於相同區域中的對等互連 VNet。

  您必須設定儲存體帳戶，以允許此對等互連 VNet 的存取權。 若要深入瞭解，請參閱[授與從虛擬網路存取權](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 使用[VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute 閘道](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)連線到主要 VNet 的內部部署網路。 

  若要深入瞭解，請參閱設定[來自內部部署網路的存取](../common/storage-network-security.md#configuring-access-from-on-premises-networks)。

- 連線到對等互連網路的內部部署網路。

  您可以使用[VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute 閘道](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)以及[閘道傳輸](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)來完成這項作業。 

> [!IMPORTANT]
> 如果您是從內部部署網路進行連線，請確定您的用戶端允許透過埠111和2048的連出通訊。 NFS 3.0 通訊協定會使用這些埠。

## <a name="azure-storage-features-not-yet-supported"></a>尚不支援 Azure 儲存體功能

當您在帳戶上啟用 NFS 3.0 通訊協定時，不支援下列 Azure 儲存體功能。 

- Azure Active Directory (AD) 安全性

- 類似 POSIX 的存取控制清單 (Acl) 

- 能夠在現有的儲存體帳戶上啟用 NFS 3.0 支援

- 在儲存體帳戶中停用 NFS 3.0 支援的功能 (啟用之後) 

- 能夠使用 REST Api 或 Sdk 寫入 blob。 
  
## <a name="nfs-30-features-not-yet-supported"></a>尚未支援的 NFS 3.0 功能

Azure Data Lake Storage Gen2 尚不支援下列 NFS 3.0 功能。

- 透過 UDP 的 NFS 3.0。 僅支援透過 TCP 的 NFS 3.0。

- 使用網路鎖定管理員鎖定檔案 (NLM) 。 掛接命令必須包含 `-o nolock` 參數。

- 裝載子目錄。 您只能將根目錄掛接 (容器) 。

- 列出裝載 (例如：使用命令 `showmount -a`) 

- 列出匯出 (例如：使用命令 `showmount -e`) 

- 將容器匯出為唯讀

## <a name="pricing"></a>定價

在預覽期間，儲存在儲存體帳戶中的資料會以每個月每 GB blob 儲存體費用的相同容量費率計費。 

在預覽期間，不會收取交易的費用。 交易的定價可能會變更，而且會在正式推出時決定。

## <a name="next-steps"></a>後續步驟

若要開始，請參閱[使用網路檔案系統掛接 Blob 儲存體 (NFS) 3.0 通訊協定 (preview) ](network-file-system-protocol-support-how-to.md)。





