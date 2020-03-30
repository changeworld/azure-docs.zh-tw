---
title: 使用 SSH 連接到 SFTP 伺服器
description: 藉由使用 SSH 和 Azure Logic Apps，讓針對 SFTP 伺服器監視、建立、管理、傳送及接收檔案的工作自動化
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 03/7/2020
tags: connectors
ms.openlocfilehash: d4ab7425c967d3a176c0a576d0be38ece1701b8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128405"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>藉由使用 SSH 和 Azure Logic Apps 來監視、建立及管理 SFTP 檔案

若要使用 [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) 通訊協定在[安全檔案傳輸通訊協定 (SFTP)](https://www.ssh.com/ssh/sftp/) \(英文\) 伺服器上，將監視、建立、傳送及接收檔案的工作自動化，您可以使用 Azure Logic Apps 和 SFTP-SSH 連接器來建置整合工作流程並自動化。 SFTP 是一個網路通訊協定，可透過任何可靠的資料流提供檔案存取、檔案傳輸和檔案管理。 以下是一些您可自動化的範例工作：

* 監視檔案何時新增或變更。
* 取得、建立、複製、重新命名、更新、列出及刪除檔案。
* 建立資料夾。
* 取得檔案內容與中繼資料。
* 將封存檔案解壓縮到資料夾。

您可以使用觸發程序來監視 SFTP 伺服器上的事件，並讓輸出可供其他動作使用。 您可以使用動作，在 SFTP 伺服器上執行各種工作。 您也可以讓邏輯應用程式中的其他動作使用 SFTP 動作的輸出。 例如，如果您定期從 SFTP 伺服器擷取檔案，可以藉由使用 Office 365 Outlook 連接器或 Outlook.com 連接器，傳送關於那些檔案及其內容的電子郵件警示。 如果您是邏輯應用的新增功能，請查看什麼是[Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)

有關 SFTP-SSH 連接器和 SFTP 連接器之間的差異，請查看本主題後面的[比較 SFTP-SSH 與 SFTP](#comparison)部分。

## <a name="limits"></a>限制

* 支援[分塊](../logic-apps/logic-apps-handle-large-messages.md)的 SFTP-SSH 操作可以處理最多 1 GB 的檔，而不支援分塊的 SFTP-SSH 操作可以處理高達 50 MB 的檔。 儘管預設塊大小為 15 MB，但根據網路延遲、伺服器回應時間等因素，此大小可以動態更改，從 5 MB 開始，逐漸增加到 50 MB 最大值。

  > [!NOTE]
  > 對於[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用，此連接器的 ISE 標記版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

  當您[指定要使用的常量塊大小時，](#change-chunk-size)可以重寫此自我調整行為。 此大小的範圍可以從 5 MB 到 50 MB。 例如，假設您有一個 45 MB 的檔和網路，可以支援該檔案大小，而不會延遲。 自我調整分塊會導致多個調用，而不是一個調用。 要減少調用數，可以嘗試設置 50 MB 區塊大小。 在不同的方案中，如果邏輯應用超時，例如，當使用 15 MB 塊時，可以嘗試將大小減小到 5 MB。

  塊大小與連接相關聯，這意味著您可以將同一連接用於支援分塊的操作，然後對不支援分塊的操作使用相同的連接。 在這種情況下，不支援分塊操作的區塊大小範圍從 5 MB 到 50 MB。 下表顯示了哪些 SFTP-SSH 操作支援分塊：

  | 動作 | 塊狀支援 | 覆蓋塊大小支援 |
  |--------|------------------|-----------------------------|
  | **複製檔案** | 否 | 不適用 |
  | **創建檔** | 是 | 是 |
  | **創建資料夾** | 不適用 | 不適用 |
  | **刪除檔案** | 不適用 | 不適用 |
  | **將封存檔案解壓縮到資料夾** | 不適用 | 不適用 |
  | **取得檔案內容** | 是 | 是 |
  | **使用路徑來取得檔案內容** | 是 | 是 |
  | **獲取檔中繼資料** | 不適用 | 不適用 |
  | **使用路徑取得檔案中繼資料** | 不適用 | 不適用 |
  | **列出資料夾中的檔案** | 不適用 | 不適用 |
  | **重新命名檔案** | 不適用 | 不適用 |
  | **更新檔案** | 否 | 不適用 |
  ||||

* SFTP-SSH 觸發器不支援消息分塊。 請求檔內容時，觸發器僅選擇 15 MB 或較小的檔。 要獲取大於 15 MB 的檔，請改用此模式：

  1. 使用僅返回檔案屬性的 SFTP-SSH 觸發器，例如**添加或修改檔時（僅限屬性）。**

  1. 使用 SFTP-SSH Get**檔內容**操作遵循觸發器，該操作讀取完整檔並隱式使用消息分塊。

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>比較 SFTP-SSH 與 SFTP

以下是 SFTP-SSH 連接器與 SFTP 連接器之間的其他主要差異，其中 SFTP-SSH 連接器具備這些功能：

* 使用[SSH.NET庫](https://github.com/sshnet/SSH.NET)，它是支援 .NET 的開源安全外殼 （SSH） 庫。

* 提供**建立資料夾**動作，可在 SFTP 伺服器上指定的路徑中建立資料夾。

* 提供**重新命名檔案**動作，可重新命名 SFTP 伺服器上的檔案。

* 可將連線快取至 SFTP 伺服器*最多 1 小時*，這可以改善效能並減少嘗試連線伺服器的次數。 若要設定此快取行為的持續期間，請編輯 SFTP 伺服器 SSH 組態中的 [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) 屬性。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您的 SFTP 伺服器位址和帳戶認證，這讓您的邏輯應用程式能夠存取您的 SFTP 帳戶。 您也可以存取 SSH 私密金鑰和 SSH 私密金鑰密碼。 要在上載大型檔時使用分塊，需要在 SFTP 伺服器上的根資料夾同時使用讀取和寫入權限。 否則，您會收到"401 未授權"錯誤。

  > [!IMPORTANT]
  >
  > SFTP-SSH 連接器*只*支援這些私密金鑰格式、演算法和指紋：
  >
  > * **私密金鑰格式**： RSA （Rivest Shamir Adleman） 和 DSA （數位簽章演算法） 鍵在 OpenSSH 和ssh.com格式。 如果您的私密金鑰為 PuTTY （.ppk） 檔案格式，請先[將金鑰轉換為 OpenSSH （.pem） 檔案格式](#convert-to-openssh)。
  >
  > * **加密演算法**：DES-EDE3-CBC、DES-EDE3-CFB、 DES-CBC、AES-128-CBC、AES-192-CBC 和 AES-256-CBC
  >
  > * **指紋**：MD5
  >
  > 將 SFTP-SSH 觸發器或操作添加到邏輯應用後，必須為 SFTP 伺服器提供連接資訊。 為此連接提供 SSH 私密金鑰時，***不要手動輸入或編輯該金鑰***，這可能會導致連接失敗。 相反，請確保從 SSH 私密金鑰檔***複製金鑰***，並將該金鑰***粘貼***到連接詳細資訊中。 
  > 有關詳細資訊，請參閱本文後面的 SSH 部分連接到[SFTP。](#connect)

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 SFTP 帳戶的邏輯應用程式。 若要開始使用 SFTP-SSH 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 SFTP-SSH 動作，請使用其他觸發程序來啟動邏輯應用程式，例如「週期」**** 觸發程序。

## <a name="how-sftp-ssh-triggers-work"></a>SFTP-SSH 觸發器的工作原理

SFTP-SSH 通過輪詢 SFTP 檔案系統並查找自上次輪詢以來更改的任何檔來觸發工作。 某些工具可讓您在檔案變更時保留時間戳記。 在這些情況下，您必須停用此功能，以便讓您的觸發程序可以運作。 以下是一些常見的設定：

| SFTP 用戶端 | 動作 |
|-------------|--------|
| Winscp | 轉到**選項** > **首選項** > **傳輸** > **Edit** > **Disable**編輯**保留時間戳**禁用 >  |
| FileZilla | 轉到**傳輸** > **保留已傳輸檔** > 的時間戳記**禁用** |
|||

當觸發程序找到新檔案時，觸發程序會確認該新檔案是完整檔案，而不是部分寫入的檔案。 例如，當觸發程序檢查檔案伺服器時，檔案可能正在進行變更。 為避免傳回部分寫入的檔案，觸發程序會備註最近發生變更之檔案的時間戳記，但不會立即傳回該檔案。 觸發程序只有在再次輪詢伺服器時，才會傳回該檔案。 有時，此行為可能會導致最長可達觸發程序輪詢間隔兩倍的延遲。

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>將基於 PuTTY 的金鑰轉換為 OpenSSH

如果您的私密金鑰採用 PuTTY 格式（使用 .ppk （PuTTY 私密金鑰）檔案名副檔名），請先將金鑰轉換為 OpenSSH 格式，該格式使用 .pem（隱私增強郵件）檔案名副檔名。

### <a name="unix-based-os"></a>基於 Unix 的作業系統

1. 如果系統上尚未安裝 PuTTY 工具，請立即執行此操作，例如：

   `sudo apt-get install -y putty`

1. 運行此命令，該命令創建可用於 SFTP-SSH 連接器的檔：

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   例如：

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows 作業系統

1. 如果您尚未這樣做，[請下載最新的 PuTTY 產生器 （puttygen.exe） 工具](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)，然後啟動該工具。

1. 在此螢幕上，選擇 **"載入**"。

   ![選擇"載入"](./media/connectors-sftp-ssh/puttygen-load.png)

1. 以 PuTTY 格式流覽到您的私密金鑰檔，然後選擇 **"打開**"。

1. 從 **"轉換"** 功能表中，選擇 **"匯出 OpenSSH"鍵**。

   ![選擇"匯出 OpenSSH 金鑰"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. 使用`.pem`檔案名副檔名保存私密金鑰檔。

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>使用 SSH 連線至 SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 對於空白邏輯應用，在搜索框中，輸入`sftp ssh`作為篩選器。 在觸發程序清單底下，選取您想要的觸發程序。

   -或-

   對於現有邏輯應用，在要添加操作的最後一步下，選擇 **"新建步驟**"。 在搜尋方塊中，輸入 `sftp ssh` 作為篩選條件。 在動作清單底下，選取您想要的動作。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 為您的連線提供必要的詳細資料。

   > [!IMPORTANT]
   >
   > 當您在 [SSH 私密金鑰]**** 屬性中輸入 SSH 私密金鑰之後，請執行這些額外的步驟，以協助確定您會為這個屬性提供完整且正確的值。 無效的金鑰會導致連線失敗。

   雖然您可以使用任何文字編輯器，但下列範例步驟會使用 Notepad.exe 作為範例，來示範如何正確地複製並貼上您的金鑰。

   1. 在文字編輯器中開啟您的 SSH 私密金鑰檔案。 這些步驟會使用記事本作為範例。

   1. 在記事本**編輯**功能表上，**選擇"全部**"。

   1. 選擇**編輯** > **副本**。

   1. 在您新增的 SFTP-SSH 觸發程序或動作中，將所複製的「完整」** 金鑰貼到 [SSH 私密金鑰]**** 屬性中，此屬性支援多行。  ***請確定您會貼上***金鑰。 ***請勿手動輸入或編輯此金鑰***。

1. 輸入連接詳細資訊後，選擇 **"創建**"。

1. 現在請為您選取的觸發程序或動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>覆蓋塊大小

要覆蓋分塊使用的預設自我調整行為，可以指定從 5 MB 到 50 MB 的常量塊大小。

1. 在操作的右上角，選擇橢圓按鈕 **（...），** 然後選擇 **"設置**"。

   ![打開 SFTP-SSH 設置](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. 在 **"內容傳輸**"下，在 **"塊大小**"屬性中，輸入`5`從`50`到 的整數值，例如： 

   ![指定要改用的區塊大小](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. 完成之後，選取 [完成]****。

## <a name="examples"></a>範例

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH 觸發程序：當新增或修改檔案時

此觸發程序會在 SFTP 伺服器上新增或變更了檔案時，啟動邏輯應用程式工作流程。 舉例來說，您可以新增條件來檢查檔案的內容，並根據內容是否符合指定條件來取得內容。 接著，您可以新增要取得檔案內容的動作，並將該內容放置於 SFTP 伺服器上的資料夾中。

**企業範例**：您可以使用此觸發程序，來監視代表客戶訂單的新檔案 SFTP 資料夾。 然後，您可以使用 SFTP 動作 (例如**取得檔案內容**)，來取得訂單的內容以進一步處理，並將該訂單儲存在訂單資料庫中。

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH 操作：使用路徑獲取內容

此動作會從 SFTP 伺服器上的檔案取得內容。 舉例來說，您可以新增來自上一個範例中的觸發程序，以及新增檔案內容必須符合的條件。 如果條件為 true，則可以執行會取得內容的動作。

## <a name="connector-reference"></a>連接器參考

有關此連接器的更多技術詳細資訊，例如連接器的 Swagger 檔所述的觸發器、操作和限制，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/sftpwithssh/)。

> [!NOTE]
> 對於[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用，此連接器的 ISE 標記版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
