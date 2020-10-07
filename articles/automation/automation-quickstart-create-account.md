---
title: Azure 快速入門 - 建立 Azure 自動化帳戶 | Microsoft Docs
description: 本文可協助您開始建立 Azure 自動化帳戶及執行 Runbook。
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "83836697"
---
# <a name="create-an-azure-automation-account"></a>建立 Azure 自動化帳戶

您可以使用 Azure 入口網站透過 Azure 建立 Azure 自動化帳戶，此入口網站是瀏覽器型的使用者介面，可讓您存取一些資源。 一個「自動化帳戶」可以管理所指定租用戶之所有區域和訂用帳戶的資源。 

本快速入門引導您建立自動化帳戶，並在帳戶中執行 Runbook。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

[登入 Azure](https://portal.azure.com)。

## <a name="create-automation-account"></a>建立自動化帳戶

1. 為您的 Azure 帳戶選擇名稱。 自動化帳戶名稱在每一區域和資源群組中是唯一的。 刪除自動化帳戶後，其名稱並非立即可供使用。

    > [!NOTE]
    > 當帳戶名稱在使用者介面中輸入之後，就無法變更。 

2. 按一下 Azure 入口網站左上角的 [建立資源] 按鈕。

3. 選取 [IT 與管理工具]，然後選取 [自動化]。

4. 輸入帳戶資訊，包括選取的帳戶名稱。 針對 [建立 Azure 執行身分帳戶] 選擇 [是]，就會自動啟用簡化對 Azure 進行驗證的構件。 資訊完整後，按一下 [建立] 可啟動自動化帳戶部署。

    ![在頁面中輸入您自動化帳戶的相關資訊](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > 如需可部署自動化帳戶之處的更新清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)。

5. 當部署完成時，按一下 [所有服務]。

6. 選取 [自動化帳戶]，然後選擇您所建立的自動化帳戶。

    ![自動化帳戶概觀](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>執行 Runbook

執行其中一個教學課程 Runbook。

1. 按一下 [程序自動化] 下的 [Runbook]。 隨即顯示 Runbook 的清單。 根據預設，會啟用帳戶中的幾個教學課程 Runbook。

    ![自動化帳戶 Runbook 清單](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. 選取 **AzureAutomationTutorialScript** Runbook。 這個動作會開啟 [Runbook 概觀] 頁面。

    ![Runbook 概觀](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. 按一下 [啟動]，然後在 [啟動 Runbook] 頁面上，按一下 [確定] 來啟動 Runbook。

    ![Runbook 作業頁面](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. 作業狀態變成 `Running` 之後，按一下 [輸出] 或 [所有記錄] 可檢視 Runbook 作業輸出。 在本教學課程的 Runbook 中，輸出就是您 Azure 資源的清單。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署自動化帳戶、啟動 Runbook 作業，並檢視作業結果。 若要深入了解 Azure 自動化，請繼續閱讀快速入門來建立您的第一個 Runbook。

> [!div class="nextstepaction"]
> [自動化快速入門 - 建立 Azure 自動化 Runbook](./automation-quickstart-create-runbook.md)

