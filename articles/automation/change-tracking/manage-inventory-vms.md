---
title: 管理來自 VM 的 Azure 自動化清查集合 | Microsoft Docs
description: 此文章說明如何管理來自 VM 的清查集合。
services: automation
ms.subservice: change-inventory-management
keywords: 清查、自動化、變更、追蹤
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2acf22ba76acdfa6152ef8966f4000aa325a9e91
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209500"
---
# <a name="manage-inventory-collection-from-vms"></a>從虛擬機器管理清查集合

您可以從機器的資源頁面啟用 Azure VM 的清查追蹤。 您可以在您的電腦上收集並檢視下列清查資訊：

- Windows 更新、Windows 應用程式、服務、檔案和登錄機碼
- Linux 軟體套件、精靈和檔案

Azure 自動化變更追蹤和清查提供瀏覽器型使用者介面，可用來設定清查集合。

## <a name="before-you-begin"></a>開始之前

如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。

此文章假設您有可啟用變更追蹤和清查功能的 VM。 如果您沒有 Azure VM，可以[建立一個 VM](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>從 VM 資源頁面啟用清查收集

1. 在 Azure 入口網站的左側窗格中，選取 [虛擬機器]。
2. 在 VM 清單中，選取一部機器。
3. 在 [資源] 功能表上，於 [作業] 下選取 [清查]。
4. 選取 Log Analytics 工作區來儲存資料記錄。
    如果該區域沒有工作區可供您使用，系統會提示您建立預設工作區和自動化帳戶。
5. 若要開始啟用您的電腦，請選取 [啟用]。

   ![檢視上架選項](./media/manage-inventory-vms/inventory-onboarding-options.png)

    狀態列會通知您正在啟用變更追蹤和清查功能。 此程序可能需要 15 分鐘的時間。 在此期間，您可以關閉視窗，或可以保持開啟，其會在功能啟用時通知您。 您可以從通知窗格監視部署狀態。

   ![檢視清查](./media/manage-inventory-vms/inventory-onboarded.png)

部署完成時，狀態列就會消失。 系統仍在收集清查資料，可能還無法看到資料。 完整的資料收集可能需要 24 小時。

## <a name="configure-your-inventory-settings"></a>進行清查設定

根據預設會設定收集軟體、Windows 服務及 Linux 精靈。 若要收集 Windows 登錄和檔案清查，請進行清查收集設定。

1. 在 [清查] 頁面上，按一下頁面頂端的 [編輯設定]。
2. 若要新增收集設定，請透過選取 [Windows 登錄]、[Windows 檔案] 或 [Linux 檔案] 索引標籤，前往您想要新增的設定類別。
3. 選取適當的類別，按一下頁面頂端的 [新增]。

下列各節提供各種類別可設定的每個屬性相關資訊。

### <a name="windows-registry"></a>Windows 登錄

|屬性  |描述  |
|---------|---------|
|啟用     | 判斷是否已套用設定        |
|項目名稱     | 所要追蹤檔案的易記名稱        |
|群組     | 用於將檔案以邏輯方式分組的群組名稱        |
|Windows 登錄機碼   | 要檢查檔案的路徑。例如："HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Windows 檔案

|屬性  |描述  |
|---------|---------|
|啟用     | 如果已套用設定，則為 True，否則為 False。        |
|項目名稱     | 要追蹤之檔案的易記名稱。        |
|群組     | 以邏輯方式分組檔案的群組名稱。       |
|輸入路徑     | 要檢查檔案的路徑，例如，**c:\temp\myfile.txt**。

### <a name="linux-files"></a>Linux 檔案

|屬性  |描述  |
|---------|---------|
|啟用     | 如果已套用設定，則為 True，否則為 False。        |
|項目名稱     | 要追蹤之檔案的易記名稱。        |
|群組     | 以邏輯方式分組檔案的群組名稱。        |
|輸入路徑     | 要檢查檔案的路徑，例如， **/etc/*.conf**。       |
|路徑類型     | 要追蹤的項目類型。 值為 [檔案] 和 [目錄]。        |
|遞迴     | 如果在尋找要追蹤的項目時使用遞迴，則為 True，否則為 False。        |
|使用 sudo     | 如果檢查項目時使用 sudo，則為 True，否則為 False。         |
|連結     | 此值指出當周遊目錄時，處理符號連結的方式。 可能的值包括： <br> 忽略 - 忽略符號連結，而不包含參考的檔案/目錄<br>追蹤 - 在遞迴期間追蹤符號連結，並且包含參考的檔案/目錄<br>管理 - 遵循符號連結並允許變更所傳回內容的處理方式      |

## <a name="manage-machine-groups"></a>管理電腦群組

清查可讓您在 Azure 監視器中建立及檢視電腦群組。 電腦群組是由 Azure 監視器記錄中的查詢所定義的電腦集合。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

若要檢視您的電腦群組，請選取 [清查] 頁面上的 [電腦群組] 索引標籤。

![在清查頁面上檢視電腦群組](./media/manage-inventory-vms/inventory-machine-groups.png)

從清單中選取電腦群組，就會開啟 [電腦群組] 頁面。 此頁面會顯示電腦群組的相關詳細資料。 這些詳細資料包括用來定義群組的 Azure 監視器記錄查詢。 頁面底部是屬於該群組的電腦分頁清單。

![檢視電腦群組頁面](./media/manage-inventory-vms/machine-group-page.png)

按一下 [+ 複製] 來複製電腦群組。 您必須為群組指定群組的新名稱和別名。 在這個階段可以改變定義。 變更查詢之後，按一下 [驗證查詢] 可預覽所要選取的機器。 當您滿意群組時，按一下 [建立] 以建立電腦群組。

若要建立新的電腦群組，請按一下 [+ 建立電腦群組]。 此按鈕會開啟 [正在建立電腦群組] 頁面，您可以在其中定義新的群組。 按一下 [建立]  以建立群組。

![建立新的電腦群組](./media/manage-inventory-vms/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>讓 VM 脫離管理

將 VM 從變更追蹤和清查管理中移除：

1. 在 Azure 入口網站的左窗格中，選取 [Log Analytics]，然後選取您啟用 VM 的變更追蹤和清查功能時所使用的工作區。
2. 在 [ **Log Analytics** ] 頁面上，開啟 [ **資源** ] 功能表。
3. 在 [工作區資料來源] 底下，選取 [虛擬機器]。
4. 在清單中，選取您要中斷連線的 VM。 機器在 [OMS 連線] 欄中，[此工作區] 文字旁邊會出現綠色核取記號。

   >[!NOTE]
   >Operations Management Suite (OMS) 現在稱為 Azure 監視器記錄。

5. 在下一個頁面的頂端，按一下 [中斷連線]。
6. 在確認視窗中，按一下 [是] 以中斷機器與管理的連線。

>[!NOTE]
>取消註冊之後仍會顯示電腦，因為我們會報告過去24小時內清查的所有機器。 中斷機器的連線之後，您必須等候24小時，再也不會再列出。

## <a name="next-steps"></a>後續步驟

* 如需使用此功能的詳細資訊，請參閱[管理變更追蹤和清查](manage-change-tracking.md)。
* 若要深入了解追蹤軟體變更，請參閱[使用變更追蹤來追蹤環境中的軟體變更](overview.md)。
* 若要對此功能的一般問題進行疑難排解，請參閱[對變更追蹤和清查問題進行疑難排解](../troubleshoot/change-tracking.md)。
