---
title: 將適用於 VM 的 Azure 監視器對應與 Operations Manager 整合 |Microsoft Docs
description: 適用於 VM 的 Azure 監視器會自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 本文討論如何使用 [對應] 功能，在 Operations Manager 中自動建立分散式應用程式圖表。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: a20884c33d5232661e858e0b3ec722f139825aea
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82928001"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>整合 System Center Operations Manager 與適用於 VM 的 Azure 監視器對應功能

在適用於 VM 的 Azure 監視器中，您可以在 Azure 或您的環境中執行的 Windows 和 Linux 虛擬機器（Vm）上，查看探索到的應用程式元件。 透過對應功能與 System Center Operations Manager 之間的這項整合，您可以根據適用於 VM 的 Azure 監視器中的動態相依性對應，在 Operations Manager 中自動建立分散式應用程式圖表。 本文說明如何設定您的 System Center Operations Manager 管理群組，以支援這項功能。

>[!NOTE]
>如果您已部署服務對應，您可以在適用於 VM 的 Azure 監視器中查看對應，其中包含可監視 VM 健全狀況和效能的其他功能。 適用於 VM 的 Azure 監視器的對應功能主要是用來取代獨立的服務對應解決方案。 若要深入了解，請參閱[適用於 VM 的 Azure 監視器概觀](vminsights-overview.md)。

## <a name="prerequisites"></a>Prerequisites

* System Center Operations Manager 管理群組（2012 R2 或更新版本）。
* 設定為支援適用於 VM 的 Azure 監視器的 Log Analytics 工作區。
* 一部或多部 Windows 和 Linux 虛擬機器或實體電腦，受 Operations Manager，並將資料傳送至 Log Analytics 工作區。 向 Operations Manager 管理群組回報的 Linux 伺服器必須設定為直接連接到 Azure 監視器。 如需詳細資訊，請參閱[使用 Log Analytics 代理程式收集記錄資料](../platform/log-analytics-agent.md)中的總覽。
* 可存取與 Log Analytics 工作區相關聯之 Azure 訂用帳戶的服務主體。 如需詳細資訊，請移至[建立服務主體](#create-a-service-principal)。

## <a name="install-the-service-map-management-pack"></a>安裝服務對應管理組件

您可以藉由匯入 SystemCenter. ServiceMap 管理元件配套（SystemCenter. ServiceMap. .mpb）來啟用 Operations Manager 與對應功能之間的整合。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=55763)下載管理組件配套。 此配套包含下列管理組件︰

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>設定整合

安裝服務對應管理元件之後，Operations Manager Operations 主控台的 [**管理**] 窗格中的 [ **Operations management Suite** ] 底下會顯示新的節點 [**服務對應**]。

>[!NOTE]
>[Operations Management Suite](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)是包含 Log Analytics 的服務集合，現在是[Azure 監視器](../overview.md)的一部分。

若要設定適用於 VM 的 Azure 監視器對應整合，請執行下列動作：

1. 若要開啟設定精靈，在 [服務對應概觀]**** 窗格中，按一下 [新增工作區]****。  

    ![[服務對應概觀] 窗格](media/service-map-scom/scom-configuration.png)

2. 在 [連線設定]**** 視窗中，輸入租用戶名稱或識別碼、應用程式識別碼 (也稱為使用者名稱或用戶端識別碼) 以及服務主體的密碼，然後按一下 [下一步]****。 如需詳細資訊，請移至 [建立服務主體]。

    ![[連線設定] 視窗](media/service-map-scom/scom-config-spn.png)

3. 在 [訂閱選取項目]**** 視窗中，選取 Azure 訂用帳戶、Azure 資源群組 (包含 Log Analytics 工作區的那個)，以及 Log Analytics 工作區，然後按 [下一步]****。

    ![Operations Manager 設定工作區](media/service-map-scom/scom-config-workspace.png)

4. 在 [機器群組選取項目]**** 視窗中，您要選擇您想要同步至 Operations Manager 的服務對應機器群組。 按一下 [新增/移除機器群組]****，從 [可用的機器群組]**** 清單中選擇群組，然後按一下 [新增]****。  當您完成群組選取時，按一下 [確定]**** 來完成。

    ![Operations Manager 設定機器群組](media/service-map-scom/scom-config-machine-groups.png)

