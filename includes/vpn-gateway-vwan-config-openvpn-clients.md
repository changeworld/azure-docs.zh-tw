---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986699"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows 用戶端

1. 從[官方OpenVPN網站](https://openvpn.net/index.php/open-source/downloads.html)下載並安裝OpenVPN用戶端(2.4版或更高版本)。
2. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [點對站設定] 索引標籤，或是 PowerShell 中的 'New-AzVpnClientConfiguration' 來完成。
3. 將設定檔解壓縮。 接下來，使用記事本開啟 OpenVPN 資料夾中的 vpnconfig.ovpn** 設定檔。
4. 將您創建並上傳到閘道上的 P2S 設定的點對點客戶端證書匯出。 使用以下文章連結:

   * [VPN 閘道](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)說明
   
   * [虛擬廣域網路](../articles/virtual-wan/certificates-point-to-site.md#clientexport)指令
5. 從 .pfx** 擷取私密金鑰和 base64 指紋。 做法有好幾種。 其中一種方式是在電腦上使用 OpenSSL。 profileinfo.txt** 檔案包含 CA 和用戶端憑證的私密金鑰和指紋。 請務必使用用戶端憑證的指紋。

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. 在記事本中開啟 profileinfo.txt**。 若要取得用戶端 (子系) 憑證的指紋，請選取子系憑證介於 "-----BEGIN CERTIFICATE-----" 和 "-----END CERTIFICATE-----" 之間 (包含這兩句) 的文字，並加以複製。 您可以藉由查看 subject=/ 這一行來識別子系憑證。
7. 切換至您在步驟 3 中於記事本開啟的 vpnconfig.ovpn** 檔案。 尋找如下所示區段，並取代 "cert" 和 "/cert" 之間的所有內容。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 在記事本中開啟 profileinfo.txt**。 要獲取私鑰,請選擇"-----,私鑰-----"和"-----END 私鑰-----"的文本(包括和介於兩者之間)並複製它。
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

## <a name="mac-clients"></a><a name="mac"></a>Mac 用戶端

1. 下載並安裝 OpenVPN 用戶端,如[TunnelBlick](https://tunnelblick.net/downloads.html)。 
2. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [點對站設定] 索引標籤，或是使用 PowerShell 中的 'New-AzVpnClientConfiguration' 來完成。
3. 將設定檔解壓縮。 從文字編輯器中的 OpenVPN 資料夾中開啟 vpnconfig.ovpn 設定檔。
4. 以 Base64 的 P2S 用戶端憑證公開金鑰填入 P2S 用戶端憑證區段。 在 PEM 格式的憑證中，您只需開啟 .cer 檔案並在憑證標題之間複製 Base64 金鑰。 使用以下文章連結取得有關如何匯出憑證以取得編碼公開金鑰的資訊:

   * [VPN 閘道](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer)說明 
   
   * [虛擬廣域網路](../articles/virtual-wan/certificates-point-to-site.md#cer)指令
5. 以 Base64 的 P2S 用戶端憑證私密金鑰填入私密金鑰區段。 有關如何提取私鑰的資訊,請參閱 OpenVPN 網站上的[「匯出您的私密金鑰](https://openvpn.net/community-resources/how-to/#pki)」。
6. 請勿變更任何其他欄位。 使用用戶端輸入中填入的設定來連線至 VPN。
7. 按兩下設定檔以在 Tunnelblick 建立設定檔。
8. 從應用程式資料夾中啟動隧道。
9. 按一下系統匣中的 Tunnelblick 圖示並選取連接。

> [!IMPORTANT]
>OpenVPN 通訊協定僅支援 iOS 11.0 和更新版本以及 MacOS 10.13 和更新版本。
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS 用戶端

1. 從應用商店安裝 OpenVPN 用戶端(版本 2.4 或更高版本)。
2. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [點對站設定] 索引標籤，或是使用 PowerShell 中的 'New-AzVpnClientConfiguration' 來完成。
3. 將設定檔解壓縮。 從文字編輯器中的 OpenVPN 資料夾中開啟 vpnconfig.ovpn 設定檔。
4. 以 Base64 的 P2S 用戶端憑證公開金鑰填入 P2S 用戶端憑證區段。 在 PEM 格式的憑證中，您只需開啟 .cer 檔案並在憑證標題之間複製 Base64 金鑰。 使用以下文章連結取得有關如何匯出憑證以取得編碼公開金鑰的資訊:

   * [VPN 閘道](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer)說明 
   
   * [虛擬廣域網路](../articles/virtual-wan/certificates-point-to-site.md#cer)指令
5. 以 Base64 的 P2S 用戶端憑證私密金鑰填入私密金鑰區段。 關於如何提取私密金鑰的資訊,請參考在 OpenVPN 網站匯[出私密金鑰](https://openvpn.net/community-resources/how-to/#pki)。
6. 請勿變更任何其他欄位。
7. 透過電子郵件將設定檔 (.ovpn) 傳送到您在 iPhone 上的郵件應用中設定的電子郵件帳戶。 
8. 開啟 iPhone 上的郵件應用中的電子郵件,然後點按附加檔案

    ![開啟電子郵件](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. 如果您沒有看到 **「複製到 OpenVPN」** 選項,請點擊 **「更多**」

    ![更多資訊](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. 點擊 **"複製到打開VPN"** 

    ![複製到 OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. 點擊 **"匯入設定檔**"頁面中的 **"添加"**

    ![加](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. 點擊 **"匯入設定檔**"頁面中的 **"添加"**

    ![點擊"添加"](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. 啟動 OpenVPN 應用程式,並在 **「設定檔」** 頁面右方滑動交換機以進行連線

    ![連線](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux 用戶端

1. 開啟新的終端機工作階段。 同時按下 'Ctrl + Alt + t' 即可開啟新的工作階段。
2. 輸入下列命令以安裝所需的元件：

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. 下載閘道的 VPN 設定檔。 這可從 Azure 入口網站中的 [點對站設定] 索引標籤來完成。
4. 匯出您所建立並上傳至閘道上 P2S 組態的 P2S 用戶端憑證。 使用以下文章連結:

   * [VPN 閘道](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)說明 
   
   * [虛擬廣域網路](../articles/virtual-wan/certificates-point-to-site.md#clientexport)指令
5. 從 .pfx 擷取私密金鑰和 base64 指紋。 做法有好幾種。 其中一種方式是在電腦上使用 OpenSSL。

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   profileinfo.txt** 檔案會包含 CA 和用戶端憑證的私密金鑰和指紋。 請務必使用用戶端憑證的指紋。

6. 在文字編輯器中開啟 profileinfo.txt**。 若要取得用戶端 (子系) 憑證的指紋，請選取子系憑證介於 "-----BEGIN CERTIFICATE-----" 和 "-----END CERTIFICATE-----" 之間 (包含這兩句) 的文字，並加以複製。 您可以藉由查看 subject=/ 這一行來識別子系憑證。

7. 開啟 vpnconfig.ovpn** 檔案，並尋找如下所示區段。 取代 "cert" 和 "/cert" 之間的所有內容。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. 在文字編輯器中開啟 profileinfo.txt。 要取得私鑰,請選擇包含"-----一個私密金鑰-----"和"-----END PRIVATE KEY-----"的文本,然後複製它。

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
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. 若要使用 GUI 建立連線，請移至系統設定。
13. 按一**+** 下 以添加新 VPN 連接。
14. 在 [新增 VPN]****，點選 [匯入檔案]****
15. 瀏覽至設定檔並按兩下或點選 [開啟]****。
16. 按一下 [新增 VPN]**** 視窗上的 [新增]****。
  
    ![匯入檔案](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. 在 [網路設定]**** 頁面上或在系統匣中網路圖示下將 VPN 切換到 [開]**** 即可建立連線。
