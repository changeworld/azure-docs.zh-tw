---
title: 'Azure 資訊安全中心的 SQL 機器 advanced data Security (Preview) '
description: 瞭解如何在 Azure 資訊安全中心中啟用 SQL 電腦的 advanced data security
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
ms.openlocfilehash: e0085ef5213853a1577ec039d5e360114aa7c64e
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566262"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>SQL 電腦的 Advanced data security (Preview) 

Azure 資訊安全中心的 SQL 機器 advanced data Security 可保護裝載于 Azure、其他雲端環境中的 SQL 伺服器，甚至是內部部署機器。 這可延伸 Azure 原生 SQL Server 的保護，以完整支援混合式環境。

此預覽功能包含用來識別和減緩潛在資料庫弱點，以及偵測可能表示對資料庫有威脅之異常活動的功能： 

* **弱點評定** -要探索、追蹤並協助您補救潛在資料庫弱點的掃描服務。 評量掃描可概述您的 SQL 機器的安全性狀態，以及任何安全性發現的詳細資料。

* [先進的威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) -此偵測服務會持續監視您的 sql server 是否有威脅，例如 sql 插入式攻擊、暴力密碼破解攻擊，以及特權濫用。 這項服務在 Azure 資訊安全中心中提供動作導向的安全性警示，並提供可疑活動的詳細資料、如何緩解威脅的指引，以及使用 Azure Sentinel 繼續進行調查的選項。

>[!TIP]
> SQL 機器的 advanced data security 是 Azure 資訊安全中心的 [advanced data security 封裝](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)的延伸模組，適用于 Azure SQL Database、Azure SYNAPSE 和 SQL 受控執行個體。


## <a name="set-up-advanced-data-security-for-sql-machines"></a>設定 SQL 電腦的 advanced data security 

設定 SQL 電腦的 Azure 資訊安全中心 advanced data Security 包含兩個步驟：

* 在您的 SQL server 主機上布建 Log Analytics 代理程式。 這會提供與 Azure 的連線。

* 在 [安全性中心的定價和設定] 頁面中啟用選用套件組合。

以下將說明這兩種情況。

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>步驟 1： 在您的 SQL server 主機上布建 Log Analytics 代理程式：

