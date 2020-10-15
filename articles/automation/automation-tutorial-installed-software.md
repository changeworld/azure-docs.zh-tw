---
title: 探索您具有 Azure 自動化的 VM 上安裝了哪些軟體 | Microsoft Docs
description: 本文說明在您環境中的 VM 上安裝的軟體。
services: automation
keywords: 清查、自動化、變更追蹤
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: cad92ef376a14805049772cd0bfdbac04309f6c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185818"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>探索您的 VM 上安裝了哪些軟體

在本教學課程中，您將了解如何使用 Azure 自動化的變更追蹤和清查功能，找出您的環境中安裝了哪些軟體。 您可以在電腦上收集並檢視軟體、檔案、Linux 精靈、Windows 服務和 Windows 登錄機碼的清查。 追蹤您電腦的設定可協助您找出環境中的操作問題，並進一步了解您電腦的狀態。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 啟用變更追蹤和清查
> * 啟用 Azure VM
> * 啟用非 Azure VM
> * 檢視已安裝的軟體
> * 搜尋已安裝軟體的清查記錄

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](./index.yml)可保存監看員和動作 Runbook，以及監看員工作。
* 要啟用此功能的[虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 上登入 Azure 入口網站。

## <a name="enable-change-tracking-and-inventory"></a>啟用變更追蹤和清查

您必須先在本教學課程中啟用變更追蹤和清查。 如果您先前已啟用此功能，就不需要執行此步驟。

>[!NOTE]
>如果欄位呈現灰色，就表示 VM 已啟用另一個自動化功能，而您必須使用相同的工作區和自動化帳戶。

1. 瀏覽至您的自動化帳戶，然後選取 [組態管理] 下的 [清查] 或 [變更追蹤]。

2. 選擇 [Log Analytics](../azure-monitor/log-query/log-query-overview.md) 工作區。 此工作區會收集變更追蹤和清查等功能所產生的資料。 工作區提供單一位置來檢閱和分析來自多個來源的資料。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. 選取要使用的自動化帳戶。

4. 設定部署的位置。

5. 按一下 [啟用] 為您的 VM 部署此功能。 

    ![清查設定橫幅](./media/automation-tutorial-installed-software/enableinventory.png)

在設定期間，VM 的佈建會透過適用於 Windows 的 Log Analytics 代理程式和[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)來進行。 啟用變更追蹤和清查可能需要多達 15 分鐘的時間。 在此期間，請勿關閉瀏覽器視窗。

啟用此功能之後，VM 上安裝的軟體和變更相關資訊會流向 Azure 監視器記錄。 可能需要 30 分鐘到 6 小時，資料才可供分析。

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>將 Azure VM 新增至變更追蹤和清查

1. 在您的自動化帳戶中，瀏覽至 [組態管理] 下的 [清查] 或 [變更追蹤]。

2. 選取 [+ 新增 Azure VM]。

3. 從 VM 清單中選取您的 VM。 

4. 按一下 [啟用] 以在 VM 上啟用變更追蹤和清查。 適用於 Windows 的 Log Analytics 代理程式會部署至 VM，並將 VM 設定為與 Log Analytics 工作區通訊。 設定作業可能需要幾分鐘的時間。 

5. 此時，如有需要，您可以從清單中選取要啟用此功能的新 VM。

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>將非 Azure 電腦新增至變更追蹤和清查

若要為非 Azure 電腦啟用此功能：

1. 安裝[適用於 Windows 的 Log Analytics 代理程式](../azure-monitor/platform/agent-windows.md)或[適用於 Linux 的 Log Analytics 代理程式](automation-linux-hrw-install.md) (視您的作業系統而定)。 

2. 瀏覽至您的自動化帳戶，然後移至 [組態管理] 下的 [清查] 或 [變更追蹤]。 

3. 按一下 [管理電腦]。 您會看到向 Log Analytics 工作區回報的電腦清單，而這些電腦並未啟用變更追蹤和清查。 為您的環境選取適當選項：

    * **在所有可用電腦上啟用** - 此選項可在此時向您 Log Analytics 工作區回報的所有電腦上啟用此功能。
    * **在所有可用電腦和未來電腦上啟用** - 此選項可在向您 Log Analytics 工作區回報的所有電腦上，以及之後會新增至工作區的所有未來電腦上啟用功能。
    * **在選取的電腦上啟用** - 此選項只會在您選取的電腦上啟用功能。

    ![管理機器](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>檢視已安裝的軟體

一旦啟用變更追蹤和清查功能後，您就可以檢視 [清查] 頁面上的結果。

1. 在您的自動化帳戶中，選取 [組態管理] 下的 [清查]。

2. 在 [清查] 頁面上，按一下 [軟體] 索引標籤。

3. 請注意以下列出已找到軟體的表格。 軟體會依軟體名稱及版本群組。 每一筆軟體記錄的高階詳細資料都可在資料表中進行檢視。 這些詳細資料包括軟體名稱、版本、發行者、上次重新整理的時間 (由群組中電腦所報告的最近重新整理時間) 和電腦 (具有該軟體的電腦計數)。

    ![軟體清查](./media/automation-tutorial-installed-software/inventory-software.png)

4. 按一下資料列可檢視軟體記錄的屬性，以及具有該軟體之電腦的名稱。

5. 若要尋找特定軟體或軟體的群組，您可以在軟體清單上方的文字方塊中直接搜尋。
篩選器可讓您以軟體名稱、版本或發行者作為基礎進行搜尋。 例如，搜尋 **Contoso** 會傳回名稱、發行者或版本中包含 **Contoso** 的所有軟體。

## <a name="search-inventory-logs-for-installed-software"></a>搜尋已安裝軟體的清查記錄

變更追蹤和清查所產生的記錄資料會傳送到 Azure 監視器記錄。 若要透過執行查詢來搜尋記錄，請選取 [清查] 頁面頂端的 [Log Analytics]。 清查資料會儲存在 `ConfigurationData` 類型之下。

下列 Log Analytics 查詢範例會傳回發行者 Microsoft Corporation 的清查結果。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

若要深入了解如何在 Azure 監視器記錄中執行和搜尋記錄，請參閱 [Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)。

## <a name="see-the-software-inventory-for-a-single-machine"></a>查看單一電腦的軟體清查

若要查看單一電腦的軟體清查，您可以從 Azure VM 資源頁面存取清查，或使用 Azure 監視器記錄來篩選到對應的機器。 下列範例 Log Analytics 查詢會傳回名為 **ContosoVM** 的電腦軟體清單。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已了解如何檢視軟體清查：

> [!div class="checklist"]
> * 啟用變更追蹤和清查
> * 啟用 Azure VM
> * 啟用非 Azure VM
> * 檢視已安裝的軟體
> * 搜尋已安裝軟體的清查記錄

如需深入了解，請繼續閱讀變更追蹤和清查功能的概觀。

> [!div class="nextstepaction"]
> [變更追蹤和清查概觀](change-tracking.md)
