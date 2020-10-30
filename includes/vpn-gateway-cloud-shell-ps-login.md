---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061591"
---
如果您在本機執行 PowerShell，請以較高的許可權開啟 PowerShell 主控台，並連接到您的 Azure 帳戶。 *Connect-AzAccount* Cmdlet 會提示您輸入認證。 驗證後即會下載您的帳戶設定供 Azure PowerShell 使用。

如果您使用 Azure Cloud Shell 而不是在本機執行 PowerShell，您會發現您不需要執行 *disconnect-azaccount* 。 當您選取 [ **試試看** ] 之後，Azure Cloud Shell 會自動連接到您的 Azure 帳戶。

1. 如果您正在本機執行 PowerShell，請登入。

   ```azurepowershell
   Connect-AzAccount
   ```

1. 如果您有多個訂用帳戶，請取得 Azure 訂用帳戶的清單。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 指定您要使用的訂用帳戶。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
