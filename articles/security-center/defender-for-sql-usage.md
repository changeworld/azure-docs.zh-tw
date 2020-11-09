---
title: 如何使用適用于 SQL 的 Azure Defender
description: 瞭解如何使用 Azure 資訊安全中心的選用 Azure Defender for SQL 方案
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 4f83500e539ff5254db290d156dfcf50ea60de14
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372552"
---
# <a name="azure-defender-for-sql-servers-on-machines"></a>電腦上適用于 SQL server 的 Azure Defender 

此 Azure Defender 方案會偵測異常活動，指出有不尋常且可能有害的嘗試存取或惡意探索資料庫。

有可疑的資料庫活動、潛在弱點或 SQL 插入式攻擊，以及異常的資料庫存取和查詢模式時，您將會看到警示。

## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|預覽<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|定價：|**電腦上適用于 SQL server 的 Azure Defender** 會依 [定價頁面](security-center-pricing.md)上的顯示方式計費|
|受保護的 SQL 版本：|Azure SQL Server (Microsoft 支援服務所涵蓋的所有版本) |
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中國 Gov、其他 Gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>為電腦上的 SQL server 設定 Azure Defender

若要啟用此方案：

* 在您的 SQL server 主機上布建 Log Analytics 代理程式。 這會提供與 Azure 的連線。

* 在 [安全性中心的定價和設定] 頁面中啟用選擇性方案。

以下將說明這兩種情況。

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>步驟 1： 在您的 SQL server 主機上布建 Log Analytics 代理程式：

