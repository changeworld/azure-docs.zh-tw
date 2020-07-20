---
title: Azure 資訊安全中心的 SQL 機器先進資料安全性（預覽）
description: 瞭解如何在 Azure 資訊安全中心中啟用 SQL 電腦的先進資料安全性
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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f159d2cdc48b144d0c75c62cd8a7ba6667424243
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043864"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>SQL 電腦的 Advanced data security （預覽）

Azure 資訊安全中心的 SQL 機器先進資料安全性，可保護裝載于 Azure、其他雲端環境，甚至是內部部署機器上的 SQL Server。 這會為您的 Azure 原生 SQL 伺服器擴充保護，以完整支援混合式環境。

這項預覽功能包含可識別和減輕潛在資料庫弱點，以及偵測可能表示對資料庫有威脅之異常活動的功能： 

* **弱點評估**-掃描服務，可探索、追蹤及協助您修復潛在的資料庫弱點。 評量掃描提供 SQL 電腦安全性性狀態的總覽，以及任何安全性結果的詳細資料。

* [先進的威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)-此偵測服務會持續監視您的 sql server 是否有威脅，例如 sql 插入式攻擊、暴力密碼破解攻擊和許可權濫用。 這項服務會在 Azure 資訊安全中心中提供動作導向的安全性警示，其中包含可疑活動的詳細資料、如何緩和威脅的指引，以及使用 Azure Sentinel 繼續進行調查的選項。

>[!TIP]
> SQL 電腦的 advanced data security 是 Azure 資訊安全中心的[advanced data security 套件](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)的延伸模組，適用于 Azure SQL Database、Azure SYNAPSE 和 SQL 受控執行個體。


## <a name="set-up-advanced-data-security-for-sql-machines"></a>設定 SQL 電腦的 advanced data security 

為 SQL 電腦設定 Azure 資訊安全中心的先進資料安全性包含兩個步驟：

* 在您的 SQL server 主機上布建 Log Analytics 代理程式。 這會提供與 Azure 的連線。

* 在資訊安全中心的 [定價和設定] 頁面中，啟用選用的配套。

以下說明這兩種情況。

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>步驟 1： 在您的 SQL server 主機上布建 Log Analytics 代理程式：

