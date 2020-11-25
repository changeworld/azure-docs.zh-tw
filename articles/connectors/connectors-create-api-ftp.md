---
title: 連接到 FTP 伺服器
description: 使用 Azure Logic Apps 將建立、監視及管理 FTP 伺服器上檔案的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999567"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>藉由使用 Azure Logic Apps 來建立、監視及管理 FTP 檔案

您可以使用 Azure Logic Apps 和 FTP 連接器，建立自動化的工作和工作流程，以便透過您在 FTP 伺服器上的帳戶，建立、監視、傳送及接收檔案，還有其他動作，例如：

* 監視檔案何時新增或變更。
* 取得、建立、複製、更新、列出以及刪除檔案。
* 取得檔案內容與中繼資料。
* 將封存檔案解壓縮到資料夾。

您可以使用觸發程序，從 FTP 伺服器收到回應，並且讓輸出可供其他動作使用。 您可以在邏輯應用程式中使用執行動作來管理 FTP 伺服器上的檔案。 您也可以讓其他動作使用 FTP 動作的輸出。 例如，如果您定期從 FTP 伺服器取得檔案，可以藉由使用 Office 365 Outlook 連接器或 Outlook.com 連接器，傳送關於這些檔案及其內容的電子郵件。 如果您不熟悉邏輯應用程式，請參閱 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)。

## <a name="limitations"></a>限制

* FTP 連接器僅支援透過 TLS/SSL (FTPS) 的明確 FTP，而且與隱含 FTPS 不相容。

* 根據預設，FTP 動作可以讀取或寫入 *50 MB 或更小* 的檔案。 為了處理大於 50 MB 的檔案，FTP 動作支援 [訊息區塊](../logic-apps/logic-apps-handle-large-messages.md)處理。 「 **取得檔案內容** 」動作會隱含地使用區塊化。

* FTP 觸發程式不支援區塊化。 在要求檔案內容時，觸發程式只會選取 50 MB 或更小的檔案。 若要取得大於 50 MB 的檔案，請依照下列模式：

  * 使用會傳回檔案屬性的 FTP 觸發程式，例如 **(屬性新增或修改檔案時，只)**。

  * 遵循具有 FTP **取得檔案內容** 動作的觸發程式，它會讀取完整的檔案，並隱含地使用區塊化。

