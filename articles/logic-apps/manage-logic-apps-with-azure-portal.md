---
title: 管理 Azure 入口網站中的邏輯應用程式
description: 使用 Azure 入口網站來編輯、啟用、停用或刪除邏輯應用程式。
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/20/2020
ms.openlocfilehash: d50f577a7170982be004cc8957114f79675fbc6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078616"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>管理 Azure 入口網站中的邏輯應用程式

您可以使用 [Azure 入口網站](https://portal.azure.com) 或 [Visual Studio](manage-logic-apps-with-visual-studio.md)來管理邏輯應用程式。 本文說明如何在 Azure 入口網站中編輯、停用、啟用或刪除邏輯應用程式。 如果您不熟悉 Azure Logic Apps，請參閱 [Azure Logic Apps 是什麼](logic-apps-overview.md)？

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 現有的邏輯應用程式。 若要瞭解如何在 Azure 入口網站中建立邏輯應用程式，請參閱 [快速入門：使用 Azure Logic Apps Azure 入口網站建立您的第一個工作流程](quickstart-create-first-logic-app-workflow.md)。

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>尋找邏輯應用程式

若要尋找並開啟您的邏輯應用程式，請遵循下列步驟：

1. 使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

1. 在 Azure 搜尋列中，輸入 `logic apps` ，然後選取 [ **Logic Apps**]。

   ![Azure 入口網站功能表的螢幕擷取畫面，並在搜尋列中搜尋並選取 Logic Apps。](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. 在 [ **Logic Apps** ] 頁面中，尋找並選取您想要管理的邏輯應用程式。

   邏輯應用程式的 **[總覽** ] 窗格開啟之後，您可以透過下列方式篩選顯示在 **Logic Apps** 頁面上的清單：

   * 依名稱搜尋邏輯應用程式
   * 依訂用帳戶、資源群組、位置和標記篩選邏輯應用程式
   * 依資源群組、類型、訂用帳戶和位置來分組邏輯應用程式

## <a name="view-logic-app-properties"></a>查看邏輯應用程式屬性

1. 在 Azure 入口網站中， [尋找並開啟您的邏輯應用程式](#find-logic-app)。

1. 從邏輯應用程式的功能表的 [ **設定**] 底下，選取 [ **屬性**]。

1. 在 [ **屬性** ] 窗格中，您可以查看和複製邏輯應用程式的下列資訊：

   * **名稱**
   * **資源識別碼**
   * **資源群組**
   * **位置**
   * **型別** 
   * **訂用帳戶名稱**
   * **訂用帳戶識別碼**
   * **存取端點**
   * **執行時間連出 IP 位址**
   * **存取端點 IP 位址**
   * **連接器連出 IP 位址**

## <a name="disable-or-enable-logic-apps"></a>停用或啟用邏輯應用程式

您可以在 Azure 入口網站中同時啟用或停用 [單一邏輯應用程式](#disable-enable-single-logic-app) 或 [多個邏輯應用程式](#disable-or-enable-multiple-logic-apps) 。 您也可以 [在 Visual Studio 中啟用或停用邏輯應用程式](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)。

停用邏輯應用程式會影響您的工作流程實例，並以下列方式執行：

* 所有進行中和擱置中的執行都會繼續執行，直到完成為止。 視這些執行的數目而定，此程式可能需要一些時間。

* Logic Apps 引擎不會建立或執行新的工作流程實例。

* 觸發程式不會在下一次符合其條件時引發。

* 觸發程式狀態會記住邏輯應用程式停止的時間點。 因此，如果您重新啟用邏輯應用程式，則會在上次執行之後引發所有未處理專案的觸發程式。

  若要停止邏輯應用程式在上一次執行之後引發未處理的專案，請在重新啟用邏輯應用程式之前，先清除觸發程式的狀態：

  1. 在 Azure 入口網站中， [尋找並開啟您的邏輯應用程式](#find-logic-app)。

  1. 編輯邏輯應用程式觸發程式的任何部分。

  1. 儲存您的變更。 此步驟會重設觸發程式的目前狀態。

  1. [重新啟用邏輯應用程式](#disable-enable-single-logic-app)。

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>停用或啟用單一邏輯應用程式

1. 在 Azure 入口網站中， [尋找並開啟您的邏輯應用程式](#find-logic-app)。

1. 在邏輯應用程式的功能表中，選取 **[總覽**]。 從下列選項中選擇：

   * 在工具列上，選取 [停用]  。

     ![邏輯應用程式工具列的螢幕擷取畫面，其中顯示已選取的停用按鈕。](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     如果您的邏輯應用程式已停用，您只會看到 [ **啟用** ] 選項。

   * 在工具列上，選取 [ **啟用**]。

     ![邏輯應用程式工具列的螢幕擷取畫面，其中顯示已選取 [啟用] 按鈕。](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     如果您的邏輯應用程式已啟用，您只會看到 [ **停** 用] 選項。 

   Azure 入口網站會在主要 Azure 工具列上顯示通知，以確認您的作業是否成功或失敗。

   ![Azure 入口網站的螢幕擷取畫面，其中顯示確認操作狀態的通知](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>停用或啟用多個邏輯應用程式

1. 在 Azure 入口網站中，尋找您想要停用或啟用 [的邏輯應用程式](#find-logic-app) 。

1. 若要檢查邏輯應用程式目前是否已啟用或停用，請在 [ **Logic Apps** ] 頁面上，檢查該邏輯應用程式的 [ **狀態** ] 資料行。 

   ![Azure 入口網站 Logic Apps 頁面的螢幕擷取畫面，其中顯示依狀態資料行排序的邏輯應用程式清單。](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   如果未顯示 [ **狀態** ] 資料行，請選取 [ **Logic Apps** ] 工具列上的 [ **試用預覽**]。

   ![Azure 入口網站 Logic Apps 頁面的螢幕擷取畫面，其中顯示已選取的 [試用預覽] 按鈕。](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. 在 [核取方塊] 欄中，選取您要停用或啟用的邏輯應用程式。 在工具列上，選取 [ **停** 用] 或 [ **啟用**]。

   ![Azure 入口網站 Logic Apps 頁面的螢幕擷取畫面，其中顯示多個邏輯應用程式的 [啟用] 和 [停用] 按鈕。](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 當確認方塊出現時，選取 [ **是]** 以繼續。

   Azure 入口網站會在主要 Azure 工具列上顯示通知，以確認您的作業是否成功或失敗。

## <a name="delete-logic-apps"></a>刪除邏輯應用程式

您可以 [刪除單一邏輯應用程式](#delete-single-logic-app) ，或同時刪除 Azure 入口網站中的 [多個邏輯應用程式](#delete-multiple-logic-apps) 。 您也可以 [在 Visual Studio 中刪除邏輯應用程式](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)。

刪除邏輯應用程式會以下列方式影響您的工作流程實例：

* 所有進行中和擱置中的執行都會繼續執行，直到完成為止。 視這些執行的數目而定，此程式可能需要一些時間。

* Logic Apps 引擎不會建立或執行新的工作流程實例。

> [!NOTE]
> 如果您刪除並重新建立子邏輯應用程式，則必須重新儲存父邏輯應用程式。 重新建立的子應用程式將會有不同的中繼資料。
> 如果您未在重新建立父邏輯應用程式之後重新儲存它的子系，您對子邏輯應用程式的呼叫將會失敗，並出現「未經授權」的錯誤。 此行為適用于父子式邏輯應用程式，例如使用整合帳戶中的成品或呼叫 Azure 函式的應用程式。

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>刪除單一邏輯應用程式

1. 在 Azure 入口網站中， [尋找並開啟您的邏輯應用程式](#find-logic-app)。

1. 在邏輯應用程式的功能表中，選取 **[總覽**]。 在邏輯應用程式的工具列上，選取 [ **刪除**]。

   ![邏輯應用程式工具列的螢幕擷取畫面，其中顯示已選取的 [刪除] 按鈕。](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 當確認方塊出現時，請輸入邏輯應用程式的名稱，然後選取 [ **刪除**]。

   ![確認刪除單一邏輯應用程式的 Logic Apps 提示螢幕擷取畫面。](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure 入口網站會在主要 Azure 工具列上顯示通知，以確認您的作業是否成功或失敗。

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>刪除多個邏輯應用程式

1. 在 Azure 入口網站中， [尋找您想要刪除的邏輯應用程式](#find-logic-app)。

1. 在 [核取方塊] 欄中，選取您要刪除的邏輯應用程式。 在工具列上，選取 [刪除]  。

   ![Logic Apps 頁面的螢幕擷取畫面，其中顯示已選取要刪除之清單中的多個邏輯應用程式。](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 當確認方塊出現時，請輸入 `yes` ，然後選取 [ **刪除**]。

   ![確認刪除多個邏輯應用程式的 Logic Apps 提示螢幕擷取畫面。](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure 入口網站會在主要 Azure 工具列上顯示通知，以確認您的作業是否成功或失敗。

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>管理邏輯應用程式版本

您可以使用 Azure 入口網站來控制邏輯應用程式的版本。 您可以尋找邏輯應用程式的版本歷程記錄，並提升先前的版本。

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>尋找和查看之前的版本

1. 在 Azure 入口網站中， [尋找您想要管理的邏輯應用程式](#find-logic-app)。

1. 在邏輯應用程式功能表的 [ **開發工具**] 底下，選取 [ **版本**]。

   ![Azure 入口網站中邏輯應用程式的螢幕擷取畫面，其中顯示 [開發工具] 下的 [版本] 頁面選擇。](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 從清單中選取您要管理的邏輯應用程式的 **版本** 。 您可以在搜尋列中輸入 **版本** 識別碼來篩選清單。

1. 在 [歷程 **記錄版本** ] 頁面上，您會在唯讀模式中看到先前版本的詳細資料。 您可以在 Logic Apps **設計** 工具和程式 **代碼視圖** 模式之間進行選取。

   ![Logic Apps 歷程記錄版本] 頁面的螢幕擷取畫面，其中顯示程式碼視圖和設計工具的視圖選項。](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>升級先前的版本

1. 在邏輯應用程式的版本歷程記錄中， [尋找並選取您要升級的版本](#find-version-history)。

1. 在 [歷程 **記錄版本** ] 頁面上，選取 [ **升級**]。

   ![邏輯應用程式版本歷程記錄的螢幕擷取畫面，顯示升級舊版的按鈕。](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 在開啟的 **Logic Apps 設計** 工具頁面上，視需要編輯您要升級的版本。 您可以在 **設計** 工具和程式 **代碼視圖** 模式之間切換。 您也可以更新 **參數**、 **範本**和 **連接器**。

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示升級舊版邏輯應用程式的按鈕。](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 若要儲存任何更新，並完成升級舊版的升級，請選取 [ **儲存**]。  (或，若要取消您的變更，請選取 [ **捨棄**]。 )  

   當您再次 [查看邏輯應用程式的版本歷程記錄](#find-version-history) 時，升級版本會顯示在清單頂端，並具有新的識別碼。

## <a name="next-steps"></a>接下來的步驟

* [監視 Logic Apps](monitor-logic-apps.md)