- **在 AZURE vm 上 SQL Server** -如果您的 SQL 機器裝載于 azure vm 上，您可以自動布建[Log Analytics 代理程式](security-center-enable-data-collection.md#workspace-configuration)。 或者，您可以遵循手動[程式來新增 AZURE VM](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines)。

- **Azure arc 上的 SQL Server** -如果您的 SQL Server 裝載于[azure arc](https://docs.microsoft.com/azure/azure-arc/)機器上，您可以使用資訊安全中心建議的「log analytics 代理程式」安裝在您的 Windows 型 Azure arc 機器（預覽）上，以部署 log analytics 代理程式。 或者，您也可以依照[Azure Arc 檔](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)中的手動程式進行操作。

- **SQL Server 內部內部部署**-如果您的 SQL Server 裝載于不含 Azure Arc 的內部部署 Windows 機器上，您有兩個選項可將其連接至 azure：
    
    - **部署 Azure Arc** -您可以將任何 Windows 機器連接到資訊安全中心。 不過，Azure Arc 在您*所有*的 azure 環境中提供更深入的整合。 如果您設定了 Azure Arc，您會在入口網站中看到 [ **SQL Server – Azure arc** ] 頁面，而您的安全性警示會出現在該頁面的專用 [**安全性**] 索引標籤上。 因此，第一個和建議的選項是[在主機上設定 Azure arc](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) ，並遵循上述**azure arc 上 SQL Server**的指示。
        
    - **不使用 Azure Arc 連接 windows 電腦**-如果您選擇連接在 windows 電腦上執行的 SQL Server，而不使用 azure arc，請依照[將 Windows 機器連線到 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)中的指示進行。


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>步驟 2： 在資訊安全中心的 [定價和設定] 頁面中啟用選用套件組合：

1. 從資訊安全中心的提要欄位中，開啟 [**定價 & 設定**] 頁面。

    - 如果您使用**Azure 資訊安全中心的預設工作區**（名為 "defaultworkspace-[您的訂用帳戶 id]-[region]"），請選取相關的**訂**用帳戶。

    - 如果您使用**非預設工作區**，請選取相關的**工作區**（如有必要，請在篩選準則中輸入工作區的名稱）：

        ![title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. 將 [**電腦上的 SQL server （預覽）** ] 的選項切換為 [已啟用]。 

    [![具有選用配套的資訊安全中心定價頁面](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    所有連線到所選工作區的 SQL server，將會在電腦上啟用 SQL server 的 Advanced Data Security。 在第一次重新開機 SQL Server 之後，保護將會完全有效。 

    >[!TIP] 
    > 若要建立新的工作區，請遵循[建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中的指示。


1. （選擇性）設定安全性警示的電子郵件通知。 
    您可以設定要在產生資訊安全中心警示時接收電子郵件通知的收件者清單。 此電子郵件包含 Azure 資訊安全中心中警示的直接連結，以及所有相關的詳細資料。 如需詳細資訊，請參閱[設定安全性警示的電子郵件通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)。



## <a name="explore-vulnerability-assessment-reports"></a>探索弱點評定報告

弱點評估服務會每週掃描您的資料庫一次。 掃描會在一周中啟用服務的同一天執行。

[弱點評定] 儀表板可讓您大致瞭解所有資料庫的評量結果，以及狀況良好和狀況不良資料庫的摘要，以及根據風險散發而失敗檢查的整體摘要。

您可以直接從資訊安全中心查看弱點評估結果。

1. 從資訊安全中心的提要欄位中，開啟 [**建議**] 頁面，然後選取**您的 SQL server 上應補救的建議弱點（預覽）**。 如需詳細資訊，請參閱[資訊安全中心建議](security-center-recommendations.md)。 


    [![* * 電腦上 SQL server 上的弱點應予以補救（預覽） * * 建議](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    這項建議的詳細觀點隨即出現。

    [![您應補救機器上 SQL server 上的 * * 弱點的詳細觀點（預覽） * * 建議](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 如需更多詳細資料，請向下切入：

    * 如需掃描的資源（資料庫）和已測試的安全性檢查清單的總覽，請選取您感關注的伺服器。

    * 如需依特定 SQL 資料庫分組的弱點總覽，請選取您感關注的資料庫。

    在每個視圖中，安全性檢查都會依**嚴重性**排序。 按一下特定的安全性檢查以查看詳細資料窗格，其中包含**描述**、如何**修復**它，以及其他相關資訊，例如**影響**或**基準測試**。

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>針對電腦上的 SQL server 進行先進的威脅防護警示
警示是由不尋常且可能有害的嘗試存取或惡意探索 SQL 電腦所產生。 這些事件可以觸發 [[警示參考] 頁面的 [SQL Database 的警示] 和 [SQL 資料倉儲] 區段](alerts-reference.md#alerts-sql-db-and-warehouse)中所顯示的警示。



## <a name="explore-and-investigate-security-alerts"></a>探索和調查安全性警示

安全性警示可在資訊安全中心的 [警示] 頁面、資源的 [安全性] 索引標籤，或透過警示電子郵件中的直接連結取得。

1. 若要查看警示，請從資訊安全中心的提要欄位中選取 [**安全性警示**]，然後選取警示。

1. 警示的設計是獨立的，其中每一項都有詳細的補救步驟和調查資訊。 您可以使用其他 Azure 資訊安全中心和 Azure Sentinel 功能進一步調查，以提供更廣泛的觀點：

    * 啟用 SQL Server 的審核功能以進一步調查。 如果您是 Azure Sentinel 使用者，您可以將 SQL 審核記錄從 Windows 安全性記錄檔事件上傳到 Sentinel，並享有豐富的調查體驗。 [深入瞭解 SQL Server 的審核](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15)。
    * 若要改善您的安全性狀態，請針對每個警示中所指出的主機電腦，使用資訊安全中心的建議。 這會降低未來攻擊的風險。 

    [深入瞭解如何管理和回應警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。


## <a name="next-steps"></a>後續步驟

如需相關內容，請參閱下列文章：

- [SQL Database 和 SQL 資料倉儲的安全性警示](alerts-reference.md#alerts-sql-db-and-warehouse)
- [設定安全性警示的電子郵件通知](security-center-provide-security-contact-details.md)
- [深入瞭解 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Azure 資訊安全中心的先進資料安全性封裝](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)