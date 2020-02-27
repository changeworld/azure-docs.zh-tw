---
title: Azure 資訊安全中心中 IaaS 的先進資料安全性 |Microsoft Docs
description: " 瞭解如何在 Azure 資訊安全中心中啟用 IaaS 的先進資料安全性。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617100"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 上 SQL server 的 Advanced data security 虛擬機器（預覽）
Azure 上 SQL Server 的 advanced data security 虛擬機器是先進 SQL 安全性功能的整合套件。 這項預覽功能包含可識別和減輕潛在資料庫弱點，以及偵測可能表示對資料庫有威脅之異常活動的功能。 

適用于 Azure Vm SQL server 的此安全性供應專案，是以[Azure SQL Database Advanced Data security 套件](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)中所用的相同基本技術為基礎。


## <a name="overview"></a>概觀

先進的資料安全性提供一組先進的 SQL 安全性功能，其中包含弱點評估和先進的威脅防護。

* [弱點評估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是容易設定的服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 它會顯示您的安全性狀態，並包含解決安全性問題和增強資料庫防護的步驟。
* [先進的威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索您的 SQL server。 它會持續監視您的資料庫是否有可疑的活動，並針對異常的資料庫存取模式提供動作導向的安全性警示。 這些警示會提供可疑的活動詳細資料，以及調查和緩和威脅的建議動作。

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>開始使用 Azure Vm 上的 SQL Advanced data security

下列步驟可讓您開始使用 Azure Vm 上 SQL 的先進資料安全性公開預覽版。

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>在 Azure Vm 上設定 SQL 的 advanced data security

在訂用帳戶/工作區層級的虛擬機器上，啟用 SQL Server 的先進資料安全性：
 
1. 從資訊安全中心的提要欄位中，開啟 [**定價 & 設定**] 頁面。

1. 選取您想要為 Azure Vm 上的 SQL 啟用 Advanced Data Security 的訂用帳戶或工作區。

1. 將 [ **VM 上的 SQL server （預覽）** ] 的選項切換為 [已啟用]。 

    （按一下以展開螢幕擷取畫面）

    [![Windows 系統管理中心所見的資訊安全中心建議和警示](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    所有連線到所選工作區的 SQL Server 或所選訂用帳戶的預設工作區，都會啟用 SQL Server 的 Advanced Data Security。

    >[!NOTE]
    > 在第一次重新開機 SQL Server 之後，解決方案將會完全有效。 

若要建立新的工作區，請遵循[建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中的指示。

若要將 SQL Server 的主機連線到工作區，請依照[將 Windows 電腦連線到 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)中的指示進行。


## <a name="set-up-email-notification-for-security-alerts"></a>設定安全性警示的電子郵件通知 

您可以設定要在產生資訊安全中心警示時接收電子郵件通知的收件者清單。 此電子郵件包含 Azure 資訊安全中心中警示的直接連結，以及所有相關的詳細資料。 

1. 移至**資訊安全中心** > **定價 & 設定**，然後按一下相關的訂用帳戶

    ![訂用帳戶設定](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. 從 [設定] 功能表中，按一下 [**電子郵件通知**]。 
1. 在 [**電子郵件地址**] 文字方塊中，輸入要接收通知的電子郵件地址。 您可以輸入一個以上的電子郵件地址，方法是以逗號（，）分隔電子郵件地址。  例如 admin1@mycompany.com、admin2@mycompany.com、admin3@mycompany.com

    ![電子郵件設定](./media/security-center-advanced-iaas-data/email-settings.png)

1. 在**電子郵件通知**設定中，設定下列選項：
  
    * **傳送高嚴重性警示的電子郵件通知**：不是傳送所有警示的電子郵件，而是只傳送高嚴重性警示。
    * **也傳送電子郵件通知給訂**用帳戶擁有者：將通知傳送給訂閱擁有者。

1. 在 [**電子郵件通知**] 畫面頂端，按一下 [**儲存**]。

  > [!NOTE]
  > 在關閉視窗之前，請務必按一下 [**儲存**]，否則不會儲存新的**電子郵件通知**設定。

## <a name="explore-vulnerability-assessment-reports"></a>探索弱點評定報告

[弱點評定] 儀表板可讓您大致瞭解所有資料庫的評量結果。 您可以根據 SQL Server 版本來查看資料庫的散發，以及失敗與傳遞資料庫的摘要，以及根據風險散發而失敗檢查的整體摘要。

您可以直接從資訊安全中心查看弱點評估結果。

1. 從資訊安全中心的提要欄位中，選取 [資源安全性防護] 底下的 [**資料 & 儲存體**]。

1. **在 [應補救的 vm （預覽）] 中，選取您的 SQL 資料庫上**的建議弱點。 如需詳細資訊，請參閱[資訊安全中心建議](security-center-recommendations.md)。 

    [![* * Vm 中 SQL 資料庫的弱點應予以補救（預覽） * * 建議](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    這項建議的詳細觀點隨即出現。

    [![詳細資訊，請參閱 Vm 中 SQL 資料庫的弱點（預覽） * * 建議事項](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 若要向下切入以取得更多詳細資料：

    * 如需掃描的資源（資料庫）和已測試的安全性檢查清單的總覽，請按一下感關注的伺服器。
    [依 SQL server 分組的 ![弱點](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * 如需依特定 SQL 資料庫分組的弱點總覽，請按一下感關注的資料庫。
    [依 SQL server 分組的 ![弱點](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    在每個視圖中，安全性檢查都會依**嚴重性**排序。 按一下特定的安全性檢查以查看詳細資料窗格，其中包含**描述**、如何**修復**它，以及其他相關資訊，例如**影響**或**基準測試**。

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure Vm 上 SQL server 的先進威脅防護警示
警示是由不尋常且可能有害的嘗試存取或惡意探索 SQL Server 所產生。 這些事件會觸發下列警示：

### <a name="anomalous-access-pattern-alerts-preview"></a>異常存取模式警示（預覽）

* **從不尋常的位置存取：** 當 SQL server 的存取模式有所變更，而有人從不尋常的地理位置登入 SQL server 時，就會觸發此警示。 可能的原因：
    * 攻擊者或先前的惡意採用已存取您的 SQL Server。
    * 合法的使用者已從新的位置存取您的 SQL Server。
* **從可能有害的應用程式存取**：使用可能有害的應用程式用存取資料庫時，會觸發此警示。 可能的原因：
    * 攻擊者嘗試使用常見的攻擊工具來危害您的 SQL。
    * 合法的滲透測試。
* **從不熟悉的主體存取**：有人使用不尋常的主體 (SQL 使用者) 登入 SQL Server，而使 SQL Server 的存取模式有所變更時，會觸發此警示。 可能的原因：
    * 攻擊者或先前的惡意採用已存取您的 SQL Server。 
    * 合法的使用者已從與新的主體存取您的 SQL Server。
* **暴力 SQL 認證**：有使用不同認證的異常大量登入失敗時，會觸發此警示。 可能的原因：
    * 攻擊者嘗試使用暴力密碼破解您的 SQL。
    * 合法的滲透測試。

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>潛在的 SQL 插入式攻擊（SQL Server 2019 中支援）

* **SQL 插入的弱點**：當應用程式在資料庫中產生錯誤的 SQL 語句時，就會觸發此警示。 此警示表示 SQL 插入式攻擊的可能弱點。 可能的原因：
    * 應用程式的程式碼中有缺失，而建構了錯誤的 SQL 陳述式
    * 應用程式的程式碼或預存程序在建構錯誤的 SQL 陳述式時未處理使用者輸入，這可能會遭到 SQL 插入式攻擊的侵害
* **可能的 SQL 插入**：在 SQL 插入的已知應用程式弱點遭到作用中的攻擊時，會觸發此警示。 這表示有攻擊者嘗試使用有弱點的應用程式程式碼或預存程序插入惡意 SQL 陳述式。


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Unsafe 命令（SQL Server 2019 中支援）

* **可能不安全的動作**：當執行高許可權且可能不安全的命令時，就會觸發此警示。 可能的原因：
    * 已啟用建議停用以獲得更佳安全性狀態的命令。
    * 嘗試利用 SQL 存取或提升許可權的攻擊者。   


## <a name="explore-and-investigate-security-alerts"></a>探索和調查安全性警示

您可以在資訊安全中心的 [警示] 頁面、資源的 [安全性] 索引標籤，或透過警示電子郵件中的直接連結，取得您的資料安全性警示。

1. 若要查看警示：

    * 在 [資訊安全中心] 中，按一下提要欄位中的 [**安全性警示**]，然後選取警示。
    * 在資源範圍中-開啟相關的資源頁面，然後從提要欄位中按一下 [**安全性**]。 

1. 警示的設計是獨立的，其中每一項都有詳細的補救步驟和調查資訊。 您可以使用其他 Azure 資訊安全中心和 Azure Sentinel 功能進一步調查，以提供更廣泛的觀點：

    * 啟用 SQL Server 的審核功能以進一步調查。 如果您是 Azure Sentinel 使用者，您可以將 SQL 審核記錄從 Windows 安全性記錄檔事件上傳到 Sentinel，並享有豐富的調查體驗。
    * 若要改善您的安全性狀態，請針對每個警示中所指出的主機電腦，使用資訊安全中心的建議。 這會降低未來攻擊的風險。 


## <a name="next-steps"></a>後續步驟

如需相關內容，請參閱下列文章：

- [如何修復建議](security-center-remediate-recommendations.md)