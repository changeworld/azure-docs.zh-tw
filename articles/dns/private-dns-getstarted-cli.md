---
title: 快速入門 - 使用 Azure CLI 建立 Azure 私人 DNS 區域
description: 在本快速入門中，您可以建立並測試私人 DNS 區域，並記錄在 Azure DNS 中。 這份逐步指南將引導您使用 Azure CLI，來建立和管理第一個私人 DNS 區域和第一筆記錄。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 34a9858ffbec2360a347b8c765d53551fd2d9fc0
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935435"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure 私人 DNS 區域

此快速入門將逐步引導您使用 Azure CLI 來建立第一個私人 DNS 區域與第一筆記錄。

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 若要將私人 DNS 區域發佈至虛擬網路，指定可以在區域內解析記錄的虛擬網路清單。  這稱為「連結」  虛擬網路。 啟用自動註冊時，Azure DNS 也會在每次建立虛擬機器時更新區域記錄，變更其 IP 位址，或者在刪除虛擬機器時更新。

在此快速入門中，您可了解如何：

> [!div class="checklist"]
> * 建立私人 DNS 區域
> * 建立測試虛擬機器
> * 建立其他的 DNS 記錄
> * 測試私人區域

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

如果您想要的話，可以使用 [Azure PowerShell](private-dns-getstarted-powershell.md) 完成此快速入門。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>建立資源群組

首先，請建立包含 DNS 區域的資源群組： 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>建立私人 DNS 區域

下列範例會建立名為 **myAzureVNet** 的虛擬網路。 然後，它會在 **MyAzureResourceGroup**資源群組中建立名為 **private.contoso.com** 的 DNS 區域、將 DNS 區域連結到 **MyAzureVnet** 虛擬網路，以及啟用自動註冊。

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

如果您只想建立一個區域僅供名稱解析使用 (沒有自動主機名稱註冊)，您可以使用 `-e false` 參數。

### <a name="list-dns-private-zones"></a>列出 DNS 私人區域

若要列舉 DNS 區域，請使用 `az network private-dns zone list`。 如需協助，請參閱 `az network dns zone list --help`。

指定資源群組只會列出資源群組內的區域︰

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

省略資源群組則會列出訂用帳戶中的所有區域︰

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>建立測試虛擬機器

現在，請建立兩部虛擬機器，讓您可以測試私人 DNS 區域：

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

這需要幾分鐘才能完成。

## <a name="create-an-additional-dns-record"></a>建立其他的 DNS 記錄

若要建立 DNS 記錄，請使用 `az network private-dns record-set [record type] add-record` 命令。 如需說明，例如 A 記錄，請參閱 `az network private-dns record-set A add-record --help`。

 下列範例會在資源群組 **MyAzureResourceGroup** 中的 DNS 區域 **private.contoso.com** 中，建立具有相對名稱 **db** 的記錄。 記錄集的完整名稱是 **db.private.contoso.com**。 記錄類型為 "A"，IP 位址是 "10.2.0.4"。

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>檢視 DNS 記錄

若要列出區域中的 DNS 記錄，請執行︰

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>測試私人區域

現在，您可以測試 **private.contoso.com** 私人區域的名稱解析。

### <a name="configure-vms-to-allow-inbound-icmp"></a>設定 VM，以允許輸入 ICMP

您可以使用 Ping 命令來測試名稱解析。 因此，請在兩部虛擬機器上設定防火牆，以允許輸入的 ICMP 封包。

1. 連線至 myVM01，然後以系統管理員權限開啟 Windows PowerShell 視窗。
2. 執行以下命令：

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

針對 myVM02 重複。

### <a name="ping-the-vms-by-name"></a>依照名稱 Ping VM

1. 從 myVM02 Windows PowerShell 命令提示字元中，使用自動註冊的主機名稱 Ping myVM01：

   ```
   ping myVM01.private.contoso.com
   ```

   您應該會看到如下所示的輸出：

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. 現在 Ping 您先前建立的 **db** 名稱：

   ```
   ping db.private.contoso.com
   ```

   您應該會看到如下所示的輸出：

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>刪除所有資源

當不再需要時，請刪除 **MyAzureResourceGroup** 資源群組，來刪除本快速入門中建立的資源。

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure DNS 私人區域案例](private-dns-scenarios.md)

