---
title: 從 Azure 自動化變更追蹤和清查中移除 VM
description: 本文說明如何從變更追蹤和清查中移除 VM。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 22cb49c414e21e5c47330f2c67fc2cf30e3364b2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836663"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>從變更追蹤和清查中移除 VM

當您完成將 VM 的變更部署至環境中的作業後，您可以從[變更追蹤和清查](change-tracking.md)功能中移除這些 VM。

1. 從您的自動化帳戶中，選取 [組態管理] 底下的 [變更追蹤] 或 [清查]。

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

* 若要繼續使用變更追蹤和清查，請參閱[管理變更追蹤和清查](change-tracking-file-contents.md)。
* 若要解決一般功能問題，請參閱[對變更追蹤和清查問題進行疑難排解](troubleshoot/change-tracking.md)。