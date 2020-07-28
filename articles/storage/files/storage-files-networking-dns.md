---
title: 設定 Azure 檔案儲存體的 DNS 轉送 | Microsoft Docs
description: Azure 檔案儲存體的網路功能選項概觀。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6404115e64ba0ac1f65ba1cfc8d26604f1ce9cfa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509960"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>設定 Azure 檔案儲存體的 DNS 轉送
Azure 檔案儲存體可讓您針對包含檔案共用的儲存體帳戶建立私人端點。 雖然適用於許多不同的應用程式，但私人端點特別適用於透過 VPN 或 ExpressRoute 連線，使用私人對等互連從內部部署網路連線至您的 Azure 檔案共用。 

為了讓儲存體帳戶的連線通過您的網路通道，您儲存體帳戶的完整網域名稱 (FQDN) 必須解析為您私人端點的私人 IP 位址。 若要達到此目的，您必須將儲存體端點尾碼 (公用雲端區域適用 `core.windows.net`) 轉送至您虛擬網路內可存取的 Azure 私人 DNS 服務。 本指南將說明如何設定 DNS 轉送，以適當地解析為您儲存體帳戶的私人端點 IP 位址。

強烈建議您先閱讀[規劃 Azure 檔案服務部署](storage-files-planning.md)和 [Azure 檔案儲存體的網路功能考量](storage-files-networking-overview.md)，再完成本文章中描述的步驟。

## <a name="overview"></a>概觀
Azure 檔案儲存體提供兩種主要的端點類型來存取 Azure 檔案共用： 
- 公用端點，具有公用 IP 位址，並可從全球任何地方存取。
- 私人端點，存在於虛擬網路內，並具有該虛擬網路位址空間內的私人 IP 位址。

公用和私人端點都存在於 Azure 儲存體帳戶上。 儲存體帳戶是一種管理構造，所代表的是儲存體的共用集區，您可以在此集區中部署多個檔案共用，以及其他儲存體資源 (例如，Blob 容器或佇列)。

每個儲存體帳戶都有完整網域名稱 (FQDN)。 針對公用雲端區域，此 FQDN 會遵循 `storageaccount.file.core.windows.net` 模式，其中 `storageaccount` 是儲存體帳戶的名稱。 當您對此名稱提出要求時 (例如，使用 SMB 在工作站上掛接共用)，您的作業系統會執行 DNS 查閱，將完整網域名稱解析成 IP 位址，以便用來作為傳送 SMB 要求的目標。

根據預設，`storageaccount.file.core.windows.net` 會解析為公用端點的 IP 位址。 儲存體帳戶的公用端點裝載於 Azure 儲存體叢集上，該叢集會裝載許多其他儲存體帳戶的公用端點。 當您建立私人端點時，私人 DNS 區域會連結至其加入的虛擬網路，並且包含將 `storageaccount.file.core.windows.net` 對應至 A 記錄項目的 CNAME，此 A 記錄項目代表儲存體帳戶私人端點的私人 IP 位址。 這可讓您在虛擬網路內使用 `storageaccount.file.core.windows.net` FQDN，並將其解析為私人端點的 IP 位址。

因為我們的最終目標是要使用網路通道 (例如 VPN 或 ExpressRoute 連線) 從內部部署環境存取裝載於儲存體帳戶內的 Azure 檔案共用，所以您必須設定內部部署 DNS 伺服器，將針對 Azure 檔案儲存體服務提出的要求轉送至 Azure 私人 DNS 服務。 若要完成這項操作，您需要設定 `*.core.windows.net` (或適用於美國政府、德國或中國雲端的適當儲存體端點尾碼) 的「條件式轉送」  ，以將其轉送至裝載於 Azure 虛擬網路內的 DNS 伺服器。 此 DNS 伺服器接著會以遞迴方式將要求轉送至 Azure 的私人 DNS 服務，以將儲存體帳戶的完整網域名稱解析為適當的私人 IP 位址。

