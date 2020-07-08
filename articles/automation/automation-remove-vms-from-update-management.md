---
title: 從 Azure 自動化更新管理移除 VM
description: 本文說明如何移除以更新管理管理的機器。
services: automation
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: mvc
ms.openlocfilehash: 9745ddea1035f239a9ca65a073fb698a8f42c01f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610042"
---
# <a name="remove-vms-from-update-management"></a>從更新管理中移除 VM

當您完成將 VM 的更新部署至環境之後，可以從[更新管理](automation-update-management.md)功能中移除這些 VM。

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

* 若要繼續使用更新管理，請參閱[管理 Azure VM 的更新和修補](automation-tutorial-update-management.md)。
* 若要解決一般功能問題，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 針對 Windows 更新代理程式的問題，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 針對 Linux 更新代理程式的問題，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。