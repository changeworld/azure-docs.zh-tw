---
title: 從電腦連線到 VNet-P2S VPN 和原生 Azure 憑證驗證： PowerShell
description: 使用 P2S 和自我簽署或 CA 發出的憑證，將 Windows 和 macOS 用戶端安全地連線至 Azure 虛擬網路。 本文使用 PowerShell。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: b6df7aa919721576aad10d6a476be976ef81df7d
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145866"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>使用原生 Azure 憑證驗證設定 VNet 的點對站 VPN 連線： PowerShell

本文可協助您將執行 Windows、Linux 或 macOS 的個別用戶端安全地連線至 Azure VNet。 當您想要從遠端位置連線到您的 VNet 時，例如從家用或會議住家時，點對站 VPN 連線會很有用。 如果您只有少數用戶端必須連線至 VNet，您也可以使用 P2S，而不使用站對站 VPN。 點對站連線不需要 VPN 裝置或公眾對應 IP 位址。 P2S 會建立透過 SSTP (安全通訊端通道通訊協定) 或 IKEv2 的 VPN 連線。

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="從電腦連線到 Azure VNet 點對站連接圖":::

如需點對站 VPN 的詳細資訊，請參閱 [關於點對站 vpn](point-to-site-about.md)。 若要使用 Azure 入口網站建立此設定，請參閱 [使用 Azure 入口網站設定點對站 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Prerequisites

請確認您有 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，您可以啟用 [MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) 或註冊 [免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> 本文中的許多步驟都可以使用 Azure Cloud Shell。 不過，您無法使用 Cloud Shell 來產生憑證。 此外，若要上傳根憑證公開金鑰，您必須在本機使用 Azure PowerShell，或 Azure 入口網站。
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1.登入

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. 宣告變數

我們會使用這篇文章中的變數，讓您可以輕鬆地變更值以套用至您自己的環境，而不需要自行變更範例。 宣告您想要使用的變數。 您可以使用下列範例，視需要將值取代為您自己的值。 如果您在練習的任何時刻關閉了 PowerShell/Cloud Shell 工作階段，請再次複製值並貼上，以重新宣告變數。

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. 設定 VNet

1. 建立資源群組。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. 建立虛擬網路的子網設定，並將其命名為 *前端* 和 *GatewaySubnet* 。 這些前置詞必須是您宣告的 VNet 位址空間的一部分。

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. 建立虛擬網路

   在此範例中，-DnsServer 伺服器是選擇性的。 指定一個值並不會建立新的 DNS 伺服器。 您指定的 DNS 伺服器 IP 位址應該是可以解析您從 VNet 連線之資源名稱的 DNS 伺服器。 此範例使用私人 IP 位址，但這可能不是您 DNS 伺服器的 IP 位址。 請務必使用您自己的值。 您指定的值會供部署至 VNet 的資源使用，而非供 P2S 連線或 VPN 用戶端使用。

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. 指定您所建立的虛擬網路的變數。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. VPN 閘道必須具有公用 IP 位址。 您會先要求 IP 位址資源，然後在建立虛擬網路閘道時參考它。 建立 VPN 閘道時，系統會將 IP 位址動態指派給此資源。 VPN 閘道目前僅支援 *動態* 公用 IP 位址配置。 您無法要求靜態公用 IP 位址指派。 不過，這不表示之後的 IP 位址變更已指派至您的 VPN 閘道。 公用 IP 位址只會在刪除或重新建立閘道時變更。 它不會因為重新調整、重設或 VPN 閘道的其他內部維護/升級而變更。

   要求動態指派的公用 IP 位址。

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. 建立 VPN 閘道

在此步驟中，您會設定和建立 VNet 的虛擬網路閘道。

* -GatewayType 必須是 **Vpn** ，而且 -VpnType 必須是 **RouteBased** 。
* 使用 -VpnClientProtocol 可指定您想要啟用的通道類型。 通道選項為 **OpenVPN、SSTP** 和 **IKEv2** 。 您可以選擇啟用其中一個或任何支援的組合。 如果您想要啟用多個類型，請指定以逗號分隔的名稱。 OpenVPN 和 SSTP 無法一起啟用。 Android 和 Linux 上的 strongSwan 用戶端以及 iOS 和 OSX 上的原生 IKEv2 VPN 用戶端只會使用 IKEv2 通道來進行連線。 Windows 用戶端會先嘗試 IKEv2，如果無法連線，就會切換回使用 SSTP。 您可以使用 OpenVPN 用戶端連接到 OpenVPN 通道類型。
* 虛擬網路閘道的「基本」 SKU 不支援 IKEv2、OpenVPN 或 RADIUS 驗證。 如果您打算讓 Mac 用戶端連線到您的虛擬網路，請勿使用「基本」SKU。
* 視您選取的[閘道 sku](vpn-gateway-about-vpn-gateway-settings.md) 而定，VPN 閘道可能需要 45 分鐘的時間才能完成。 此範例使用 IKEv2。

1. 設定和建立 VNet 的虛擬網路閘道。 建立閘道大約需要45分鐘。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. 建立閘道之後，您可以使用下列範例來加以查看。 如果您關閉了 PowerShell 或在建立閘道時超時，則可以再次宣告 [變數](#declare) 。

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. 新增 VPN 用戶端位址集區

VPN 閘道完成建立之後，您可以新增 VPN 用戶端位址集區。 VPN 用戶端位址集區是 VPN 用戶端在連線時將從其接收 IP 位址的範圍。 使用不會重疊的私人 IP 位址範圍搭配您從其連線的內部部署位置，或搭配您要連線至的 VNet。

在此範例中，VPN 用戶端位址集區在先前的步驟中會宣告為 [變數](#declare) 。

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. 產生憑證

>[!IMPORTANT]
> 您無法使用 Azure Cloud Shell 產生憑證。 您必須使用本節中所述的其中一個方法。 如果您想要使用 PowerShell，您必須在本機進行安裝。
>

Azure 會使用憑證來驗證 VPN 用戶端的點對站 Vpn。 您會將根憑證的公開金鑰資訊上傳至 Azure。 公開金鑰就會被視為「受信任」。 用戶端憑證必須從信任的根憑證產生，然後安裝在 [憑證-目前使用者/個人憑證] 存放區中的每部用戶端電腦上。 在用戶端初始 VNet 連線時，此憑證用來驗證用戶端。 

如果您使用自我簽署憑證，則必須使用特定參數來建立這些憑證。 您可以依循 [PowerShell 和 Windows 10](vpn-gateway-certificates-point-to-site.md) 的指示建立自我簽署憑證；或者，如果您沒有 Windows 10，可以使用 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)。 產生自我簽署的根憑證和用戶端憑證時，請務必遵循指示中的步驟。 否則，您所產生的憑證將無法與 P2S 連線相容，而且會收到連線錯誤的訊息。

### <a name="root-certificate"></a><a name="cer"></a>根憑證

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. 建立根憑證之後，請將公開憑證資料 (不是私密金鑰) 以 Base64 編碼的 x.509 .cer 檔案的形式 [匯出](vpn-gateway-certificates-point-to-site.md#cer) 。

### <a name="client-certificate"></a><a name="generate"></a>用戶端憑證

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. 建立用戶端憑證之後，請將其 [匯出](vpn-gateway-certificates-point-to-site.md#clientexport) 。 用戶端憑證將會散發至將連接的用戶端電腦。

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. 上傳根憑證公開金鑰資訊

確認 VPN 閘道已完成建立。 完成之後，您可以將受信任根憑證的 .cer 檔案 (其中包含公開金鑰資訊) 上傳至 Azure。 一旦上傳 .cer 檔案，Azure 就可以使用它來驗證已安裝從受信任根憑證產生之用戶端憑證的用戶端。 如有需要，您稍後可以上傳其他受信任的根憑證檔案 (最多總計 20 個檔案)。

>[!NOTE]
> 您無法使用 Azure Cloud Shell 上傳 .cer 檔案。 您可以在本機電腦上使用 PowerShell，也可以使用 [Azure 入口網站步驟](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)。
>

1. 宣告您的憑證名稱的變數，以自己的值取代。

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. 將檔案路徑以您的路徑取代，然後執行 Cmdlet。

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. 將公開金鑰資訊上傳至 Azure。 一旦上傳憑證資訊，Azure 會將它視為受信任的根憑證。 上傳時，請確定您是在電腦本機上執行 PowerShell，您也可以使用 [Azure 入口網站步驟](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)。 您無法使用 Azure Cloud Shell 上傳。

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. 安裝匯出的用戶端憑證

下列步驟可協助您在 Windows 用戶端上安裝。 如需其他用戶端和詳細資訊，請參閱 [安裝用戶端憑證](point-to-site-how-to-vpn-client-install-azure-cert.md)。

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

請確定用戶端憑證已隨著整個憑證鏈結匯出為 .pfx (這是預設值)。 否則，根憑證資訊不存在於用戶端電腦上，而且用戶端將無法正確驗證。

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. 設定 VPN 用戶端

在本節中，您會設定電腦的 native client 以連接到虛擬網路閘道。 例如，當您移至 Windows 電腦上的 VPN 設定時，您可以新增 VPN 連線。 點對站連線需要特定的設定。 這些步驟可協助您使用原生 VPN 用戶端透過點對站連線來連線至虛擬網路，以建立具有特定設定的套件。

您可以使用下列快速範例來產生和安裝用戶端設定套件。 如需有關如何產生和安裝 VPN 用戶端設定檔的套件內容和其他指示的詳細資訊，請參閱 [建立和安裝 vpn 用戶端設定檔](point-to-site-vpn-client-configuration-azure-cert.md)。

如果您需要再次宣告變數，您可以在 [這裡](#declare)找到它們。

### <a name="to-generate-configuration-files"></a>產生設定檔

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>若要安裝用戶端設定套件

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. 連接至 Azure

### <a name="windows-vpn-client"></a>Windows VPN 用戶端

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac VPN 用戶端

從 [網路] 對話方塊，找出您要使用的用戶端設定檔，然後按一下 [連線]  。
如需詳細指示，請參閱[安裝 - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac)。 如果您在連線時發生問題，請確認虛擬網路閘道不是使用「基本」SKU。 針對 Mac 用戶端不支援「基本」SKU。

  ![Mac 連線](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>驗證連接

這些指示適用於 Windows 用戶端。

1. 若要驗證您的 VPN 連線為作用中狀態，請開啟提升權限的命令提示字元，並執行 *ipconfig/all* 。
2. 檢視結果。 請注意，您收到的 IP 位址是您在設定中指定的點對站 VPN 用戶端位址集區內的其中一個位址。 結果類似於此範例：

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>連線至虛擬機器

這些指示適用於 Windows 用戶端。

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* 請確認 VPN 用戶端設定套件是在針對 VNet 指定的 DNS 伺服器 IP 位址之後產生。 如果您已更新 DNS 伺服器 IP 位址，請產生並安裝新的 VPN 用戶端設定套件。

* 請使用 'ipconfig' 來檢查指派給所連線電腦上的乙太網路介面卡之 IPv4 位址。 如果 IP 位址位在您要連線的 VNet 位址範圍內，或在您 VPNClientAddressPool 的位址範圍內，這稱為重疊位址空間。 當您的位址空間以這種方式重疊時，網路流量不會連線到 Azure，它會保留在本機網路上。

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>新增或移除根憑證

您可以從 Azure 新增和移除受信任的根憑證。 當您移除根憑證時，從根憑證產生憑證的用戶端無法進行驗證，而且無法進行連線。 若希望用戶端進行驗證和連線，您需要安裝從 Azure 信任 (已上傳至 Azure) 的根憑證產生的新用戶端憑證。 這些步驟需要在本機電腦上安裝 Azure PowerShell Cmdlet， (不 Azure Cloud Shell) 。 您也可以使用 Azure 入口網站來新增根憑證。

**若要新增：**

您最多可將 20 個根憑證 .cer 檔案新增至 Azure。 下列步驟可協助您新增根憑證。 

1. 準備要上傳的 .cer 檔案：

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. 上傳檔案。 您一次只能上傳一個檔案。

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. 若要確認憑證檔案已上傳：

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**若要移除：**

1. 宣告變數。 修改範例中的變數，以符合您想要移除的憑證。

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. 移除憑證。

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. 使用下列範例確認已成功移除憑證。

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>撤銷或恢復用戶端憑證

您可以撤銷用戶端憑證。 憑證撤銷清單可讓您選擇性地拒絕以個別用戶端憑證為基礎的點對站連線能力。 這與移除受信任的根憑證不同。 若您從 Azure 移除受信任的根憑證 .cer，就會撤銷所有由撤銷的根憑證所產生/簽署的用戶端憑證之存取權。 撤銷用戶端憑證，而不是根憑證，可以繼續使用從根憑證產生的憑證進行驗證。

常見的做法是使用根憑證管理小組或組織層級的存取權，然後使用撤銷的用戶端憑證針對個別使用者進行細部的存取控制。

**撤銷：**

1. 擷取用戶端憑證指紋。 如需詳細資訊，請參閱 [如何取出憑證的憑證指紋](https://msdn.microsoft.com/library/ms734695.aspx)。

1. 將資訊複製到文字編輯器，並移除所有的空格，讓它是連續字串。 這個字串在下一個步驟中會宣告為變數。

1. 宣告變數。 請確定宣告您在上一個步驟中擷取的指紋。

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. 將指紋新增到撤銷的憑證清單。 指紋新增時，您會看到「成功」。

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. 確認指紋已新增到憑證撤銷清單。

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. 新增指紋之後，憑證無法再用於連線接。 嘗試使用此憑證進行連線的用戶端會收到訊息，指出憑證不再有效。

**若要恢復：**

您可以從撤銷的用戶端憑證清單中移除指紋來恢復用戶端憑證。

1. 宣告變數。 請確定針對您要恢復的憑證宣告正確指紋。

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. 從憑證撤銷清單中移除憑證指紋。

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. 檢查指紋是否已從撤銷的清單中移除。

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>點對站常見問題集

如需其他點對站資訊，請參閱 [VPN 閘道點對站常見問題](vpn-gateway-vpn-faq.md#P2S)

## <a name="next-steps"></a>下一步

一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/)。 若要了解網路與虛擬機器的詳細資訊，請參閱 [Azure 與 Linux VM 網路概觀](../virtual-machines/linux/azure-vm-network-overview.md)。

如需 P2S 疑難排解詳細資訊，請參閱[疑難排解：Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
