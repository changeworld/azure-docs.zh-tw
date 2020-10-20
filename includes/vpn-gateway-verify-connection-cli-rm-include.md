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
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217933"
---
您可以使用 [az network vpn-connection show](/cli/azure/network/vpn-connection) 命令來確認連線是否成功。 在範例中， '--name' 是指您想要測試之連線的名稱。 當系統在建立連線時，其連線狀態會顯示「連線中」。 連線建立好之後，狀態會變更為 [已連線]。

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
