---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015909"
---
## <a name="scenario"></a>狀況

為了更清楚說明如何設定 VM 的靜態 IP 位址，本檔會使用下列案例：

![虛擬網路案例：前端和後端子網，具有前端子網的靜態 IP 位址](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

在此案例中，您會在 *前端* 子網中建立名為 *DNS01* 的 VM，然後將它設定為使用 *192.168.1.101* 的靜態 IP 位址。
