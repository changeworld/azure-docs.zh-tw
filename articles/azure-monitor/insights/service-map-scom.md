---
title: 將 VM 映射的 Azure 監視器與操作管理器集成 |微軟文檔
description: 虛擬機器的 Azure 監視器會自動探索 Windows 和 Linux 系統上的應用程式元件，並映射服務之間的通信。 本文討論使用 Map 功能在操作管理器中自動創建分散式應用程式關係圖。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663448"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>將系統中心操作管理器與 Azure 監視器集成，用於 VM 映射功能

在 VM 的 Azure 監視器中，您可以查看在 Azure 或環境中運行的 Windows 和 Linux 虛擬機器 （VM） 上發現的應用程式元件。 通過 Map 功能和系統中心操作管理器之間的此集成，您可以在操作管理器中基於 AZURE 監視器中針對 VM 的動態依賴關係映射創建分散式應用程式關係圖。 本文介紹如何配置系統中心操作管理器管理組以支援此功能。

>[!NOTE]
>如果已部署服務映射，則可以在 VM 的 Azure 監視器中查看地圖，其中包括用於監視 VM 運行狀況和性能的其他功能。 VM Azure 監視器的映射功能旨在替換獨立的服務映射解決方案。 若要深入了解，請參閱[適用於 VM 的 Azure 監視器概觀](vminsights-overview.md)。

## <a name="prerequisites"></a>Prerequisites

* 系統中心運營經理管理組（2012 R2 或更高版本）。
* 配置為支援 VM 的 Azure 監視器的日誌分析工作區。
* 操作管理器監視並將資料發送到日誌分析工作區的一個或多個 Windows 和 Linux 虛擬機器或物理電腦。 需要將報告給操作管理器管理組的 Linux 伺服器配置為直接連接到 Azure 監視器。 有關詳細資訊，請查看[日誌分析代理在"收集日誌資料"中的](../platform/log-analytics-agent.md)概述。
* 可存取與 Log Analytics 工作區相關聯之 Azure 訂用帳戶的服務主體。 如需詳細資訊，請移至[建立服務主體](#create-a-service-principal)。

## <a name="install-the-service-map-management-pack"></a>安裝服務對應管理組件

通過導入 Microsoft.SystemCenter.ServiceMap 管理包包（Microsoft.SystemCenter.ServiceMap.mpb），可以啟用操作管理器和地圖功能之間的集成。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=55763)下載管理組件配套。 此配套包含下列管理組件︰

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>組態集成

安裝服務映射管理包後，操作管理器操作主控台**的管理**窗格中的 **"操作管理套件**"下將顯示一個新節點"**服務映射**"。

>[!NOTE]
>[操作管理套件是包含日誌分析的服務的集合](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)，現在是[Azure 監視器](../overview.md)的一部分。

要為 VM 映射集成配置 Azure 監視器，請執行以下操作：

1. 若要開啟設定精靈，在 [服務對應概觀]**** 窗格中，按一下 [新增工作區]****。  

    ![[服務對應概觀] 窗格](media/service-map-scom/scom-configuration.png)

2. 在 [連線設定]**** 視窗中，輸入租用戶名稱或識別碼、應用程式識別碼 (也稱為使用者名稱或用戶端識別碼) 以及服務主體的密碼，然後按一下 [下一步]****。 如需詳細資訊，請移至 [建立服務主體]。

    ![[連線設定] 視窗](media/service-map-scom/scom-config-spn.png)

3. 在 [訂閱選取項目]**** 視窗中，選取 Azure 訂用帳戶、Azure 資源群組 (包含 Log Analytics 工作區的那個)，以及 Log Analytics 工作區，然後按 [下一步]****。

    ![Operations Manager 設定工作區](media/service-map-scom/scom-config-workspace.png)

4. 在 [機器群組選取項目]**** 視窗中，您要選擇您想要同步至 Operations Manager 的服務對應機器群組。 按一下 [新增/移除機器群組]****，從 [可用的機器群組]**** 清單中選擇群組，然後按一下 [新增]****。  當您完成群組選取時，按一下 [確定]**** 來完成。

    ![Operations Manager 設定機器群組](media/service-map-scom/scom-config-machine-groups.png)

