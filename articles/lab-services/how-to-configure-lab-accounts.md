---
title: 在 Azure 實驗室服務中設定 VM 的自動關機
description: 本文說明如何在實驗室帳戶中設定 VM 的自動關機。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8647aed0e66993b8a7b8e5c0a42c8ceabbb1fb9e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798443"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>設定實驗室帳戶的 Vm 自動關機

您可以啟用數個自動關機成本控制功能，以在虛擬機器未主動使用時主動預防額外的成本。 下列三個自動關機和中斷連線功能的組合，會攔截使用者不小心讓虛擬機器執行的大部分情況：
 
- 自動中斷使用者與 OS 認為閒置 (Windows) 的虛擬機器連線。
- 當使用者中斷 (Windows & Linux) 的連線時，自動關閉虛擬機器。
- 自動關閉已啟動但使用者未連接的虛擬機器。

在 [ [使用自動關機設定的最大化成本控制](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) ] 區段中，查看自動關機功能的更多詳細資料。

## <a name="enable-automatic-shutdown"></a>啟用自動關機

1. 在 [Azure 入口網站](https://portal.azure.com/) 流覽至 [ **實驗室帳戶** ] 頁面。
1. 選取左側功能表上的 [ **實驗室設定** ]。
1. 選取適用于您案例的自動關機設定 (s) 。  

    > [!div class="mx-imgBorder"]
    > ![實驗室帳戶的自動關機設定](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    設定 (s) 適用于在實驗室帳戶中建立的所有實驗室。 實驗室建立者 (授課者) 可以在實驗室層級覆寫此設定。 對於實驗室帳戶的此設定所進行的變更，只會影響在進行變更之後所建立的實驗室。

    若要停用 (s) 的設定，請取消核取此頁面上)  (s 的核取方塊。 

## <a name="next-steps"></a>後續步驟

若要瞭解實驗室擁有者在實驗室層級如何設定或覆寫此設定，請參閱 [設定實驗室的 vm 自動關機](how-to-enable-shutdown-disconnect.md)
