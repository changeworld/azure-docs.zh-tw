---
title: VPN 閘道：修改閘道 IP 位址設定： Azure 入口網站
description: 本文逐步解說如何使用 Azure 入口網站來變更區域網路閘道的 IP 位址首碼。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983153"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>使用 Azure 入口網站修改區域網路閘道設定

有時候區域網路閘道 AddressPrefix 或 GatewayIPAddress 的設定會變更。 本文將說明如何修改區域網路閘道設定。 您也可以從下列清單選取不同的選項來使用不同的方法修改這些設定：

在您刪除連線之前，可能會想要先為您的連線端裝置下載設定，以取得已定義的 PSK。 如此一來，您便不需要在另一端重新定義它。

> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>局域網路閘道設定

下列螢幕擷取畫面顯示使用公用 IP 位址端點之局域網路閘道資源的**設定頁面：**

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="設定局域網路閘道-IP 位址":::

這是具有 FQDN 端點的相同設定頁面：

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="設定局域網路閘道-IP 位址":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>修改閘道 IP 位址

如果您想要連線的 VPN 裝置已變更其公用 IP 位址，您需要修改區域網路閘道，以反映該變更。

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 在 [IP 位址]**** 方塊中，修改 IP 位址。
3. 按一下 [儲存] **** 來儲存這些設定。

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>修改閘道 FQDN

如果您要連線的 VPN 裝置已變更其 FQDN (完整功能變數名稱) ，您需要修改局域網路閘道以反映該變更。

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 在 [ **FQDN** ] 方塊中，修改功能變數名稱。
3. 按一下 [儲存] **** 來儲存這些設定。

> !記您無法變更 FQDN 端點和 IP 位址端點之間的局域網路閘道。 您必須刪除與此局域網路閘道相關聯的所有連線，並使用新的端點 (IP 位址或 FQDN) 建立新的連線，然後重新建立連線。

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>修改 IP 位址首碼

### <a name="to-add-additional-address-prefixes"></a>若要新增其他位址首碼：

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 在 [新增其他位址範圍]** 方塊中新增 IP 位址空間。
3. 按一下 [Save] **** 儲存您的設定。

### <a name="to-remove-address-prefixes"></a>若要移除位址首碼：

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 按一下包含您要移除之首碼的那一行上的 [...]****。
3. 按一下 **[移除]** 。
4. 按一下 [Save] **** 儲存您的設定。

## <a name="modify-bgp-settings"></a><a name="bgp"></a>修改 BGP 設定

### <a name="to-add-or-update-bgp-settings"></a>若要新增或更新 BGP 設定：

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 選取 **[設定 BGP 設定]** 以顯示或更新此局域網路閘道的 BGP 設定
3. 在對應的欄位中新增或更新自發系統編號或 BGP 對等 IP 位址
4. 按一下 [Save] **** 儲存您的設定。

### <a name="to-remove-bgp-settings"></a>移除 BGP 設定：

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 取消選取 [ **設定 BGP 設定]** 以移除現有的 BGP ASN 和 BGP 對等 IP 位址
3. 按一下 [Save] **** 儲存您的設定。

## <a name="next-steps"></a>後續步驟

您可以驗證閘道連線。 請參閱 [驗證閘道連線](vpn-gateway-verify-connection-resource-manager.md)。
