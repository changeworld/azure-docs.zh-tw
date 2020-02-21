---
title: 虛擬 WAN：不同使用者群組的 Azure AD 租使用者： Azure AD 驗證
description: 您可以使用 P2S VPN，使用 Azure AD authentication 來連線到您的 VNet
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 4e667dcccd612873e8633c375bc3ce611e11c962
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485883"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>建立 P2S OpenVPN 通訊協定連接的 Azure Active Directory 租使用者

連線到您的 VNet 時，您可以使用以憑證為基礎的驗證或 RADIUS 驗證。 不過，當您使用 Open VPN 通訊協定時，您也可以使用 Azure Active Directory 驗證。 如果您想要讓不同的使用者集合能夠連線到不同的 VPN 閘道，您可以在 AD 中註冊多個應用程式，並將它們連結至不同的 VPN 閘道。

本文可協助您設定 P2S OpenVPN authentication 的 Azure AD 租使用者，並在 Azure AD 中建立並註冊多個應用程式，以允許不同的使用者和群組進行不同的存取。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="site"></a>6. 建立新的 P2S 設定

P2S 設定會定義用於連線遠端用戶端的參數。

1. 視需要針對您的環境取代相關值，以設定下列變數。

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. 執行以下命令以建立設定：

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > 在上述命令中，請勿使用 Azure VPN 用戶端的應用程式識別碼：它會將 VPN 閘道的存取權授與所有使用者。 使用您所註冊之應用程式的識別碼。

## <a name="hub"></a>7. 編輯中樞指派

1. 瀏覽至虛擬 WAN 底下的 [中樞] 刀鋒視窗。

2. 選取要與 VPN 伺服器設定產生關聯的中樞，然後按一下省略符號 (...)。

    ![新網站](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. 按一下 [編輯虛擬中樞]。

4. 勾選 [包含點對站閘道] 核取方塊，然後挑選您想要的 [閘道縮放單位]。

    ![新網站](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. 輸入要從中將 IP 位址指派給 VPN 用戶端的 [位址集區]。

6. 按一下 [確認]。

7. 此作業最多可能需要 30 分鐘才能完成。

## <a name="device"></a>8. 下載 VPN 設定檔

使用 VPN 設定檔來設定用戶端。

1. 在虛擬 WAN 的頁面上，按一下 [使用者 VPN 設定]。

2. 在頁面頂端，按一下 [下載使用者 VPN 設定]。

3. 檔案建立完成之後，您可以按一下連結來下載。

4. 使用設定檔檔案來設定 VPN 用戶端。

5. 將下載的 zip 檔案解壓縮。

6. 流覽至解壓縮的 "AzureVPN" 資料夾。

7. 記下 "azurevpnconfig" 檔案的位置。 Azurevpnconfig 包含 VPN 連線的設定，而且可以直接匯入至 Azure VPN 用戶端應用程式。 您也可以將此檔案散發給所有需要透過電子郵件或其他方式連接的使用者。 使用者需要有效的 Azure AD 認證，才能成功連接。

## <a name="9-configure-user-vpn-clients"></a>9. 設定使用者 VPN 用戶端

若要連線，您必須下載 Azure VPN 用戶端，並匯入先前步驟中要連線到 VNet 的每一部電腦上所下載的 VPN 用戶端設定檔。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

#### <a name="to-download-the-azure-vpn-client"></a>下載 Azure VPN Client

使用此[連結](https://go.microsoft.com/fwlink/?linkid=2117554)來下載 Azure VPN 用戶端。

#### <a name="import"></a>匯入用戶端設定檔

1. 在頁面上，選取 [匯入]。

    ![入口](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. 瀏覽至設定檔 xml 檔案，並加以選取。 選取檔案後，請選取 [開啟]。

    ![入口](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. 指定設定檔的名稱，然後選取 [儲存]。

    ![入口](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. 選取 [連線] 以連線至 VPN。

    ![入口](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. 連線之後，圖示將會變成綠色，並顯示為 [已連線]。

    ![入口](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="delete"></a>刪除用戶端設定檔

1. 選取要刪除的用戶端設定檔旁的省略符號 (...)。 然後，選取 [移除]。

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. 選取 [移除] 以刪除。

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="diagnose"></a>若要診斷連接問題

1. 若要診斷連線問題，您可以使用**診斷**工具。 選取要診斷的 VPN 連線旁的省略符號 (...)，以顯示功能表。 然後，選取 [診斷]。

    ![診斷](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. 在 [連線屬性] 頁面上，選取 [執行診斷]。

    ![診斷](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. 使用您的認證登入。

    ![診斷](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. 檢視診斷結果。

    ![診斷](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>10. 觀看您的虛擬 WAN

1. 瀏覽至虛擬 WAN。

2. 在 [概觀] 頁面中，地圖上的每個點都代表著中樞。 暫留在任一點上，即可檢視中樞健康情況摘要。

3. 在 [中樞與連線] 區段中，您可以檢視中樞狀態、網站、區域、VPN 連線狀態和輸入與輸出位元組。

## <a name="viewhealth"></a>11. 查看您的資源健康狀態

1. 瀏覽至您的 WAN。

2. 在 WAN 頁面上的 [支援 + 疑難排解] 區段中，按一下 [健康情況] 並檢視您的資源。

## <a name="cleanup"></a>清除資源

您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源。 將 "myResourceGroup" 取代為您的資源群組名稱，然後執行下列 PowerShell 命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
