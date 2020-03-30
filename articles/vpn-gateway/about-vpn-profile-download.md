---
title: Azure VPN 閘道：關於 P2S VPN 用戶端設定檔
description: 這有助於您使用用戶端設定檔檔
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528501"
---
# <a name="about-p2s-vpn-client-profiles"></a>關於 P2S VPN 用戶端設定檔

下載的設定檔包含配置 VPN 連接所必需的資訊。 本文將説明您獲取和理解 VPN 用戶端設定檔所需的資訊。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN 資料夾**包含需要修改以包含金鑰和證書的*ovpn*設定檔。 有關詳細資訊，請參閱為[Azure VPN 閘道配置 OpenVPN 用戶端](vpn-gateway-howto-openvpn-clients.md#windows)。 如果在 VPN 閘道上選擇了 Azure AD 身份驗證，則 ZIP 檔案中將不存在此資料夾。 相反，azurevpnconfig.xml 將位於 AzureVPN 資料夾中。

## <a name="next-steps"></a>後續步驟

有關點對點的詳細資訊，請參閱[關於點到網站](point-to-site-about.md)的資訊。