- **AZURE vm 上的 SQL Server** -如果您的 SQL 機器裝載于 azure vm 上，您可以自動布建 [Log Analytics 代理程式](security-center-enable-data-collection.md#workspace-configuration)。 或者，您也可以依照手動程式來將 [Azure Stack vm 上架](quickstart-onboard-machines.md#onboard-your-azure-stack-vms)。
- **SQL Server Azure Arc** -如果您的 SQL Server 是由 [Azure Arc](../azure-arc/index.yml) 啟用的伺服器管理，您可以使用資訊安全中心建議的「log analytics 代理程式應該安裝在 Windows 架構的 Azure Arc 電腦 (Preview) 」來部署 log analytics 代理程式。 或者，您可以遵循 [Azure Arc 檔](../azure-arc/servers/manage-vm-extensions.md)中所述的安裝方法。

- **SQL Server 內部內部部署** -如果您的 SQL Server 裝載于內部部署 Windows 機器上，而沒有 Azure Arc，您有兩個選項可將其連接到 Azure：
    
    - **部署 Azure Arc** -您可以將任何 Windows 電腦連接到安全性中心。 不過，Azure Arc 可在 *所有* Azure 環境之間提供更深入的整合。 如果您設定了 Azure Arc，您會在入口網站中看到 [ **SQL Server] Azure Arc** 頁面，而您的安全性警示將會出現在該頁面上的 [專用 **安全性** ] 索引標籤上。 第一個和建議的選項是 [設定主機上的 Azure Arc](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) ，並遵循上述 **Azure Arc 上 SQL Server** 的指示。
        
    - **不 Azure Arc 連接 windows 電腦** -如果您選擇連接 windows 電腦上執行的 SQL Server，而不使用 Azure Arc，請依照將 [windows 機器連線至 Azure 監視器](../azure-monitor/platform/agent-windows.md)中的指示進行。


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>步驟 2： 在 [安全性中心的定價和設定] 頁面中啟用選擇性方案：

1. 在 [安全性中心] 功能表中，開啟 [ **定價 & 設定** ] 頁面。

    - 如果您使用 **Azure 資訊安全中心的預設工作區** (名稱為 "defaultworkspace-[您的訂用帳戶識別碼]-[區域]" ) ，請選取相關的 **訂** 用帳戶。

    - 如果您使用 **非預設工作區** ，請選取相關的 **工作區** (在 [必要時，在篩選中輸入工作區的名稱]) ：

        ![依標題尋找您的非預設工作區](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. 針對 **電腦上的 SQL server 設定 Azure Defender 的選項 (Preview)** 方案設定為 **開啟** 。 

    ![具有選擇性方案的 [安全性中心定價] 頁面](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)

    此方案將會在所有連線到所選工作區的 SQL 伺服器上啟用。 在第一次重新開機 SQL Server 實例之後，保護將會完全有效。

    >[!TIP] 
    > 若要建立新的工作區，請遵循 [建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)中的指示。


1. （選擇性）設定安全性警示的電子郵件通知。 
    您可以設定要在產生安全性中心警示時收到電子郵件通知的收件者清單。 電子郵件包含 Azure 資訊安全中心中警示的直接連結，其中包含所有相關的詳細資料。 如需詳細資訊，請參閱 [設定安全性警示的電子郵件通知](security-center-provide-security-contact-details.md)。



## <a name="explore-vulnerability-assessment-reports"></a>探索弱點評定報告

弱點評定服務會每週掃描一次您的資料庫。 掃描會在您已啟用服務的一周同一天執行。

弱點評估儀表板提供您所有資料庫的評量結果總覽，以及狀況良好和狀況不良資料庫的摘要，以及根據風險分佈的失敗檢查的整體摘要。

您可以直接從 [安全性中心] 查看弱點評定結果。

1. 從資訊安全中心的提要欄位中，開啟 [ **建議** ] 頁面，然後選取 **您的 SQL server 上應補救的建議弱點 (預覽)** 。 如需詳細資訊，請參閱資訊 [安全中心建議](security-center-recommendations.md)。 

    :::image type="content" source="./media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png" alt-text="您應補救電腦上 SQL server 的弱點評定結果 (預覽版) ":::

    此建議的詳細觀點隨即出現。

    :::image type="content" source="./media/security-center-advanced-iaas-data/all-servers-view.png" alt-text="建議的詳細觀點":::

1. 如需詳細資料，請向下切入：

    * 如需 (資料庫) 的掃描資源，以及已測試的安全性檢查清單，請選取您感興趣的伺服器。

    * 如需依特定 SQL database 分組的弱點總覽，請選取您感興趣的資料庫。

    在每個觀點中，安全性檢查會依 **嚴重性** 排序。 按一下特定的安全性檢查，以查看具有 **描述** 的詳細資料窗格、如何進行 **修復** ，以及其他相關資訊，例如 **影響** 或 **基準測試** 。

## <a name="azure-defender-for-sql-alerts"></a>適用于 SQL 的 Azure Defender 警示
警示是由不尋常且可能有害的嘗試存取或惡意探索 SQL 電腦所產生。 這些事件可以在 [ [警示參考] 頁面的 [SQL Database 和 Azure Synapse Analytics (先前的 SQL 資料倉儲) 區段](alerts-reference.md#alerts-sql-db-and-warehouse)中，觸發顯示的警示。

## <a name="explore-and-investigate-security-alerts"></a>探索和調查安全性警示

您可以在 [安全性中心的警示] 頁面、資源的 [安全性] 索引標籤、 [Azure Defender 儀表板](azure-defender-dashboard.md)，或透過警示電子郵件中的直接連結取得 Azure Defender 警示。

1. 若要查看警示，請從 [安全性中心] 功能表選取 [ **安全性警示** ]，然後選取警示。

1. 警示是設計成獨立的，其中每一項都有詳細的補救步驟和調查資訊。 您可以使用其他 Azure 資訊安全中心和 Azure Sentinel 功能進一步調查，以取得更廣泛的觀點：

    * 啟用 SQL Server 的審核功能，以進行進一步的調查。 如果您是 Azure Sentinel 使用者，您可以將來自 Windows 安全性記錄檔事件的 SQL 審核記錄上傳至 Sentinel，並享受豐富的調查體驗。 [深入瞭解 SQL Server 的審核](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15)。
    * 若要改善您的安全性狀態，請針對每個警示中指出的主機電腦使用安全中心的建議。 這會降低未來攻擊的風險。 

    [深入瞭解如何管理及回應警示](security-center-managing-and-responding-alerts.md)。


## <a name="next-steps"></a>後續步驟

如需相關材質，請參閱下列文章：

- [先前的 SQL 資料倉儲 SQL Database 和 Azure Synapse Analytics (的安全性警示) ](alerts-reference.md#alerts-sql-db-and-warehouse)
- [設定安全性警示的電子郵件通知](security-center-provide-security-contact-details.md)
- [深入瞭解 Azure Sentinel](../sentinel/index.yml)
- [Azure 資訊安全中心的資料安全性封裝](../azure-sql/database/azure-defender-for-sql.md)