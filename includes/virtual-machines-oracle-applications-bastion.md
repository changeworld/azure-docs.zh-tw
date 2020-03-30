---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361540"
---
### <a name="bastion-tier"></a>堡壘層

堡壘主機是一個可選元件，您可以將其用作跳轉伺服器來訪問應用程式和資料庫實例。 堡壘主機 VM 可以為其分配公共 IP 位址，但建議使用本地網路設置 ExpressRoute 連接或網站到網站 VPN 以進行安全訪問。 此外，應僅打開 SSH（埠 22、Linux）或 RDP（埠 3389，Windows 伺服器）才能用於傳入流量。 為獲得高可用性，請將堡壘主機部署在兩個可用性區域或單個可用性集中。

您還可以在 VM 上啟用 SSH 代理轉發，這允許您通過從堡壘主機轉發憑據來訪問虛擬網路中的其他 VM。 或者，使用 SSH 隧道訪問其他實例。

下面是代理轉發的示例：

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

此命令連接到堡壘，然後立即再次運行`ssh`，因此您可以在目標實例上獲取終端。 如果群集配置不同，則可能需要在目標實例上指定 root 以外的使用者。 參數`-A`轉發代理連接，以便自動使用本地電腦上的私密金鑰。 請注意，代理轉發是一個鏈，因此第二`ssh`個命令還包括`-A`以便從目標實例啟動的任何後續 SSH 連接也使用本地私密金鑰。