5. 在 **"伺服器選擇**"視窗中，您將服務映射伺服器組與要在操作管理器和映射功能之間同步的伺服器進行配置。 按一下 [新增/移除伺服器]****。

    若要讓整合針對伺服器建立分散式應用程式圖表，該伺服器必須是︰

   * 運營經理監控
   * 配置為向配置的日誌分析工作區報告，該工作區配置了用於 VM 的 Azure 監視器
   * 列於服務對應伺服器群組中

     ![Operations Manager 設定群組](media/service-map-scom/scom-config-group.png)

6. 可選：選擇所有管理伺服器資源池以與日誌分析通信，然後按一下"**添加工作區**"。

    ![Operations Manager 設定資源集區](media/service-map-scom/scom-config-pool.png)

    設定和註冊 Log Analytics 工作區可能需要一分鐘的時間。 配置後，操作管理器將啟動第一個映射同步。

    ![Operations Manager 設定資源集區](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>監控集成

連接日誌分析工作區後，操作管理器操作主控台的 **"監視"** 窗格中將顯示一個新資料夾"服務映射"。

![Operations Manager [監視] 窗格](media/service-map-scom/scom-monitoring.png)

服務對應資料夾有四個節點︰

* **活動警報**：列出有關操作管理器和 Azure 監視器之間通信的所有活動警報。  

  >[!NOTE]
  >這些警報不是與操作管理器同步的日誌分析警報，它們根據服務映射管理包中定義的工作流在管理組中生成。

* **伺服器**：列出配置為從 Azure 監視器同步 VM 映射功能的受監視伺服器。

    ![Operations Manager [監視伺服器] 窗格](media/service-map-scom/scom-monitoring-servers.png)

* **電腦群組依賴項視圖**：列出從"地圖"功能同步的所有電腦群組。 您可以按一下任何群組，以檢視其分散式應用程式圖表。

    ![Operations Manager 分散式應用程式圖表](media/service-map-scom/scom-group-dad.png)

* **伺服器依賴項視圖**：列出從映射功能同步的所有伺服器。 您可以按一下任何伺服器，以檢視其分散式應用程式圖表。

    ![Operations Manager 分散式應用程式圖表](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>編輯或刪除工作區

您可以透過 [服務對應概觀]**** 窗格 ([系統管理]**** 窗格 > [Operations Management Suite]**** > [服務對應]****) 編輯或刪除已設定的工作區。

>[!NOTE]
>[Operations Management Suite 為包含 Log Analytics 的一系列服務](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand)，現在已納入 [Azure 監視器](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md)。

在此當前版本中，您只能配置一個日誌分析工作區。

![Operations Manager [編輯工作區] 窗格](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>設定規則和覆寫

規則 *，Microsoft.SystemCenter.ServiceMapImport.Rule，* 定期從 Azure 監視器獲取 VM 映射功能的資訊。 要修改同步間隔，可以重寫規則並修改參數**間隔分鐘的值**。

![Operations Manager [覆寫內容] 視窗](media/service-map-scom/scom-overrides.png)

* **Enabled**：啟用或停用自動更新。
* **間隔分鐘**：指定更新之間的時間。 預設間隔是一小時。 如果要更頻繁地同步地圖，可以更改該值。
* **超時秒**：指定請求超時前的時間長度。
* **時間視窗分鐘**：指定查詢資料的時間視窗。 預設值為 60 分鐘，這是允許的最大間隔。

## <a name="known-issues-and-limitations"></a>已知問題和限制

目前的設計有以下問題和限制︰

* 您只能連線到單一 Log Analytics 工作區。
* 雖然您可以透過 [撰寫]**** 窗格將伺服器手動新增至服務對應伺服器群組，但這些伺服器的對應不會立即進行同步。 它們將在下一個同步週期期間從 VM 映射 Azure 監視器同步。
* 如果對管理包創建的分散式應用程式關係圖進行任何更改，這些更改可能會在與 VM 的 Azure 監視器同步時被覆蓋。

## <a name="create-a-service-principal"></a>建立服務主體

如需建立服務主體的官方 Azure 文件，請參閱︰

* [使用 PowerShell 建立服務主體](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用 Azure CLI 建立服務主體](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [使用 Azure 入口網站建立服務主體](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>意見反應
您是否對 VM 地圖功能的 Azure 監視器集成或本文檔有任何回饋？ 請瀏覽我們的 [User Voice 頁面 (英文)](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，您可以在此頁面提出功能建議或對現有的建議進行投票。
