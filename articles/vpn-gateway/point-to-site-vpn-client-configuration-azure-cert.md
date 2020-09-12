---
title: 建立 & 安裝 P2S VPN 用戶端設定檔：憑證驗證
titleSuffix: Azure VPN Gateway
description: 建立和安裝 Windows、Linux、Linux (strongSwan) 及 Mac OS X VPN 用戶端組態檔以進行 P2S 憑證驗證。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 17a9339fff27a0fbd7fa389933d21ef85e29248b
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420773"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>建立和安裝適用於原生 Azure 憑證驗證 P2S 組態的 VPN 用戶端組態檔

VPN 用戶端組態檔包含在 ZIP 檔案內。 設定檔提供原生 Windows、Mac IKEv2 VPN 或 Linux 用戶端所需的設定，以透過使用原生 Azure 憑證驗證的點對站連線來連接到虛擬網路。

用戶端設定檔專用於虛擬網路的 VPN 設定。 如果您在產生 VPN 用戶端組態檔後對「點對站 VPN 組態」進行任何變更 (例如 VPN 通訊協定類型或驗證類型)，請務必為使用者裝置產生新的 VPN 用戶端組態檔。 

* 如需點對站連線的詳細資訊，請參閱[關於點對站 VPN](point-to-site-about.md)。
* 如需 OpenVPN 相關指示，請參閱[設定 P2S 的 OpenVPN](vpn-gateway-howto-openvpn.md) 和[設定 OpenVPN 用戶端](vpn-gateway-howto-openvpn-clients.md)。

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>產生 VPN 用戶端組態檔

在開始之前，請確定所有要連線的使用者都已在使用者裝置上安裝有效的憑證。 如需如何安裝用戶端憑證的詳細資訊，請參閱[安裝用戶端憑證](point-to-site-how-to-vpn-client-install-azure-cert.md)。

您可以使用 PowerShell 或使用 Azure 入口網站來產生用戶端組態檔。 任一方法都會傳回相同的 zip 檔案。 將該檔案解壓縮以檢視下列資料夾：

  * **WindowsAmd64** 和 **WindowsX86**，這兩個資料夾分別包含 Windows 32 位元和 64 位元的安裝程式套件。 **WindowsAmd64** 安裝程式套件不只適用於 AMD，也適用於所有受支援的 64 位元 Windows 用戶端。
  * **Generic**，這個資料夾包含用來建立自有 VPN 用戶端組態的一般資訊。 當閘道上設定了 IKEv2 或 SSTP+IKEv2 時才會提供 Generic 資料夾。 如果只設定了 SSTP，則不會提供 Generic 資料夾。

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>使用 Azure 入口網站產生檔案

