---
title: VPN 閘道：用於 OpenVPN 協定 P2S 連接的 VPN 用戶端：Azure AD 身份驗證
description: 您可以使用 P2S VPN 使用 Azure AD 身份驗證連接到 VNet
services: vpn-gateway
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: alzam
ms.openlocfilehash: edb509d43742aeecf74107ae8cb625aeafbccb9f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385619"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>為 P2S OpenVPN 協定連接配置 VPN 用戶端：Azure AD 身份驗證

本文可説明您配置 VPN 用戶端，以便使用點對點 VPN 和 Azure 活動目錄身份驗證連接到虛擬網路。 必須先配置 Azure AD 租戶，然後才能使用 Azure AD 進行連接和身份驗證。 有關詳細資訊，請參閱配置[Azure AD 租戶](openvpn-azure-ad-tenant.md)。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>使用用戶端設定檔

要進行連接，您需要下載 Azure VPN 用戶端，並在要連接到 VNet 的每台電腦上配置 VPN 用戶端設定檔。 您可以在電腦上創建用戶端設定檔，將其匯出，然後將其導入其他電腦。

### <a name="to-download-the-azure-vpn-client"></a>下載 Azure VPN Client

使用此[連結](https://go.microsoft.com/fwlink/?linkid=2117554)下載 Azure VPN Client。 請確保 Azure VPN 用戶端具有在後臺運行的許可權。 要檢查/啟用許可權，請按照以下步驟操作：

1. 轉到 "開始"，然後選擇"設置">"隱私>後臺應用。
2. 在後臺應用下，請確保**在後臺運行的應用**已打開。
3. 在"選擇可以在後臺運行的應用"下，將 Azure VPN 用戶端的設置轉至**打開**。

  ![權限 (permission)](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>創建基於證書的用戶端設定檔

使用基於證書的設定檔時，請確保在用戶端電腦上安裝了相應的證書。 有關證書的詳細資訊，請參閱[安裝用戶端憑證](certificates-point-to-site.md)。

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>創建 RADIUS 用戶端設定檔

  ![半徑](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> 伺服器金鑰可以在 P2S VPN 用戶端設定檔中匯出。  有關如何匯出用戶端設定檔的說明可[在此處](about-vpn-profile-download.md)找到。
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>匯出和分發用戶端設定檔

一旦您擁有了工作設定檔，並且需要將其分發給其他使用者，則可以使用以下步驟匯出它：

1. 突出顯示要匯出的 VPN 用戶端設定檔，選擇 **..."，** 然後選擇 **"匯出**"。

    ![匯出](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 選擇要保存此設定檔的位置，保留檔案名，然後選擇 **"保存"** 以保存 xml 檔。

    ![匯出](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>匯入用戶端設定檔

1. 在頁面上，選取 [匯入]****。

    ![入口](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 瀏覽至設定檔 xml 檔案，並加以選取。 選取檔案後，請選取 [開啟]****。

    ![入口](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 指定設定檔的名稱，然後選取 [儲存]****。

    ![入口](./media/openvpn-azure-ad-client/import/import3.jpg)

4. 選取 [連線]**** 以連線至 VPN。

    ![入口](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 連線之後，圖示將會變成綠色，並顯示為 [已連線]****。

    ![入口](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>刪除用戶端設定檔

1. 選擇要刪除的用戶端設定檔旁邊的橢圓。 然後，選取 [移除]****。

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. 選取 [移除]**** 以刪除。

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>創建連接

1. 在頁面上，選擇**+**，然後 [**添加**。

    ![連接](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 填寫連接資訊。 如果您不確定這些值，請與管理員聯繫。 填寫值後，選擇 **"保存**"。

    ![連接](./media/openvpn-azure-ad-client/create/create2.jpg)

3. 選取 [連線]**** 以連線至 VPN。

    ![連接](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 選擇正確的憑據，然後選擇 **"繼續**"。

    ![連接](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 成功連接後，圖示將變為綠色，並說 **"已連接**"。

    ![連接](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>自動連接

這些步驟可説明您配置連接以自動連接"始終打開"。

1. 在 VPN 用戶端的主頁上，選擇**VPN 設置**。

    ![自動](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 在"切換應用"對話方塊上選擇 **"是**"。

    ![自動](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 請確保要設置的連接尚未連接，然後突出顯示設定檔並選中 **"自動連接**"核取方塊。

    ![自動](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. 選擇 **"連接"** 以啟動 VPN 連接。

    ![自動](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>診斷連線問題

1. 若要診斷連線問題，您可以使用**診斷**工具。 選擇要診斷的 VPN 連接旁邊的 **...** 以顯示功能表。 然後，選取 [診斷]****。

    ![診斷](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. 在 [連線屬性]**** 頁面上，選取 [執行診斷]****。

    ![診斷](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 使用您的認證登入。

    ![診斷](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 檢視診斷結果。

    ![診斷](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>常見問題集

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>如何向 VPN 用戶端添加 DNS 尾碼？

您可以修改下載的設定檔 XML 檔，並在**\<dnssufix \<>\<> /dnssufix>\</dnssuffix>標記添加 dnssuffix**

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>如何向 VPN 用戶端添加自訂 DNS 伺服器？

您可以修改下載的設定檔 XML 檔，並添加**\<dns\<伺服器>\<dnsserver> \</dnsserver>/dnsserver>** 標記

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
> OpenVPN Azure AD 用戶端使用 DNS 名稱解析策略表 （NRPT） 條目，這意味著 DNS 伺服器將不會列`ipconfig /all`在 的輸出下。 要確認您在使用的 DNS 設置，請參閱 PowerShell 中的[獲取 DnsClientnrpt 政策](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps)。
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>如何向 VPN 用戶端添加自訂路由？

您可以修改下載的設定檔 XML 檔，並添加**\<包含路由>\<路由>\<目標>\<掩蔽\<> /\<目標>\</遮罩\<>/路由>/包括路由>** 標記

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>如何阻止（排除）來自 VPN 用戶端的路由？

您可以修改下載的設定檔 XML 檔，並添加**\<排除路由>\<路由>\<目標>\<遮罩\<> /\<目標>\</遮罩\<>/路由>/排除路由>** 標記

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

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱[為使用 Azure AD 身份驗證的 P2S 開放 VPN 連接創建 Azure 活動目錄租戶](openvpn-azure-ad-tenant.md)。