5. 在 [**伺服器選擇**] 視窗中，您可以使用要在 Operations Manager 與對應功能之間同步的伺服器來設定 [服務對應伺服器] 群組。 按一下 [新增/移除伺服器]****。

    若要讓整合針對伺服器建立分散式應用程式圖表，該伺服器必須是︰

   * 受 Operations Manager 監視
   * 設定為向以適用於 VM 的 Azure 監視器設定的 Log Analytics 工作區報告
   * 列於服務對應伺服器群組中

     ![Operations Manager 設定群組](media/service-map-scom/scom-config-group.png)

6. 選擇性：選取 [所有管理伺服器資源集區] 以與 Log Analytics 通訊，然後按一下 [**新增工作區**]。

    ![Operations Manager 設定資源集區](media/service-map-scom/scom-config-pool.png)

    設定和註冊 Log Analytics 工作區可能需要一分鐘的時間。 設定之後，Operations Manager 起始第一個對應同步處理。

    ![Operations Manager 設定資源集區](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>監視整合

在連接 Log Analytics 工作區之後，Operations Manager Operations 主控台的 [**監視**] 窗格中會顯示新的資料夾服務對應。

![Operations Manager [監視] 窗格](media/service-map-scom/scom-monitoring.png)

服務對應資料夾有四個節點︰

* 作用中**警示**：列出有關 Operations Manager 和 Azure 監視器之間通訊的所有作用中警示。  

  >[!NOTE]
  >這些警示不是與 Operations Manager 同步處理的 Log Analytics 警示，而是以服務對應管理元件中定義的工作流程為基礎，在管理群組中產生。

* **伺服器**：列出已設定為從適用於 VM 的 Azure 監視器對應功能同步處理的受監視伺服器。

    ![Operations Manager [監視伺服器] 窗格](media/service-map-scom/scom-monitoring-servers.png)

* **電腦群組**相依性視圖：列出所有從對應功能同步處理的電腦群組。 您可以按一下任何群組，以檢視其分散式應用程式圖表。

    ![Operations Manager 分散式應用程式圖表](media/service-map-scom/scom-group-dad.png)

* **伺服器**相依性視圖：列出從對應功能同步處理的所有伺服器。 您可以按一下任何伺服器，以檢視其分散式應用程式圖表。

    ![Operations Manager 分散式應用程式圖表](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>編輯或刪除工作區

您可以透過 [服務對應概觀]**** 窗格 ([系統管理]**** 窗格 > [Operations Management Suite]**** > [服務對應]****) 編輯或刪除已設定的工作區。

> [!NOTE]
> [Operations Management Suite 為包含 Log Analytics 的一系列服務](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)，現在已納入 [Azure 監視器](../overview.md)。

在此目前的版本中，您只能設定一個 Log Analytics 工作區。

![Operations Manager [編輯工作區] 窗格](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>設定規則和覆寫

*SystemCenter*規則會定期從適用於 VM 的 Azure 監視器對應] 功能提取資訊。 若要修改同步處理間隔，您可以覆寫規則，並修改參數**IntervalMinutes**的值。

![Operations Manager [覆寫內容] 視窗](media/service-map-scom/scom-overrides.png)

* **Enabled**：啟用或停用自動更新。
* **IntervalMinutes**：指定更新之間的時間。 預設間隔是一小時。 如果您想要更頻繁地同步處理對應，可以變更此值。
* **TimeoutSeconds**：指定要求超時前的時間長度。
* **TimeWindowMinutes**：指定用來查詢資料的時間範圍。 預設值是60分鐘，這是允許的最大間隔。

## <a name="known-issues-and-limitations"></a>已知問題和限制

目前的設計有以下問題和限制︰

* 您只能連線到單一 Log Analytics 工作區。
* 雖然您可以透過 [撰寫]**** 窗格將伺服器手動新增至服務對應伺服器群組，但這些伺服器的對應不會立即進行同步。 系統會在下一個同步週期期間，從適用於 VM 的 Azure 監視器對應功能同步處理它們。
* 如果您對管理元件所建立的分散式應用程式圖表進行任何變更，這些變更可能會在下一次與適用於 VM 的 Azure 監視器同步時遭到覆寫。

## <a name="create-a-service-principal"></a>建立服務主體

如需建立服務主體的官方 Azure 文件，請參閱︰

* [使用 PowerShell 建立服務主體](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用 Azure CLI 建立服務主體](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [使用 Azure 入口網站建立服務主體](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>意見反應
您是否有關于與適用於 VM 的 Azure 監視器對應功能或本檔整合的任何意見反應？ 請瀏覽我們的 [User Voice 頁面 (英文)](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，您可以在此頁面提出功能建議或對現有的建議進行投票。
