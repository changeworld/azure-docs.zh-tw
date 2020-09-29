---
title: 將適用於 VM 的 Azure 監視器對應與 Operations Manager 整合 |Microsoft Docs
description: 適用於 VM 的 Azure 監視器會自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 本文討論如何使用對應功能，在 Operations Manager 中自動建立分散式應用程式圖表。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 489b4bfee1eaa2c381b16e88dbcc0bcb7fd8555f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441507"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>整合 System Center Operations Manager 與適用於 VM 的 Azure 監視器對應功能

在適用於 VM 的 Azure 監視器中，您可以在 Windows 和 Linux 虛擬機器上，查看在 Azure 或您的環境中執行 (Vm) 上探索到的應用程式元件。 透過這項地圖功能與 System Center Operations Manager 之間的整合，您可以根據適用於 VM 的 Azure 監視器中的動態相依性對應，在 Operations Manager 中自動建立分散式應用程式圖表。 本文說明如何設定 System Center Operations Manager 管理群組，以支援這項功能。

>[!NOTE]
>如果您已部署服務對應，您可以在適用於 VM 的 Azure 監視器中查看對應，其中包含可監視 VM 健康情況和效能的其他功能。 適用於 VM 的 Azure 監視器的地圖功能是為了取代獨立的服務對應解決方案。 若要深入了解，請參閱[適用於 VM 的 Azure 監視器概觀](vminsights-overview.md)。

## <a name="prerequisites"></a>必要條件

