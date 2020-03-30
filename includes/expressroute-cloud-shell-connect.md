---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173712"
---
 如果使用 Azure 雲外殼，則在按一下"試用"後自動登入到 Azure 帳戶。 要在本地登錄，請打開具有提升許可權的 PowerShell 主控台，然後運行 Cmdlet 進行連接。

```azurepowershell
Connect-AzAccount
```

如果您有多個訂用帳戶，請取得 Azure 訂用帳戶的清單。

```azurepowershell-interactive
Get-AzSubscription
```

指定您要使用的訂用帳戶。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```