---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812656"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. 從官方網站下載並安裝 OpenVPN 用戶端。
1. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [使用者 VPN 設定] 索引標籤，或是 PowerShell 中的 New-AzureRmVpnClientConfiguration 來完成。
1. 將設定檔解壓縮。 在記事本中開啟 OpenVPN 資料夾中的 vpnconfig.ovpn 設定檔。
1. 以 Base64 的 P2S 用戶端憑證公開金鑰填入 P2S 用戶端憑證區段。 在 PEM 格式的憑證中，您可以開啟 .cer 檔案並在憑證標題之間複製 Base64 金鑰。 如需相關步驟，請參閱[如何匯出憑證來取得編碼的公開金鑰](../articles/virtual-wan/certificates-point-to-site.md)。
1. 以 Base64 的 P2S 用戶端憑證私密金鑰填入私密金鑰區段。 如需相關步驟，請參閱[如何擷取私密金鑰](../articles/virtual-wan/howto-openvpn-clients.md#windows)。
1. 請勿變更任何其他欄位。 使用用戶端輸入中填入的設定來連線至 VPN。
1. 將 vpnconfig.ovpn 檔案複製到 C:\Program Files\OpenVPN\config 資料夾。
1. 以滑鼠右鍵按一下系統匣中的 OpenVPN 圖示，然後選取 [連線]。

##### <a name="ikev2"></a>IKEv2

1. 選取 Windows 電腦架構所對應的 VPN 用戶端組態檔。 若是 64 位元的處理器架構，請選擇 'VpnClientSetupAmd64' 安裝程式套件。 若是 32 位元的處理器架構，請選擇 'VpnClientSetupX86' 安裝程式套件。
1. 對套件按兩下來加以安裝。 如果您看到 SmartScreen 快顯視窗，請選取 [更多資訊]，然後選取 [仍要執行]。
1. 在用戶端電腦上，瀏覽至**網路設定**，然後按一下 [VPN]。 VPN 連線會顯示其連線的虛擬網路名稱。
1. 嘗試連線之前，請確認您已在用戶端電腦上安裝用戶端憑證。 使用原生 Azure 憑證驗證類型時，必須提供用戶端憑證才能通過驗證。 如需有關產生憑證的詳細資訊，請參閱[產生憑證](../articles/virtual-wan/certificates-point-to-site.md)。 如需有關如何安裝用戶端憑證的資訊，請參閱[安裝用戶端憑證](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)。
