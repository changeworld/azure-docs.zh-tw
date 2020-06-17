---
title: 從 Azure 自動化更新管理移除 VM
description: 本文說明如何從更新管理移除 VM。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836646"
---
# <a name="remove-vms-from-update-management"></a>從更新管理中移除 VM

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

當您完成將 VM 的更新部署至環境之後，可以從[更新管理](automation-update-management.md)功能中移除這些 VM。

1. 從您的自動化帳戶中，選取 [更新管理] 之下的 [更新管理]。

2. 使用下列命令識別要從管理中移除之 VM 的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在 Log Analytics 工作區的 [一般] 底下，存取已儲存的搜尋。

4. 針對已儲存的搜尋 `MicrosoftDefaultComputerGroup`，按一下右側的省略符號，然後選取 [編輯]。 

5. 移除 VM 的 UUID。

6. 對任何其他要移除的 VM 重複這些步驟。

7. 在完成編輯後，儲存已儲存的搜尋。 

## <a name="next-steps"></a>後續步驟

* 若要繼續使用更新管理，請參閱[管理 Azure VM 的更新和修補](automation-tutorial-update-management.md)。
* 若要解決一般功能問題，請參閱[針對更新管理問題進行疑難排解](troubleshoot/update-management.md)。
* 針對 Windows 更新代理程式的問題，請參閱[針對 Windows 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues.md)。
* 針對 Linux 更新代理程式的問題，請參閱[針對 Linux 更新代理程式問題進行疑難排解](troubleshoot/update-agent-issues-linux.md)。