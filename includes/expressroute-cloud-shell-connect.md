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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173712"
---
 如果您使用 Azure Cloud Shell，您可以在按一下 [試用] 之後自動登入您的 Azure 帳戶。 若要在本機登入，請以提高的權限開啟 PowerShell 主控台並執行 Cmdlet 以連線。

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