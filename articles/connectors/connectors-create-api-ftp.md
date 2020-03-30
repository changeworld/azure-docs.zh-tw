---
title: 連接到 FTP 伺服器
description: 使用 Azure 邏輯應用自動執行在 FTP 伺服器上創建、監視和管理檔的任務和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 94ca609d9dc070e6e2b4dc878ecd8dfaf9331ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648167"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>藉由使用 Azure Logic Apps 來建立、監視及管理 FTP 檔案

您可以使用 Azure Logic Apps 和 FTP 連接器，建立自動化的工作和工作流程，以便透過您在 FTP 伺服器上的帳戶，建立、監視、傳送及接收檔案，還有其他動作，例如：

* 監視檔案何時新增或變更。
* 取得、建立、複製、更新、列出以及刪除檔案。
* 取得檔案內容與中繼資料。
* 將封存檔案解壓縮到資料夾。

您可以使用觸發程序，從 FTP 伺服器收到回應，並且讓輸出可供其他動作使用。 您可以在邏輯應用程式中使用執行動作來管理 FTP 伺服器上的檔案。 您也可以讓其他動作使用 FTP 動作的輸出。 例如，如果您定期從 FTP 伺服器取得檔案，可以藉由使用 Office 365 Outlook 連接器或 Outlook.com 連接器，傳送關於這些檔案及其內容的電子郵件。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md)。

## <a name="limitations"></a>限制

* FTP 連接器僅支援通過 SSL （FTPS） 的顯式 FTP，並且與隱式 FTPS 不相容。

* 預設情況下，FTP 操作可以讀取或寫入*50 MB 或較小的*檔。 要處理大於 50 MB 的檔，FTP 操作支援[消息分塊](../logic-apps/logic-apps-handle-large-messages.md)。 **獲取檔內容**操作隱式使用分塊。

* FTP 觸發器不支援分塊。 請求檔內容時，觸發器僅選擇 50 MB 或較小的檔。 若要取得大於 50 MB 的檔案，請依照下列模式：

  * 使用返回檔案屬性的 FTP 觸發器，例如**添加或修改檔時（僅限屬性）。**

  * 使用 FTP Get**檔內容**操作遵循觸發器，該操作讀取完整檔並隱式使用分塊。

* 如果您有本地 FTP 伺服器，請考慮創建[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)或使用 Azure[應用服務混合連接](../app-service/app-service-hybrid-connections.md)，這兩個連接都允許您訪問本地資料來源而無需使用本地資料閘道。

## <a name="how-ftp-triggers-work"></a>FTP 觸發器的工作原理

FTP 通過輪詢 FTP 檔案系統並查找自上次輪詢以來更改的任何檔來觸發工作。 某些工具可讓您在檔案變更時保留時間戳記。 在這些情況下，您必須停用此功能，以便讓您的觸發程序可以運作。 以下是一些常見的設定：

| SFTP 用戶端 | 動作 |
|-------------|--------|
| Winscp | 轉到**選項** > **首選項** > **傳輸** > **Edit** > **Disable**編輯**保留時間戳**禁用 >  |
| FileZilla | 轉到**傳輸** > **保留已傳輸檔** > 的時間戳記**禁用** |
|||

當觸發程序找到新檔案時，觸發程序會確認該新檔案是完整檔案，而不是部分寫入的檔案。 例如，當觸發程序檢查檔案伺服器時，檔案可能正在進行變更。 為避免傳回部分寫入的檔案，觸發程序會備註最近發生變更之檔案的時間戳記，但不會立即傳回該檔案。 觸發程序只有在再次輪詢伺服器時，才會傳回該檔案。 有時，此行為可能會導致最長可達觸發程序輪詢間隔兩倍的延遲。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* FTP 主機伺服器位址和帳戶認證

  要使用 FTP 連接器，FTP 伺服器必須可從網際網路來存取，並設定為以「被動」** 模式運作。 認證允許邏輯應用程式建立連線並存取 FTP 帳戶。

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 FTP 帳戶的邏輯應用程式。 若要開始使用 FTP 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 FTP 動作，請使用其他觸發程序來啟動邏輯應用程式，例如**週期**觸發程序。

## <a name="connect-to-ftp"></a>連接至 FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登錄到 Azure[門戶](https://portal.azure.com)，並在邏輯應用設計器中打開邏輯應用。

1. 對於空白邏輯應用，在搜索框中，輸入`ftp`作為篩選器。 從 **"觸發器"** 清單中，選擇所需的觸發器。

   -或-

   對於現有邏輯應用，在要添加操作的最後一步下，選擇 **"新建步驟**"，然後選擇"**添加操作**"。 在搜尋方塊中，輸入 `ftp` 作為篩選條件。 從 **"操作"** 清單中，選擇所需的操作。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 提供連接資訊，然後選擇 **"創建**"。

1. 提供所選觸發器或操作的資訊，並繼續構建邏輯應用的工作流。

## <a name="examples"></a>範例

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>添加 FTP 觸發器

