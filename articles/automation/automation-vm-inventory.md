---
title: 使用清查收集來管理 Azure 虛擬機器 | Microsoft Docs
description: 使用清查收集來管理虛擬機器
services: automation
ms.subservice: change-inventory-management
keywords: 清查、自動化、變更、追蹤
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617372"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>使用清查收集來管理 Azure 虛擬機器

您可以從虛擬機器的資源頁面啟用 Azure 虛擬機器的清查追蹤。 您可以在電腦上收集並查看下列清查資訊：

- Windows 軟體（Windows 應用程式和 Windows 更新）、服務、檔案和登錄機碼
- Linux 軟體（套件）守護程式和檔案

此方法提供以瀏覽器為基礎的使用者介面，讓您設定清查收集。

## <a name="before-you-begin"></a>開始之前

如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。

本文假設您擁有可設定解決方案的 VM。 如果您沒有 Azure 虛擬機器，請[建立虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>從虛擬機器資源頁面啟用清查收集

1. 在 Azure 入口網站的左側窗格中，選取 [虛擬機器]****。
2. 在虛擬機器清單中，選取虛擬機器。
3. 在 [資源]**** 功能表上，於 [作業]**** 下選取 [清查]****。
4. 選取 Log Analytics 工作區來儲存您的資料記錄。
    如果該區域沒有工作區可供您使用，系統會提示您建立預設工作區和自動化帳戶。
5. 若要開始將您的電腦上架，請按一下 [啟用]****。

   ![檢視上架選項](./media/automation-vm-inventory/inventory-onboarding-options.png)

    狀態列會通知您解決方案已啟用。 此程序可能需要 15 分鐘的時間。 在此期間，您可以關閉視窗，或可以保持開啟，它會在解決方案啟用時通知您。 您可以從通知窗格監視部署狀態。

   ![上架之後立即檢視清查解決方案](./media/automation-vm-inventory/inventory-onboarded.png)

部署完成時，狀態列就會消失。 系統仍在收集清查資料，可能還無法看到資料。 完整的資料收集可能需要 24 小時。

## <a name="configure-your-inventory-settings"></a>進行清查設定

根據預設會設定收集軟體、Windows 服務及 Linux 精靈。 若要收集 Windows 登錄和檔案清查，請進行清查收集設定。

1. 在 [清查] 頁面上，按一下頁面頂端的 [**編輯設定**]。
2. 若要加入新的集合設定，請選取 [ **windows**登錄]、[ **windows**檔案] 或 [ **Linux**檔案] 索引標籤，以移至您想要新增的設定類別。
3. 選取適當的類別，然後按一下頁面頂端的 [**新增**]。

下表提供各種類別可設定的每個屬性相關資訊。

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
|輸入路徑     | 要檢查檔案的路徑，例如**c:\temp\myfile.txt**。

### <a name="linux-files"></a>Linux 檔案

|屬性  |描述  |
|---------|---------|
|啟用     | 如果已套用設定，則為 True，否則為 False。        |
|項目名稱     | 要追蹤之檔案的易記名稱。        |
|群組     | 以邏輯方式分組檔案的群組名稱。        |
|輸入路徑     | 要檢查檔案的路徑，例如， **/etc/*. 會議**。       |
|路徑類型     | 要追蹤的專案類型。 值為 [檔案] 和 [目錄]。        |
|遞迴     | 如果在尋找要追蹤的專案時使用遞迴，則為 True，否則為 False。        |
|使用 Sudo     | 如果檢查項目時使用了 sudo，則為 True，否則為 False。         |
|連結     | 值，指出當遍歷目錄時，符號連結的處理方式。 可能的值包括： <br> 忽略 - 忽略符號連結，而不包含參考的檔案/目錄<br>遵循 - 遵循遞迴期間的符號連結，而且包含參考的檔案/目錄<br>管理 - 遵循符號連結並允許變更所傳回內容的處理方式      |

## <a name="manage-machine-groups"></a>管理電腦群組

清查可讓您建立及查看 Azure 監視器記錄中的電腦群組。 電腦群組是由 Azure 監視器記錄中的查詢所定義的機器集合。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

若要檢視您的電腦群組，請選取 [清查] 頁面上的 [電腦群組]**** 索引標籤。

![在清查頁面上檢視電腦群組](./media/automation-vm-inventory/inventory-machine-groups.png)

從清單中選取電腦群組，就會開啟 [電腦群組] 頁面。 此頁面會顯示電腦群組的相關詳細資料。 這些詳細資料包括用來定義群組的記錄分析查詢。 頁面底部是屬於該群組的電腦分頁清單。

![檢視電腦群組頁面](./media/automation-vm-inventory/machine-group-page.png)

按一下 [ **+ 複製**] 以複製電腦群組。 您必須為群組指定群組的新名稱和別名。 在這個階段可以改變定義。 變更查詢之後，請按一下 [**驗證查詢**] 以預覽要選取的電腦。 當您對群組感到滿意時，請按一下 [**建立**] 以建立電腦群組。

如果您想要建立新的電腦群組，請按一下 [ **+ 建立電腦群組**]。 此按鈕會開啟 [**建立電腦群組**] 頁面，您可以在其中定義新的群組。 按一下 [建立] **** 以建立群組。

![建立新的電腦群組](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>讓虛擬機器脫離管理

若要從清查管理中移除您虛擬機器：

1. 在 Azure 入口網站的左窗格中，選取 [Log Analytics]****，然後選取您在將虛擬機器上架時使用的工作區。
2. 在 [Log Analytics] 頁面上，開啟 [**資源**] 功能表。
3. 選取 [**工作區資料來源**] 底下的**虛擬機器**。
4. 在清單中，選取您要中斷連線的虛擬機器。 虛擬機器在 [OMS 連線]**** 資料行中，**這個工作區**文字旁邊會出現綠色核取記號。

   >[!NOTE]
   >Operations Management Suite （OMS）現在稱為 Azure 監視器記錄。
   
5. 在下一個頁面頂端，按一下 [**中斷連線]**。
6. 在確認視窗中，按一下 [**是**] 以中斷電腦與管理的連線。

## <a name="next-steps"></a>後續步驟

* 若要了解在虛擬機器上管理檔案和登錄設定的變更，請參閱[使用變更追蹤解決方案來追蹤環境中的軟體變更](../log-analytics/log-analytics-change-tracking.md)。
* 若要了解在虛擬機器上管理 Windows 和套件更新，請參閱 [Azure 中的更新管理解決方案](../operations-management-suite/oms-solution-update-management.md)。