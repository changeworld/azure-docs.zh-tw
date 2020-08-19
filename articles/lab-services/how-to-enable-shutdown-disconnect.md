---
title: 在 Azure 實驗室服務中設定實驗室的 Vm 自動關機
description: 了解如何在遠端桌面連線已中斷時，啟用或停用 VM 的自動關機。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 3188117ac651f31057b4db88e32dfb42c45abb60
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604881"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>設定實驗室的 Vm 自動關機

本文說明如何設定實驗室的 Vm 自動關機。

您可以啟用數個自動關機成本控制功能，以在虛擬機器未主動使用時主動預防額外的成本。 下列三個自動關機和中斷連線功能的組合，會攔截使用者不小心讓虛擬機器執行的大部分情況：
 
* 自動中斷使用者與 OS 認為閒置 (Windows) 的虛擬機器連線。
* 當使用者中斷 (Windows & Linux) 的連線時，自動關閉虛擬機器。
* 自動關閉已啟動但使用者未連接的虛擬機器。

在 [ [使用自動關機設定的最大化成本控制](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) ] 區段中，查看自動關機功能的更多詳細資料。

實驗室帳戶管理員可以針對您用來建立實驗室的實驗室帳戶進行此設定。 如需詳細資訊，請參閱 [設定實驗室帳戶的 vm 自動關機](how-to-configure-lab-accounts.md)。 身為實驗室擁有者，您可以在建立實驗室時或在實驗室建立好之後覆寫設定。 

## <a name="configure-for-the-lab-level"></a>設定實驗室層級

您可以在 [Azure 實驗室服務](https://labs.azure.com/)中設定自動關機設定。

* 在 **實驗室原則** 中建立實驗室 () 或
* 建立實驗室之後 (**設定**) 

> [!div class="mx-imgBorder"]
> ![在建立實驗室時進行設定](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

請務必在 [ [使用自動關機設定的最大化成本控制](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) ] 區段中，查看自動關機的詳細資料。

> [!WARNING]
> 如果您在中斷 RDP 工作階段與 VM 的連線之前，就先關閉 VM 上的 Windows 作業系統 (OS)，自動關機功能將無法正常運作。  

## <a name="next-steps"></a>後續步驟

[教室實驗室的儀表板](use-dashboard.md)
