---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060379"
---
## <a name="scenario"></a>狀況

為了更好地說明如何為 VM 配置靜態 IP 位址，本文檔使用以下方案：

![虛擬網路方案：前端和後端子網，具有前端子網的靜態 IP 位址](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

在這種情況下，您將在*FrontEnd*子網中創建名為*DNS01*的 VM，然後將其設置為使用*192.168.1.101*的靜態 IP 位址。
