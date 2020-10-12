---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68361540"
---
### <a name="bastion-tier"></a>防禦層

防禦主機是選用元件，您可以用來做為存取應用程式和資料庫實例的跳躍伺服器。 防禦主機 VM 可以有指派的公用 IP 位址，但建議您使用內部部署網路來設定 ExpressRoute 連線或站對站 VPN，以進行安全存取。 此外，只有 SSH (埠22、Linux) 或 RDP (埠3389，Windows Server) 應該開啟以取得連入流量。 為了達到高可用性，請在兩個可用性區域中或在單一可用性設定組中部署防禦主機。

您也可以在您的 Vm 上啟用 SSH 代理程式轉送，這可讓您藉由從您的防禦主機轉送認證來存取虛擬網路中的其他 Vm。 或者，使用 SSH 通道來存取其他實例。

以下是代理程式轉送的範例：

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

此命令會連接到防禦，然後立即 `ssh` 再執行一次，讓您取得目標實例上的終端機。 如果您的叢集設定方式不同，您可能需要在目標實例上指定 root 以外的使用者。 `-A`引數會轉送代理程式連線，因此會自動使用您本機電腦上的私密金鑰。 請注意，代理程式轉送是一種鏈，因此第二個 `ssh` 命令也會包含， `-A` 以便從目標實例起始的任何後續 SSH 連線也會使用您的本機私密金鑰。