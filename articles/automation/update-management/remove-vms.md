---
title: 從 Azure 自動化更新管理移除 VM
description: 本文說明如何移除以更新管理管理的電腦。
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 774dbe29cbb6b4d063d3619d0c710efb1949b99a
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222050"
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

4. 在您的 Log Analytics 工作區中，選取 [ **記錄** ]，然後從頂端的 [動作] 功能表選擇 [ **查詢瀏覽器** ]。

5. 從右窗格的 [ **查詢瀏覽器** ] 中，展開 [ **已儲存的 Queries\Updates** ]，然後選取已儲存的搜尋查詢 `MicrosoftDefaultComputerGroup` 來編輯它。

6. 在查詢編輯器中，檢查查詢並尋找 VM 的 UUID。 移除 VM 的 UUID，然後針對您想要移除的任何其他 Vm 重複這些步驟。

7. 當您完成編輯已儲存的搜尋時，請從頂端列選取 [ **儲存** ]。

>[!NOTE]
>取消註冊之後仍會顯示電腦，因為我們會報告過去24小時內評估的所有機器。 移除機器之後，您需要等候24小時，再也不會再列出。

## <a name="next-steps"></a>後續步驟

若要重新啟用虛擬機器的管理，請 [流覽 Azure 入口網站](enable-from-portal.md) 或 [從 Azure VM 啟用更新管理](enable-from-vm.md)，以啟用更新管理。