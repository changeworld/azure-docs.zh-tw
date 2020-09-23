---
title: VPN 閘道：適用于 OpenVPN 通訊協定的 VPN 用戶端 P2S 連接： Azure AD authentication
description: 瞭解如何使用 P2S VPN 搭配 Azure AD authentication 來連線至您的 VNet。
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e64de987bd82f6b93350cdd706fc1a243d2855b1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977831"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>設定 P2S OpenVPN 通訊協定連線的 VPN 用戶端：Azure AD 驗證

本文可協助您設定 VPN 用戶端，以使用點對站 VPN 連線到虛擬網路，並 Azure Active Directory 驗證。 您必須先設定 Azure AD 租使用者，才可以使用 Azure AD 進行連線和驗證。 如需詳細資訊，請參閱 [設定 Azure AD 租](openvpn-azure-ad-tenant.md)使用者。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>使用用戶端設定檔

若要連線，您必須下載 Azure VPN Client，並在每台想要連線至 VNet 的電腦上設定 VPN 用戶端設定檔。 您可以在電腦上建立用戶端設定檔、將它匯出，然後將它匯入至其他電腦。

### <a name="to-download-the-azure-vpn-client"></a>下載 Azure VPN Client

使用此[連結](https://go.microsoft.com/fwlink/?linkid=2117554)下載 Azure VPN Client。 請確定 Azure VPN Client 具有在背景中執行的許可權。 若要檢查/啟用許可權，請遵循下列步驟：

1. 移至 [開始]，然後選取 [設定] > [隱私權] > 背景應用程式。
2. 在 [背景應用程式] 底下，確定已開啟 **[讓應用程式在背景中執行** ]。
3. 在 [選擇可在背景中執行的應用程式] 底下 **，將 [Azure VPN Client 的設定**開啟]。

  ![權限 (permission)](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>建立以憑證為基礎的用戶端設定檔

使用以憑證為基礎的設定檔時，請確定用戶端電腦上已安裝適當的憑證。 如需憑證的詳細資訊，請參閱 [安裝用戶端憑證](certificates-point-to-site.md)。

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>建立 RADIUS 用戶端設定檔

  ![半徑](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> 您可以在 P2S VPN 用戶端設定檔中匯出伺服器秘密。  您可以在 [這裡](about-vpn-profile-download.md)找到如何匯出用戶端設定檔的指示。
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>匯出和散發用戶端設定檔

當您有工作設定檔，並需要將其散發給其他使用者時，您可以使用下列步驟匯出它：

1. 反白顯示您要匯出的 VPN 用戶端設定檔，選取 [ **...**]，然後選取 [ **匯出**]。

    ![匯出](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 選取您要儲存此設定檔的位置，將檔案名保持原狀，然後選取 [ **儲存** ] 以儲存 xml 檔案。

    ![匯出](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>匯入用戶端設定檔

1. 在頁面上，選取 [匯入]****。

    ![import](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 瀏覽至設定檔 xml 檔案，並加以選取。 選取檔案後，請選取 [開啟]****。

    ![import](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 指定設定檔的名稱，然後選取 [儲存]****。

    ![import](./media/openvpn-azure-ad-client/import/import3.jpg)

4. 選取 [連線]**** 以連線至 VPN。

    ![import](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 連線之後，圖示將會變成綠色，並顯示為 [已連線]****。

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>刪除用戶端設定檔

1. 選取您要刪除之用戶端設定檔旁的省略號。 然後，選取 [移除]****。

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. 選取 [移除]**** 以刪除。

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>建立連接

1. 在頁面上，選取 [] **+** ，然後選取 [ **+ 新增**]。

    ![連接](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 填寫連接資訊。 如果您不確定其值，請洽詢您的系統管理員。 填妥值之後，請選取 [ **儲存**]。

    ![連接](./media/openvpn-azure-ad-client/create/create2.jpg)

3. 選取 [連線]**** 以連線至 VPN。

    ![連接](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 選取適當的認證，然後選取 [ **繼續**]。

    ![連接](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 成功連線後，圖示將會變成綠色，並說出 **已連線**。

    ![連接](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>自動連接

這些步驟可協助您設定連線，以使用 Always on 自動連接。

1. 在 VPN 用戶端的首頁上，選取 [ **Vpn 設定**]。

    ![自動](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 在 [切換應用程式] 對話方塊中選取 **[是]** 。

    ![自動](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 確定您要設定的連接尚未連接，然後反白顯示設定檔，然後選取 [自動連線 **]** 核取方塊。

    ![自動](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. 選取 **[連線]** 以起始 VPN 連線。

    ![自動](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>診斷連線問題

1. 若要診斷連線問題，您可以使用**診斷**工具。 選取您要診斷的 VPN 連線旁的 **...** 以顯示功能表。 然後，選取 [診斷]****。

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. 在 [連線屬性]**** 頁面上，選取 [執行診斷]****。

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 使用您的認證登入。

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 檢視診斷結果。

    ![diagnose](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>常見問題集

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>如何? 將 DNS 尾碼新增至 VPN 用戶端？

您可以修改下載的設定檔 XML 檔案，並** \<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes> **新增標記

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>如何? 將自訂 DNS 伺服器新增至 VPN 用戶端？

您可以修改下載的設定檔 XML 檔案，並** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> **新增標記

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> OpenVPN Azure AD 用戶端會使用 DNS 名稱解析原則資料表 (NRPT) 專案，這表示 DNS 伺服器不會列在的輸出下 `ipconfig /all` 。 若要確認使用中的 DNS 設定，請參閱 PowerShell 中的 [get-dnsclientnrptpolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) 。
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>如何? 將自訂路由新增至 VPN 用戶端？

您可以修改下載的設定檔 XML 檔案，並** \<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes> **新增標記

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>如何? 將所有流量導向 VPN 通道 (強制通道) ？

您可以修改下載的設定檔 XML 檔案，並** \<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes> **新增標記

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>0.0.0.0</destination><mask>1</mask>
        </route>
        <route>
            <destination>128.0.0.0</destination><mask>1</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>如何? 區塊 (從 VPN 用戶端排除) 路由？

您可以修改下載的設定檔 XML 檔案，並** \<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes> **新增標記

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```
### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>我可以從命令列提示字元匯入設定檔嗎？

您可以從命令提示字元匯入設定檔，方法是將下載的 **azurevpnconfig.xml** 檔案放在 **%userprofile%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** 資料夾中，然後執行下列命令：

```
azurevpn -i azurevpnconfig.xml 
```
若要強制匯入，也請使用 **-f** 參數


## <a name="next-steps"></a>下一步

如需詳細資訊，請參閱 [為使用 Azure AD 驗證的 P2S 開放式 VPN 連線建立 Azure Active Directory 的租](openvpn-azure-ad-tenant.md)使用者。
