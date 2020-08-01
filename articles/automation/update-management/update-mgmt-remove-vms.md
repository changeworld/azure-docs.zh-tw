---
title: 從 Azure 自動化更新管理移除 VM
description: 本文說明如何移除以更新管理管理的機器。
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449982"
---
# <a name="remove-vms-from-update-management"></a>從更新管理中移除 VM

當您完成在環境中管理 Vm 上的更新時，您可以使用[更新管理](update-mgmt-overview.md)功能停止管理 vm。

## <a name="sign-into-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="to-remove-your-vms"></a>移除您的 Vm

1. 從您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 使用下列命令來識別您想要從管理中移除之電腦的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在您的 Log Analytics 工作區的 **[一般**] 底下，存取已儲存的範圍設定搜尋 `MicrosoftDefaultScopeConfig-Updates` 。

4. 針對已儲存的搜尋 `MicrosoftDefaultComputerGroup`，按一下右側的省略符號，然後選取 [編輯]。

5. 移除 VM 的 UUID。

6. 對任何其他要移除的 VM 重複這些步驟。

7. 在完成編輯後，儲存已儲存的搜尋。

>[!NOTE]
>取消註冊後仍會顯示機器，因為我們會報告在過去24小時內評估的所有機器。 中斷電腦連線之後，您必須等待24小時，才會再列出它們。

## <a name="next-steps"></a>後續步驟

若要重新啟用管理您的虛擬機器，請參閱[從 AZURE VM](update-mgmt-enable-vm.md)流覽 Azure 入口網站或啟用更新管理來[啟用更新管理](update-mgmt-enable-portal.md)。