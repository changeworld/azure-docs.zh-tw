---
title: 在小組內建立 Azure 實驗室服務排程
description: 瞭解如何在小組內建立實驗室服務排程。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042332"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>在小組內建立及管理實驗室服務排程

排程可讓您設定教室實驗室，使實驗室中的 VM 可在指定的時間自動啟動和關閉。 您可以定義一次性排程或週期性排程。 下列程序將為您提供建立和管理教室實驗室排程的步驟： 

以下是排程對實驗室虛擬機器的影響： 

- 範本虛擬機器未包含在排程中。 
- 只會啟動已指派的虛擬機器。 這表示，如果使用者未宣告電腦 (student) ，電腦將不會在排程的時間內啟動。 
- 所有虛擬機器 (無論是由使用者宣告，還是不) 會根據實驗室排程來停止。 

> [!IMPORTANT]
> VM 的排程執行時間不會計入分配給使用者的配額中。 該配額用於學生在排程時間以外花費於 VM 上的時間。 

使用者可以建立、編輯和刪除小組內的實驗室排程，就像在 [實驗室網站](https://labs.azure.com)一樣。 請參閱有關 [建立和管理](how-to-create-schedules-within-teams.md)排程的文章。

## <a name="automatic-shutdown-and-disconnect-settings"></a>自動關機與中斷連線設定

您可以啟用數個自動關機成本控制功能，以在虛擬機器未主動使用時主動預防額外的成本。 下列三個自動關機和中斷連線功能的組合，會攔截使用者不小心讓虛擬機器執行的大部分情況：
 
- 自動中斷使用者與作業系統認為閒置的虛擬機器的連線。
- 當使用者中斷連線時，自動關閉虛擬機器。
- 自動關閉已啟動但使用者未連接的虛擬機器。

如需詳細資訊，請參閱 [設定實驗室自動關機設定](how-to-enable-shutdown-disconnect.md)的相關文章。

## <a name="next-steps"></a>後續步驟

查看下列文章：

- [在小組內使用 Azure 實驗室服務總覽](lab-services-within-teams-overview.md)
- [開始使用並在小組內建立實驗室](how-to-get-started-create-lab-within-teams.md)
- [管理小組內的實驗室使用者清單](how-to-manage-user-lists-within-teams.md)
- [管理小組內實驗室的 VM 集區](how-to-manage-vm-pool-within-teams.md)
- [存取小組內的 VM –學生版視圖](how-to-access-vm-for-students-within-teams.md)
