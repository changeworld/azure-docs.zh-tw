---
title: 在 Azure 門戶管理邏輯應用
description: 使用 Azure 入口編輯、啟用、禁用或刪除邏輯應用。
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416002"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>在 Azure 門戶管理邏輯應用

您可以使用[Azure 門戶](https://portal.azure.com)或[可視化工作室](manage-logic-apps-with-visual-studio.md)管理邏輯應用。 本文演示如何在 Azure 門戶中編輯、禁用、啟用或刪除邏輯應用。 如果您是 Azure 邏輯應用的新增功能,請參閱[什麼是 Azure 邏輯應用](logic-apps-overview.md)?

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 現有邏輯應用。 要瞭解如何在 Azure 門戶中建立邏輯應用,請參閱[快速入門:使用 Azure 邏輯應用 - Azure 門戶建立第一個工作流](quickstart-create-first-logic-app-workflow.md)。

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>尋找邏輯應用程式

要查找和打開邏輯應用,請按照以下步驟操作:

1. 使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

1. 在 Azure 搜尋`logic apps`列中 ,輸入 ,然後選擇**邏輯應用**。

   ![尋找並選取「邏輯應用程式」](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. 在 **「邏輯應用」** 頁上,尋找並選擇要管理的邏輯應用。

   開啟邏輯應用的 **「概述」** 窗格後,可以透過以下方式篩選 **「邏輯應用」** 頁上顯示的清單:

   * 依名稱搜尋邏輯應用
   * 按訂閱、資源組、位置和標記篩選邏輯應用
   * 依資源群組、類型、訂閱和位置對邏輯應用進行群組

## <a name="view-logic-app-properties"></a>檢視邏輯應用屬性

1. 在 Azure 門戶中,[尋找並開啟邏輯應用](#find-logic-app)。

1. 從邏輯應用的選單中,**在"設置"** 下,選擇 **"屬性**"。

1. 在 **「屬性」** 窗格中, 您可以檢視與複製有關邏輯應用的以下資訊:

   * **名稱**
   * **資源識別碼**
   * **資源群組**
   * **位置**
   * **型別** 
   * **訂閱名稱**
   * **訂閱識別碼**
   * **存取終結點**
   * **執行時傳出 IP 位址**
   * **存取終結點 IP 位址**
   * **連接器傳出 IP 位址**

## <a name="disable-or-enable-logic-apps"></a>關閉或啟用邏輯套用

您可以在 Azure 門戶中同時啟用或關閉[單個邏輯應用](#disable-enable-single-logic-app)或[多個邏輯應用](#disable-or-enable-multiple-logic-apps)。 您可以在[Visual Studio 開啟或關閉的邏輯應用](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)。

禁用邏輯應用會影響工作流實例,並按以下方式運行:

* 所有正在進行的和掛起的運行將繼續,直到它們完成。 根據這些運行的數量,此過程可能需要一些時間。

* 邏輯應用引擎不會創建或運行新的工作流實例。

* 下次滿足其條件時,觸發器不會觸發。

* 觸發器狀態記住邏輯應用停止的點。 因此,如果重新啟用邏輯應用,觸發器將觸發自上次運行以來所有未處理的專案。

  要阻止邏輯應用自上次運行以來觸發未處理的專案,請清除觸發器的狀態,然後再重新啟用邏輯應用:

  1. 在 Azure 門戶中,[尋找並開啟邏輯應用](#find-logic-app)。

  1. 編輯邏輯應用觸發器的任何部分。

  1. 儲存您的變更。 此步驟將重置觸發器的當前狀態。

  1. [重新開啟邏輯套用](#disable-enable-single-logic-app)。

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>關閉或啟用單一個邏輯套用

1. 在 Azure 門戶中,[尋找並開啟邏輯應用](#find-logic-app)。

1. 在邏輯應用的功能表中,選擇 **「概述**」。。 從以下選項中選擇:

   * 在工具列上，選取 [停用]****。

     ![在 Azure 門戶中關閉單一個邏輯應用](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     如果邏輯應用已被禁用,則只能看到 **"啟用**"選項。

   * 在工具列上,選擇 **「啟用」。**

     ![在 Azure 門戶開啟單一個邏輯應用](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     如果邏輯應用已啟用,則只能看到 **"禁用**"選項。 

   Azure 門戶在主 Azure 工具列上顯示一條通知,用於確認操作是成功還是失敗。

   ![確認操作狀態的通知](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>關閉或啟用多個邏輯套用

1. 在 Azure 門戶中,找到要關閉或啟用[的邏輯應用](#find-logic-app)。

1. 要檢查邏輯應用當前是啟用還是禁用,請在 **「邏輯應用」** 頁上查看該邏輯應用**的狀態**列。 

   ![邏輯應用狀態列](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   如果 **「狀態**」列不可見,請在 **「邏輯應用」** 工具列上選擇 **「嘗試預覽**」。。

   ![開啟預覽](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. 在複選方塊列中,選擇要禁用或啟用的邏輯應用。 在工具列上,選擇 **「禁用**」或 **「啟用**」。

   ![在 Azure 門戶中啟用或關閉多個邏輯應用](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 當確認框出現時,選擇 **「是**」以繼續。

   Azure 門戶在主 Azure 工具列上顯示一條通知,用於確認操作是成功還是失敗。

## <a name="delete-logic-apps"></a>移除邏輯應用

您可以在 Azure 閘戶中同時[刪除單一個邏輯應用](#delete-single-logic-app)或刪除[多個邏輯應用](#delete-multiple-logic-apps)。 您也可以在[Visual Studio 中移除邏輯應用程式](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)。

刪除邏輯應用會以以下方式影響工作流實例:

* 所有正在進行的和掛起的運行將繼續,直到它們完成。 根據這些運行的數量,此過程可能需要一些時間。

* 邏輯應用引擎不會創建或運行新的工作流實例。

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>移除單一個邏輯應用

1. 在 Azure 門戶中,[尋找並開啟邏輯應用](#find-logic-app)。

1. 在邏輯應用的功能表中,選擇 **「概述**」。。 在邏輯應用的工具列上,選擇 **「刪除**」。。

   ![在邏輯應用工具列上,選擇"刪除"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 當確認框出現時,輸入邏輯應用的名稱,然後選擇 **「刪除**」。

   ![確認刪除邏輯應用](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure 門戶在主 Azure 工具列上顯示一條通知,用於確認操作是成功還是失敗。

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>移除多個邏輯應用

1. 在 Azure 門戶[中,尋找要刪除的邏輯應用](#find-logic-app)。

1. 在複選方塊列中,選擇要刪除的邏輯應用。 在工具列上，選取 [刪除]****。

   ![移除多個邏輯應用](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 當確認框出現時,輸入`yes`,然後選擇 **"刪除**"。

   ![確認刪除邏輯應用](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure 門戶在主 Azure 工具列上顯示一條通知,用於確認操作是成功還是失敗。

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>管理邏輯應用版本

可以使用 Azure 門戶對邏輯應用進行版本控制。 您可以找到邏輯應用的版本歷史記錄,並升級以前的版本。

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>尋找及檢視早期版本

1. 在 Azure 門戶中,[找到要管理的邏輯應用](#find-logic-app)。

1. 在邏輯應用的功能表中,在**開發工具**下,選擇 **「版本」。**

   ![在邏輯應用的功能表上,選擇"開發工具"下的"版本"](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 從清單中選擇要管理的邏輯應用**的版本**。 您可以在搜尋列中輸入 **「版本**」識別碼以篩選清單。

1. 在**歷史記錄版本**頁上,您將在唯讀模式下看到以前版本的詳細資訊。 您可以在邏輯應用**設計器**和**代碼檢視**模式之間進行選擇。

   ![具有代碼檢視和邏輯應用設計器檢視的邏輯應用的歷史紀錄版本頁](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>升級早期版本

1. 在邏輯應用的版本紀錄中,[尋找並選擇要升級的版本](#find-version-history)。

1. 在**歷史記錄版本**「頁上,選擇 **」升級**」。

   ![在邏輯應用的版本歷史紀錄中推廣按鈕](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 在打開**的邏輯應用設計器**頁面上,根據需要編輯要升級的版本。 您可以在**設計器**和**代碼檢視**模式之間切換。 您可以更新**參數**、**樣本**與**連接器**。

   ![用於推廣早期版本的邏輯應用設計器頁面](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 要保存任何更新並完成升級以前的版本,請選擇"**儲存**" (或者,要取消更改,請選擇 **"丟棄**") 

   當您再次[查看邏輯應用的版本歷史記錄](#find-version-history)時,升級的版本將顯示在列表頂部,並具有新的標識碼。

## <a name="next-steps"></a>後續步驟

* [監視邏輯應用程式](monitor-logic-apps.md)
