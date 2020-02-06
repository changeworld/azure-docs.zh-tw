---
title: 快速入門：建立 Azure DNS 區域和記錄 - Azure PowerShell
titleSuffix: Azure DNS
description: 了解如何在 Azure DNS 中建立 DNS 區域和記錄。 這份逐步快速入門將引導您使用 Azure PowerShell 來建立和管理第一個 DNS 區域和第一筆記錄。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: e33f6fdba1a15032d76b94804d610e292f663d59
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937166"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立 Azure DNS 區域和記錄

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在本快速入門中，您會使用 Azure PowerShell 建立第一個 DNS 區域和第一筆記錄。 您也可以使用 [Azure 入口網站](dns-getstarted-portal.md)或 [Azure CLI](dns-getstarted-cli.md) 來執行這些步驟。 

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 最後，若要將 DNS 區域發佈至網際網路，您需要設定網域的名稱伺服器。 以下說明上述各步驟。

Azure DNS 也支援建立私人網域。 如需有關如何建立第一個私人 DNS 區域與記錄的逐步指示，請參閱[透過 PowerShell 開始使用 Azure DNS 私人區域 ](private-dns-getstarted-powershell.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-resource-group"></a>建立資源群組

建立 DNS 區域之前，請先建立包含 DNS 區域的資源群組：

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>建立 DNS 區域

使用 `New-AzDnsZone` Cmdlet 建立 DNS 區域。 下列範例會在稱為 MyResourceGroup  的資源群組中建立稱為 contoso.xyz  的 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>建立 DNS 記錄

您可以使用 `New-AzDnsRecordSet` Cmdlet 來建立記錄集。 下列範例會在資源群組 "MyResourceGroup" 中的 DNS 區域 "contoso.xyz" 中，建立具有相對名稱 "www" 的記錄。 記錄集的完整名稱是 "www.contoso.xyz"。 記錄類型為 'A'，IP 位址是 "10.10.10.10"，TTL 為 3600 秒。

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>檢視記錄

若要列出區域中的 DNS 記錄，請使用︰

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>測試名稱解析

現在您已有內含測試 'A' 記錄的測試 DNS 區域，接下來您可以使用名為 *nslookup* 的工具來測試名稱解析。 

**若要測試 DNS 名稱解析：**

1. 執行下列 Cmdlet 來取得您區域中的名稱伺服器清單：

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. 從上一個步驟的輸出複製其中一個名稱伺服器名稱。

1. 開啟命令提示字元，並執行下列命令：

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   例如：

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   您應該會看到類似於下列畫面的內容：

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

主機名稱 **www\.contoso.xyz** 解析為 **10.10.10.10**，正如您所設定。 此結果確認了名稱解析正常運作。

## <a name="delete-all-resources"></a>刪除所有資源

不再需要時，您可以藉由刪除資源群組，刪除在本快速入門中建立的所有資源：

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

既然您已經使用 Azure PowerShell 建立第一個 DNS 區域和第一筆記錄，就可以在自訂網域中為 Web 應用程式建立記錄。

> [!div class="nextstepaction"]
> [在自訂網域中建立 Web 應用程式的 DNS 記錄](./dns-web-sites-custom-domain.md)

