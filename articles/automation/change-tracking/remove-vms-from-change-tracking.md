---
title: 從 Azure 自動化變更追蹤和清查中移除 VM
description: 本文說明如何從變更追蹤和清查中移除 VM。
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 61ced7eda78d3ce56d78dfd6cc54e0e92d376e68
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100672"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>從變更追蹤和清查中移除 VM

當您在環境中的 Vm 上完成追蹤變更時，您可以使用 [變更追蹤和清查](overview.md) 功能停止管理它們。 若要停止管理它們，您必須 `MicrosoftDefaultComputerGroup` 在與您的自動化帳戶連結的 Log Analytics 工作區中，編輯已儲存的搜尋查詢。

## <a name="sign-into-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="to-remove-your-vms"></a>移除 Vm

1. 在 Azure 入口網站中，從 Azure 入口網站的頂端導覽啟動 **Cloud Shell** 。 如果您不熟悉 Azure Cloud Shell，請參閱 [Azure Cloud Shell 的總覽](/azure/cloud-shell/overview)。

2. 使用下列命令來識別您想要從管理中移除之電腦的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在 Azure 入口網站中，流覽至 **Log Analytics 工作區** 。 從清單中選取您的工作區。

4. 在您的 Log Analytics 工作區中，選取 [ **記錄** ]，然後從頂端的 [動作] 功能表選擇 [ **查詢瀏覽器** ]。

5. 從右窗格的 [ **查詢瀏覽器** ] 中，展開 [ **已儲存的 Queries\Updates** ]，然後選取已儲存的搜尋查詢 `MicrosoftDefaultComputerGroup` 來編輯它。

6. 在查詢編輯器中，檢查查詢並尋找 VM 的 UUID。 移除 VM 的 UUID，然後針對您想要移除的任何其他 Vm 重複這些步驟。

7. 當您完成編輯已儲存的搜尋時，請從頂端列選取 [ **儲存** ]。

>[!NOTE]
>取消註冊之後仍會顯示電腦，因為我們會報告過去24小時內評估的所有機器。 移除機器之後，您需要等候24小時，再也不會再列出。

## <a name="next-steps"></a>下一步

若要重新啟用這項功能，請參閱 [從自動化帳戶啟用變更追蹤和清查](enable-from-automation-account.md)、 [流覽 Azure 入口網站來啟用變更追蹤和清查](enable-from-portal.md)、 [從 runbook 啟用變更追蹤和清查](enable-from-runbook.md)，或 [從 Azure VM 啟用變更追蹤和清查](enable-from-vm.md)。