---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173809"
---
您可以使用 [az network vpn-connection show](/cli/azure/network/vpn-connection) 命令來確認連線是否成功。 在範例中， '--name' 是指您想要測試之連線的名稱。 當系統在建立連線時，其連線狀態會顯示「連線中」。 連線建立好之後，狀態會變更為 [已連線]。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
