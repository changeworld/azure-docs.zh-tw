---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520821"
---
以下配置用於以下步驟：

  | | |
  |---|---|
  |電腦| Ubuntu Server 18.04|
  |相依性| strongSwan |


使用下列命令來安裝所需的 strongSwan 組態：

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

使用以下命令安裝 Azure 命令列介面：

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[有關如何安裝 Azure CLI 的其他說明](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
