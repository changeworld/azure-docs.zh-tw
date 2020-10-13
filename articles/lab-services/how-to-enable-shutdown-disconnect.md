---
title: 在 Azure 實驗室服務中設定實驗室的 Vm 自動關機
description: 了解如何在遠端桌面連線已中斷時，啟用或停用 VM 的自動關機。
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 67b51941207fdd4913df9a92362959bbd468d336
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649876"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>設定實驗室的 Vm 自動關機

本文說明如何設定實驗室的 Vm 自動關機。

您可以啟用數個自動關機成本控制功能，以在虛擬機器未主動使用時主動預防額外的成本。 下列三個自動關機和中斷連線功能的組合，會攔截使用者不小心讓虛擬機器執行的大部分情況：
 
* 自動中斷使用者與作業系統認為閒置的虛擬機器的連線。
* 當使用者中斷連線時，自動關閉虛擬機器。
* 自動關閉已啟動但使用者未連接的虛擬機器。

在 [ [使用自動關機設定的最大化成本控制](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) ] 區段中，查看自動關機功能的更多詳細資料。

實驗室帳戶管理員可以針對您用來建立實驗室的實驗室帳戶進行此設定。 如需詳細資訊，請參閱 [設定實驗室帳戶的 vm 自動關機](how-to-configure-lab-accounts.md)。 身為實驗室擁有者，您可以在建立實驗室時或在實驗室建立好之後覆寫設定。 

## <a name="configure-for-the-lab-level"></a>設定實驗室層級

您可以在 [Azure 實驗室服務](https://labs.azure.com/)中設定自動關機設定。

* 在 **實驗室原則** 中建立實驗室 () 或
* 建立實驗室之後 (**設定**) 

> [!div class="mx-imgBorder"]
> ![在建立實驗室時進行設定](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

請務必在 [ [使用自動關機設定的最大化成本控制](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) ] 區段中，查看自動關機的詳細資料。

> [!WARNING]
> 如果您在將 RDP 會話中斷連線至 VM 之前，關閉了 Linux 或 Windows 作業系統 (OS) VM，自動關機功能將無法正常運作。  
## <a name="next-steps"></a>後續步驟

[教室實驗室的儀表板](use-dashboard.md)
