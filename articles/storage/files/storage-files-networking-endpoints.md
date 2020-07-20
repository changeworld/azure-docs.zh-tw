---
title: 設定 Azure 檔案儲存體網路端點 | Microsoft Docs
description: Azure 檔案儲存體的網路功能選項概觀。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0859b034cf0caa60039fbf9eb4dd9be54448a940
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510330"
---
# <a name="configuring-azure-files-network-endpoints"></a>設定 Azure 檔案儲存體網路端點
Azure 檔案儲存體提供兩種主要的端點類型來存取 Azure 檔案共用： 
- 公用端點，具有公用 IP 位址，並可從全球任何地方存取。
- 私人端點，存在於虛擬網路內，並具有該虛擬網路位址空間內的私人 IP 位址。

公用和私人端點都存在於 Azure 儲存體帳戶上。 儲存體帳戶是一種管理構造，所代表的是儲存體的共用集區，您可以在此集區中部署多個檔案共用，以及其他儲存體資源 (例如，Blob 容器或佇列)。

本文著重於如何設定儲存體帳戶的端點，以便直接存取 Azure 檔案共用。 本文件中提供的大部分詳細資料，也適用於了解 Azure 檔案同步如何與儲存體帳戶的公用及私人端點相互操作，但如需有關 Azure 檔案同步部署的網路考慮詳細資訊，請參閱[設定 Azure 檔案同步 Proxy 和防火牆設定](storage-sync-files-firewall-and-proxy.md)。

閱讀本操作說明指南之前，建議您先閱讀 [Azure 檔案儲存體的網路功能可量](storage-files-networking-overview.md)。

## <a name="prerequisites"></a>必要條件
- 本文會假設您已經建立 Azure 訂用帳戶。 如果您還沒有訂用帳戶，則先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。
- 本文會假設在您想從內部部署環境連線的儲存體帳戶中，您已建立 Azure 檔案共用。 若要了解如何建立 Azure 檔案共用，請參閱[建立 Azure 檔案共用](storage-how-to-create-file-share.md)。
- 如果您想要使用 Azure PowerShell，請[安裝最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 如果您想要使用 Azure CLI，請[安裝最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-private-endpoint"></a>建立私人端點
為您的儲存體帳戶建立私人端點時會部署下列 Azure 資源：

- **私人端點**：代表儲存體帳戶私人端點的 Azure 資源。 您可以將此視為連線儲存體帳戶和網路介面的資源。
- **網路介面 (NIC)** ：維護指定虛擬網路/子網路中私人 IP 位址的網路介面。 這與您部署虛擬機器時所部署的資源完全相同，但此資源不會指派給 VM，而是由私人端點所擁有。
- **私人 DNS 區域**：如果您之前從未對此虛擬網路部署過私人端點，系統將會為虛擬網路部署新的私人 DNS 區域。 同時也會為此 DNS 區域中的儲存體帳戶建立 DNS A 記錄。 如果您已在此虛擬網路中部署私人端點，新的儲存體帳戶 A 記錄將會新增至現有 DNS 區域。 部署 DNS 區域是選擇性的，但強烈建議您部署，而且如果您使用 AD 服務主體或 FileREST API 來掛接 Azure 檔案共用，則這是必要動作。

> [!Note]  
> 本文會針對 Azure 公用區域使用儲存體帳戶 DNS 尾碼 `core.windows.net`。 此註解也適用於 Azure 主權雲端 (例如 Azure 美國政府雲端和 Azure 中國雲端)，只需替換為適合您環境的尾碼即可。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

如果您的虛擬網路中有虛擬機器，或已依照 [設定 Azure 檔案儲存體的 DNS 轉送](storage-files-networking-dns.md) 其中所述來設定 DNS 轉送，您便可透過 PowerShell、命令列或終端機 (適用於 Windows、Linux 或 macOS) 執行下列命令，藉此測試您的私人端點是否已正確設定。 您必須將 `<storage-account-name>` 取代為適當的儲存體帳戶名稱：

```
nslookup <storage-account-name>.file.core.windows.net
```

如果一切都已成功運作，您應該會看到下列輸出，其中 `192.168.0.5` 是虛擬網路中私人端點的私人 IP 位址 (Windows 版的輸出)：

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

如果您的虛擬網路內有虛擬機器，或已如 [設定 Azure 檔案儲存體的 DNS 轉送](storage-files-networking-dns.md) 其中所述來設定 DNS 轉送，您便可藉由下列命令來測試您的私人端點是否已正確設定：

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

如果一切都已成功運作，您應該會看到下列輸出，其中 `192.168.0.5` 是虛擬網路中私人端點的私人 IP 位址：

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

如果您的虛擬網路內有虛擬機器，或已如 [設定 Azure 檔案儲存體的 DNS 轉送](storage-files-networking-dns.md) 其中所述來設定 DNS 轉送，您便可藉由下列命令來測試您的私人端點是否已正確設定：

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

如果一切都已成功運作，您應該會看到下列輸出，其中 `192.168.0.5` 是虛擬網路中私人端點的私人 IP 位址。 請注意，您仍然應該使用 storageaccount.file.core.windows.net 來裝載檔案共用，而不是 `privatelink` 路徑。

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>限制公用端點的存取
您可以使用儲存體帳戶防火牆設定來限制公用端點的存取。 一般而言，大部分的儲存體帳戶防火牆原則都會將網路存取限制在一或多個虛擬網路內。 有兩種方法可以將對於儲存體帳戶的存取限制在虛擬網路內：

- [為儲存體帳戶建立一個或多個私人端點](#create-a-private-endpoint)，並限制所有對公用端點的存取。 這可確保只有來自所要虛擬網路內的流量，才能存取儲存體帳戶內的 Azure 檔案共用。
- 將公用端點限制在一或多個虛擬網路內。 其運作方式是使用稱為「服務端點」的虛擬網路功能。 當您透過服務端點來限制流往儲存體帳戶的流量時，您仍然可以透過公用 IP 位址來存取儲存體帳戶。

### <a name="disable-access-to-the-public-endpoint"></a>停用公用端點的存取權
當公用端點的存取權停用時，仍然可以透過其私人端點來存取儲存體帳戶。 否則，以儲存體帳戶公用端點為目標的有效要求將會遭到拒絕。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>將公用端點的存取限制在特定虛擬網路
如果您將儲存體帳戶限制為允許特定虛擬網路的存取，表示您允許來自指定虛擬網路內的公用端點要求。 其運作方式是使用稱為「服務端點」的虛擬網路功能。 這可以搭配使用或不搭配使用私人端點。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>另請參閱
- [Azure 檔案儲存體的網路功能考量](storage-files-networking-overview.md)
- [設定 Azure 檔案儲存體的 DNS 轉送](storage-files-networking-dns.md)
- [設定 Azure 檔案儲存體的 S2S VPN](storage-files-configure-s2s-vpn.md)