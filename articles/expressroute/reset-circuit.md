---
title: 重設失敗的電路-ExpressRoute： PowerShell： Azure |Microsoft Docs
description: 本文可協助您重設處於失敗狀態的 ExpressRoute 電路。
services: expressroute
author: kumudD
ms.service: expressroute
ms.topic: how-to
ms.date: 11/28/2018
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: e0f79ce0959e7b7dccc20e46493f34e1963df70e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537100"
---
# <a name="reset-a-failed-expressroute-circuit"></a>重設失敗的 ExpressRoute 電路

當 ExpressRoute 電路上的作業未成功完成時，電路可能會進入 [失敗] 狀態。 本文可協助您重設失敗的 Azure ExpressRoute 電路。

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>重設線路

1. 安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 如需詳細資訊，請參閱[安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)。

2. 以提高的權限開啟 PowerShell 主控台並連線至您的帳戶。 使用下列範例來協助您連接：

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. 如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. 指定您要使用的訂用帳戶。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. 執行下列命令來重設處於失敗狀態的電路：

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

電路現在應該狀況良好。 如果電路仍處於失敗狀態，請使用 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援票證。

## <a name="next-steps"></a>後續步驟

如果仍會發生問題，請向 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 開啟支援票證。
