---
title: 從小組管理 Azure 實驗室服務中的 VM 集區
description: 瞭解如何從小組管理 Azure 實驗室服務中的 VM 集區。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946626"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>從小組管理實驗室服務中的 VM 集區

虛擬機器 (VM) 建立會在範本 VM 第一次發行時立即開始。 Vm 必須為將會建立實驗室使用者清單中的使用者數目。 系統會在第一次登入 Azure 實驗室服務時，自動將 Vm 指派給學生。 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>發佈範本並管理 VM 集區

若要發佈範本，請移至 [小組實驗室服務] 視窗，選取 [**範本**] 索引標籤-> **...**  -> **發佈**。

設定範本 VM 之後，以及當教師選取發佈範本時，將會建立相當於實驗室使用者清單中使用者數目的 Vm 數目。 一旦發佈實驗室並建立 Vm 之後，使用者將會自動向實驗室註冊，並且在第一次登入 Azure 實驗室服務時將 Vm 指派給這些 Vm，也就是在第一次存取具有 **Azure 實驗室服務** 應用程式的索引標籤時。 

當觸發使用者清單同步時，實驗室) 中的實驗室容量 (Vm 數目，會根據小組成員資格的變更自動更新。 新增的 Vm 會在新增使用者時建立，而且指派給從小組移除之使用者的 Vm 也會一併刪除。 如需詳細資訊，請參閱 [如何管理小組中的使用者](how-to-manage-user-lists-within-teams.md)。 

教師可以直接從 [VM 集區] 索引標籤，繼續存取學生 Vm。而且，教師也可以從 [ **虛擬機器集** 區] 索引標籤或按一下 [我的 **虛擬機器** ] 按鈕 (畫面的右上角，來存取指派給他們自己的 vm) 。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM 集區":::

## <a name="next-steps"></a>後續步驟

查看下列文章：

- [在小組內使用 Azure 實驗室服務總覽](lab-services-within-teams-overview.md)
- [開始使用並從小組建立實驗室服務實驗室](how-to-get-started-create-lab-within-teams.md)
- [從小組管理實驗室服務使用者清單](how-to-manage-user-lists-within-teams.md)
- [從小組建立實驗室服務排程](how-to-create-schedules-within-teams.md)
- [從小組存取實驗室服務中的 VM (student view) ](how-to-access-vm-for-students-within-teams.md)


