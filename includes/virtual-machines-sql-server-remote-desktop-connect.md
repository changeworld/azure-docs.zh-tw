---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: fe5daa38c43723c85fb464e191ee4a3e85700e0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173935"
---
1. 建立並執行 Azure 虛擬機器後，請按一下 Azure 入口網站中的 [虛擬機器] 圖示，以檢視您的 VM。

1. 按一下新 VM 的省略符號 ( **...** )。

1. 按一下 [ **連接**]。

   ![在入口網站中連線到 VM](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. 開啟您的瀏覽器為 VM 下載的 **RDP** 檔案。

1. [遠端桌面連線] 會通知您無法識別這個遠端連線的發行者。 按一下 [連接]  以繼續。

1. 在 [Windows 安全性]  對話方塊中，按一下 [使用不同帳戶]  。 您可能必須按一下 [更多選擇]  才能看到它。 指定您建立 VM 時所設定的使用者名稱和密碼。 您必須在使用者名稱之前新增反斜線。

   ![遠端桌面驗證](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

1. 按一下 [確定]  進行連線。
