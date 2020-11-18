---
title: 使用 P2S 將電腦連線到虛擬網路：憑證驗證： Azure 入口網站傳統
titleSuffix: Azure VPN Gateway
description: 使用 Azure 入口網站建立傳統的點對站 VPN 閘道連線。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657069"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>使用憑證驗證設定點對站連線 (傳統)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

本文說明如何建立具有點對站連線的 VNet。 您可以使用 Azure 入口網站，以傳統部署模型建立此 VNet。 此組態會使用自我簽署或 CA 核發的憑證來驗證連線用戶端。 您也可以使用下列文章中所述的選項，透過不同的部署工具或模型來建立這個組態：

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

## <a name="settings-and-requirements"></a>設定和需求

### <a name="requirements"></a>需求

點對站憑證驗證連線需要下列專案。 本文中的步驟可協助您建立它們。

* 動態 VPN 閘道。
* 已上傳至 Azure 之根憑證的公開金鑰 (.cer 檔案)。 此金鑰會被視為受信任的憑證並且用於驗證。
* 用戶端憑證是從根憑證產生，並安裝在每部即將連線的用戶端電腦上。 此憑證使用於用戶端憑證。
* 必須在每部連線的用戶端電腦上產生並安裝 VPN 用戶端組態套件。 用戶端組態套件會使用連線到 VNet 的必要資訊，設定已在作業系統上的原生 VPN 用戶端。

點對站連線不需要 VPN 裝置或內部部署公眾對應 IP 位址。 已透過 SSTP (安全通訊端通道通訊協定) 建立 VPN 連線。 我們在伺服器端上支援 SSTP 1.0、1.1 和 1.2 版。 用戶端會決定要使用的版本。 若為 Windows 8.1 和更新版本，SSTP 預設使用 1.2。

