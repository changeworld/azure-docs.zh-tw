---
title: VPN 閘道：修改閘道 IP 位址設定： Azure 入口網站
description: 本文逐步解說如何使用 Azure 入口網站來變更區域網路閘道的 IP 位址首碼。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143159"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>使用 Azure 入口網站修改區域網路閘道設定

有時候區域網路閘道 AddressPrefix 或 GatewayIPAddress 的設定會變更。 本文將說明如何修改區域網路閘道設定。 您也可以從下列清單選取不同的選項來使用不同的方法修改這些設定：

> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>局域網路閘道設定

下列螢幕擷取畫面顯示使用公用 IP 位址端點之局域網路閘道資源的**設定頁面：**

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="IP 位址設定" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

這是具有 FQDN 端點的設定頁面：

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="IP 位址設定":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>修改閘道 IP 位址或 FQDN

> [!NOTE]
> 您無法變更 FQDN 端點和 IP 位址端點之間的局域網路閘道。 您必須刪除與此局域網路閘道相關聯的所有連線，並使用新的端點 (IP 位址或 FQDN) 建立新的連線，然後重新建立連線。
>

如果您要連線的 VPN 裝置已變更其公用 IP 位址，請使用下列步驟修改局域網路閘道：

1. 在 [局域網路閘道] 資源的 [設定] 區段中 **，選取 [****設定**]。
2. 在 [IP 位址]**** 方塊中，修改 IP 位址。
3. 選取 [儲存] **** 以儲存設定。

如果您要連線的 VPN 裝置已變更其 FQDN (完整功能變數名稱) ，請使用下列步驟修改局域網路閘道：

1. 在 [局域網路閘道] 資源的 [設定] 區段中 **，選取 [****設定**]。
2. 在 [ **FQDN** ] 方塊中，修改功能變數名稱。
3. 選取 [儲存] **** 以儲存設定。

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>修改 IP 位址首碼

若要新增其他位址首碼：

1. 在 [局域網路閘道] 資源的 [設定] 區段中 **，選取 [****設定**]。
2. 在 [新增其他位址範圍]** 方塊中新增 IP 位址空間。
3. 選取 [儲存] 以儲存您的設定。

若要移除位址首碼：

1. 在 [局域網路閘道] 資源的 [設定] 區段中 **，選取 [****設定**]。
2. 在包含您要移除之前置詞的行上，選取 [ **...** ]。
3. 選取 [移除]。
4. 選取 [儲存] 以儲存您的設定。

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>修改 BGP 設定

若要新增或更新 BGP 設定：

1. 在 [局域網路閘道] 資源的 [設定] 區段中 **，選取 [****設定**]。
2. 選取 **[設定 BGP 設定]** 以顯示或更新此局域網路閘道的 BGP 設定
3. 在對應的欄位中新增或更新自發系統編號或 BGP 對等 IP 位址
4. 選取 [儲存] 以儲存您的設定。

移除 BGP 設定：

1. 在 [局域網路閘道] 資源的 [設定] 區段中 **，選取 [****設定**]。
2. 取消選取 [ **設定 BGP 設定]** 以移除現有的 BGP ASN 和 BGP 對等 IP 位址
3. 選取 [儲存] 以儲存您的設定。

## <a name="next-steps"></a>後續步驟

您可以驗證閘道連線。 請參閱 [驗證閘道連線](vpn-gateway-verify-connection-resource-manager.md)。
