---
title: Azure VPN 閘道：關於 P2S VPN 用戶端設定檔
description: 這可協助您使用用戶端設定檔
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 326abaa123ede1fbe371c69fcbed47a310b54511
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424832"
---
# <a name="about-p2s-vpn-client-profiles"></a>關於 P2S VPN 用戶端設定檔

下載的設定檔包含設定 VPN 連線所需的資訊。 本文將協助您取得並了解 VPN 用戶端設定檔所需的資訊。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 資料夾**包含需要修改以包含金鑰和憑證的 ovpn 設定檔。 如需詳細資訊，請參閱[針對 Azure VPN 閘道設定 OpenVPN 用戶端](vpn-gateway-howto-openvpn-clients.md#windows)。 如果在 VPN 閘道上選取了 Azure AD 驗證，此資料夾就不會出現在 zip 檔案中。 請改為瀏覽至 AzureVPN 資料夾並找出 azurevpnconfig.xml。

## <a name="next-steps"></a>後續步驟

如需點對站詳細資訊，請參閱[關於點對站](point-to-site-about.md)。
