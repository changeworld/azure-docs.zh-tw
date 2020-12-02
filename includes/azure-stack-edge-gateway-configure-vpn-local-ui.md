---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466262"
---
在裝置的本機 web UI 中執行下列步驟。 此步驟大約需要15分鐘的時間，包括上傳 VPN 設定檔 (或服務標記檔案) 。 

1. 移至 **Configuration > VPN**。 選取 [設定] 。

    ![設定本機 UI 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. 在 [ **設定 VPN** ] 分頁中：

    - 啟用 **VPN 設定**。
    - 提供 **VPN 共用機密**。 這是您建立 Azure VPN 連線資源時所提供的共用金鑰。
    - 提供 **VPN 閘道 IP** 位址。 這是 Azure 局域網路閘道 IP 位址。
    - 針對 [ **PFS 群組**]，選取 [ **無**]。 
    - 針對 [ **DH 群組**]，選取 [ **Group2**]。
    - 針對 [ **IPsec 完整性方法**]，選取 [ **SHA256**]。
    - 若為 **IPsec 加密轉換常數**，請選取 [ **GCMAES256**]。
    - 針對 **IPsec 驗證轉換常數**，請選取 [ **GCMAES256**]。
    - 針對 [ **IKE 加密方法**]，選取 [ **AES256**]。
    - 選取 [套用]。

        ![設定本機 UI 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    如需有關支援的密碼編譯演算法的詳細資訊，請移至 [關於密碼編譯需求和 AZURE VPN 閘道](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq)。 

3. 若要上傳 VPN 路由設定檔，請選取 **[上傳**]。 

    ![設定本機 UI 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - 流覽至您在上一個步驟中于本機系統上下載的服務標記 *json* 檔案。
    - 選取區域作為與裝置、虛擬網路和閘道相關聯的 Azure 區域。
    - 選取 [套用]。

        ![設定本機 UI 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    裝置上的上傳大約需要7-8 分鐘。

4. 若要新增用戶端特定的路由，請將 IP 位址範圍設定為僅使用 VPN 進行存取。 

    - 在 **只使用 VPN 存取的 IP 位址範圍** 下，選取 [ **設定**]。
    - 提供有效的 IPv4 範圍，然後選取 [ **新增**]。 重複這些步驟來新增其他範圍。
    - 選取 [套用]。

        ![設定本機 UI 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