若要設定 Azure 檔案儲存體的 DNS 轉送，您需要執行虛擬機器來裝載用於轉送要求的 DNS 伺服器，不過，這是一次性步驟，適用於虛擬網路中裝載的所有 Azure 檔案共用。 此外，這不是 Azure 檔案儲存體的專屬需求 - 只要您想要從內部部署環境存取的 Azure 服務支援私人端點，皆可利用您將在本指南中設定的 DNS 轉送：Azure Blob 儲存體、SQL Azure、Cosmos DB 等。 

本指南將說明為 Azure 儲存體端點設定 DNS 轉送的步驟，因此，除了 Azure 檔案儲存體以外，其他所有 Azure 儲存體服務 (Azure Blob 儲存體、Azure 資料表儲存體、Azure 佇列儲存體等) 的 DNS 名稱解析要求也將會轉送到 Azure 的私人 DNS 服務。 如有需要，您也可以為其他 Azure 服務新增其他端點。 送回您內部部署 DNS 伺服器的 DNS 轉送也會一併設定，好讓虛擬網路內的雲端資源 (例如 DFS-N 伺服器) 能夠解析內部部署機器名稱。 

## <a name="prerequisites"></a>Prerequisites
您必須完成下列步驟，才能設定以 Azure 檔案儲存體為目標的 DNS 轉送：

