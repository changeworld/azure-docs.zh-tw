---
title: 從小組管理 Azure 實驗室服務使用者清單
description: 瞭解如何從小組管理 Azure 實驗室服務的使用者清單。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946615"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>從小組管理實驗室服務使用者清單

在小組內建立實驗室時 (請參閱 [從小組開始使用和建立實驗室服務實驗室](how-to-get-started-create-lab-within-teams.md)) ，實驗室使用者清單會自動填入並與小組成員資格同步。 小組中的每個人，包括擁有者、成員和來賓都會自動新增至實驗室使用者清單。 Azure 實驗室服務會維護與團隊成員資格的同步處理，並每隔24小時觸發一次自動同步處理。 

## <a name="sync-users"></a>同步使用者

當小組成員資格更新之後，教育工作者可以使用 [ **同步** 處理] 按鈕來觸發手動同步處理。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="同步使用者":::

自動或手動同步處理完成後，會根據實驗室是否已發佈來滿足下列條件。

* 如果實驗室至少未發佈一次：
    * 使用者將依據小組成員資格的變更，從實驗室使用者清單中新增或刪除使用者。 
* 如果實驗室至少已發佈一次，則除了新增或刪除使用者之外，實驗室容量也會自動更新。
    * 如果小組有任何新的新增專案，將會建立新的 Vm。
    * 如果有任何使用者已從小組刪除，則會一併刪除相關聯的 VM。

## <a name="next-steps"></a>後續步驟

設定範本 VM 之後，以及當教師選取發佈範本時，將會建立相當於實驗室使用者清單中使用者數目的 Vm 數目。 一旦發佈實驗室並建立 Vm 之後，使用者將會自動向實驗室註冊，並且在第一次登入 Azure 實驗室服務時將 Vm 指派給這些 Vm，也就是在第一次存取具有 **Azure 實驗室服務** 應用程式的索引標籤時。 

若要發佈範本 VM，請移至 [小組實驗室服務] 視窗，選取 [**範本**] 索引標籤-> **...**  -> **發佈**。

若要管理 VM 集區，請參閱 [從小組管理實驗室服務中的 vm 集](how-to-manage-vm-pool-within-teams.md)區。

### <a name="also-review"></a>另請參閱

請參閱下列文章：

- [在小組內使用 Azure 實驗室服務總覽](lab-services-within-teams-overview.md)
- [開始使用並從小組建立實驗室服務實驗室](how-to-get-started-create-lab-within-teams.md)
- [從小組建立實驗室服務排程](how-to-create-schedules-within-teams.md)
- [從小組存取實驗室服務中的 VM (student view) ](how-to-access-vm-for-students-within-teams.md)

