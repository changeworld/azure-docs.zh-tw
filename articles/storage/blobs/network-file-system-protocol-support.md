---
title: Azure Blob 儲存體中的網路檔案系統3.0 支援 (預覽版) |Microsoft Docs
description: Blob 儲存體現在支援 (NFS) 3.0 通訊協定的網路檔案系統。 這項支援可讓 Windows 和 Linux 用戶端從 Azure 虛擬機器 (VM) 或在內部部署執行的電腦上，掛接 Blob 儲存體中的容器。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 52f7b328b013fd520787fca420a45ffdc5e9d9b1
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250803"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob 儲存體中的網路檔案系統 (NFS) 3.0 通訊協定支援 (預覽) 

Blob 儲存體現在支援 (NFS) 3.0 通訊協定的網路檔案系統。 這項支援可讓 Windows 或 Linux 用戶端從 Azure 虛擬機器 (VM) 或內部部署電腦，將容器掛接在 Blob 儲存體中。 

> [!NOTE]
> Azure Blob 儲存體中的 NFS 3.0 通訊協定支援處於公開預覽狀態。 它支援在下欄區域中具有標準層效能的 GPV2 儲存體帳戶：澳大利亞東部、韓國中部和美國中南部。 預覽版也支援所有公用區域中具有 premium 效能層的區塊 blob。

## <a name="general-workflow-mounting-a-storage-account-container"></a>一般工作流程：裝載儲存體帳戶容器

若要掛接儲存體帳戶容器，您必須進行這些動作。

1. 向您的訂用帳戶註冊 NFS 3.0 通訊協定功能。

2. 確認已註冊此功能。

3.  (VNet) 建立 Azure 虛擬網路。

4. 設定網路安全性。

5. 建立並設定只接受來自 VNet 之流量的儲存體帳戶。

6. 在儲存體帳戶中建立容器。

7. 裝載容器。

如需逐步指引，請參閱 [使用 Network File System (NFS) 3.0 通訊協定 (preview) 掛接 Blob 儲存體 ](network-file-system-protocol-support-how-to.md)。

> [!IMPORTANT]
> 請務必依序完成這些工作。 在您的帳戶上啟用 NFS 3.0 通訊協定之前，您無法掛接您所建立的容器。 此外，在您的帳戶上啟用 NFS 3.0 通訊協定之後，您就無法將它停用。

## <a name="network-security"></a>網路安全性

您的儲存體帳戶必須包含在 VNet 內。 VNet 可讓用戶端安全地連接到您的儲存體帳戶。 保護您帳戶中資料的唯一方法是使用 VNet 和其他網路安全性設定。 用來保護資料的任何其他工具，包括帳戶金鑰授權、Azure Active Directory (AD) 安全性，以及 (Acl) 的存取控制清單，在其上已啟用 NFS 3.0 通訊協定支援的帳戶尚未受到支援。 

若要深入瞭解，請參閱 [Blob 儲存體的網路安全性建議](security-recommendations.md#networking)。

## <a name="supported-network-connections"></a>支援的網路連接

用戶端可以透過公用或 [私人端點](../common/storage-private-endpoints.md)進行連線，而且可以從下列任何一個網路位置進行連接：

- 您為儲存體帳戶設定的 VNet。 

  基於本文的目的，我們會將該 VNet 稱為 *主要 vnet*。 若要深入瞭解，請參閱 [從虛擬網路授與存取權](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 對等互連 VNet，與主要 VNet 位於相同的區域中。

  您必須設定您的儲存體帳戶，以允許此對等互連 VNet 的存取權。 若要深入瞭解，請參閱 [從虛擬網路授與存取權](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 使用 [VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute 閘道](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)連接到您主要 VNet 的內部部署網路。 

  若要深入瞭解，請參閱設定 [來自內部部署網路的存取](../common/storage-network-security.md#configuring-access-from-on-premises-networks)。

- 連線到對等互連網路的內部部署網路。

  您可以使用 [VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute 閘道](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) 以及 [閘道傳輸](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)來完成這項操作。 

> [!IMPORTANT]
> 如果您要從內部部署網路進行連線，請確定您的用戶端允許透過埠111和2048的連出通訊。 NFS 3.0 通訊協定會使用這些埠。

## <a name="azure-storage-features-not-yet-supported"></a>尚不支援 Azure 儲存體功能

當您在您的帳戶上啟用 NFS 3.0 通訊協定時，不支援下列 Azure 儲存體功能。 

- Azure Active Directory (AD) 安全性

- 類似 POSIX 的存取控制清單 (Acl) 

- 能夠在現有的儲存體帳戶上啟用 NFS 3.0 支援

- 在儲存體帳戶中停用 NFS 3.0 支援的功能 (啟用之後) 

- 使用 REST Api 或 Sdk 寫入 blob 的能力。 
  
## <a name="nfs-30-features-not-yet-supported"></a>尚未支援的 NFS 3.0 功能

Azure Data Lake Storage Gen2 尚不支援下列 NFS 3.0 功能。

- 透過 UDP 的 NFS 3.0。 僅支援透過 TCP 的 NFS 3.0。

- 使用網路鎖定管理員鎖定檔案 (NLM) 。 掛接命令必須包含 `-o nolock` 參數。

- 裝載子目錄。 您只能將根目錄 (容器) 掛接。

- 列出裝載 (例如：使用命令 `showmount -a`) 

- 列出匯出 (例如：使用命令 `showmount -e`) 

- 硬連結

- 將容器匯出為唯讀

## <a name="pricing"></a>定價

在預覽期間，儲存在儲存體帳戶中的資料會以相同的容量費率計費，每月每 GB 的 blob 儲存體費用。 

在預覽期間，不會向交易收費。 交易的定價可能會變更，並會在正式推出時決定。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱 [使用 Network File System (NFS) 3.0 通訊協定 (preview) 掛接 Blob 儲存體 ](network-file-system-protocol-support-how-to.md)。
