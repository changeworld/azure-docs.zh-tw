---
title: 在 Windows 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體 | Microsoft Docs
description: 如何在 Windows 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95386af4522adca1d65e04b01c2a349a80e9ab8a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81273472"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>在 Windows 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體
您可以使用點對站 (P2S) VPN 連線，從 Azure 外部透過 SMB 掛接 Azure 檔案共用，而不需要開啟連接埠 445。 點對站 VPN 連線是 Azure 與個別用戶端之間的 VPN 連線。 若要將 P2S VPN 連線用於 Azure 檔案儲存體，必須為每個要連線的用戶端設定 P2S VPN 連線。 如果您有許多用戶端需要從內部部署網路連線至 Azure 檔案共用，您可以對每個用戶端使用站對站 (S2S) VPN 連線，而不使用點對站連線。 若要深入了解，請參閱[設定站對站 VPN 以用於 Azure 檔案儲存體](storage-files-configure-s2s-vpn.md)。

強烈建議您先閱讀[直接 Azure 檔案共用存取的網路考量](storage-files-networking-overview.md)，再繼續閱讀此操作說明文章，以充分了解適用於 Azure 檔案儲存體的網路選項。

本文將詳細說明在 Windows (Windows 用戶端和 Windows Server) 上設定點對站 VPN 以直接在內部部署掛接 Azure 檔案共用的步驟。 如果您想要透過 VPN 來路由傳送 Azure 檔案同步流量，請參閱[設定 Azure 檔案同步 Proxy 和防火牆設定](storage-sync-files-firewall-and-proxy.md)。

## <a name="prerequisites"></a>Prerequisites
- 最新版本的 Azure PowerShell 模組。 如需如何安裝 Azure PowerShell 的詳細資訊，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)，並選取您的作業系統。 您也可以在 Windows 上使用 Azure CLI，但以下顯示的是適用於 Azure PowerShell 的指示。

- 您要在內部部署掛接的 Azure 檔案共用。 Azure 檔案共用會部署在儲存體帳戶中，也就是代表儲存體共用集區的管理結構，您可以在此集區中部署多個檔案共用，以及其他儲存體資源 (例如，Blob 容器或佇列)。 您可以在[建立 Azure 檔案共用](storage-how-to-create-file-share.md)中深入了解如何部署 Azure 檔案共用和儲存體帳戶。

- 要在內部部署掛接的 Azure 檔案共用所屬的儲存體帳戶私人端點。 若要深入了解如何建立私人端點，請參閱[設定 Azure 檔案儲存體網路端點](storage-files-networking-endpoints.md?tabs=azure-powershell)。 

## <a name="deploy-a-virtual-network"></a>部署虛擬網路
若要透過點對站 VPN 從內部部署存取您的 Azure 檔案共用和其他 Azure 資源，您必須建立虛擬網路或 VNet。 您將自動建立的 P2S VPN 連線，是內部部署 Windows 機器與此 Azure 虛擬網路之間的聯繫管道。

下列 PowerShell 會建立具有三個子網路的 Azure 虛擬網路：一個用於儲存體帳戶的服務端點，一個用於儲存體帳戶的私人端點 (必須要有此端點，才能存取內部部署儲存體帳戶，而無須針對可能會變更的儲存體帳戶公用 IP 建立自訂路由)，另一個則用於提供 VPN 服務的虛擬網路閘道。 

請記得將 `<region>`、`<resource-group>` 和 `<desired-vnet-name>` 取代為您的環境適用的值。

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>建立 VPN 驗證的根憑證
為了讓來自內部部署 Windows 機器的 VPN 連線通過驗證，以存取您的虛擬網路，您必須建立兩個憑證：將提供給虛擬機器閘道的根憑證，以及將以根憑證簽署的用戶端憑證。 下列 PowerShell 會建立根憑證；建立 Azure 虛擬網路閘道之後，會使用來自閘道的資訊建立用戶端憑證。 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>部署虛擬網路閘道
Azure 虛擬網路閘道是您的內部部署 Windows 機器所將連線到的服務。 部署此服務需要兩個基本元件：一個公用 IP，用來識別您的用戶端在世界各地的閘道，以及您稍早建立的根憑證，用來驗證您的用戶端。

請記得將 `<desired-vpn-name-here>` 取代為您要用於這些資源的名稱。

> [!Note]  
> 部署 Azure 虛擬網路閘道最多可能需要 45 分鐘的時間。 在此資源部署期間，此 PowerShell 指令碼將會進行封鎖，以讓部署完成。 這是預期行為。

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>建立用戶端憑證
用戶端憑證會使用虛擬網路閘道的 URI 來建立。 此憑證會以您先前建立的根憑證簽署。

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>設定 VPN 用戶端
Azure 虛擬網路閘道會建立可供下載的套件，其中包含在您的內部部署 Windows 機器上初始化 VPN 連線所需的組態檔。 我們將使用 Windows 10/Windows Server 2016+ 的 [Always On VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) 功能來設定 VPN 連線。 此套件也包含可執行檔套件，會在需要時設定舊版 Windows VPN 用戶端。 本指南使用 Always On VPN，而不是舊版 Windows VPN 用戶端，因為 Always On VPN 用戶端可讓使用者直接從 Azure VPN 進行連線/中斷連線，而無須具備機器的系統管理員權限。 

下列指令碼會安裝對虛擬網路閘道進行驗證、下載及安裝 VPN 套件所需的用戶端憑證。 請記得將 `<computer1>` 和 `<computer2>` 取代為所需的電腦。 您可以視需要在不限數量的機器上執行此指令碼，只要將更多 PowerShell 工作階段新增至 `$sessions` 陣列即可。 您的使用帳戶必須是每個機器上的系統管理員。 如果其中一個機器是您執行指令碼的本機電腦，則您必須從提升權限的 PowerShell 工作階段執行指令碼。 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>掛接 Azure 檔案共用
在您已設定點對站 VPN 後，您可以使用它在您透過 PowerShell 設定的電腦上掛接 Azure 檔案共用。 下列範例會掛接共用、列出共用的根目錄以證明共用已確實掛接，以及卸載共用。 可惜的是，您無法透過 PowerShell 遠端處理持續性地掛接共用。 若要持續性地掛接，請參閱[搭配 Windows 使用 Azure 檔案共用](storage-how-to-use-files-windows.md)。 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>另請參閱
- [直接 Azure 檔案共用存取的網路考量](storage-files-networking-overview.md)
- [在 Linux 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體](storage-files-configure-p2s-vpn-linux.md)
- [設定站對站 (S2S) VPN 以用於 Azure 檔案儲存體](storage-files-configure-s2s-vpn.md)
