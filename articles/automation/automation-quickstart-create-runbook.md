---
title: Azure 快速入門 - 建立 Azure 自動化 Runbook | Microsoft Docs
description: 本文可協助您開始建立 Azure 自動化 Runbook。
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 948ca820347c7cdcd560ade46e850f66b25bc88e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90987298"
---
# <a name="create-an-azure-automation-runbook"></a>建立 Azure 自動化 Runbook

您可以透過 Azure 建立 Azure 自動化 Runbook。 此方法可提供以瀏覽器為基礎的使用者介面，可用於建立自動化 Runbook。 在本快速入門中，您會逐步建立、編輯、測試及發佈自動化 PowerShell Runbook。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure。

## <a name="create-the-runbook"></a>建立 runbook

首先，建立 Runbook。 依預設，本快速入門中建立的範例 Runbook 會輸出 `Hello World`。

1. 開啟自動化帳戶。

1. 按一下 [程序自動化] 下的 [Runbook]。 隨即顯示 Runbook 的清單。

1. 按一下清單頂端的 [建立 Runbook]。

1. 在 [名稱] 欄位中輸入 Runbook 的名稱 `Hello-World`，然後在 [ Runbook 類型] 欄位選取 [PowerShell]。 

   ![在頁面中輸入您自動化 Runbook 的相關資訊](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. 按一下頁面底部的 [新增] 。 隨即建立 Runbook，並隨即開啟 [編輯 PowerShell Runbook] 頁面。

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="[編輯 PowerShell Runbook] 頁面的螢幕擷取畫面。":::

1. 輸入或複製以下程式碼並貼到編輯窗格中。 其會建立稱為 `Name`的選擇性輸入參數，並包含預設值 `World`，並輸出使用此輸入值的字串：

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. 按一下 [儲存] 可儲存 Runbook 草稿副本。

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="[編輯 PowerShell Runbook] 頁面的螢幕擷取畫面。":::

## <a name="test-the-runbook"></a>測試 Runbook

建立 Runbook 之後，您必須測試 Runbook 來驗證其是否可運作。

1. 按一下 [測試窗格]  來開啟 [測試] 窗格。

1. 輸入一個 [名稱] 的值，然後按一下 [啟動]。 隨即啟動測試作業，並顯示作業狀態和輸出。

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="[編輯 PowerShell Runbook] 頁面的螢幕擷取畫面。":::

1. 按一下右上角的 **X** 可關閉 [測試] 頁面。 在隨即出現的快顯視窗中選取 [確定]。

1. 在 [編輯 PowerShell Runbook] 頁面上，按一下 [發佈]，可在帳戶中發佈 Runbook 作為官方版本的 Runbook。

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="[編輯 PowerShell Runbook] 頁面的螢幕擷取畫面。":::

## <a name="run-the-runbook"></a>執行 Runbook

一旦 Runbook 發佈之後，隨即顯示 [概觀] 頁面。

1. 在 Runbook [概觀] 頁面中，按一下 [啟動] 可開啟這個 Runbook 的 [啟動 Runbook] 設定頁面。

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="[編輯 PowerShell Runbook] 頁面的螢幕擷取畫面。":::

1. 將 [名稱] 保留空白以便使用預設值，然後按一下 [確定]。 隨即提交 Runbook 作業，且隨即出現 [作業] 頁面。

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="[編輯 PowerShell Runbook] 頁面的螢幕擷取畫面。":::

1. 當作業狀態為 `Running` 或 `Completed` 時，按一下 [輸出] 可開啟 [輸出] 窗格並檢視 Runbook 輸出。

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="[編輯 PowerShell Runbook] 頁面的螢幕擷取畫面。":::

## <a name="clean-up-resources"></a>清除資源

若不再需要，請刪除 Runbook。 若要這樣做，選取 Runbook 清單中的 Runbook，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立、編輯、測試和發佈 Runbook，並啟動 Runbook 作業。 若要深入了解自動化 Runbook，請繼續閱讀相關文件，了解您可以在自動化中建立並使用的不同 Runbook 類型。

> [!div class="nextstepaction"]
> [Azure 自動化 Runbook 類型](./automation-runbook-types.md)
