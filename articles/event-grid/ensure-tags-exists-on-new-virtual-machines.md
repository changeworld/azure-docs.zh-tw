---
title: 整合 Azure 自動化與事件格線 | Microsoft Docs
description: 了解如何在新的 VM 建立時自動加入標記，並傳送通知給 Microsoft Teams。
keywords: 自動化, Runbook, 小組, 事件格線, 虛擬機器, VM
services: automation,event-grid
author: eamonoreilly
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 07/07/2020
ms.author: eamono
ms.openlocfilehash: 3b9b49a4d38566891f442a3d2d7eac9bf1d36465
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461998"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>教學課程：將 Azure 自動化與事件格線和 Microsoft Teams 整合

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 匯入事件格線範例 Runbook。
> * 建立選用的 Microsoft Teams Webhook。
> * 建立 Runbook 的 Webhook。
> * 建立事件格線訂用帳戶。
> * 建立觸發 Runbook 的 VM。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

若要完成本教學課程，需要有 [Azure 自動化帳戶](../automation/index.yml)，才能保留從 Azure 事件格線訂用帳戶觸發的 Runbook。

* 需將 `AzureRM.Tags` 模組載入自動化帳戶中，請參閱[如何將模組匯入 Azure 自動化](../automation/automation-update-azure-modules.md)，了解如何將模組匯入 Azure 自動化。

## <a name="import-an-event-grid-sample-runbook"></a>匯入事件格線範例 Runbook

1. 選取您的自動化帳戶，然後選取 [Runbook]  頁面。

   ![選取 Runbook](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. 選取 [瀏覽資源庫]  按鈕。

3. 搜尋**事件格線**，然後選取 [整合 Azure 自動化與事件格線]  。

    ![匯入資源庫 Runbook](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. 選取 [匯入]  ，並將其命名為 **Watch-VMWrite**。

5. 匯入之後，選取 [編輯]  以檢視 Runbook 來源。 
6. 將指令碼中的第 74 行更新為使用 `Tag`，而不是 `Tags`。

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. 選取 [發佈]  按鈕。

## <a name="create-an-optional-microsoft-teams-webhook"></a>建立選用的 Microsoft Teams Webhook

1. 在 Microsoft Teams 中，選取通道名稱旁的 [更多選項]  ，然後選取 [連接器]  。

    ![Microsoft Teams 連線](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. 將連接器清單捲動至 [傳入的 Webhook]  ，並選取 [新增]  。

3. 輸入 **AzureAutomationIntegration** 作為名稱，然後選取 [建立]  。

4. 將 Webhook URL 複製到剪貼簿並加以儲存。 Webhook URL 是用來將資訊傳送給 Microsoft Teams。

5. 選取 [完成]  以儲存此 Webhook。

## <a name="create-a-webhook-for-the-runbook"></a>建立 Runbook 的 Webhook

1. 開啟 Watch-VMWrite Runbook。

2. 選取 [Webhook]  ，然後選取 [新增 Webhook]  按鈕。

3. 輸入 **WatchVMEventGrid** 作為名稱。 將 URL 複製到剪貼簿並加以儲存。

    ![設定 Webhook 名稱](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. 選取 [設定參數並執行設定]  ，並針對 **CHANNELURL** 輸入 Microsoft Teams Webhook URL。 保留 **WEBHOOKDATA** 空白。

    ![設定 Webhook 參數](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. 選取 [建立]  以建立自動化 Runbook Webhook。

## <a name="create-an-event-grid-subscription"></a>建立事件格線訂用帳戶

1. 在 [自動化帳戶]  概觀頁面上，選取 [事件格線]  。

    ![選取 Event Grid](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. 按一下 [+ 事件訂閱]  。

3. 使用下列資訊來設定訂用帳戶：
    1. 針對 [主題類型]  ，選取 [Azure 訂用帳戶]  。
    2. 取消核取 [訂閱所有事件類型]  核取方塊。
    3. 輸入 **AzureAutomation** 作為名稱。
    4. 在 [定義的事件類型]  下拉式清單中，取消核取 [資源寫入成功]  以外的所有選項。

        > [!NOTE] 
        > Azure Resource Manager 目前不會區分建立和更新，因此，對您 Azure 訂用帳戶中的所有 Microsoft.Resources.ResourceWriteSuccess 事件實作本教學課程可能會導致大量呼叫。
    1. 針對 [端點類型]  ，選取 [Webhook]  。
    2. 按一下 [選取端點]  。 在開啟的 [選取 Web Hook]  頁面上，貼上您為 Watch-VMWrite Runbook 建立的 Webhook URL。
    3. 在 [篩選]  下方，輸入您要在其中建立新 VM 的訂用帳戶和資源群組。 它看起來應該像：`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. 選取 [建立]  以儲存事件格線訂用帳戶。

## <a name="create-a-vm-that-triggers-the-runbook"></a>建立觸發 Runbook 的 VM

1. 在事件格線訂用帳戶 [前置詞篩選] 中您所指定的資源群組內，建立新的 VM。

2. 這應該會呼叫 Watch-VMWrite Runbook，並將新的標記新增至 VM。

    ![VM 標記](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. 新的訊息會傳送至 Microsoft Teams 通道。

    ![Microsoft Teams 通知](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您將設定事件格線和自動化之間的整合。 您已了解如何︰

> [!div class="checklist"]
> * 匯入事件格線範例 Runbook。
> * 建立選用的 Microsoft Teams Webhook。
> * 建立 Runbook 的 Webhook。
> * 建立事件格線訂用帳戶。
> * 建立觸發 Runbook 的 VM。

> [!div class="nextstepaction"]
> [使用 Event Grid 建立和路由傳送自訂事件](../event-grid/custom-event-quickstart.md)