**當檔被添加或修改（僅屬性）** 觸發器檢測到在 FTP 伺服器上添加或更改檔時，觸發器將啟動邏輯應用工作流。 例如，您可以添加一個條件，檢查檔的內容，並根據該內容是否符合指定條件決定是否獲取該內容。 最後，您可以添加獲取檔內容的操作，並將該內容放入 SFTP 伺服器上的不同資料夾中。

例如，可以使用此觸發器監視 FTP 資料夾，以獲取描述客戶訂單的新檔。 然後，可以使用 FTP 操作（如**獲取檔中繼資料**）來獲取該新檔的屬性，然後使用**Get 檔內容**從該檔獲取內容以進行進一步處理，並將該順序存儲在訂單資料庫中。

下面是一個示例，演示如何使用 **"何時添加或修改檔（僅限屬性）"** 觸發器。

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 對於空白邏輯應用，在搜索框中，輸入`ftp`作為篩選器。 在觸發器清單下，選擇此觸發器：**添加或修改歸檔的觸發器時（僅限屬性）**

   ![查找並選擇 FTP 觸發器](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. 提供連接的必要詳細資訊，然後選擇 **"創建**"。

   根據預設，此連接器會以文字格式傳送檔案。 若要以二進位格式傳送檔案，例如，在使用編碼的位置和時間，選取**二進位傳輸**。

   ![創建與 FTP 伺服器的連接](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. 在 **"資料夾**"框中，選擇資料夾圖示，以便顯示清單。 要查找要監視新檔或已編輯檔的資料夾，請選擇直角箭頭 （），**>** 然後選擇該資料夾。

   ![尋找並選取要監視的資料夾](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   所選取的資料夾會出現在 [資料夾]**** 方塊中。

   ![所選資料夾將顯示在"資料夾"屬性中](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]****。

邏輯應用程式已具有觸發程序，接著請新增在邏輯應用程式發現新檔案或編輯過的檔案時，所要執行的動作。 針對此範例，您可以新增 FTP 動作，用以取得新內容或更新過的內容。

<a name="get-content"></a>

### <a name="add-ftp-action"></a>添加 FTP 操作

**Get 檔中繼資料**操作獲取 FTP 伺服器上的檔的屬性，**而 Get 檔內容**操作基於 FTP 伺服器上有關該檔的資訊獲取檔內容。 例如，您可以在添加或編輯該檔後，從前面的示例中添加觸發器和這些操作來獲取檔的內容。

1. 在觸發器或任何其他操作下，選擇 **"新建步驟**"。

1. 在搜尋方塊中，輸入 `ftp` 作為篩選條件。 在動作清單下，選擇此操作：**獲取檔中繼資料**

   ![選擇"獲取檔中繼資料"操作](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. 如果您已經連線至 FTP 伺服器和帳戶，請移至下一個步驟。 否則，請提供該連接的必要詳細資訊，然後選擇 **"創建**"。

   ![建立 FTP 伺服器連線](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. **顯示"獲取檔中繼資料**"操作後，按一下 **"檔**"框，以便顯示動態內容清單。 您現在可以選取先前步驟的輸出中所具有的屬性。 在動態內容清單中，在 **"獲取檔中繼資料**"下，選擇"**檔 Id 清單"** 屬性，該屬性引用添加或更新檔的集合。

   ![查找並選擇"檔 ID 清單"屬性](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   "**檔 ID 清單"** 屬性現在顯示在 **"檔"** 框中。

   ![選定的"檔 ID 清單"屬性](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. 現在添加此 FTP 操作：**獲取檔內容**

   ![查找並選擇"獲取檔內容"操作](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. **顯示"獲取檔內容"** 操作後，按一下 **"檔**"框，以便顯示動態內容清單。 您現在可以選取先前步驟的輸出中所具有的屬性。 在動態內容清單中，在 **"獲取檔中繼資料**"下，選擇 **"Id"** 屬性，該屬性引用已添加或更新的檔。

   ![查找並選擇"Id"屬性](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   **Id**屬性現在顯示在 **"檔"** 框中。

   ![選定的"Id"屬性](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. 儲存您的邏輯應用程式。

## <a name="test-your-logic-app"></a>測試應用程式邏輯

要檢查工作流是否返回預期內容，請添加另一個從上載或更新檔中向您發送內容的操作。

1. 在 **"獲取檔內容**"操作下，添加一個可以向您發送檔內容的操作。 本示例添加 Office 365 Outlook**的電子郵件發送操作**。

   ![添加用於發送電子郵件的操作](./media/connectors-create-api-ftp/select-send-email-action.png)

1. 出現操作後，請提供資訊並包含要測試的屬性。 例如，包括 **"檔內容**"屬性，該屬性在選擇"**獲取檔內容"** 部分**See more**後顯示在動態內容清單中。

   ![提供電子郵件動作的相關資訊](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. 儲存您的邏輯應用程式。 要在工具列上運行和觸發邏輯應用，請選擇 **"運行**"，然後將檔添加到邏輯應用現在監視的 FTP 資料夾中。

## <a name="connector-reference"></a>連接器參考

有關此連接器的更多技術詳細資訊，例如連接器的 Swagger 檔所述的觸發器、操作和限制，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/ftpconnector/)。

> [!NOTE]
> 對於[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用，此連接器的 ISE 標記版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