*  (2012 R2 或更新版本) 的 System Center Operations Manager 管理群組。
* 設定為支援適用於 VM 的 Azure 監視器的 Log Analytics 工作區。
* Operations Manager 的一或多部 Windows 和 Linux 虛擬機器或實體電腦，並將資料傳送至您的 Log Analytics 工作區。 向 Operations Manager 管理群組報告的 Linux 伺服器必須設定為直接連線到 Azure 監視器。 如需詳細資訊，請參閱 [使用 Log Analytics 代理程式收集記錄資料](../platform/log-analytics-agent.md)中的總覽。
* 可存取與 Log Analytics 工作區相關聯之 Azure 訂用帳戶的服務主體。 如需詳細資訊，請移至[建立服務主體](#create-a-service-principal)。

## <a name="install-the-service-map-management-pack"></a>安裝服務對應管理組件

您可以藉由匯入 Microsoft.SystemCenter ServiceMap 管理元件組合 ( # B1 temCenter. ServiceMap. .mpb) ，來啟用 Operations Manager 與地圖功能之間的整合。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=55763)下載管理組件配套。 此配套包含下列管理組件︰

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>設定整合

安裝服務對應管理元件之後，系統會在 Operations Manager Operations 主控台的 [系統**管理**] 窗格中，于**Operations management Suite**下顯示新的節點**服務對應**。

>[!NOTE]
>[Operations Management Suite](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) 是包含 Log Analytics 的服務集合，現在是 [Azure 監視器](../overview.md)的一部分。

若要設定適用於 VM 的 Azure 監視器對應整合，請執行下列動作：

1. 若要開啟設定精靈，在 [服務對應概觀]**** 窗格中，按一下 [新增工作區]****。  

    ![[服務對應概觀] 窗格](media/service-map-scom/scom-configuration.png)

2. 在 [連線設定]**** 視窗中，輸入租用戶名稱或識別碼、應用程式識別碼 (也稱為使用者名稱或用戶端識別碼) 以及服務主體的密碼，然後按一下 [下一步]****。 如需詳細資訊，請移至 [建立服務主體]。

    ![[連線設定] 視窗](media/service-map-scom/scom-config-spn.png)

3. 在 [訂閱選取項目]**** 視窗中，選取 Azure 訂用帳戶、Azure 資源群組 (包含 Log Analytics 工作區的那個)，以及 Log Analytics 工作區，然後按 [下一步]****。

    ![Operations Manager 設定工作區](media/service-map-scom/scom-config-workspace.png)

4. 在 [機器群組選取項目]**** 視窗中，您要選擇您想要同步至 Operations Manager 的服務對應機器群組。 按一下 [新增/移除機器群組]****，從 [可用的機器群組]**** 清單中選擇群組，然後按一下 [新增]****。  當您完成群組選取時，按一下 [確定]**** 來完成。

    ![Operations Manager 設定機器群組](media/service-map-scom/scom-config-machine-groups.png)

5. 在 [ **伺服器選擇** ] 視窗中，您可以將服務對應 Servers] 群組設定為 Operations Manager 和 Map 功能之間要同步處理的伺服器。 按一下 [新增/移除伺服器]****。

    若要讓整合針對伺服器建立分散式應用程式圖表，該伺服器必須是︰

   * 受 Operations Manager 監視
   * 設定為向使用適用於 VM 的 Azure 監視器設定的 Log Analytics 工作區報告
   * 列於服務對應伺服器群組中

     ![Operations Manager 設定群組](media/service-map-scom/scom-config-group.png)

6. 選擇性：選取 [所有管理伺服器] 資源集區來與 Log Analytics 通訊，然後按一下 [ **新增工作區**]。

    ![[新增 Microsoft Operations Management Suite 工作區] 中選取 [所有管理伺服器] 資源集區之 [伺服器集區] 畫面的螢幕擷取畫面。](media/service-map-scom/scom-config-pool.png)

    設定和註冊 Log Analytics 工作區可能需要一分鐘的時間。 設定之後，Operations Manager 會起始第一個對應同步處理。

    ![[新增 Microsoft Operations Management Suite] 工作區中的 [完成] 畫面的螢幕擷取畫面，確認已新增工作區。](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>監視整合

連線到 Log Analytics 工作區之後，Operations Manager Operations 主控台的 [ **監視** 中] 窗格會顯示服務對應的新資料夾。

![Operations Manager [監視] 窗格](media/service-map-scom/scom-monitoring.png)

服務對應資料夾有四個節點︰

* 作用中**警示**：列出 Operations Manager 與 Azure 監視器之間通訊的所有作用中警示。  

  >[!NOTE]
  >這些警示不是與 Operations Manager 同步的 Log Analytics 警示，會根據服務對應管理元件中定義的工作流程，在管理群組中產生這些警示。

* **伺服器**：列出設定為從適用於 VM 的 Azure 監視器對應功能同步處理的受監視伺服器。

    ![Operations Manager [監視伺服器] 窗格](media/service-map-scom/scom-monitoring-servers.png)

* **電腦群組**相依性視圖：列出所有從地圖功能同步處理的電腦群組。 您可以按一下任何群組，以檢視其分散式應用程式圖表。

    ![服務對應的螢幕擷取畫面，其中顯示每個電腦群組的影像，以及表示兩者之間相依性的線。](media/service-map-scom/scom-group-dad.png)

* **伺服器**相依性視圖：列出所有從地圖功能同步處理的伺服器。 您可以按一下任何伺服器，以檢視其分散式應用程式圖表。

    ![服務對應的螢幕擷取畫面，其中顯示每一部伺服器的影像，以及表示兩者之間相依性的各項圖表。](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>編輯或刪除工作區

您可以透過 [服務對應概觀]**** 窗格 ([系統管理]**** 窗格 > [Operations Management Suite]**** > [服務對應]****) 編輯或刪除已設定的工作區。

> [!NOTE]
> [Operations Management Suite 為包含 Log Analytics 的一系列服務](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)，現在已納入 [Azure 監視器](../overview.md)。

您只能在此目前的版本中設定一個 Log Analytics 工作區。

![Operations Manager [編輯工作區] 窗格](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>設定規則和覆寫

規則（ *Microsoft.SysTemCenter microsoft.systemcenter.servicemapimport.rule*）會定期從適用於 VM 的 Azure 監視器對應功能提取資訊。 若要修改同步處理間隔，您可以覆寫規則，並修改參數 **IntervalMinutes**的值。

![Operations Manager [覆寫內容] 視窗](media/service-map-scom/scom-overrides.png)

* **Enabled**：啟用或停用自動更新。
* **IntervalMinutes**：指定更新之間的時間。 預設間隔是一小時。 如果您想要更頻繁地同步處理對應，可以變更值。
* **TimeoutSeconds**：指定要求超時之前的時間長度。
* **TimeWindowMinutes**：指定查詢資料的時間範圍。 預設值為60分鐘，這是允許的最大間隔。

## <a name="known-issues-and-limitations"></a>已知的問題及限制

目前的設計有以下問題和限制︰

* 您只能連線到單一 Log Analytics 工作區。
* 雖然您可以透過 [撰寫]**** 窗格將伺服器手動新增至服務對應伺服器群組，但這些伺服器的對應不會立即進行同步。 在下一個同步處理週期中，它們會從適用於 VM 的 Azure 監視器對應功能進行同步處理。
* 如果您對管理元件所建立的分散式應用程式圖表進行任何變更，這些變更可能會在下一次與適用於 VM 的 Azure 監視器進行同步處理時覆寫。

## <a name="create-a-service-principal"></a>建立服務主體

如需建立服務主體的官方 Azure 文件，請參閱︰

* [使用 PowerShell 建立服務主體](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [使用 Azure CLI 建立服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)
* [使用 Azure 入口網站建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>建議

您是否有任何意見反應，可以與適用於 VM 的 Azure 監視器地圖功能或此檔整合？ 請瀏覽我們的 [User Voice 頁面 (英文)](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，您可以在此頁面提出功能建議或對現有的建議進行投票。

