---
title: 管理 Azure 入口網站中的邏輯應用程式
description: 使用 Azure 入口網站來編輯、啟用、停用或刪除邏輯應用程式。
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416002"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>管理 Azure 入口網站中的邏輯應用程式

您可以使用[Azure 入口網站](https://portal.azure.com)或[Visual Studio](manage-logic-apps-with-visual-studio.md)來管理邏輯應用程式。 本文說明如何編輯、停用、啟用或刪除 Azure 入口網站中的邏輯應用程式。 如果您不熟悉 Azure Logic Apps，請參閱[什麼是 Azure Logic Apps](logic-apps-overview.md)？

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 現有的邏輯應用程式。 若要瞭解如何在 Azure 入口網站中建立邏輯應用程式，請參閱[快速入門：使用 Azure Logic Apps Azure 入口網站建立您的第一個工作流程](quickstart-create-first-logic-app-workflow.md)。

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>尋找邏輯應用程式

若要尋找並開啟您的邏輯應用程式，請遵循下列步驟：

1. 使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

1. 在 Azure 搜尋服務列中， `logic apps`輸入，然後選取 [ **Logic Apps**]。

   ![尋找並選取「邏輯應用程式」](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. 在 [ **Logic Apps** ] 頁面上，尋找並選取您要管理的邏輯應用程式。

   邏輯應用程式的 **[總覽**] 窗格開啟之後，您可以透過下列方式篩選顯示在 [ **Logic Apps** ] 頁面上的清單：

   * 依名稱搜尋邏輯應用程式
   * 依訂用帳戶、資源群組、位置和標記篩選邏輯應用程式
   * 依資源群組、類型、訂用帳戶和位置來群組邏輯應用程式

## <a name="view-logic-app-properties"></a>查看邏輯應用程式屬性

1. 在 [Azure 入口網站中，[尋找並開啟您的邏輯應用程式](#find-logic-app)。

1. 從邏輯應用程式的功能表中，選取 [**設定**] 底下的 [**屬性**]。

1. 在 [**屬性**] 窗格上，您可以查看並複製邏輯應用程式的下列相關資訊：

   * **Name**
   * **資源識別碼**
   * **資源群組**
   * **位置**
   * **類型** 
   * **訂用帳戶名稱**
   * **訂用帳戶識別碼**
   * **存取端點**
   * **執行時間傳出 IP 位址**
   * **存取端點 IP 位址**
   * **連接器連出 IP 位址**

## <a name="disable-or-enable-logic-apps"></a>停用或啟用邏輯應用程式

您可以在 Azure 入口網站中同時啟用或停用[單一邏輯應用程式](#disable-enable-single-logic-app)或[多個邏輯應用程式](#disable-or-enable-multiple-logic-apps)。 您也可以[在 Visual Studio 中啟用或停用邏輯應用程式](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)。

停用邏輯應用程式會影響工作流程實例，並以下列方式執行：

* 所有進行中和暫止的執行都會繼續執行，直到完成為止。 視這些執行的數目而定，此程式可能需要一些時間。

* Logic Apps 引擎不會建立或執行新的工作流程實例。

* 觸發程式不會在下一次符合其條件時引發。

* 觸發狀態會記住邏輯應用程式停止的時間點。 因此，如果您重新啟用邏輯應用程式，則會在上次執行後，針對所有未處理的專案引發觸發程式。

  若要在上次執行後停止您的邏輯應用程式引發未處理的專案，請清除觸發程式的狀態，再重新啟用邏輯應用程式：

  1. 在 [Azure 入口網站中，[尋找並開啟您的邏輯應用程式](#find-logic-app)。

  1. 編輯邏輯應用程式觸發程式的任何部分。

  1. 儲存您的變更。 此步驟會重設觸發程式的目前狀態。

  1. [重新啟用您的邏輯應用程式](#disable-enable-single-logic-app)。

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>停用或啟用單一邏輯應用程式

1. 在 [Azure 入口網站中，[尋找並開啟您的邏輯應用程式](#find-logic-app)。

1. 在邏輯應用程式的功能表中，選取 **[總覽**]。 從下列選項中選擇：

   * 在工具列上，選取 [停用]****。

     ![停用 Azure 入口網站中的單一邏輯應用程式](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     如果您的邏輯應用程式已停用，則只會看到 [**啟用**] 選項。

   * 在工具列上，選取 [**啟用**]。

     ![在 Azure 入口網站中啟用單一邏輯應用程式](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     如果您的邏輯應用程式已啟用，您只會看到 [**停**用] 選項。 

   Azure 入口網站會在主要 Azure 工具列上顯示通知，以確認您的作業是否成功或失敗。

   ![確認操作狀態的通知](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>停用或啟用多個邏輯應用程式

1. 在 [Azure 入口網站中，尋找您想要停用或啟用[的邏輯應用程式](#find-logic-app)。

1. 若要檢查邏輯應用程式目前是否已啟用或已停用，請在 [ **Logic Apps** ] 頁面上，檢查該邏輯應用程式的 [**狀態**] 資料行。 

   ![Logic Apps 狀態] 資料行](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   如果 [**狀態**] 欄位不可見，請在 [ **Logic Apps** ] 工具列上，選取 [**嘗試預覽**]。

   ![開啟預覽](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. 在 [checkbox] 資料行中，選取您想要停用或啟用的邏輯應用程式。 在工具列上，選取 [**停**用] 或 [**啟用**]。

   ![啟用或停用 Azure 入口網站中的多個邏輯應用程式](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 當確認方塊出現時，選取 **[是]** 以繼續。

   Azure 入口網站會在主要 Azure 工具列上顯示通知，以確認您的作業是否成功或失敗。

## <a name="delete-logic-apps"></a>刪除邏輯應用程式

您可以在 Azure 入口網站中，同時[刪除單一邏輯應用程式](#delete-single-logic-app)或[刪除多個邏輯應用程式](#delete-multiple-logic-apps)。 您也可以[在 Visual Studio 中刪除邏輯應用程式](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)。

刪除您的邏輯應用程式會以下列方式影響您的工作流程實例：

* 所有進行中和暫止的執行都會繼續執行，直到完成為止。 視這些執行的數目而定，此程式可能需要一些時間。

* Logic Apps 引擎不會建立或執行新的工作流程實例。

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>刪除單一邏輯應用程式

1. 在 [Azure 入口網站中，[尋找並開啟您的邏輯應用程式](#find-logic-app)。

1. 在邏輯應用程式的功能表中，選取 **[總覽**]。 在邏輯應用程式的工具列上，選取 [**刪除**]。

   ![在邏輯應用程式工具列上，選取 [刪除]](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 當確認方塊出現時，請輸入邏輯應用程式的名稱，然後選取 [**刪除**]。

   ![確認刪除您的邏輯應用程式](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure 入口網站會在主要 Azure 工具列上顯示通知，以確認您的作業是否成功或失敗。

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>刪除多個邏輯應用程式

1. 在 [Azure 入口網站中，[尋找您想要刪除的邏輯應用程式](#find-logic-app)。

1. 在 [checkbox] 資料行中，選取您想要刪除的邏輯應用程式。 在工具列上，選取 [刪除]****。

   ![刪除多個邏輯應用程式](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 當確認方塊出現時，請`yes`輸入，然後選取 [**刪除**]。

   ![確認刪除您的邏輯應用程式](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure 入口網站會在主要 Azure 工具列上顯示通知，以確認您的作業是否成功或失敗。

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>管理邏輯應用程式版本

您可以使用 Azure 入口網站進行邏輯應用程式的版本控制。 您可以找到邏輯應用程式的版本歷程記錄，並升級先前的版本。

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>尋找及查看舊版

1. 在 [Azure 入口網站中，[尋找您想要管理的邏輯應用程式](#find-logic-app)。

1. 在邏輯應用程式功能表的 [**開發工具**] 底下，選取 [**版本**]。

   ![在邏輯應用程式的功能表上，選取 [開發工具] 底下的 [版本]](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 從清單中選取您要管理的邏輯應用程式**版本**。 您可以在搜尋列中輸入**版本**識別碼，以篩選清單。

1. 在 [歷程**記錄版本**] 頁面上，您會在唯讀模式中看到先前版本的詳細資料。 您可以在 Logic Apps**設計**工具和程式**代碼視圖**模式之間進行選取。

   ![使用程式碼視圖和邏輯應用程式設計工具查看邏輯應用程式的歷程記錄版本頁面](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>升級先前的版本

1. 在邏輯應用程式的版本歷程記錄中，[尋找並選取您要升級的版本](#find-version-history)。

1. 在 [歷程**記錄版本**] 頁面上，選取 [**升級**]。

   ![邏輯應用程式版本歷程記錄中的 [升級] 按鈕](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 在開啟的 [ **Logic Apps 設計**工具] 頁面上，視需要編輯您要升級的版本。 您可以在**設計**工具和程式**代碼視圖**模式之間切換。 您也可以更新**參數**、**範本**和**連接器**。

   ![升級先前版本的 Logic Apps 設計工具頁面](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 若要儲存任何更新並完成升級先前的版本，請選取 [**儲存**]。 （或者，若要取消您的變更，請選取 [**捨棄**]。） 

   當您再次[查看邏輯應用程式的版本歷程記錄](#find-version-history)時，升級版本會顯示在清單頂端，並具有新的識別碼。

## <a name="next-steps"></a>後續步驟

* [監視邏輯應用程式](monitor-logic-apps.md)
