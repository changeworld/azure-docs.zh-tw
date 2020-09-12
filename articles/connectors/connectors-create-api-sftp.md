---
title: '連接至 SFTP 帳戶 (已淘汰) '
description: 使用 Azure Logic Apps，將監視、建立、管理、傳送及接收 SFTP 伺服器檔案的工作和程式自動化
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ROBOTS: NOINDEX
ms.openlocfilehash: 817c17b0e05180f5c7a616320a25c2bc3c21c5f8
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651403"
---
# <a name="monitor-create-and-manage-sftp-files-in-azure-logic-apps"></a>在 Azure Logic Apps 中監視、建立及管理 SFTP 檔案

> [!IMPORTANT]
> 請使用 [SFTP SSH 連接器](../connectors/connectors-sftp-ssh.md) ，因為 sftp 連接器已淘汰。 您無法再于邏輯應用程式設計工具中選取 SFTP 觸發程式和動作。

若要在[安全檔案傳輸通訊協定 (SFTP)](https://www.ssh.com/ssh/sftp/) \(英文\) 伺服器上，將監視、建立、傳送及接收檔案的工作自動化，您可以使用 Azure Logic Apps 和 SFTP 連接器來建置整合工作流程並自動化。 SFTP 是一個網路通訊協定，可透過任何可靠的資料流提供檔案存取、檔案傳輸和檔案管理。 以下是一些您可自動化的範例工作：

* 監視檔案何時新增或變更。
* 取得、建立、複製、更新、列出以及刪除檔案。
* 取得檔案內容與中繼資料。
* 將封存檔案解壓縮到資料夾。

您可以使用觸發程序來監視 SFTP 伺服器上的事件，並讓輸出可供其他動作使用。 您可以使用動作，在 SFTP 伺服器上執行各種工作。 您也可以讓邏輯應用程式中的其他動作使用 SFTP 動作的輸出。 例如，如果您定期從 SFTP 伺服器擷取檔案，可以藉由使用 Office 365 Outlook 連接器或 Outlook.com 連接器，傳送關於那些檔案及其內容的電子郵件警示。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>限制

SFTP 連接器只會處理 *50 MB 或更小* 且不支援 [訊息區塊](../logic-apps/logic-apps-handle-large-messages.md)處理的檔案。 針對較大的檔案，請使用 [SFTP SSH 連接器](../connectors/connectors-sftp-ssh.md)。 如需 SFTP 連接器與 SFTP SSH 連接器之間的差異，請參閱 SFTP SSH 文章中的「 [sftp-ssh」與 Sftp 比較](../connectors/connectors-sftp-ssh.md#comparison) 。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您的 SFTP 伺服器位址和帳戶認證，這讓您的邏輯應用程式能夠存取您的 SFTP 帳戶。 若要使用[安全殼層 (SSH)](https://www.ssh.com/ssh/protocol/) \(英文\) 通訊協定，您也需要存取 SSH 私密金鑰和 SSH 私密金鑰密碼。

  > [!NOTE]
  >
  > SFTP 連接器支援下列私密金鑰格式：OpenSSH、ssh.com 和 PuTTY
  >
  > 當您建立邏輯應用程式時，必須在新增所需的 SFTP 觸發程序或動作之後，提供適用於 SFTP 伺服器的連線資訊。 
  > 如果您使用 SSH 私密金鑰，請確定會從您的 SSH 私密金鑰檔案「複製」****** 金鑰，然後將該金鑰「貼到」****** 連線詳細資料中，「請勿手動輸入或編輯該金鑰」******，這樣可能導致連線失敗。 
  > 如需詳細資訊，請參閱本文稍後的步驟。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 SFTP 帳戶的邏輯應用程式。 若要開始使用 SFTP 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 SFTP 動作，請使用其他觸發程序來啟動邏輯應用程式，例如「週期」**** 觸發程序。

## <a name="how-sftp-triggers-work"></a>SFTP 觸發程式的運作方式

SFTP 觸發程式的運作方式是輪詢 SFTP 檔案系統，並尋找自上次輪詢後已變更的任何檔案。 某些工具可讓您在檔案變更時保留時間戳記。 在這些情況下，您必須停用此功能，以便讓您的觸發程序可以運作。 以下是一些常見的設定：

| SFTP 用戶端 | 動作 |
|-------------|--------|
| Winscp | 移至**選項**  >  **喜好**設定  >  **傳輸**  >  **編輯**  >  **保留時間戳**  >  **停**用 |
| FileZilla | 移至 [**傳輸**  >  **保留已傳輸檔案的時間戳記**]  >  **停**用 |
|||

當觸發程序找到新檔案時，觸發程序會確認該新檔案是完整檔案，而不是部分寫入的檔案。 例如，當觸發程序檢查檔案伺服器時，檔案可能正在進行變更。 為避免傳回部分寫入的檔案，觸發程序會備註最近發生變更之檔案的時間戳記，但不會立即傳回該檔案。 觸發程序只有在再次輪詢伺服器時，才會傳回該檔案。 有時，此行為可能會導致最長可達觸發程序輪詢間隔兩倍的延遲。

## <a name="connect-to-sftp"></a>連接至 SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 針對空白邏輯應用程式，請在搜尋方塊中輸入 "sftp" 作為篩選條件。 在觸發程序清單底下，選取您想要的觸發程序。

   -或-

   若是現有的邏輯應用程式，請在想要新增動作的最後一個步驟底下，選擇 [新增步驟]。 在搜尋方塊中，輸入 "sftp" 作為篩選條件。 在動作清單底下，選取您想要的動作。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 為您的連線提供必要的詳細資料。

   > [!IMPORTANT]
   >
   > 當您在 [SSH 私密金鑰]**** 屬性中輸入 SSH 私密金鑰之後，請執行這些額外的步驟，以協助確定您會為這個屬性提供完整且正確的值。 
   > 無效的金鑰會導致連線失敗。

   雖然您可以使用任何文字編輯器，但下列範例步驟會使用 Notepad.exe 作為範例，來示範如何正確地複製並貼上您的金鑰。

   1. 在文字編輯器中開啟您的 SSH 私密金鑰檔案。 這些步驟會使用記事本作為範例。

   1. 在 [記事本] 的 [ **編輯** ] 功能表上，選取 [ **全選**]。

   1. 選取 [**編輯**  >  **複本**]。

   1. 在您新增的 SFTP 觸發程序或動作中，將所複製的「完整」** 金鑰貼到 [SSH 私密金鑰]**** 屬性中，此屬性支援多行。 ***請確定您會貼上***金鑰。 ***請勿手動輸入或編輯此金鑰***。

1. 當您完成輸入連線詳細資料之後，請選擇 [建立]****。

1. 為您選取的觸發程序或動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

## <a name="examples"></a>範例

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP 觸發程序：當新增或修改檔案時

此觸發程序會在 SFTP 伺服器上新增或變更了檔案時，啟動邏輯應用程式工作流程。 舉例來說，您可以新增條件來檢查檔案的內容，並根據內容是否符合指定條件來取得內容。 接著，您可以新增要取得檔案內容的動作，並將該內容放置於 SFTP 伺服器上的資料夾中。

**企業範例**：您可以使用此觸發程序，來監視代表客戶訂單的新檔案 SFTP 資料夾。 然後，您可以使用 SFTP 動作 (例如**取得檔案內容**)，來取得訂單的內容以進一步處理，並將該訂單儲存在訂單資料庫中。

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP 動作：取得內容

此動作會從 SFTP 伺服器上的檔案取得內容。 舉例來說，您可以新增來自上一個範例中的觸發程序，以及新增檔案內容必須符合的條件。 如果條件為 true，則可以執行會取得內容的動作。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