1. 在 Azure 入口網站中，瀏覽至您想要連線之虛擬網路的虛擬網路閘道。
2. 在虛擬網路閘道頁面上，按一下 [點對站組態]****。

   ![下載用戶端入口網站](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. 在 [點對站台組態] 頁面頂端，按一下 [下載 VPN 用戶端]****。 產生用戶端組態套件需耗費幾分鐘的時間。
4. 您的瀏覽器指出用戶端組態 zip 檔可用。 它會命名為您閘道的相同名稱。 解壓縮檔案以檢視資料夾。

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>使用 PowerShell 產生檔案


1. 在產生 VPN 用戶端組態檔時，「-AuthenticationMethod」的值是「EapTls」。 使用下列命令來產生 VPN 用戶端組態檔：

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. 將 URL 複製到您的瀏覽器來下載 zip 檔案，然後解壓縮檔案以檢視資料夾。

## <a name="windows"></a><a name="installwin"></a>Windows

您可以在每個 Windows 用戶端電腦上使用相同的 VPN 用戶端組態套件，但前提是版本必須符合用戶端的架構。 如需用戶端支援的作業系統清單，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md#P2S)的＜點對站＞一節。

>[!NOTE]
>您必須擁有欲連線之 Windows 用戶端電腦上的系統管理員權限。
>
>

請使用下列步驟來設定原生 Windows VPN 用戶端的憑證驗證：

1. 選取 Windows 電腦架構所對應的 VPN 用戶端組態檔。 若是 64 位元的處理器架構，請選擇 'VpnClientSetupAmd64' 安裝程式套件。 若是 32 位元的處理器架構，請選擇 'VpnClientSetupX86' 安裝程式套件。 
2. 對套件按兩下來加以安裝。 如果您看到 SmartScreen 快顯視窗，請按一下 [ **更多資訊**]，然後 **繼續執行**。
3. 在用戶端電腦上，流覽至 [ **網路設定** ]，然後按一下 [ **VPN**]。 VPN 連線會顯示其連線的虛擬網路名稱。 
4. 嘗試連線之前，請確認您已在用戶端電腦上安裝用戶端憑證。 使用原生 Azure 憑證驗證類型時，必須提供用戶端憑證才能通過驗證。 如需有關產生憑證的詳細資訊，請參閱[產生憑證](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)。 如需有關如何安裝用戶端憑證的資訊，請參閱[安裝用戶端憑證](point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X) 

 您必須在將會連線到 Azure 的每部 Mac 上，手動設定原生 IKEv2 VPN 用戶端。 Azure 不提供用於原生 Azure 憑證驗證的 mobileconfig 檔案。 **Generic** 包含您進行設定所需的所有資訊。 如果未出現下載的 Generic 資料夾，則可能是未將 IKEv2 選為通道型別。 請注意，VPN 閘道基本 SKU 不支援 IKEv2。 一旦選取 IKEv2 後，重新產生 zip 檔案以擷取 Generic 資料夾。<br>Generic 資料夾包含下列檔案：

* **VpnSettings.xml**，其中包含伺服器位址和通道類型等重要設定。 
* **VpnServerRoot**，其中包含在 P2S 連線設定期間驗證 Azure VPN 閘道所需的根憑證。

使用下列步驟，在 Mac 上設定用於憑證驗證的原生 VPN 用戶端。 您必須在將會連線到 Azure 的每部 Mac 上，完成下列步驟：

1. 將 **VpnServerRoot** 根憑證匯入 Mac 中。 若要這麼做，請將該檔案複製到 Mac 上並對該檔案按兩下。 按一下 [新增]**** 來進行匯入。

   ![新增憑證](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >按兩下憑證可能不會顯示 [新增]**** 對話方塊，但是憑證已安裝在正確的存放區。 您可以在憑證類別底下的登入金鑰鏈中檢查憑證。
    >
  
2. 確認您已安裝用戶端憑證，且該憑證由您進行 P2S 設定時上傳到 Azure 的根憑證簽發。 此用戶端憑證與您在上個步驟中安裝的 VPNServerRoot 不同， 此為必要憑證，驗證時需提供。 如需有關產生憑證的詳細資訊，請參閱[產生憑證](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)。 如需有關如何安裝用戶端憑證的資訊，請參閱[安裝用戶端憑證](point-to-site-how-to-vpn-client-install-azure-cert.md)。
3. 開啟 [**網路喜好**設定] 下的 [**網路**] 對話方塊，然後按一下 [ **+** ] 以建立與 Azure 虛擬網路的 P2S 連線的新 VPN 用戶端連線設定檔。

   [介面]**** 值是 [VPN]，[VPN 類型]**** 值則是 [IKEv2]。 在 [服務名稱]**** 欄位中指定設定檔名稱，然後按一下 [建立]**** 以建立 VPN 用戶端連線設定檔。

   ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. 從 **Generic** 資料夾的 **VpnSettings.xml** 檔案複製 **VpnServer** 標記值。 將此值貼到設定檔的 [伺服器位址]**** 和 [遠端識別碼]**** 欄位。

   ![伺服器資訊](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. 按一下 [驗證設定]****，然後選取 [憑證]****。若為**Catalina**，請按一下 [**無**]，然後按一下 [**憑證**]

   ![驗證設定](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * 針對 [Catalina]，選取 [ **無** ]，然後選取 [ **憑證**]。 **選取** 正確的憑證：
   
   ![卡塔利娜](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. 按一下 [**選取**]。 以選擇您要用於驗證的用戶端憑證。 這是您在步驟 2 安裝的憑證。

   ![憑證 (certificate)](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. [選擇身分識別]**** 會顯示一份憑證清單供您選擇。 選取適當憑證，然後按一下 [繼續]****。

   ![身分識別](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. 在 [地區設定識別碼]**** 欄位中指定憑證名稱 (來自步驟 6)。 在此範例中，名稱是 "ikev2Client.com"。 然後，按一下 [套用]**** 按鈕以儲存變更。

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. 在 [網路]**** 對話方塊中按一下 [套用]**** 以儲存所有變更。 然後，按一下 **[連線]** 以啟動與 Azure 虛擬網路的 P2S 連線。

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>安裝 strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>產生憑證

如果您尚未產生憑證，請使用下列步驟：

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>安裝和設定

下列是在 Ubuntu 18.0.4 上建立的指示。 Ubuntu 16.0.10 並不支援 strongSwan GUI。 如果您想要使用 Ubuntu 16.0.10，就必須使用[命令列](#linuxinstallcli)。 視您的 Linux 和 strongSwan 版本而定，下列範例可能會與您看到的畫面不相符。

1. 執行範例中的指令，以開啟**終端機**來安裝 **strongSwan** 和其網路管理員。

   ```
   sudo apt install network-manager-strongswan
   ```
2. 選取 [ **設定**]，然後選取 [ **網路**]。

   ![編輯連線](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. 按一下 **+** 按鈕以建立新的連接。

   ![新增連線](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. 從功能表選取 [ **IPsec/IKEv2 (strongSwan) ** ]，然後按兩下。 您可以在此步驟中為連接命名。

   ![選擇連線類型](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. 從所下載用戶端組態檔中包含的 [Generic]**** 資料夾開啟 **VpnSettings.xml** 檔案。 尋找名為 **VpnServer** 的標籤，並複製名稱（開頭為 ' azuregateway ' 且結尾為 '. cloudapp.net '）。

   ![複製名稱](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. 將此名稱貼到新 VPN 連線 [閘道]**** 區段的 [位址]**** 欄位中。 接下來，選取 [憑證]**** 欄位結尾處的資料夾圖示，接著瀏覽至 [Generic]**** 資料夾，然後選取 [VpnServerRoot]**** 檔案。
7. 在連線的 [Client] \(用戶端\)**** 區段中，針對 [Authentication] \(驗證\)****，選取 [Certificate/private key] \(憑證/私密金鑰\)****。 針對 [Certificate] \(憑證\)**** 和 [Private key] \(私密金鑰\)****，選擇稍早建立的憑證和私密金鑰。 在 [Options] \(選項\)**** 中，選取 [Request an inner IP address] \(要求內部 IP 位址\)****. 然後按一下 [ **新增**]。

   ![要求內部 IP 位址](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. **開啟連接。**

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>安裝 strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>產生憑證

如果您尚未產生憑證，請使用下列步驟：

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>安裝和設定

1. 從 Azure 入口網站下載 VPNClient 套件。
2. 將檔案解壓縮。
3. 從 **Generic** 資料夾中，將 VpnServerRoot.cer 複製或移動到 /etc/ipsec.d/cacerts。
4. 將 cp client.p12 複製或移動到 /etc/ipsec.d/private/。 此檔案是 Azure VPN 閘道的用戶端憑證。
5. 開啟 VpnSettings.xml 檔案，並複製 `<VpnServer>` 值。 您將在下一個步驟中使用此值。
6. 調整下列範例中的值，然後將範例新增至 /etc/ipsec.conf 組態。
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. 將下列內容新增至 */etc/ipsec.secrets*。

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. 執行下列命令：

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>後續步驟

回到本文以[完成 P2S 設定](vpn-gateway-howto-point-to-site-rm-ps.md)。

若要對 P2S 連線進行疑難排解，請參閱下列文章：

  * [對 Azure 點對站連線進行疑難排解](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [對來自 Mac OS X VPN 用戶端的 VPN 連線進行疑難排解](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
