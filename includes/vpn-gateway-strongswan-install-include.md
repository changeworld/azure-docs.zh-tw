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
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86218200"
---
下列步驟使用下列設定：

- 電腦： Ubuntu Server 18.04
- 相依性： strongSwan


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

使用下列命令來安裝 Azure 命令列介面：

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[有關如何安裝 Azure CLI 的其他指示](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