* 如果您有內部部署 FTP 伺服器，請考慮建立 [整合服務環境 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 或使用 [Azure App Service 混合](../app-service/app-service-hybrid-connections.md)式連線，這兩者都可讓您在不使用內部部署資料閘道的情況下存取內部部署資料來源。

## <a name="how-ftp-triggers-work"></a>FTP 觸發程式的運作方式

FTP 觸發程式的運作方式是輪詢 FTP 檔案系統，並尋找自上次輪詢後已變更的任何檔案。 某些工具可讓您在檔案變更時保留時間戳記。 在這些情況下，您必須停用此功能，以便讓您的觸發程序可以運作。 以下是一些常見的設定：

| SFTP 用戶端 | 動作 |
|-------------|--------|
| Winscp | 移至 **選項**  >  **喜好** 設定  >  **傳輸**  >  **編輯**  >  **保留時間戳**  >  **停** 用 |
| FileZilla | 移至 [**傳輸**  >  **保留已傳輸檔案的時間戳記**]  >  **停** 用 |
|||

當觸發程序找到新檔案時，觸發程序會確認該新檔案是完整檔案，而不是部分寫入的檔案。 例如，當觸發程序檢查檔案伺服器時，檔案可能正在進行變更。 為避免傳回部分寫入的檔案，觸發程序會備註最近發生變更之檔案的時間戳記，但不會立即傳回該檔案。 觸發程序只有在再次輪詢伺服器時，才會傳回該檔案。 有時，此行為可能會導致最長可達觸發程序輪詢間隔兩倍的延遲。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* FTP 主機伺服器位址和帳戶認證

  要使用 FTP 連接器，FTP 伺服器必須可從網際網路來存取，並設定為以「被動」模式運作。 認證允許邏輯應用程式建立連線並存取 FTP 帳戶。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 FTP 帳戶的邏輯應用程式。 若要開始使用 FTP 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 FTP 動作，請使用其他觸發程序來啟動邏輯應用程式，例如 **週期** 觸發程序。

## <a name="connect-to-ftp"></a>連接至 FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 針對空白邏輯應用程式，請在 [搜尋] 方塊中輸入 `ftp` 作為篩選準則。 從 **觸發** 程式清單中選取您想要的觸發程式。

   -或-

   針對現有的邏輯應用程式，在您要新增動作的最後一個步驟底下，選取 [ **新增步驟**]，然後選取 [新增 **動作**]。 在搜尋方塊中，輸入 `ftp` 作為篩選條件。 從 [ **動作** ] 清單中選取您想要的動作。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇所顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 提供您的連接資訊，然後選取 [ **建立**]。

1. 提供所選觸發程式或動作的資訊，然後繼續建立邏輯應用程式的工作流程。

## <a name="examples"></a>範例

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>新增 FTP 觸發程式

**新增或修改檔案時 (屬性只有)** 觸發程式偵測到 FTP 伺服器上的檔案已新增或變更時，才會啟動邏輯應用程式工作流程。 例如，您可以新增條件來檢查檔案的內容，並根據內容是否符合指定的條件，決定是否要取得該內容。 最後，您可以新增可取得檔案內容的動作，並將該內容放在 SFTP 伺服器上的不同資料夾中。

例如，您可以使用此觸發程式來監視描述客戶訂單的新檔案 FTP 資料夾。 然後，您可以使用 FTP 動作（例如 **取得檔案中繼資料** ）來取得該新檔案的屬性，然後使用 [ **取得檔案內容** ] 取得該檔案的內容以進一步處理，並將該順序儲存在訂單資料庫中。

以下範例示範如何在 **將檔案新增或修改 (的屬性)** 觸發程式時使用。

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 針對空白邏輯應用程式，請在 [搜尋] 方塊中輸入 `ftp` 作為篩選準則。 在 [觸發程式] 清單下，選取 [此觸發 **程式：當加入或修改欄位時 (屬性])**

   ![尋找並選取 FTP 觸發程式](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. 為您的連線提供必要的詳細資料，然後選取 [ **建立**]。

   根據預設，此連接器會以文字格式傳送檔案。 若要以二進位格式傳送檔案，例如，在使用編碼的位置和時間，選取 **二進位傳輸**。

   ![建立與 FTP 伺服器的連接](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. 在 [ **資料夾** ] 方塊中，選取 [資料夾] 圖示，以顯示清單。 若要尋找您想要監視的新檔案或已編輯檔案的資料夾，請選取右角箭號 (**>**) ，流覽至該資料夾，然後選取該資料夾。

   ![尋找並選取要監視的資料夾](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   所選取的資料夾會出現在 [資料夾] 方塊中。

   ![選取的資料夾會出現在 [資料夾] 屬性中](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

邏輯應用程式已具有觸發程序，接著請新增在邏輯應用程式發現新檔案或編輯過的檔案時，所要執行的動作。 針對此範例，您可以新增 FTP 動作，用以取得新內容或更新過的內容。

<a name="get-content"></a>

### <a name="add-ftp-action"></a>新增 FTP 動作

**取得檔案中繼資料** 動作會取得 ftp 伺服器上檔案的屬性，而「**取得檔案內容**」動作會根據 ftp 伺服器上該檔案的相關資訊來取得檔案內容。 例如，您可以新增先前範例中的觸發程式，以及這些動作，以在新增或編輯該檔案之後取得檔案的內容。

1. 在觸發程式或任何其他動作底下，選取 [ **新增步驟**]。

1. 在搜尋方塊中，輸入 `ftp` 作為篩選條件。 在 [動作] 清單下方，選取此動作： **取得檔案中繼資料**

   ![選取 [取得檔案中繼資料] 動作](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. 如果您已經連線至 FTP 伺服器和帳戶，請移至下一個步驟。 否則，請提供該連接的必要詳細資料，然後選取 [ **建立**]。

   ![建立 FTP 伺服器連線](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. 出現 [ **取得檔案中繼資料** ] 動作之後，在 **[檔案** ] 方塊內按一下，以顯示動態內容清單。 您現在可以選取先前步驟的輸出中所具有的屬性。 在動態內容清單中，選取 [ **取得檔案中繼資料**] 下的 [檔案識別碼] 屬性 **清單** ，該屬性會參考新增或更新檔案的集合。

   ![尋找並選取 [檔案識別碼清單] 屬性](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   檔案 **識別碼屬性清單** 現在會出現 **在 [檔案** ] 方塊中。

   ![已選取 [檔案識別碼清單] 屬性](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. 現在新增此 FTP 動作： **取得檔案內容**

   ![尋找並選取 [取得檔案內容] 動作](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. 出現 [ **取得檔案內容** ] 動作之後， **在 [檔案** ] 方塊內按一下，以顯示動態內容清單。 您現在可以選取先前步驟的輸出中所具有的屬性。 在動態內容清單中，選取 [ **取得檔案中繼資料**] 下的 [ **Id** ] 屬性，它會參考已新增或更新的檔案。

   ![尋找並選取 [識別碼] 屬性](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   [ **識別碼** ] 屬性現在會出現 **在 [檔案** ] 方塊中。

   ![選取的 [識別碼] 屬性](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. 儲存您的邏輯應用程式。

## <a name="test-your-logic-app"></a>測試應用程式邏輯

若要檢查您的工作流程是否傳回您預期的內容，請新增另一個動作，以從上傳或更新的檔案傳送內容給您。

1. 在 [ **取得檔案內容** ] 動作下，新增可將檔案內容傳送給您的動作。 此範例會新增 Office 365 Outlook 的 [ **傳送電子郵件** ] 動作。

   ![新增傳送電子郵件的動作](./media/connectors-create-api-ftp/select-send-email-action.png)

1. 在動作出現之後，提供資訊並包含您想要測試的屬性。 例如，在 [**取得檔案內容**] 區段中選取 [**查看更多**] 之後，請包含 [檔案 **內容**] 屬性，它會出現在動態內容清單中。

   ![提供電子郵件動作的相關資訊](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. 儲存您的邏輯應用程式。 若要執行並觸發邏輯應用程式，請在工具列上選取 [ **執行**]，然後將檔案新增至邏輯應用程式現在所監視的 FTP 資料夾。

## <a name="connector-reference"></a>連接器參考

如需此連接器的更多技術詳細資料，如連接器的 Swagger 檔案所述的觸發程式、動作和限制，請參閱 [連接器的參考頁面](/connectors/ftpconnector/)。

> [!NOTE]
> 若為 [整合服務環境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用程式 (ISE) ，此連接器的 ISE 標記版本會改用 [ise 訊息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)