- **AZURE vm 上的 SQL Server** -如果您的 SQL 機器裝載于 azure vm 上，您可以自動布建 [Log Analytics 代理程式](security-center-enable-data-collection.md#workspace-configuration)。 或者，您可以依照手動程式來 [新增 AZURE VM](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines)。

- **SQL Server Azure Arc** -如果您的 SQL Server 裝載于 [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) 電腦上，您可以使用資訊安全中心建議的「log analytics 代理程式應該安裝在 Windows 架構的 Azure Arc 電腦 (Preview) 」上，以部署 Log analytics 代理程式。 或者，您可以依照 [Azure Arc 檔](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)中的手動程式進行操作。

- **SQL Server 內部內部部署** -如果您的 SQL Server 裝載于內部部署 Windows 機器上，而沒有 Azure Arc，您有兩個選項可將其連接到 Azure：
    
    - **部署 Azure Arc** -您可以將任何 Windows 電腦連接到安全性中心。 不過，Azure Arc 可在 *所有* Azure 環境之間提供更深入的整合。 如果您設定了 Azure Arc，您會在入口網站中看到 [ **SQL Server] Azure Arc** 頁面，而您的安全性警示將會出現在該頁面上的 [專用 **安全性** ] 索引標籤上。 第一個和建議的選項是 [設定主機上的 Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) ，並遵循上述 **Azure Arc 上 SQL Server**的指示。
        
    - **不 Azure Arc 連接 windows 電腦** -如果您選擇連接 windows 電腦上執行的 SQL Server，而不使用 Azure Arc，請依照將 [windows 機器連線至 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)中的指示進行。


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>步驟 2： 在 [安全性中心的定價和設定] 頁面中啟用選用套件組合：

1. 在 [安全性中心] 提要欄位中，開啟 [ **定價 & 設定** ] 頁面。

    - 如果您使用 **Azure 資訊安全中心的預設工作區** (名稱為 "defaultworkspace-[您的訂用帳戶識別碼]-[區域]" ) ，請選取相關的 **訂**用帳戶。

    - 如果您使用 **非預設工作區**，請選取相關的 **工作區** (在 [必要時，在篩選中輸入工作區的名稱]) ：

        ![title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. 切換 [ **電腦上的 SQL server] (預覽) ** 的選項以啟用。 

    [![具有選用配套的 [安全性中心] 定價頁面](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    在所有連線到所選工作區的 SQL server 上，都會啟用電腦上 SQL server 的 Advanced Data Security。 在第一次重新開機 SQL Server 實例之後，保護將會完全有效。

    >[!TIP] 
    > 若要建立新的工作區，請遵循 [建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中的指示。


1. （選擇性）設定安全性警示的電子郵件通知。 
    您可以設定要在產生安全性中心警示時收到電子郵件通知的收件者清單。 電子郵件包含 Azure 資訊安全中心中警示的直接連結，其中包含所有相關的詳細資料。 如需詳細資訊，請參閱 [設定安全性警示的電子郵件通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)。



## <a name="explore-vulnerability-assessment-reports"></a>探索弱點評定報告

弱點評定服務會每週掃描一次您的資料庫。 掃描會在您已啟用服務的一周同一天執行。

弱點評估儀表板提供您所有資料庫的評量結果總覽，以及狀況良好和狀況不良資料庫的摘要，以及根據風險分佈的失敗檢查的整體摘要。

您可以直接從 [安全性中心] 查看弱點評定結果。

1. 從資訊安全中心的提要欄位中，開啟 [ **建議** ] 頁面，然後選取 **您的 SQL server 上應補救的建議弱點 (預覽) **。 如需詳細資訊，請參閱資訊 [安全中心建議](security-center-recommendations.md)。 


    [![* * 您應補救電腦上 SQL server 的弱點 (預覽) * * 建議](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    此建議的詳細觀點隨即出現。

    [![您應補救電腦上 SQL server 上的 * * 弱點的詳細觀點 (預覽) * * 建議](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 如需詳細資料，請向下切入：

    * 如需 (資料庫) 的掃描資源，以及已測試的安全性檢查清單，請選取您感興趣的伺服器。

    * 如需依特定 SQL database 分組的弱點總覽，請選取您感興趣的資料庫。

    在每個觀點中，安全性檢查會依 **嚴重性**排序。 按一下特定的安全性檢查，以查看具有 **描述**的詳細資料窗格、如何進行 **修復** ，以及其他相關資訊，例如 **影響** 或 **基準測試**。

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>電腦上 SQL server 的 Advanced 威脅防護警示
警示是由不尋常且可能有害的嘗試存取或惡意探索 SQL 電腦所產生。 這些事件可在 [[警示參考] 頁面的 [SQL Database 和 SQL 資料倉儲的警示] 區段](alerts-reference.md#alerts-sql-db-and-warehouse)中，觸發顯示的警示。



## <a name="explore-and-investigate-security-alerts"></a>探索和調查安全性警示

安全性警示適用于安全性中心的 [警示] 頁面、資源的 [安全性] 索引標籤，或透過警示電子郵件中的直接連結。

1. 若要查看警示，請從 [安全性中心] 提要欄位選取 [ **安全性警示** ]，然後選取警示。

1. 警示是設計成獨立的，其中每一項都有詳細的補救步驟和調查資訊。 您可以使用其他 Azure 資訊安全中心和 Azure Sentinel 功能進一步調查，以取得更廣泛的觀點：

    * 啟用 SQL Server 的審核功能，以進行進一步的調查。 如果您是 Azure Sentinel 使用者，您可以將來自 Windows 安全性記錄檔事件的 SQL 審核記錄上傳至 Sentinel，並享受豐富的調查體驗。 [深入瞭解 SQL Server 的審核](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15)。
    * 若要改善您的安全性狀態，請針對每個警示中指出的主機電腦使用安全中心的建議。 這會降低未來攻擊的風險。 

    [深入瞭解如何管理及回應警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。


## <a name="next-steps"></a>後續步驟

如需相關材質，請參閱下列文章：

- [SQL Database 和 SQL 資料倉儲的安全性警示](alerts-reference.md#alerts-sql-db-and-warehouse)
- [設定安全性警示的電子郵件通知](security-center-provide-security-contact-details.md)
- [深入瞭解 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Azure 資訊安全中心的 advanced data Security 套件](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)