如需詳細資訊，請參閱 [關於點對站連接](point-to-site-about.md) 和 [常見問題](#faq)。

### <a name="example-settings"></a>範例設定

請使用下列值來建立測試環境，或參考這些值來進一步了解本文中的範例：

* **資源群組：** >testrg
* **VNet 名稱：** VNet1
* **位址空間：** 192.168.0.0/16 <br>在此範例中，我們只使用一個位址空間。 您可以針對 VNet 使用一個以上的位址空間。
* **子網路名稱：** FrontEnd
* **子網路位址範圍：** 192.168.1.0/24
* **GatewaySubnet：** 10.11.255.0/27
* **區域：** (美國) 美國東部
* **用戶端位址空間：** 172.16.201.0/24 <br> 使用這個點對站連線來連線到 VNet 的 VPN 用戶端，會收到來自指定集區的 IP 位址。
* **連線類型**：選取 **點對站**。
* **GatewaySubnet 位址範圍 (CIDR 區塊) ：** 192.168.200.0/24

在開始之前，請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

## <a name="create-a-virtual-network"></a><a name="vnet"></a>建立虛擬網路

如果您已經有 VNet，請驗證設定是否與您的 VPN 閘道設計相容。 請特別注意任何可能與其他網路重疊的子網路。

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>建立 VPN 閘道

1. 流覽至您所建立的 VNet。
1. 在 [VNet] 頁面的 [設定] 底下，選取 [ **閘道**]。 在 [ **閘道** ] 頁面上，您可以看到虛擬網路的閘道。 此虛擬網路還沒有閘道。 按一下 [ **按一下這裡新增連線與閘道**] 的附注。
1. 在 [ **設定 VPN 連接和閘道** ] 頁面上，選取下列設定：

   * 連線類型：點對站
   * 用戶端位址空間：新增 VPN 用戶端在連線時接收 IP 位址的 IP 位址範圍。 使用不會重疊的私人 IP 位址範圍搭配您從其連線的內部部署位置，或搭配您連線至的 VNet。
1. 將 [ **不要設定閘道** ] 核取方塊保留為未選取狀態。 我們將建立閘道。
1. 在頁面底部，選取 **[下一步：閘道 >]**。
1. 在 [ **閘道** ] 索引標籤上，選取下列值：

   * **大小：** 大小是虛擬網路閘道的閘道 SKU。 在 Azure 入口網站中，預設的 SKU 是 **預設**。 如需閘道 Sku 的詳細資訊，請參閱 [關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。
   * **路由類型：** 您必須選取 [ **動態** ] 以進行點對站設定。 靜態路由將無法運作。
   * **閘道子網：** 此欄位已自動填入。 您無法變更名稱。 如果您嘗試使用 PowerShell 或任何其他方式來變更名稱，閘道將無法正常運作。
   * **位址範圍 (CIDR 區塊) ：** 雖然您可以建立小至/29 的閘道子網，但我們建議您選取至少/28 或/27，建立包含更多位址的較大子網。 這麼做將回有足夠的位址可供容納您未來可能需要的其他組態。 使用閘道子網路時，避免將網路安全性群組 (NSG) 與閘道子網路產生關聯。 將網路安全性群組與此子網路產生關聯，可能會導致您的 VPN 閘道未能如預期般運作。
1. 選取 [檢閱 + 建立]  以驗證您的設定。
1. 驗證通過後，選取 [ **建立**]。 視您選取的閘道 SKU 而定，VPN 閘道可能需要 45 分鐘的時間才能完成。

## <a name="create-certificates"></a><a name="generatecerts"></a>建立憑證

針對點對站 VPN，Azure 會使用憑證來驗證 VPN 用戶端。 您會將根憑證的公開金鑰資訊上傳至 Azure。 公開金鑰接著會被視為 *受信任*。 用戶端憑證必須從信任的根憑證產生，然後安裝在 Certificates-Current User\Personal\Certificates 憑證存放區中的每部用戶端電腦上。 用戶端在連線至 VNet 時會使用此憑證來進行驗證。 

如果您使用自我簽署憑證，則必須使用特定參數來建立這些憑證。 您可以依循 [PowerShell 和 Windows 10](vpn-gateway-certificates-point-to-site.md) 的指示或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 建立自我簽署憑證。 當您使用自我簽署的根憑證並從自我簽署的根憑證產生用戶端憑證時，請務必遵循這些指示中的步驟。 否則，您建立的憑證將無法與 P2S 連線相容，而且您會收到連線錯誤的訊息。

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>取得根憑證的公開金鑰 (.cer)

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>產生用戶端憑證

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>上傳根憑證 .cer 檔案

建立閘道之後，請將受信任根憑證的 .cer 檔案 (其中包含公開金鑰資訊) 上傳至 Azure 伺服器。 請勿上傳根憑證的私密金鑰。 您上傳憑證之後，Azure 就可以用它來驗證已安裝從受信任根憑證產生之用戶端憑證的用戶端。 如有需要，您稍後可以上傳其他受信任的根憑證檔案 (最多 20 個)。

1. 流覽至您所建立的虛擬網路。
1. 在 [ **設定**] 底下，選取 [ **點對站** 連線]。
1. 選取 [ **管理憑證**]。
1. 選取 [上傳] 。
1. 在 [上 **傳憑證** ] 窗格中，選取資料夾圖示，然後流覽至您想要上傳的憑證。
1. 選取 [上傳] 。
1. 憑證上傳成功之後，您可以在 [管理憑證] 頁面上加以查看。 您可能 **需要選取 [** 重新整理]，才能查看您剛剛上傳的憑證。

## <a name="configure-the-client"></a>設定用戶端

若要使用點對站 VPN 連線至 VNet，每個用戶端都必須安裝用來設定原生 Windows VPN 用戶端的套件。 此設定套件會使用連線至虛擬網路所需的設定來設定原生 Windows VPN 用戶端。

您可以在每個用戶端電腦上使用相同的 VPN 用戶端組態套件，只要版本符合用戶端的架構。 如需支援的用戶端作業系統清單，請參閱 [關於點對站連接](point-to-site-about.md) 和 [常見問題](#faq)。

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>產生和安裝 VPN 用戶端組態套件

1. 流覽至您 VNet 的 **點對站** 連線設定。
1. 在頁面頂端，選取對應至將安裝它之用戶端作業系統的下載套件：

   * 若為64位用戶端，請選取 **VPN 用戶端 (64 位)**。
   * 若為32位用戶端，請選取 **VPN 用戶端 (32 位)**。

1. Azure 會使用用戶端所需的特定設定產生套件。 每次變更 VNet 或閘道時，您都需要下載新的用戶端設定套件，並將其安裝在用戶端電腦上。
1. 封裝產生之後，請選取 [ **下載**]。
1. 在用戶端電腦上安裝用戶端設定套件。 安裝時，如果您看到 SmartScreen 快顯視窗，指出 Windows 已保護您的電腦，請選取 [ **其他資訊**]，然後選取 [ **仍要執行**]。 您也可以儲存要在其他用戶端電腦上安裝的套件。

### <a name="install-a-client-certificate"></a>安裝用戶端憑證

在此練習中，當您產生用戶端憑證時，該憑證會自動安裝在您的電腦上。 若要從不同于用來產生用戶端憑證的用戶端電腦建立 P2S 連接，您必須在該電腦上安裝產生的用戶端憑證。

安裝用戶端憑證時，您需要匯出用戶端憑證時所建立的密碼。 一般而言，只要對憑證按兩下即可安裝該憑證。 如需詳細資訊，請參閱 [安裝匯出的用戶端憑證](vpn-gateway-certificates-point-to-site.md#install)。

## <a name="connect-to-your-vnet"></a>連接到您的 VNet

>[!NOTE]
>您必須在您所連線的用戶端電腦上具有系統管理員權限。
>

1. 在用戶端電腦上，移至 [VPN 設定]。
1. 選取您所建立的 VPN。 如果您使用範例設定，連接將會標示為 [ **群組 >testrg VNet1**]。
1. 選取 [連接]  。
1. 在 [Windows Azure 虛擬網路] 方塊中，選取 **[連線]**。 如果出現有關憑證的快顯訊息，請選取 [ **繼續** ] 以使用較高的許可權，並選取 **[是]** 以接受設定變更。
1. 當您的連線成功時，您會看到 **連線** 的通知。

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>驗證 VPN 連線

1. 請確認您的 VPN 連線為作用中狀態。 在用戶端電腦上開啟提升權限的命令提示字元，然後執行 **ipconfig/all**。
1. 檢視結果。 請注意，您接收到的 IP 位址是建立 VNet 時所指定之點對站連線位址範圍內的其中一個位址。 結果應該類似此範例：

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

## <a name="to-connect-to-a-virtual-machine"></a>連線至虛擬機器

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>新增或移除受信任的根憑證

您可以從 Azure 新增和移除受信任的根憑證。 當您移除根憑證時，從該根憑證產生憑證的用戶端就無法再進行驗證並連線。 若要讓這些用戶端可以再次進行驗證和連線，您必須安裝從 Azure 信任的根憑證所產生的新用戶端憑證。

### <a name="add-a-trusted-root-certificate"></a>新增受信任的根憑證

您可以使用您用來新增第一個受信任根憑證的相同程式，將最多20個受信任的根憑證 .cer 檔案新增至 Azure。

### <a name="remove-a-trusted-root-certificate"></a>移除受信任的根憑證

1. 在 VNet 頁面的 [ **點對站** 連線] 區段中，選取 [ **管理憑證**]。
1. 選取您要移除之憑證旁邊的省略號，然後選取 [ **刪除**]。

## <a name="to-revoke-a-client-certificate"></a>若要撤銷用戶端憑證

如有必要，您可以撤銷用戶端憑證。 憑證撤銷清單可讓您選擇性地拒絕以個別的用戶端憑證為基礎的點對站連線。 此方法不同於移除受信任的根憑證。 若您從 Azure 移除受信任的根憑證 .cer，就會撤銷所有由撤銷的根憑證所產生/簽署的用戶端憑證之存取權。 撤銷用戶端憑證，而不是根憑證，可以讓從根憑證產生的憑證繼續用於點對站連線的驗證。

常見的做法是使用根憑證管理小組或組織層級的存取權，然後使用撤銷的用戶端憑證針對個別使用者進行細部的存取控制。

您可以藉由將指紋新增至撤銷清單來撤銷用戶端憑證。

1. 擷取用戶端憑證指紋。 如需詳細資訊，請參閱[做法：擷取憑證的指紋](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)。
1. 將資訊複製到文字編輯器，並移除其空格，讓其成為連續字串。
1. 流覽至 **點對站 VPN** 連線，然後選取 [ **管理憑證**]。
1. 選取 [撤銷清單] 來開啟 [撤銷清單] 頁面。
1. 在 [指紋] 中，貼上連續一行文字且不含空格的憑證指紋。
1. 選取 [ **+ 新增至清單** ]，將憑證指紋新增至 (CRL) 的憑證撤銷清單。

更新完成之後，憑證無法再用於連線。 嘗試使用此憑證進行連線的用戶端會收到訊息，指出憑證不再有效。

## <a name="faq"></a><a name="faq"></a>常見問題集

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>後續步驟

* 連線完成後，就可以將虛擬機器新增至您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](../index.yml)。

* 若要深入了解網路與 Linux 虛擬機器，請參閱 [Azure 與 Linux VM 網路概觀](../virtual-machines/network-overview.md)。

* 如需 P2S 疑難排解的相關資訊，請參閱[針對 Azure 點對站連線進行疑難排解](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。