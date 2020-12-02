---
title: '設定商務持續性和嚴重損壞修復 (BCDR) Azure Stack Edge 虛擬私人網路 (VPN) '
description: 說明如何 Azure Stack Edge VPN 設定 BCDR。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: 4d735c623a6dffe24108d06d00caa7fba987c7df
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466246"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>針對 Azure Stack Edge VPN 設定商務持續性和嚴重損壞修復

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

本文說明如何在 Azure Stack Edge 裝置上設定的虛擬私人網路 (VPN) 上設定商務持續性和嚴重損壞修復 (BCDR) 。

本文適用于 Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 裝置。

## <a name="configure-failover-to-a-paired-region"></a>設定容錯移轉到配對的區域

您的 Azure Stack Edge 裝置會使用其他 Azure 服務，例如 Azure 儲存體。 您可以在 Azure Stack Edge 裝置使用的任何特定 Azure 服務上設定 BCDR。 如果 Azure Stack Edge 使用的 Azure 服務容錯移轉至其配對的區域，則 Azure Stack Edge 裝置現在將會連線至新的 IP 位址，而通訊將不會雙向加密。 

Azure Stack Edge 裝置會使用分割通道，以及在主區域中設定的所有資料和服務， (與 Azure Stack Edge 裝置相關聯的區域) 移至 VPN 通道。 如果 Azure 服務容錯移轉至位於 home 區域以外的配對區域，則資料將不再經過 VPN，因此不會進行雙向加密。 

在此案例中，通常只會影響少數 Azure 服務。 若要解決此問題，請在 Azure Stack Edge VPN 設定中進行下列變更：

1. 在 Azure Stack Edge 的 VPN 的內含路由中，新增容錯移轉 Azure 服務 IP 範圍 (s) 。 接著，服務會開始透過 VPN 路由傳送。

    若要加入內含路由，您需要下載具有服務特定路由的 json 檔案。 請務必使用新的路由來更新此檔案。
2. 在 Azure 路由表中新增對應的 Azure 服務 IP 範圍 (s) 。
3. 將路由新增至防火牆。

> [!NOTE]
>
> 1. Azure VPN 閘道和 Azure 虛擬網路 (VNET) 的容錯移轉，會在 [從因為嚴重損壞而失敗的 Azure 區域復原](#recover-from-a-failed-azure-region)的區段中解決。
> 2. Azure 路由表中新增的 IP 範圍可能會跨越400的限制。 如果發生這種情況，您將需要依照一節中的指導方針， [從一個 azure 區域移至另一個 azure 區域](#move-from-an-azure-region-to-another)。

## <a name="recover-from-a-failed-azure-region"></a>從失敗的 Azure 區域復原

如果整個 Azure 區域因為地震之類的災難事件而容錯移轉，該區域中的所有 Azure 服務（包括 Azure Stack Edge 服務）都會進行容錯移轉。 由於有多個服務，內含路由很容易就能分為數百個部分。 Azure 有400個路由的限制。 

區域容錯移轉時，虛擬網路 (Vnet) 也會容錯移轉至新的區域，因此虛擬網路閘道會 (VPN 閘道) 。 若要解決這項變更，請在 Azure Stack Edge VPN 設定中進行下列變更：

1. 將您的 Vnet 移至目的地區域。 如需詳細資訊，請參閱：透過 [Azure 入口網站將 Azure 虛擬網路移至另一個區域](../virtual-network/move-across-regions-vnet-portal.md)。
2. 在您移動 Vnet 的目的地區域中，部署新的 Azure VPN 閘道。 如需詳細資訊，請參閱 [建立虛擬網路閘道](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)。
3. 更新 Azure Stack Edge VPN 設定，以在 VPN 連線中使用上述 VPN 閘道，然後選取目的地區域以新增使用 VPN 閘道的路由。
4. 如果用戶端位址集區也變更，請更新傳入的 Azure 路由表。 

## <a name="move-from-an-azure-region-to-another"></a>從 Azure 區域移至另一個區域

您可以將 Azure Stack Edge 裝置從某個位置移到另一個位置。 若要使用最接近您裝置部署位置的區域，您將需要為新的主區域設定裝置。 進行下列變更：

1. 您可以更新 Azure Stack Edge VPN 設定，以使用新區域的 VPN 閘道，並選取新區域以新增使用 VPN 閘道的路由。

## <a name="next-steps"></a>後續步驟

[備份您的 Azure Stack Edge 裝置](azure-stack-edge-gpu-prepare-device-failure.md)。