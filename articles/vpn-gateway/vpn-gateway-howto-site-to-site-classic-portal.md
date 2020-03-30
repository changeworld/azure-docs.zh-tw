---
title: 將內部部署網路連接至 Azure 虛擬網路：站對站 VPN (傳統)：入口網站 | Microsoft Docs
description: 建立透過公用網際網路從內部部署網路至傳統 Azure 虛擬網路的 IPsec 連線。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cherylmc
ms.openlocfilehash: e386e5fc9c4d62266e0ca23869bf30ccaffeb91d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244442"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>使用 Azure 入口網站建立站對站連線 (傳統)


本文說明如何使用 Azure 入口網站來建立從內部部署網路到 VNet 的站對站 VPN 閘道連線。 本文中的步驟適用于經典部署模型，不適用於當前部署模型資源管理器。 您也可從下列清單中選取不同的選項，以使用不同的部署工具或部署模型來建立此組態：

> [!div class="op_single_selector"]
> * [Azure 門戶](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [電源外殼](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Cli](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure 入口網站 (傳統)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

站對站 VPN 閘道連線可用來透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道，將內部部署網路連線到 Azure 虛擬網路。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 確認您想要使用傳統部署模型。 如果您想要使用 Resource Manager 部署模型，請參閱[建立站對站連線 (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。 可能的話，建議使用 Azure Resource Manager 部署模型。
* 確定您有相容的 VPN 裝置以及能夠對其進行設定的人員。 如需相容 VPN 裝置和裝置組態的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。
* 確認您的 VPN 裝置有對外開放的公用 IPv4 位址。
* 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。 當您建立此組態時，您必須指定 IP 位址範圍的首碼，以供 Azure 路由傳送至您的內部部署位置。 內部部署網路的子網路皆不得與您所要連線的虛擬網路子網路重疊。
* 需要 PowerShell 才能指定共用金鑰並創建 VPN 閘道連接。 [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>此練習的範例組態值

本文的範例使用下列值。 您可以使用這些值來建立測試環境，或參考這些值，進一步了解本文中的範例。

* **VNet 名稱：** 測試VNet1
* **位址空間：** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (對此練習是選擇性的)
* **子網：**
  * FrontEnd：10.11.0.0/24
  * BackEnd：10.12.0.0/24 (對此練習是選擇性的)
* **GatewaySubnet：** 10.11.255.0/27
* **資源組：** TestRG1
* **位置：** 美國東部
* **DNS 伺服器：** 10.11.0.3 (對此練習是選擇性的)
* **本機網站名稱︰** Site2
* **用戶端位址空間：** 位於內部部署網站上的位址空間。

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. 創建虛擬網路

當您建立要用於 S2S 連線的虛擬網路時，您必須確定您指定的位址空間，不會與您要連接之本機網站的任何用戶端位址空間重疊。 如果有重疊的子網路，您的連線便無法正常運作。

* 如果您已經有 VNet，請驗證設定是否與您的 VPN 閘道設計相容。 請特別注意任何可能與其他網路重疊的子網路。 

* 如果您還沒有虛擬網路，請建立一個。 已提供螢幕擷取畫面做為範例。 請務必將值取代為您自己的值。

### <a name="to-create-a-virtual-network"></a>建立虛擬網路

1. 從瀏覽器導航到 Azure[門戶](https://portal.azure.com)，並在必要時使用 Azure 帳戶登錄。
2. 按一下 [*] 創建資源*。 在 [搜尋 Marketplace]**** 欄位中，輸入「虛擬網路」。 從返回的清單中找到**虛擬網路**，然後按一下以打開**虛擬網路**頁面。
3. 按一下 **（更改為經典），** 然後按一下"**創建**"。
4. 在 [建立虛擬網路 (傳統)]**** 頁面上進行 VNet 設定。 在此頁面上，您會新增您的第一個位址空間和單一子網路位址範圍。 創建 VNet 後，可以返回並添加其他子網和位址空間。

   ![建立虛擬網路頁面](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "建立虛擬網路頁面")
5. 確認 [訂用帳戶] **** 正確無誤。 您可以使用下拉式清單變更訂用帳戶。
6. 按一下 [資源群組]**** 並選取現有的資源群組，或輸入名稱以建立新的資源群組。 如需資源群組的詳細資訊，請瀏覽 [Azure Resource Manager 概觀](../azure-resource-manager/management/overview.md#resource-groups)。
7. 接著，選取 VNet 的 [位置] **** 設定。 此位置會決定您部署到此 VNet 之資源所在的位置。
8. 按一下 [建立]**** 以建立 VNet。
9. 按一下 [建立] 之後，儀表板上會出現一個反映 VNet 進度的圖格。 建立 VNet 時，此圖格會變更。

## <a name="2-add-additional-address-space"></a><a name="additionaladdress"></a>2. 添加其他位址空間

建立虛擬網路之後，您可以新增其他位址空間。 新增其他位址空間不是 S2S 組態的必要部分，但如果您需要多個位址空間，請使用下列步驟︰

1. 在門戶中查找虛擬網路。
2. 在您的虛擬網路頁面上，按一下 [設定]**** 區段下的 [位址空間]****。
3. 在 [設定空間] 頁面上，按一下 [+新增]**** 並輸入其他位址空間。

## <a name="3-specify-a-dns-server"></a><a name="dns"></a>3. 指定 DNS 伺服器

DNS 設定不是 S2S 組態的必要部分，但如果您想要名稱解析，則需要 DNS。 指定一個值並不會建立新的 DNS 伺服器。 您指定的 DNS 伺服器 IP 位址應該是可以解析您所連線之資源名稱的 DNS 伺服器。 對於範例設定，我們使用了私人 IP 位址。 我們使用的 IP 位址可能不是您 DNS 伺服器的 IP 位址。 請務必使用您自己的值。

建立虛擬網路之後，您可以新增 DNS 伺服器的 IP 位址，以便處理名稱解析。 開啟您的虛擬網路設定，按一下 DNS 伺服器，並新增您要用於名稱解析的 DNS 伺服器 IP 位址。

1. 在門戶中查找虛擬網路。
2. 在您的虛擬網路頁面上，按一下 [設定]**** 區段下的 [DNS 伺服器]****。
3. 新增 DNS 伺服器。
4. 若要儲存您的設定，按一下頁面頂端的 [儲存]****。

## <a name="4-configure-the-local-site"></a><a name="localsite"></a>4. 配置本地網站

本機網站通常是指您的內部部署位置。 它包含您要建立連線之 VPN 裝置的 IP 位址，以及將透過 VPN 閘道路由傳送至 VPN 裝置的 IP 位址範圍。

1. 在 VNet 的頁面上，在 **"設置"** 下，按一下 **"圖表**"。
1. 在**VPN 連接**頁上，按一下 **"您沒有任何現有的 VPN 連接"。按一下此處開始**。
1. 對於**連線類型**，請選擇 **"網站到網站**"。
4. 按一下 [本機網路 - 進行必要設定]**** 以開啟 [本機網路]**** 頁面。 進行設定，然後按一下 [確定]**** 以儲存設定。
   - **名稱︰** 建立可讓您輕鬆識別您的本機網站的名稱。
   - **VPN 閘道 IP 位址：** 這是您內部部署網路之 VPN 裝置的公用 IP 位址。 VPN 裝置需要 IPv4 公用 IP 位址。 為您要連線的 VPN 裝置指定有效的公用 IP 位址。 Azure 必須可聯繫到它。 如果您不知道 VPN 裝置的 IP 位址，您可以一律放入預留位置值 (只要其為有效的公用 IP 位址格式即可)，然後稍後加以變更。
   - **用戶端位址空間︰** 列出您要透過此閘道路由傳送至本機內部部署網路的 IP 位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與虛擬網路要連接的其他網路範圍重疊，或與虛擬網路本身的位址範圍重疊。

   ![本地網站](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "配置本地網站")

按一下 **"確定"** 關閉本地網站頁面。 **不要按一下"確定"以關閉"新建 VPN 連接"頁**。

## <a name="5-configure-the-gateway-subnet"></a><a name="gatewaysubnet"></a>5. 配置閘道子網

您必須為 VPN 閘道建立閘道子網路。 閘道子網路包含 VPN 閘道服務會使用的 IP 位址。


1. 在 [新增 VPN 連線]**** 頁面上，選取 [立即建立閘道]**** 核取方塊。 [選擇性閘道組態] 頁面隨即出現。 如果您未選取此核取方塊，則不會看到用來設定閘道器子網路的頁面。

   ![閘道配置 - 子網、大小、路由類型](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "閘道配置 - 子網、大小、路由類型")
2. 若要開啟 [閘道組態]**** 頁面，請按一下 [選擇性閘道組態 - 子網路、大小和路由類型]****。
3. 在 [閘道組態]**** 頁面上，按一下 [子網路 - 進行必要設定]**** 以開啟 [新增子網路]**** 頁面。 完成配置這些設置後，按一下 **"確定**"。

   ![閘道配置 - 閘道子網](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "閘道配置 - 閘道子網")
4. 在 [新增子網路]**** 頁面上，新增閘道子網路。 您所要建立的 VPN 閘道組態會決定您需要為閘道子網路指定的大小。 雖然可以建立像 /29 這麼小的閘道子網路，但建議您使用 /27 或 /28。 這可建立包含更多位址的較大子網路。 使用較大的閘道子網路可讓您擁有足夠的 IP 位址，以因應未來可能的組態。

   ![新增閘道子網路](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "新增閘道子網路")

## <a name="6-specify-the-sku-and-vpn-type"></a><a name="sku"></a>6. 指定 SKU 和 VPN 類型

1. 選取閘道**大小**。 這是您會用來建立虛擬網路閘道的閘道 SKU。 傳統 VPN 閘道會使用舊式 (舊版) 閘道 SKU。 如需舊版閘道 SKU 的詳細資訊，請參閱[使用虛擬網路閘道 SKU (舊式 SKU)](vpn-gateway-about-skus-legacy.md)。

   ![選擇 SKUL 和 VPN 類型](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "選擇 SKU 和 VPN 類型")
2. 選取閘道的 [路由類型]****。 這也稱為 VPN 類型。 選擇正確的類型非常重要，因為您不能將閘道從一種類型轉換為另一種類型。 您的 VPN 裝置必須與您所選取的路由類型相容。 有關路由類型的詳細資訊，請參閱有關[VPN 閘道設置](vpn-gateway-about-vpn-gateway-settings.md#vpntype)。 您可能會看到參考 'RouteBased' 和 'PolicyBased' VPN 類型的文章。 'Dynamic' 對應到 'RouteBased'，而 'Static' 對應到 'PolicyBased'。
3. 按一下 [確定] **** 來儲存設定。
4. 在 **"新建 VPN 連接"** 頁上，按一下頁面底部的 **"確定"** 以開始部署虛擬網路閘道。 視您選取的 SKU 而定，建立虛擬網路閘道可能需要 45 分鐘的時間。

## <a name="7-configure-your-vpn-device"></a><a name="vpndevice"></a>7. 配置 VPN 設備

內部部署網路的站對站連線需要 VPN 裝置。 在此步驟中，設定 VPN 裝置。 在設定 VPN 裝置時，您需要下列項目：

- 共用金鑰。 這個共同金鑰與您建立站對站 VPN 連線時指定的共用金鑰相同。 在我們的範例中，我們會使用基本的共用金鑰。 我們建議您產生更複雜的金鑰以供使用。
- 虛擬網路閘道的公用 IP 位址。 您可以使用 Azure 入口網站、PowerShell 或 CLI 來檢視公用 IP 位址。

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="8-create-the-connection"></a><a name="CreateConnection"></a>8. 創建連接
在此步驟中，您可以設定共用的金鑰及建立連線。 您設定的金鑰必須是 VPN 裝置組態中使用的相同金鑰。

> [!NOTE]
> 此步驟目前無法不適用於 Azure 入口網站。 您必須使用 Azure PowerShell Cmdlet 的服務管理 (SM) 版本。 有關安裝這些 Cmdlet 的資訊[，請參閱開始之前](#before)。
>

### <a name="step-1-connect-to-your-azure-account"></a>步驟 1： 連線至您的 Azure 帳戶

您必須使用 PowerShell 服務管理模組在本地運行這些命令。 

1. 使用提升許可權打開 PowerShell 主控台。 要切換到服務管理，請使用此命令：

   ```powershell
   azure config mode asm
   ```
2. 連線至您的帳戶。 使用下列範例來協助您連接：

   ```powershell
   Add-AzureAccount
   ```
3. 檢查帳戶的訂用帳戶。

   ```powershell
   Get-AzureSubscription
   ```
4. 如果您有多個訂用帳戶，請選取您要使用的訂用帳戶。

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>步驟 2： 設定共用金鑰及建立連線

在門戶中創建經典 VNet 時（不使用 PowerShell）時，Azure 會將資源組名稱添加到短名稱中。 例如，根據 Azure，您為本練習創建的 VNet 的名稱是"組 TestRG1 TestVNet1"，而不是"TestVNet1"。 PowerShell 需要虛擬網路的全名，而不是門戶中顯示的短名稱。 長名在門戶中不可見。 以下步驟可説明您匯出網路設定檔以獲取虛擬網路名稱的確切值。 

1. 在您的電腦上建立目錄，然後將網路組態檔匯出到該目錄。 在此範例中，會將網路組態檔匯出到 C:\AzureNet。

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. 使用 xml 編輯器開啟網路組態檔，並檢查 'LocalNetworkSite name' 和 'VirtualNetworkSite name' 的值。 修改本練習的示例以反映 xml 中的值。 指定包含空格的名稱時，請使用單引號括住值。

3. 設定共用金鑰及建立連線。 '-SharedKey' 是您產生和指定的值。 在範例中，我們使用的是 'abc123'，但是您可以產生且 (應該) 使用更為複雜的值。 重要的是，您在此指定的值必須與您在設定 VPN 裝置時指定的值相同。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   建立連線後，結果是︰**狀態︰成功**。

## <a name="9-verify-your-connection"></a><a name="verify"></a>9. 驗證您的連接

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

如有連線問題，請參閱左窗格中目錄的 [疑難排解]**** 區段。

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>如何重設 VPN 閘道

如果您遺失一或多個站對站 VPN 通道上的跨單位 VPN 連線，重設 Azure VPN 閘道會很有幫助。 在此情況下，您的所有內部部署 VPN 裝置都會運作正常，但無法使用 Azure VPN 閘道建立 IPsec 通道。 如需相關步驟，請參閱[重設 VPN 閘道](vpn-gateway-resetgw-classic.md#resetclassic)。

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>如何變更閘道 SKU

若需變更閘道 SKU 的步驟，請參閱[調整閘道 SKU 的大小](vpn-gateway-about-SKUS-legacy.md#classicresize)。

## <a name="next-steps"></a>後續步驟

* 一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/)。
* 有關強制隧道的資訊，請參閱[有關強制隧道](vpn-gateway-about-forced-tunneling.md)的資訊。
