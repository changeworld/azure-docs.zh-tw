---
title: 委派子域-Azure PowerShell Azure DNS
description: 透過此學習路徑，開始使用 Azure PowerShell 委派 Azure DNS 子域。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 9b37d313aa5d8c2255b4e3be69831dfcb50238ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84712542"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>使用 Azure PowerShell 委派 Azure DNS 子網域

您可以使用 Azure PowerShell 委派 DNS 子網域。 例如，如果您擁有 contoso.com 網域，可以委派名為 *engineering*的子網域至可與 contoso.com 區域分開管理的其他個別區域。

如果您想要，可以使用 [Azure 入口網站](delegate-subdomain.md)委派子網域。

> [!NOTE]
> 本文章會使用 Contoso.com 作為範例。 用您自己的網域名稱來取代 contoso.com。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必要條件

若要委派 Azure DNS 子網域，必須先將您的公用網域委派給 Azure DNS。 如需如何設定名稱伺服器以便進行委派的指示，請參閱[將網域委派給 Azure DNS](./dns-delegate-domain-azure-dns.md)。 一旦將您的網域委派給您的 Azure DNS 區域之後，就能夠委派子網域。

## <a name="create-a-zone-for-your-subdomain"></a>建立子網域的區域

首先建立 **engineering** 子網域的區域。

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>記下名稱伺服器

然後，記下 engineering 子區域的四個名稱伺服器。

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>建立測試記錄

在用於測試的 engineering 區域建立 **A** 記錄。

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>建立 NS 記錄

接下來在 contoso.com 區域中建立 **engineering** 區域的名稱伺服器 (NS) 記錄。

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>測試委派

使用 nslookup 測試委派。

1. 開啟 PowerShell 視窗。
2. 在命令提示字元中，輸入 `nslookup www.engineering.contoso.com.`
3. 您應該會收到顯示位址 **10.10.10.10** 的非授權回答。

## <a name="next-steps"></a>後續步驟

了解如何[為 Azure 裝載的服務設定反向 DNS](dns-reverse-dns-for-azure-services.md)。