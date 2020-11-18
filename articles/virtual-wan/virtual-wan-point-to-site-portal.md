---
title: 教學課程：使用 Azure 虛擬 WAN 與 Azure 建立點對站連線
description: 在本教學課程中，了解如何使用 Azure 虛擬 WAN 來與 Azure 建立點對站 VPN 連線。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7e65d5d2941765df98b3bf3b7fb8ff2e89b7e9f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411196"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>教學課程：使用 Azure 虛擬 WAN 建立使用者 VPN 連線

本教學課程會示範如何使用虛擬 WAN，透過 IPsec/IKE (IKEv2) 或 OpenVPN VPN 連線來與 Azure 中的資源連線。 此類型的連線需要在用戶端電腦上設定 VPN 用戶端。 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬 WAN
> * 建立 P2S 設定
> * 建立虛擬中樞
> * 指定 DNS 伺服器
> * 產生 VPN 用戶端設定檔組態套件
> * 設定 VPN 用戶端
> * 檢視您的虛擬 WAN

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>建立虛擬 WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>建立 P2S 設定

點對站 (P2S) 設定會定義用於連線遠端用戶端的參數。

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>建立虛擬中樞與閘道

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>指定 DNS 伺服器

您可以在建立中樞時進行此設定，或在稍後修改。 若要修改，請找出虛擬中樞。 在 [使用者 VPN (點對站)] 上選取 [設定]，然後在 [自訂 DNS 伺服器] 文字方塊中輸入 DNS 伺服器 IP 位址。 您可以指定最多 5 部 DNS 伺服器。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="自訂 DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>產生 VPN 用戶端設定檔套件

產生並下載 VPN 用戶端設定檔套件，以設定您的 VPN 用戶端。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>設定 VPN 用戶端

使用下載的設定檔套件來設定遠端存取 VPN 用戶端。 每個作業系統的程序各不相同。 依照您的系統適用的指示進行。
完成用戶端的設定之後，您就可以連線。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>檢視您的虛擬 WAN

1. 瀏覽至虛擬 WAN。
1. 在 [概觀] 頁面中，地圖上的每個點都代表著中樞。
1. 在 **中樞與連線** 區段中，您可以檢視中樞狀態、網站、區域、VPN 連線狀態和輸入與輸出位元組。

## <a name="clean-up-resources"></a><a name="cleanup"></a>清除資源

您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源。 將 "myResourceGroup" 取代為您的資源群組名稱，然後執行下列 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

接著，若要深入了解虛擬 WAN，請參閱：

> [!div class="nextstepaction"]
> * [虛擬 WAN 常見問題集](virtual-wan-faq.md)
