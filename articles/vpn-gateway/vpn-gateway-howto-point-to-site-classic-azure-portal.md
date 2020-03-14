---
title: 使用點對站將電腦連線至 Azure 虛擬網路︰Azure 入口網站傳統 | Microsoft Docs
description: 使用 Azure 入口網站建立傳統的點對站 VPN 閘道連線。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: 5d80cb2f2ed844126d1e9311151e6c53fcb11840
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244572"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>使用憑證驗證設定點對站連線 (傳統)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

本文說明如何建立具有點對站連線的 VNet。 您會使用 Azure 入口網站，透過傳統部署模型建立此 VNet。 此組態會使用自我簽署或 CA 核發的憑證來驗證連線用戶端。 您也可以使用下列文章中所述的選項，透過不同的部署工具或模型來建立這個組態：

> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure 入口網站 (傳統)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

您可以使用點對站 (P2S) VPN 閘道來建立從個別用戶端電腦到虛擬網路的安全連線。 當您想要從遠端位置連線到您的 VNet 時，點對站 VPN 連線很實用。 當您只有一些用戶端需要連線至 VNet 時，P2S VPN 會是很實用的解決方案 (而不是使用站對站 VPN)。 P2S VPN 連線的建立方式是從用戶端電腦開始。

> [!IMPORTANT]
> 傳統部署模型僅支援 Windows VPN 用戶端，並且使用安全通訊端通道通訊協定 (SSTP)，這是 SSL 型 VPN 通訊協定。 若要支援非 Windows VPN 用戶端，您必須使用 Resource Manager 部署模型來建立 VNet。 除了 SSTP 以外，Resource Manager 部署模型還支援 IKEv2 VPN。 如需詳細資料，請參閱[關於 P2S 連線](point-to-site-about.md)。
>
>

