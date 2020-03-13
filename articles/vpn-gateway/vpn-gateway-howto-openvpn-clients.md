---
title: 如何設定 Azure VPN 閘道的 OpenVPN 用戶端 |Microsoft Docs
description: 為 Azure VPN 閘道設定 OpenVPN 用戶端的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: a45a3412a1ceb8e8a9bd9fd1a34dfdbd10ba1c75
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244598"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>設定適用于 Azure VPN 閘道的 OpenVPN 用戶端

本文可協助您設定**OpenVPN®通訊協定**用戶端。

## <a name="before-you-begin"></a>開始之前



請確認您已完成為 VPN 閘道設定 OpenVPN 的步驟。 如需詳細資訊，請參閱[設定 Azure VPN 閘道的 OpenVPN 用戶端](vpn-gateway-howto-openvpn.md)。

## <a name="windows"></a>Windows 用戶端

1. 從官方[OpenVPN 網站](https://openvpn.net/index.php/open-source/downloads.html)下載並安裝 OpenVPN 用戶端（2.4 版或更新版本）。
2. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [點對站設定] 索引標籤，或是 PowerShell 中的 'New-AzVpnClientConfiguration' 來完成。
3. 將設定檔解壓縮。 接下來，使用記事本開啟 OpenVPN 資料夾中的 vpnconfig.ovpn 設定檔。
4. [匯出](vpn-gateway-certificates-point-to-site.md#clientexport)您所建立並上傳至閘道上 P2S 組態的 P2S 用戶端憑證。
5. 從 .pfx 擷取私密金鑰和 base64 指紋。 做法有好幾種。 其中一種方式是在電腦上使用 OpenSSL。 profileinfo.txt 檔案包含 CA 和用戶端憑證的私密金鑰和指紋。 請務必使用用戶端憑證的指紋。

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. 在記事本中開啟 profileinfo.txt。 若要取得用戶端 (子系) 憑證的指紋，請選取子系憑證介於 "-----BEGIN CERTIFICATE-----" 和 "-----END CERTIFICATE-----" 之間 (包含這兩句) 的文字，並加以複製。 您可以藉由查看 subject=/ 這一行來識別子系憑證。
7. 切換至您在步驟 3 中於記事本開啟的 vpnconfig.ovpn 檔案。 尋找如下所示區段，並取代 "cert" 和 "/cert" 之間的所有內容。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 在記事本中開啟 profileinfo.txt。 若要取得私密金鑰，請選取「-----開始私密金鑰-----」和「-----結束私密金鑰-----」的文字，然後將它複製。
9. 返回記事本中的 vpnconfig.ovpn 檔案，並尋找此區段。 貼上私密金鑰來取代 "key" 和 "/key" 之間的所有內容。

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. 請勿變更任何其他欄位。 使用用戶端輸入中填入的設定來連線至 VPN。
11. 將 vpnconfig.ovpn 檔案複製到 C:\Program Files\OpenVPN\config 資料夾。
12. 以滑鼠右鍵按一下系統匣中的 OpenVPN 圖示，然後按一下 [連線]。

## <a name="mac"></a>Mac 用戶端

1. 下載並安裝 OpenVPN 用戶端，例如[TunnelBlick](https://tunnelblick.net/downloads.html)。 
2. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [點對站設定] 索引標籤，或是使用 PowerShell 中的 'New-AzVpnClientConfiguration' 來完成。
3. 將設定檔解壓縮。 在文字編輯器中，從 OpenVPN 資料夾開啟 vpnconfig vpnconfig.ovpn 設定檔案。
4. 以 Base64 的 P2S 用戶端憑證公開金鑰填入 P2S 用戶端憑證區段。 在 PEM 格式的憑證中，您只需開啟 .cer 檔案並在憑證標題之間複製 Base64 金鑰。 如需如何匯出憑證以取得編碼的公開金鑰的詳細資訊，請參閱[匯出公開金鑰](vpn-gateway-certificates-point-to-site.md#cer)。
5. 以 Base64 的 P2S 用戶端憑證私密金鑰填入私密金鑰區段。 如需如何擷取私用金鑰的資訊，請參閱[匯出私密金鑰](https://openvpn.net/community-resources/how-to/#pki)。
6. 請勿變更任何其他欄位。 使用用戶端輸入中填入的設定來連線至 VPN。
7. 按兩下設定檔，在 Tunnelblick 中建立設定檔。
8. 從 [應用程式] 資料夾啟動 Tunnelblick。
9. 按一下系統匣中的 Tunnelblick 圖示，然後選擇 [連線]。

> [!IMPORTANT]
>OpenVPN 通訊協定僅支援 iOS 11.0 和更新版本以及 MacOS 10.13 和更新版本。
>
## <a name="iOS"></a>iOS 用戶端

1. 從 App store 安裝 OpenVPN 用戶端（2.4 版或更高版本）。
2. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [點對站設定] 索引標籤，或是使用 PowerShell 中的 'New-AzVpnClientConfiguration' 來完成。
3. 將設定檔解壓縮。 在文字編輯器中，從 OpenVPN 資料夾開啟 vpnconfig vpnconfig.ovpn 設定檔案。
4. 以 Base64 的 P2S 用戶端憑證公開金鑰填入 P2S 用戶端憑證區段。 在 PEM 格式的憑證中，您只需開啟 .cer 檔案並在憑證標題之間複製 Base64 金鑰。 如需如何匯出憑證以取得編碼的公開金鑰的詳細資訊，請參閱[匯出公開金鑰](vpn-gateway-certificates-point-to-site.md#cer)。
5. 以 Base64 的 P2S 用戶端憑證私密金鑰填入私密金鑰區段。 如需如何擷取私用金鑰的資訊，請參閱[匯出私密金鑰](https://openvpn.net/community-resources/how-to/#pki)。
6. 請勿變更任何其他欄位。
7. 將設定檔（. vpnconfig.ovpn）以電子郵件傳送到您在 iPhone 上的郵件應用程式中設定的電子郵件帳戶。 
8. 在 iPhone 上的郵件應用程式中開啟電子郵件，然後按一下附加的檔案

    ![開啟電子郵件](./media/vpn-gateway-howto-openvpn-clients/ios2.png)

9. 如果看不**到 [複製到 OpenVPN** ] 選項，請按一下 [**更多**]

    ![複製到 OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios3.png)

10. 按一下 [**複製到 OpenVPN** ] 

    ![複製到 OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios4.png)

11. 在 [匯**入設定檔**] 頁面中按一下 [**新增**]

    ![複製到 OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios5.png)

12. 在匯**入的設定檔**頁面中，按一下 [**新增**]

    ![複製到 OpenVPN](./media/vpn-gateway-howto-openvpn-clients/ios6.png)

13. 啟動 OpenVPN 應用程式，並在 [**設定檔**] 頁面上滑動開關以連線

    ![連線](./media/vpn-gateway-howto-openvpn-clients/ios8.png)


## <a name="linux"></a>Linux 用戶端

1. 開啟新的終端機工作階段。 同時按下 'Ctrl + Alt + t' 即可開啟新的工作階段。
2. 輸入下列命令以安裝所需的元件：

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [點對站設定] 索引標籤來完成。
4. [匯出](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport)您所建立並上傳至閘道上 P2S 組態的 P2S 用戶端憑證。 
5. 從 .pfx 擷取私密金鑰和 base64 指紋。 做法有好幾種。 其中一種方式是在電腦上使用 OpenSSL。

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   profileinfo.txt 檔案會包含 CA 和用戶端憑證的私密金鑰和指紋。 請務必使用用戶端憑證的指紋。

6. 在文字編輯器中開啟 profileinfo.txt。 若要取得用戶端 (子系) 憑證的指紋，請選取子系憑證介於 "-----BEGIN CERTIFICATE-----" 和 "-----END CERTIFICATE-----" 之間 (包含這兩句) 的文字，並加以複製。 您可以藉由查看 subject=/ 這一行來識別子系憑證。

7. 開啟 vpnconfig.ovpn 檔案，並尋找如下所示區段。 取代 "cert" 和 "/cert" 之間的所有內容。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 在文字編輯器中開啟 profileinfo.txt。 若要取得私密金鑰，請選取介於「-----開始私密金鑰-----」和「-----結束私密金鑰-----」之間的文字，並加以複製。

9. 在文字編輯器中開啟 vpnconfig.ovpn 檔案，並尋找此區段。 貼上私密金鑰來取代 "key" 和 "/key" 之間的所有內容。

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. 請勿變更任何其他欄位。 使用用戶端輸入中填入的設定來連線至 VPN。
11. 若要使用命令列建立連線，請輸入下列命令：
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. 若要使用 GUI 建立連線，請移至系統設定。
13. 按一下 **+** 以新增新的 VPN 連線。
14. 在 [新增 VPN]，點選 [匯入檔案]
15. 瀏覽至設定檔並按兩下或點選 [開啟]。
16. 按一下 [新增 VPN] 視窗上的 [新增]。
  
    ![匯入檔案](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. 在 [網路設定] 頁面上或在系統匣中網路圖示下將 VPN 切換到 [開] 即可建立連線。

## <a name="next-steps"></a>後續步驟

如果您想要 VPN 用戶端能夠存取另一個 VNet 中的資源，請遵循[vnet 對 vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)一文中的指示來設定 vnet 對 vnet 連線。 請務必啟用閘道上的 BGP 和連線，否則流量無法流動。

**「OpenVPN」是 OpenVPN Inc. 的商標。**