- 一個儲存體帳戶，其中包含您想要掛接的 Azure 檔案共用。 若要了解如何建立儲存體帳戶和 Azure 檔案共用，請參閱[建立 Azure 檔案共用](storage-how-to-create-file-share.md)。
- 儲存體帳戶的私人端點。 若要了解如何建立 Azure 檔案儲存體的私人端點，請參閱[建立私人端點](storage-files-networking-endpoints.md#create-a-private-endpoint)。
- [Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)的最新版本。

> [!Important]  
> 本指南假設您使用的 DNS 伺服器位在內部部署環境中的 Windows Server 內。 本指南中所述的所有步驟可用於任何 DNS 伺服器，而非僅限於 Windows DNS 伺服器。

## <a name="manually-configuring-dns-forwarding"></a>手動設定 DNS 轉送
如果您的 Azure 虛擬網路中已有 DNS 伺服器，或是您只是想要透過組織使用的任何方法，將自己的虛擬機器部署為 DNS 伺服器，您可以使用內建的 DNS 伺服器 PowerShell Cmdlet 來手動設定 DNS。

在您的內部部署 DNS 伺服器上，使用 `Add-DnsServerConditionalForwarderZone` 建立條件轉寄站。 此條件式轉寄站必須部署在所有內部部署 DNS 伺服器上，才能正確且有效地將流量轉送至 Azure。 請記得將 `<azure-dns-server-ip>` 取代為您的環境適用的 IP 位址。

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

您也需要在 Azure 虛擬網路內的 DNS 伺服器上準備轉寄站，讓儲存體帳戶 DNS 區域的要求可導向至 Azure 私人 DNS 服務 (保留 IP 位址 `168.63.129.16` 的前方)。 (如果您是在不同的 PowerShell 工作階段中執行命令，請記得填入 `$storageAccountEndpoint`)。

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>使用 Azure 檔案儲存體混合式模組來設定 DNS 轉送
為了盡可能簡化 DNS 轉送的設定，我們在 Azure 檔案儲存體混合式模組中提供自動化功能。 在此課程模組中提供的 DNS 操作 Cmdlet，可協助您在 Azure 虛擬網路中部署 DNS 伺服器，並更新您的內部部署 DNS 伺服器來轉送至其中。 

如果您從未使用過 Azure 檔案儲存體混合式模組，您必須先將其安裝在您的工作站上。 下載 Azure 檔案儲存體混合式 PowerShell 模組的[最新版本](https://github.com/Azure-Samples/azure-files-samples/releases)：

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

部署 DNS 轉送解決方案有兩個步驟：建立 DNS 轉送規則集 (定義您想要轉送要求到其中的 Azure 服務)，以及 DNS 轉寄站的實際部署。 

下列範例會將要求轉送至儲存體帳戶，包括以 Azure 檔案儲存體、Azure Blob 儲存體、Azure 資料表儲存體和 Azure 佇列儲存體為目標的要求。 如有需要，您可以透過 `New-AzDnsForwardingRuleSet` Cmdlet 的 `-AzureEndpoints` 參數，將其他 Azure 服務的轉送新增至規則。 請記得將 `<virtual-network-resource-group>`、`<virtual-network-name>` 和 `<subnet-name>` 取代為您的環境適用的值。

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

此外，您可能會發現提供幾個額外參數的好處或必要性：

| 參數名稱 | 類型 | 描述 |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | 根據預設，DNS 伺服器會部署到與虛擬網路相同的資源群組中。 如果這不是您想要的，此參數可讓您挑選要將這些伺服器部署到其中的替代資源群組。 |
| `DnsForwarderRootName` | `string` | 根據預設，部署在 Azure 中的 DNS 伺服器具有 `DnsFwder-*` 名稱，其中的星號會由列舉程式填入。 此參數會變更該名稱的根 (亦即 `DnsFwder`)。 |
| `VmTemporaryPassword` | `SecureString` | 根據預設，在 VM 加入網域之前，系統會為 VM 的暫存預設帳戶選擇隨機密碼。 加入網域之後，預設帳戶就會停用。 |
| `DomainToJoin` | `string` | DNS VM 要加入的網域。 根據預設，系統會依據您執行 Cmdlet 的電腦網域來選擇此網域。 |
| `DnsForwarderRedundancyCount` | `int` | 要為您虛擬網路部署的 DNS VM 數目。 根據預設，`New-AzDnsForwarder` 會在 Azure 虛擬網路中的可用性設定組中部署兩個 DNS 伺服器，以確保能提供備援。 此數字可視需要修改。 |
| `OnPremDnsHostNames` | `HashSet<string>` | 要在其中建立轉寄站的內部部署 DNS 主機名稱清單 (手動指定)。 當您不想要在所有內部部署 DNS 伺服器上套用轉寄站時 (例如當您的用戶端範圍具有手動指定的 DNS 名稱時)，即適用此參數。 |
| `Credential` | `PSCredential` | 更新 DNS 伺服器時所要使用的認證。 當您登入的使用者帳戶沒有修改 DNS 設定的權限時，即適用此參數。 |
| `SkipParentDomain` | `SwitchParameter` | 根據預設，DNS 轉寄站會套用至您環境中的最高層級網域。 例如，如果 `northamerica.corp.contoso.com` 是 `corp.contoso.com` 的子網域，則系統會針對與 `corp.contoso.com` 相關聯的 DNS 伺服器建立轉寄站。 此參數會讓轉寄站建立在 `northamerica.corp.contoso.com` 中。 |

## <a name="confirm-dns-forwarders"></a>確認 DNS 轉寄站
在測試並查看您是否已成功套用 DNS 轉寄站之前，建議您在本機工作站上使用 `Clear-DnsClientCache` 來清除 DNS 快取。 若要測試並查看您是否可以成功解析儲存體帳戶的完整網域名稱，請使用 `Resolve-DnsName` 或 `nslookup`。

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

如果名稱解析成功，您應該會看到已解析的 IP 位址符合儲存體帳戶的 IP 位址。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

如果您已經設定 VPN 或 ExpressRoute 連線，您也可以使用 `Test-NetConnection`來查看是否可以成功地對儲存體帳戶進行 TCP 連線。

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>另請參閱
- [規劃 Azure 檔案部署](storage-files-planning.md)
- [Azure 檔案儲存體的網路功能考量](storage-files-networking-overview.md)
- [設定 Azure 檔案儲存體網路端點](storage-files-networking-endpoints.md)