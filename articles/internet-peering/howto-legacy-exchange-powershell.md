---
title: 使用 PowerShell 將舊版 Exchange 對等互連轉換為 Azure 資源
titleSuffix: Azure
description: 使用 PowerShell 將舊版 Exchange 對等互連轉換為 Azure 資源
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775390"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>使用 PowerShell 將舊版 Exchange 對等互連轉換為 Azure 資源

本文介紹如何使用 PowerShell Cmdlet 將現有舊版 Exchange 對等互連轉換為 Azure 資源。

如果您願意，可以使用[門戶](howto-legacy-exchange-portal.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始配置之前，請查看[先決條件](prerequisites.md)和[交換對等演練](walkthrough-exchange-all.md)。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>將舊版 Exchange 對等互連轉換為 Azure 資源

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>獲取舊版交換對等互連以進行轉換
下面是在西雅圖對等位置獲取舊版 Exchange 對等互連的示例：

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

回應大致如下列範例所示：
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>轉換舊體對等互連
以下命令可用於將舊 Exchange 對等對等器轉換為 Azure 資源：

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> 請注意，將舊體對等互連轉換為 Azure 資源時，不支援修改&nbsp;

下面是成功完成端到端預配時的示例回應：

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>其他資源
您可以執行下列命令來取得所有參數的詳細描述：

```powershell
Get-Help Get-AzPeering -detailed
```
有關詳細資訊，請訪問[互聯網對等常見問題解答](faqs.md)

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 創建或修改 Exchange 對等互連](howto-exchange-powershell.md)
