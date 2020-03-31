---
title: 在 Linux 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體 | Microsoft Docs
description: 如何在 Linux 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cfff05ed52258ee448d83a521b99dca7d356a0f9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061055"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>在 Linux 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體
您可以使用點對站 (P2S) VPN 連線，從 Azure 外部透過 SMB 掛接 Azure 檔案共用，而不需要開啟連接埠 445。 點對站 VPN 連線是 Azure 與個別用戶端之間的 VPN 連線。 若要將 P2S VPN 連線用於 Azure 檔案儲存體，必須為每個要連線的用戶端設定 P2S VPN 連線。 如果您有許多用戶端需要從內部部署網路連線至 Azure 檔案共用，您可以對每個用戶端使用站對站 (S2S) VPN 連線，而不使用點對站連線。 若要深入了解，請參閱[設定站對站 VPN 以用於 Azure 檔案儲存體](storage-files-configure-s2s-vpn.md)。

強烈建議您先閱讀 [Azure 檔案儲存體網路概觀](storage-files-networking-overview.md)，再繼續閱讀此操作說明文章，以充分了解適用於 Azure 檔案儲存體的網路選項。

本文將詳細說明在 Linux 上設定點對站 VPN 以直接在內部部署掛接 Azure 檔案共用的步驟。 如果您想要透過 VPN 來路由傳送 Azure 檔案同步流量，請參閱[設定 Azure 檔案同步 Proxy 和防火牆設定](storage-sync-files-firewall-and-proxy.md)。

## <a name="prerequisites"></a>Prerequisites
- 最新版本的 Azure CLI。 如需如何安裝 Azure CLI 的詳細資訊，請參閱[安裝 Azure PowerShell CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)，並選取您的作業系統。 您也可以在 Linux 上使用 Azure PowerShell 模組，但以下顯示的是適用於 Azure CLI 的指示。

- 您要在內部部署掛接的 Azure 檔案共用。 Azure 檔案共用會部署在儲存體帳戶中，也就是代表儲存體共用集區的管理結構，您可以在此集區中部署多個檔案共用，以及其他儲存體資源 (例如，Blob 容器或佇列)。 您可以在[建立 Azure 檔案共用](storage-how-to-create-file-share.md)中深入了解如何部署 Azure 檔案共用和儲存體帳戶。

- 要在內部部署掛接的 Azure 檔案共用所屬的儲存體帳戶私人端點。 若要深入了解如何建立私人端點，請參閱[設定 Azure 檔案儲存體網路端點](storage-files-networking-endpoints.md?tabs=azure-cli)。 

## <a name="install-required-software"></a>安裝必要軟體
Azure 虛擬網路閘道可使用數個 VPN 通訊協定 (包括 IPsec 和 OpenVPN) 來提供 VPN 連線。 本指南說明如何使用 IPsec 和 strongSwan 套件來提供 Linux 上的支援。 

> 已使用 Ubuntu 18.10 進行驗證。

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>部署虛擬網路 
若要透過點對站 VPN 從內部部署存取您的 Azure 檔案共用和其他 Azure 資源，您必須建立虛擬網路或 VNet。 您將自動建立的 P2S VPN 連線，是內部部署 Linux 機器與此 Azure 虛擬網路之間的聯繫管道。

下列指令碼會建立具有三個子網路的 Azure 虛擬網路：一個用於儲存體帳戶的服務端點，一個用於儲存體帳戶的私人端點 (必須要有此端點，才能存取內部部署儲存體帳戶，而無須針對可能會變更的儲存體帳戶公用 IP 建立自訂路由)，另一個則用於提供 VPN 服務的虛擬網路閘道。 

請記得將 `<region>`、`<resource-group>` 和 `<desired-vnet-name>` 取代為您的環境適用的值。

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>建立 VPN 驗證的憑證
為了讓來自內部部署 Linux 機器的 VPN 連線通過驗證，以存取您的虛擬網路，您必須建立兩個憑證：將提供給虛擬機器閘道的根憑證，以及將以根憑證簽署的用戶端憑證。 下列指令碼會建立必要的憑證。

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>部署虛擬網路閘道
Azure 虛擬網路閘道是您的內部部署 Linux 機器所將連線到的服務。 部署此服務需要兩個基本元件：一個公用 IP，用來識別您的用戶端在世界各地的閘道，以及您稍早建立的根憑證，用來驗證您的用戶端。

請記得將 `<desired-vpn-name-here>` 取代為您要用於這些資源的名稱。

> [!Note]  
> 部署 Azure 虛擬網路閘道最多可能需要 45 分鐘的時間。 在此資源部署期間，此 Bash 指令碼將會進行封鎖，以讓部署完成。 這是預期行為。

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>設定 VPN 用戶端
Azure 虛擬網路閘道會建立可供下載的套件，其中包含在您的內部部署 Linux 機器上初始化 VPN 連線所需的組態檔。 下列指令碼會將您建立的憑證放在正確的位置中，並使用可下載套件中的組態檔所含的正確值來設定 `ipsec.conf` 檔案。

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>掛接 Azure 檔案共用
在您已設定點對站 VPN 後，即可掛接 Azure 檔案共用。 下列範例將非持續性地掛接共用。 若要持續性地掛接，請參閱[搭配 Linux 使用 Azure 檔案共用](storage-how-to-use-files-linux.md)。 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>另請參閱
- [Azure 檔案儲存體網路概觀](storage-files-networking-overview.md)
- [在 Windows 上設定點對站 (P2S) VPN 以用於 Azure 檔案儲存體](storage-files-configure-p2s-vpn-windows.md)
- [設定站對站 (S2S) VPN 以用於 Azure 檔案儲存體](storage-files-configure-s2s-vpn.md)