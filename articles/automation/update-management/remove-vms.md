---
title: 從 Azure 自動化更新管理移除 VM
description: 本文說明如何移除以更新管理管理的電腦。
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913187"
---
# <a name="remove-vms-from-update-management"></a>從更新管理中移除 VM

當您在環境中完成 Vm 的更新管理時，您可以使用 [更新管理](overview.md) 功能停止管理 vm。 若要停止管理它們，您必須 `MicrosoftDefaultComputerGroup` 在與您的自動化帳戶連結的 Log Analytics 工作區中，編輯已儲存的搜尋查詢。

## <a name="sign-into-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="to-remove-your-vms"></a>移除 Vm

1. 在 Azure 入口網站中，從 Azure 入口網站的頂端導覽啟動 **Cloud Shell** 。 如果您不熟悉 Azure Cloud Shell，請參閱 [Azure Cloud Shell 的總覽](../../cloud-shell/overview.md)。

2. 使用下列命令來識別您想要從管理中移除之電腦的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在 Azure 入口網站中，流覽至 **Log Analytics 工作區**。 從清單中選取您的工作區。

4. 在 Log Analytics 工作區中，選取 [ **Advanced settings** ]，然後從左側功能表中選擇 [ **電腦群組** ]。

5. 從右窗格中的 [ **電腦群組** ]，選取 [ **已儲存的群組**]。

6. 從資料表中，針對儲存的搜尋查詢 **更新： >microsoftdefaultcomputergroup**，按一下 [ **視圖成員** ] 圖示來執行和查看其成員。

7. 在查詢編輯器中，檢查查詢並尋找 VM 的 UUID。 移除 VM 的 UUID，然後針對您想要移除的任何其他 Vm 重複這些步驟。

8. 當您完成編輯已儲存的搜尋時，請從頂端列選取 [ **儲存** ]。 出現提示時，請指定下列各項：

    * **名稱**： >microsoftdefaultcomputergroup
    * **另存** 新檔：函式
    * **別名**： Updates__MicrosoftDefaultComputerGroup
    * **類別**：更新

>[!NOTE]
>取消註冊之後仍會顯示電腦，因為我們會報告過去24小時內評估的所有機器。 移除機器之後，您需要等候24小時，再也不會再列出。

## <a name="next-steps"></a>後續步驟

若要重新啟用虛擬機器的管理，請 [流覽 Azure 入口網站](enable-from-portal.md) 或 [從 Azure VM 啟用更新管理](enable-from-vm.md)，以啟用更新管理。