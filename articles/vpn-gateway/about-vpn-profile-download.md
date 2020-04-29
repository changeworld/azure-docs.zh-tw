---
title: Azure VPN 閘道：關於 P2S VPN 用戶端設定檔
description: 這可協助您使用用戶端設定檔
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528501"
---
# <a name="about-p2s-vpn-client-profiles"></a>關於 P2S VPN 用戶端設定檔

下載的設定檔包含設定 VPN 連線所需的資訊。 本文將協助您取得並瞭解 VPN 用戶端設定檔所需的資訊。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 資料夾**包含需要修改的*vpnconfig.ovpn*設定檔，以包含金鑰和憑證。 如需詳細資訊，請參閱[設定 Azure VPN 閘道的 OpenVPN 用戶端](vpn-gateway-howto-openvpn-clients.md#windows)。 如果在 VPN 閘道上選取了 Azure AD authentication，此資料夾就不會出現在 zip 檔案中。 相反地，azurevpnconfig 會在 AzureVPN 資料夾中。

## <a name="next-steps"></a>後續步驟

如需有關點對站的詳細資訊，請參閱[關於點對站](point-to-site-about.md)。
