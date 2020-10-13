---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 8d6c3125d0109ae9005414add27f9aa08e932e49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083455"
---
透過在 VM 建立期間傳遞的 IP，使用遠端桌面通訊協定 (RDP) 來連線至您的 Windows VM。

1. 在您的用戶端上啟動 RDP。 移至 [ **開始** ]，然後輸入 **mstsc**。
1. 輸入 VM 的 IP 位址，以及您在 VM 範本參數檔案中使用的存取認證。

    ![透過 RDP 連接到 Windows VM](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)
1. 您可能需要核准連接至不受信任的電腦。 
1. 您現在已登入在設備上執行的 VM。 