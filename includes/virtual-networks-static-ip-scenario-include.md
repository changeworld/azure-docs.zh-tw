---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80060379"
---
## <a name="scenario"></a>狀況

為了更清楚說明如何設定 VM 的靜態 IP 位址，本檔會使用下列案例：

![虛擬網路案例：前端和後端子網，具有前端子網的靜態 IP 位址](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

在此案例中，您會在*前端*子網中建立名為*DNS01*的 VM，然後將它設定為使用*192.168.1.101*的靜態 IP 位址。
