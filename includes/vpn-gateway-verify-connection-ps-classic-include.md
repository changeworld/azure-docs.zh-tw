---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bd096e5a562bd9117ee895534c087d6fde48cb7e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217918"
---
您可以使用 'Get-AzureVNetConnection' Cmdlet，驗證連線是否成功。

1. 請使用下列 Cmdlet 範例，並將值設定為與您狀況相符的值。 如果虛擬網路的名稱包含空格，則必須以引號括住。

   ```azurepowershell
   Get-AzureVNetConnection "Group ClassicRG TestVNet1"
   ```
2. 完成 Cmdlet 之後，請檢視值。 在下列範例中，[連線狀態] 會顯示為 [已連接]，而您可以看見輸入和輸出位元組。

   ```output
   ConnectivityState         : Connected
   EgressBytesTransferred    : 181664
   IngressBytesTransferred   : 182080
   LastConnectionEstablished : 10/19/22020 12:40:54 AM
   LastEventID               : 24401
   LastEventMessage          : The connectivity state for the local network site 'F7F7BFC7_SiteVNet4' changed from Connecting to
                               Connected.
   LastEventTimeStamp        : 10/19/2020 12:40:54 AM
   LocalNetworkSiteName      : F7F7BFC7_SiteVNet4
   ```