![Point-to-Site-diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Prerequisites

點對站憑證驗證連線需要下列先決條件：

* 動態 VPN 閘道。
* 已上傳至 Azure 之根憑證的公開金鑰 (.cer 檔案)。 此金鑰會被視為受信任的憑證並且用於驗證。
* 用戶端憑證是從根憑證產生，並安裝在每部即將連線的用戶端電腦上。 此憑證使用於用戶端憑證。
* 必須在每部連線的用戶端電腦上產生並安裝 VPN 用戶端組態套件。 用戶端組態套件會使用連線到 VNet 的必要資訊，設定已在作業系統上的原生 VPN 用戶端。

點對站連線不需要 VPN 裝置或內部部署公眾對應 IP 位址。 已透過 SSTP (安全通訊端通道通訊協定) 建立 VPN 連線。 我們在伺服器端上支援 SSTP 1.0、1.1 和 1.2 版。 用戶端會決定要使用的版本。 若為 Windows 8.1 和更新版本，SSTP 預設使用 1.2。 

如需點對站連線的詳細資訊，請參閱[點對站常見問題集](#point-to-site-faq)。

### <a name="example-settings"></a>範例設定

請使用下列值來建立測試環境，或參考這些值來進一步了解本文中的範例：

- **建立虛擬網路 (傳統) 設定**
   - **名稱**：輸入*VNet1*。
   - **位址空間**：輸入*192.168.0.0/16*。 在此範例中，我們只使用一個位址空間。 您可以為 VNet 使用多個位址空間，如圖所示。
   - **子網名稱**：輸入*前端*。
   - **子網位址範圍**：輸入*192.168.1.0/24*。
   - **訂**用帳戶：從可用的訂閱清單中選取訂用帳戶。
   - **資源群組**：輸入*TestRG*。 選取 [新建] (如果資源群組不存在)。
   - **位置**：從清單中選取 [**美國東部**]。

  - **VPN 連線設定**
    - **連線類型**：選取 [**點對站**]。
    - **用戶端位址空間**：輸入*172.16.201.0/24*。 使用這個點對站連線來連線到 VNet 的 VPN 用戶端，會收到來自指定集區的 IP 位址。

- **閘道閘道子網路設定**
   - **名稱**：使用*GatewaySubnet*的自動填入。
   - **位址範圍**：輸入*192.168.200.0/24*。 

- **閘道組態設定**：
   - **大小**：選取您要使用的閘道 SKU。
   - **路由類型**：選取 [**動態**]。

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>建立虛擬網路和 VPN 閘道

在開始之前，請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

### <a name="part-1-create-a-virtual-network"></a>第 1 部份 - 建立虛擬網路

如果您還沒有虛擬網路 (VNet)，請建立一個。 已提供螢幕擷取畫面做為範例。 請務必將值取代為您自己的值。 若要使用 Azure 入口網站建立 VNet，請使用下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 功能表或 [**首頁**] 頁面上，選取 [**建立資源**]。 [新增] 頁面隨即開啟。

2. 在 [搜尋 Marketplace] 欄位中，輸入「虛擬網路」，然後從傳回的清單中選取 [虛擬網路]。 [虛擬網路] 頁面隨即開啟。

3. 從 [選取部署模型] 清單，選取 [傳統]，然後選取 [建立]。 [建立虛擬網路] 頁面隨即開啟。

4. 在 [建立虛擬網路] 頁面上進行 VNet 設定。 在此頁面上，您會新增您的第一個位址空間和單一子網路位址範圍。 完成 VNet 建立之後，您可以返回並新增其他子網路和位址空間。

   ![建立虛擬網路頁面](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. 從下拉式清單選取您想要使用的 [訂用帳戶]。

6. 選取現有的 [資源群組]。 或者，選取 [新建] 並輸入名稱來建立新的資源群組。 如果您要建立新的資源群組，請根據您計劃的組態值來命名資源群組。 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/management/overview.md#resource-groups)。

7. 選取 VNet 的 [位置]。 此設定會決定您部署到此 VNet 的資源所在的地理位置。

8. 選取 [建立] 來建立 VNet。 從 [通知] 頁面，您會看到**部署進行中**訊息。

8. 在虛擬網路建立好之後，[通知] 頁面上的訊息會變成**部署成功**。 如果您想要輕鬆地在儀表板上找到您的 VNet，請選取 [釘選至儀表板]。 

10. 新增 DNS 伺服器 (選擇性)。 建立虛擬網路之後，您可以新增 DNS 伺服器的 IP 位址，以便進行名稱解析。 您指定的 DNS 伺服器 IP 位址應該是 DNS 伺服器的位址，其可解析 VNet 中資源的名稱。

    若要新增 DNS 伺服器，請從 VNet 頁面選取 [DNS 伺服器]。 然後，輸入所要使用 DNS 伺服器的 IP 位址，並選取 [儲存]。

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>第2部分：建立閘道子網和動態路由閘道

在此步驟中，您會建立一個閘道子網路和一個動態路由閘道。 在傳統部署模型的 Azure 入口網站中，您可以透過相同的組態頁面來建立閘道子網路和閘道。 閘道子網路只能用於閘道服務。 切勿將任何項目直接部署至閘道子網路 (例如虛擬機器或其他服務)。

1. 在 Azure 入口網站中，移至您想要建立閘道的虛擬網路。

2. 在虛擬網路的頁面上，選取 [概觀]，然後在 [VPN 連線] 區段中，選取 [閘道]。

   ![選取以建立閘道](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. 在 [新增 VPN 連線] 頁面上，選取 [點對站]。

   ![點對站連線類型](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. 針對 [用戶端位址空間]，請新增 VPN 用戶端在連線時所要接收的 IP 位址所在的 IP 位址範圍。 使用不會重疊的私人 IP 位址範圍搭配您從其連線的內部部署位置，或搭配您連線至的 VNet。 您可以使用所要使用的私人 IP 位址範圍來覆寫自動填入的範圍。 此範例說明自動填入的範圍。 

   ![用戶端位址空間](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. 選取 [立即建立閘道]，然後選取 [選擇性閘道組態] 以開啟 [閘道組態] 頁面。

   ![選取選擇性閘道組態](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. 從 [閘道組態] 頁面，選取 [子網路] 以新增閘道子網路。 您可以建立小至 /29 的閘道子網路。 不過，我們建議您選取至少 /28 或 /27，建立包含更多位址的較大子網路。 這麼做將回有足夠的位址可供容納您未來可能需要的其他組態。 使用閘道子網路時，避免將網路安全性群組 (NSG) 與閘道子網路產生關聯。 將網路安全性群組與此子網路產生關聯，可能會導致您的 VPN 閘道未能如預期般運作。 選取 [確定] 以儲存此設定。

   ![新增 GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. 選取閘道**大小**。 大小是虛擬網路閘道的閘道 SKU。 在 Azure 入口網站中，預設的 SKU 是**預設**。 如需關於閘道 SKU 的資訊，請參閱[關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

   ![閘道大小](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. 選取閘道的 [路由類型]。 P2S 組態需要**動態**路由類型。 完成此頁面的設定時，請選取 [確定]。

   ![設定路由類型](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. 在 [新增 VPN 連線] 頁面上，選取頁面底部的 [確定]，開始建立虛擬網路閘道。 視您選取的閘道 SKU 而定，VPN 閘道可能需要 45 分鐘的時間才能完成。
 
## <a name="generatecerts"></a>建立憑證

針對點對站 VPN，Azure 會使用憑證來驗證 VPN 用戶端。 您會將根憑證的公開金鑰資訊上傳至 Azure。 公開金鑰就會被視為「受信任」。 用戶端憑證必須從信任的根憑證產生，然後安裝在 Certificates-Current User\Personal\Certificates 憑證存放區中的每部用戶端電腦上。 用戶端在連線至 VNet 時會使用此憑證來進行驗證。 

如果您使用自我簽署憑證，則必須使用特定參數來建立這些憑證。 您可以依循 [PowerShell 和 Windows 10](vpn-gateway-certificates-point-to-site.md) 的指示或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 建立自我簽署憑證。 當您使用自我簽署的根憑證並從自我簽署的根憑證產生用戶端憑證時，請務必遵循這些指示中的步驟。 否則，您建立的憑證將無法與 P2S 連線相容，而且您會收到連線錯誤的訊息。

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>取得根憑證的公開金鑰 (.cer)

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>產生用戶端憑證

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>上傳根憑證 .cer 檔案

建立閘道之後，請將受信任根憑證的 .cer 檔案 (其中包含公開金鑰資訊) 上傳至 Azure 伺服器。 請勿上傳根憑證的私密金鑰。 您上傳憑證之後，Azure 就可以用它來驗證已安裝從受信任根憑證產生之用戶端憑證的用戶端。 如有需要，您稍後可以上傳其他受信任的根憑證檔案 (最多 20 個)。  

1. 在 VNet 頁面的 [VPN 連線] 區段中，選取用戶端圖形以開啟 [點對站 VPN 連線] 頁面。

   ![用戶端](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. 在 [點對站 VPN 連線] 頁面上，選取 [管理憑證] 來開啟 [憑證] 頁面。

   ![憑證頁面](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. 在 [憑證] 頁面上，選取 [上傳] 來開啟 [上傳憑證] 頁面。

    ![上傳憑證頁面](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. 選取資料夾圖形來瀏覽 .cer 檔案。 選取檔案，然後選取 [確定]。 所上傳的憑證就會出現在 [憑證] 頁面上。

   ![Upload certificate](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>設定用戶端

若要使用點對站 VPN 連線至 VNet，每個用戶端都必須安裝用來設定原生 Windows VPN 用戶端的套件。 此設定套件會使用連線至虛擬網路所需的設定來設定原生 Windows VPN 用戶端。

您可以在每個用戶端電腦上使用相同的 VPN 用戶端組態套件，只要版本符合用戶端的架構。 如需用戶端支援的作業系統清單，請參閱[點對站連線常見問題集](#point-to-site-faq)。

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>產生和安裝 VPN 用戶端組態套件

1. 在 Azure 入口網站中，於 VNet 的 [概觀] 頁面的 [VPN 連線] 中，選取用戶端圖形以開啟 [點對站 VPN 連線] 頁面。

2. 從 [點對站 VPN 連線] 頁面，選取與安裝所在用戶端作業系統對應的下載套件：

   * 若為 64 位元用戶端，請選取 [VPN 用戶端 (64 位元)]。
   * 若為 32 位元用戶端，請選取 [VPN 用戶端 (32 位元)]。

   ![下載 VPN 用戶端組態封裝](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. 在產生套件之後，將它下載並安裝在用戶端電腦上。 如果您看到 SmartScreen 快顯視窗，請選取 [更多資訊]，然後選取 [仍要執行]。 您也可以儲存要在其他用戶端電腦上安裝的套件。

### <a name="install-a-client-certificate"></a>安裝用戶端憑證

若要從不同於用來產生用戶端憑證的用戶端電腦建立 P2S 連線，請安裝用戶端憑證。 安裝用戶端憑證時，您需要匯出用戶端憑證時所建立的密碼。 一般而言，只要對憑證按兩下即可安裝該憑證。 如需詳細資訊，請參閱[安裝匯出的用戶端憑證](vpn-gateway-certificates-point-to-site.md#install)。


## <a name="connect-to-your-vnet"></a>連接到您的 VNet

>[!NOTE]
>您必須在您所連線的用戶端電腦上具有系統管理員權限。
>
>

1. 若要連接到您的 VNet，請在用戶端電腦上移至 Azure 入口網站中的 [ **vpn**連線]，並找出您所建立的 vpn 連線。 VPN 連線的名稱會與虛擬網路相同。 選取 [連接]。 如果出現關於憑證的快顯訊息，請選取 [繼續] 以使用較高的權限。

2. 在 [連線] 狀態頁面上，選取 [連線] 以便開始連線。 如果您看到 [選取憑證] 畫面，請確認所顯示的用戶端憑證是否正確。 如果不正確，請從下拉式清單中選取正確的憑證，然後選取 [確定]。

3. 如果連線成功，您將會看到**已連線**通知。


### <a name="troubleshooting-p2s-connections"></a>針對 P2S 連線進行疑難排解

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>驗證 VPN 連線

1. 請確認您的 VPN 連線為作用中狀態。 在用戶端電腦上開啟提升權限的命令提示字元，然後執行 **ipconfig/all**。
2. 檢視結果。 請注意，您接收到的 IP 位址是建立 VNet 時所指定之點對站連線位址範圍內的其中一個位址。 結果應該類似此範例：

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="connect-to-a-virtual-machine"></a>連接到虛擬機器

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>新增或移除受信任的根憑證

您可以從 Azure 新增和移除受信任的根憑證。 當您移除根憑證時，從該根憑證產生憑證的用戶端就無法再進行驗證並連線。 若要讓這些用戶端可以再次進行驗證和連線，您必須安裝從 Azure 信任的根憑證所產生的新用戶端憑證。

### <a name="to-add-a-trusted-root-certificate"></a>若要新增受信任的根憑證

您最多可新增 20 個受信任的根憑證 .cer 檔案至 Azure。 如需指示，請參閱「上傳根憑證 .cer 檔案」。

### <a name="to-remove-a-trusted-root-certificate"></a>移除受信任的根憑證

1. 在 VNet 頁面的 [VPN 連線] 區段中，選取用戶端圖形以開啟 [點對站 VPN 連線] 頁面。

   ![用戶端](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. 在 [點對站 VPN 連線] 頁面上，選取 [管理憑證] 來開啟 [憑證] 頁面。

   ![憑證頁面](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. 在 [憑證] 頁面上，選取您想要移除之憑證旁邊的省略符號，然後選取 [刪除]。

   ![刪除根憑證](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>撤銷用戶端憑證

如有必要，您可以撤銷用戶端憑證。 憑證撤銷清單可讓您選擇性地拒絕以個別的用戶端憑證為基礎的點對站連線。 此方法不同於移除受信任的根憑證。 若您從 Azure 移除受信任的根憑證 .cer，就會撤銷所有由撤銷的根憑證所產生/簽署的用戶端憑證之存取權。 撤銷用戶端憑證，而不是根憑證，可以讓從根憑證產生的憑證繼續用於點對站連線的驗證。

常見的做法是使用根憑證管理小組或組織層級的存取權，然後使用撤銷的用戶端憑證針對個別使用者進行細部的存取控制。

### <a name="to-revoke-a-client-certificate"></a>若要撤銷用戶端憑證

您可以藉由將指紋新增至撤銷清單來撤銷用戶端憑證。

1. 擷取用戶端憑證指紋。 如需詳細資訊，請參閱[做法：擷取憑證的指紋](https://msdn.microsoft.com/library/ms734695.aspx)。
2. 將資訊複製到文字編輯器，並移除其空格，讓其成為連續字串。
3. 移至傳統虛擬網路。 選取 [點對站 VPN 連線]，然後選取 [管理憑證] 來開啟 [憑證] 頁面。
4. 選取 [撤銷清單] 來開啟 [撤銷清單] 頁面。 
5. 選取 [新增憑證] 來開啟 [將憑證新增至撤銷清單] 頁面。
6. 在 [指紋] 中，貼上連續一行文字且不含空格的憑證指紋。 選取 [確定] 來完成。

更新完成之後，憑證無法再用於連線。 嘗試使用此憑證進行連線的用戶端會收到訊息，指出憑證不再有效。

## <a name="point-to-site-faq"></a>點對站常見問題集

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>後續步驟

- 連線完成後，就可以將虛擬機器新增至您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/)。 

- 若要深入了解網路與 Linux 虛擬機器，請參閱 [Azure 與 Linux VM 網路概觀](../virtual-machines/linux/network-overview.md)。

- 如需 P2S 疑難排解的相關資訊，請參閱[針對 Azure 點對站連線進行疑難排解](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
