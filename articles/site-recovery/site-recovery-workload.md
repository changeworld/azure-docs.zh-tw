---
title: 關於使用 Azure Site Recovery 的內部部署應用程式的嚴重損壞修復
description: 描述可以透過 Azure Site Recovery 服務使用災害復原來保護的工作負載。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80062829"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>關於內部部署應用程式的災害復原

本文說明內部部署工作負載和應用程式，您可以使用 [Azure Site Recovery](site-recovery-overview.md) 服務保護災難復原。

## <a name="overview"></a>概觀

組織需要商務持續性和嚴重損壞修復 (BCDR) 策略，以確保工作負載和資料的安全，並在發生規劃和未規劃的停機期間提供。 而且，復原到正常運作的情況。

Site Recovery 是一項有助於建立 BCDR 策略的 Azure 服務。 使用 Site Recovery，您可以將應用程式感知的複寫部署到雲端，或部署到次要站台。 您可以使用 Site Recovery 來管理複寫、執行損毀修復測試，以及執行容錯移轉和容錯回復。 您的應用程式可以在 Windows 或 Linux 電腦、實體伺服器、VMware 或 Hyper-v 上執行。

Site Recovery 與 Microsoft 應用程式（例如 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory）整合。 Microsoft 與領導廠商密切合作，包括 Oracle、SAP 和 Red Hat。 您可以根據各應用程式自訂複寫解決方案。

## <a name="why-use-site-recovery-for-application-replication"></a>為何要使用 Site Recovery 進行應用程式複寫？

Site Recovery 可協助您實作應用程式層級的保護和復原，如下所示：

- 應用程式不相容，並為在支援的機器上執行的任何工作負載提供複寫。
- 近乎同步複寫，復原點目標 (RPO) 低達30秒，以滿足大多數重要商務應用程式的需求。
- 適用於單一或多層式架構應用程式的應用程式一致性快照。
- 與 SQL Server AlwaysOn 整合，並與其他應用層級複寫技術合作。 例如，Active Directory replication、SQL AlwaysOn 和 Exchange 資料庫可用性群組 (Dag) 。
- 彈性的復原方案，可讓您只需按一下就能復原整個應用程式堆疊，並在方案中納入外部腳本和手動動作。
- Site Recovery 和 Azure 中的先進網路管理可簡化應用程式網路需求。 網路管理，例如能夠保留 IP 位址、設定負載平衡，以及與低復原時間目標的 Azure 流量管理員整合 (RTO) 網路轉換。
- 豐富的自動化程式庫，提供已可用於生產環境的應用程式特定指令碼，這些指令碼可供下載並與復原方案整合。

## <a name="workload-summary"></a>工作負載摘要

Site Recovery 可複寫在支援的機器上執行的任何應用程式。 我們與產品小組合作，針對下表所指定的應用程式進行其他測試。

| **[工作負載]** |**將 Azure VM 複寫至 Azure** |**將 Hyper-V VM 複寫至次要網站** | **將 Hyper-V VM 複寫至 Azure** | **將 VMware VM 複寫到次要網站** | **將 VMware VM 複寫到 Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory、DNS |是 |是 |是 |是 |是|
| Web 應用程式 (IIS、SQL) |是 |是 |是 |是 |是|
| System Center Operations Manager |是 |是 |是 |是 |是|
| SharePoint |是 |是 |是 |是 |是|
| SAP<br/><br/>將 SAP 網站複寫至非叢集的 Azure |是 (由 Microsoft) 測試 |是 (由 Microsoft) 測試 |是 (由 Microsoft) 測試 |是 (由 Microsoft) 測試 |是 (由 Microsoft) 測試|
| Exchange (非 DAG) |是 |是 |是 |是 |是|
| 遠端桌面/VDI |是 |是 |是 |是 |是|
| Linux (作業系統和應用程式) |是 (由 Microsoft) 測試 |是 (由 Microsoft) 測試 |是 (由 Microsoft) 測試 |是 (由 Microsoft) 測試 |是 (由 Microsoft) 測試|
| Dynamics AX |是 |是 |是 |是 |是|
| Windows 檔案伺服器 |是 |是 |是 |是 |是|
| Citrix XenApp 和 XenDesktop |是|N/A |是 |N/A |是 |

## <a name="replicate-active-directory-and-dns"></a>複寫 Active Directory 和 DNS

Active Directory 和 DNS 基礎結構是大多數企業應用程式的必要項目。 在災難復原期間，您必須在復原工作負載和應用程式之前，先保護和復原這些基礎結構元件。

您可以使用 Site Recovery，對 Active Directory 和 DNS 建立一個完整的自動化災害復原方案。 例如，若要將 SharePoint 和 SAP 從主要網站容錯移轉至次要網站，您可以設定復原計畫，以進行第一次容錯移轉 Active Directory。 然後使用其他應用程式特定的復原方案，來容錯移轉依賴 Active Directory 的其他應用程式。

[深入瞭解](site-recovery-active-directory.md) ACTIVE DIRECTORY 和 DNS 的嚴重損壞修復。

## <a name="protect-sql-server"></a>保護 SQL Server

SQL Server 針對內部部署資料中心內的許多商務應用程式，提供資料服務基礎。 Site Recovery 可以與 SQL Server HA/DR 技術搭配使用，以保護使用 SQL Server 的多層式企業應用程式。

Site Recovery 提供：

- 針對 SQL Server 提供簡單且符合成本效益的災害復原解決方案。 將多個版本的 SQL Server 獨立伺服器和叢集複寫至 Azure 或次要網站。
- 整合 SQL AlwaysOn 可用性群組，利用 Azure Site Recovery 復原方案管理容錯移轉和容錯回復。
- 應用程式中各層 (包括 SQL Server 資料庫) 的端對端復原方案。
- 使用 Site Recovery 來調整尖峰負載的 SQL Server，方法是在 Azure 中將其高載為較 _大的 IaaS_ 虛擬機器大小。
- 簡單的 SQL Server 災害復原測試。 您可以測試容錯移轉，以分析資料並執行相容性檢查，但不會影響您的生產環境。

[深入瞭解](site-recovery-sql.md) SQL server 的嚴重損壞修復。

## <a name="protect-sharepoint"></a>保護 SharePoint

Azure Site Recovery 可協助保護 SharePoint 部署，如下所示：

- 排除對災害復原之待命伺服器陣列的需要和相關聯的基礎結構成本。 使用 Site Recovery，將整個伺服器陣列 (web、應用程式和資料庫層) 複製到 Azure 或次要網站。
- 簡化應用程式部署和管理作業。 系統會自動複寫部署至主要網站的更新。 在次要網站中容錯移轉和復原伺服器陣列之後，就可以使用這些更新。 降低將待命伺服器陣列保持在最新狀態的相關管理複雜性和成本。
- 藉由建立類似生產的複本隨選複本環境進行測試和偵錯，以簡化 SharePoint 應用程式開發和測試。
- 藉由使用 Site Recovery 將 SharePoint 部署移轉至 Azure，來簡化雲端轉換。

[深入瞭解](site-recovery-sharepoint.md) SharePoint 的嚴重損壞修復。

## <a name="protect-dynamics-ax"></a>保護 Dynamics AX

Azure Site Recovery 可協助保護您的 Dynamics AX ERP 解決方案，方法如下：

- 管理整個 Dynamics AX 環境的複寫 (Web 和 AOS 層、資料庫層、SharePoint) 至 Azure 或次要網站。
- 簡化 Dynamics AX 部署到雲端 (Azure) 的移轉。
- 藉由建立類似生產的複本隨選進行測試和偵錯，以簡化 Dynamics AX 應用程式開發和測試。

[深入瞭解](site-recovery-dynamicsax.md) 動態 AX 的嚴重損壞修復。

## <a name="protect-remote-desktop-services"></a>保護遠端桌面服務

遠端桌面服務 (RDS) 讓虛擬桌面基礎結構 (VDI) 、以會話為基礎的桌上型電腦和應用程式，讓使用者可以在任何地方工作。

您可以使用 Azure Site Recovery 複寫下列服務：

- 將受管理或未受管理的集區虛擬桌面複寫至次要網站。
- 將遠端應用程式和會話複寫至次要網站或 Azure。

下表顯示覆寫選項：

| **Rds** |**將 Azure VM 複寫至 Azure** | **將 Hyper-V VM 複寫至次要網站** | **將 Hyper-V VM 複寫至 Azure** | **將 VMware VM 複寫到次要網站** | **將 VMware VM 複寫到 Azure** | **將實體伺服器複寫到次要網站** | **將實體伺服器複寫到 Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **共用的虛擬桌面 (非受控) ** |否|是 |否 |是 |否 |是 |否 |
| **集區化虛擬桌面 (受控但不含 UPD)** |否|是 |否 |是 |否 |是 |否 |
| **遠端應用程式和桌面工作階段 (不含 UPD)** |是|是 |是 |是 |是 |是 |是 |

[深入瞭解](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) RDS 的嚴重損壞修復。

## <a name="protect-exchange"></a>保護 Exchange

Site Recovery 協助保護 Exchange 的方式如下所示：

- 對於小型 Exchange 部署 (例如單一或獨立式伺服器)，Site Recovery 可以複寫並容錯移轉至 Azure 或次要網站。
- 對於大型部署，Site Recovery 會與 Exchange DAG 整合。
- Exchange DAG 是企業中 Exchange 災害復原的建議解決方案。  Site Recovery 復原方案可以包含 DAG，以協調網站之間的 DAG 容錯移轉。

若要深入瞭解 Exchange 的嚴重損壞修復，請參閱 [Exchange dag](/Exchange/high-availability/database-availability-groups/database-availability-groups) 和 exchange 嚴重損壞 [修復](/Exchange/high-availability/disaster-recovery/disaster-recovery)。

## <a name="protect-sap"></a>保護 SAP

使用 Site Recovery 保護您的 SAP 部署，如下所示：

- 藉由將元件複寫到 Azure，啟用在內部部署環境執行之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
- 藉由將元件複寫到另一個 Azure 資料中心，啟用執行 Azure 之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
- 使用 Site Recovery 將您的 SAP 部署移轉至 Azure，來簡化雲端移轉。
- 藉由建立隨選生產複本來測試 SAP 應用程式，簡化 SAP 專案升級、測試和原型設計。

[深入瞭解](site-recovery-sap.md) SAP 的嚴重損壞修復。

## <a name="protect-internet-information-services"></a>保護 Internet Information Services

使用 Site Recovery 來保護 Internet Information Services (IIS) 部署，如下所示：

Azure Site Recovery 提供災害復原，方法為將環境中的重要元件複寫至冷遠端站台或類似 Microsoft Azure 的公用雲端。 由於具有網頁伺服器和資料庫的虛擬機器會複寫到復原網站，因此不需要針對設定檔或憑證進行個別的備份。 取決於容錯移轉後之環境變數的應用程式對應和繫結，可以透過指令碼整合到災害復原計劃進行更新。 只有在容錯移轉期間，才會在復原網站上啟動虛擬機器。 Azure Site Recovery 也可提供下列功能，協助您協調端對端容錯移轉：

- 排序各層中的虛擬機器啟動與關機。
- 新增腳本，以允許在虛擬機器啟動之後更新應用程式相依性和系結。 指令碼也可用來更新 DNS 伺服器指向復原站台。
- 藉由對應主要和復原網路，並使用不需要在容錯移轉後更新的腳本，將 IP 位址配置給虛擬機器預先容錯移轉。
- 能夠針對多個 web 應用程式進行單鍵容錯移轉，以避免發生嚴重損壞時的混淆範圍。
- 能夠在 DR 鑽研的隔離環境中測試復原計劃。

[深入瞭解](site-recovery-iis.md) IIS 的嚴重損壞修復。

## <a name="protect-citrix-xenapp-and-xendesktop"></a>保護 Citrix XenApp 和 XenDesktop

使用 Site Recovery 保護您的 Citrix XenApp 和 XenDesktop 部署，如下所示：

- 啟用 Citrix XenApp 和 XenDesktop 部署的保護。 將不同的部署層複寫至 Azure： Active Directory、DNS 伺服器、SQL database 伺服器、Citrix 傳遞控制器、店面伺服器、XenApp 主要 (VDA) 、Citrix XenApp 授權伺服器。
- 使用 Site Recovery 將您的 Citrix XenApp 和 XenDesktop 部署移轉至 Azure，來簡化雲端移轉。
- 藉由建立類似生產的隨選複本來進行測試和偵錯，以簡化 Citrix XenApp/XenDesktop 測試。
- 此解決方案只適用于 Windows Server 虛擬桌面，而不適用用戶端虛擬桌面。 Azure 中的授權尚未支援用戶端虛擬桌面。 [深入了解](https://azure.microsoft.com/pricing/licensing-faq/)如何在 Azure 中進行用戶端/伺服器桌面的授權。

[深入瞭解](site-recovery-citrix-xenapp-and-xendesktop.md) Citrix XenApp 和 XenDesktop 部署的嚴重損壞修復。 或者，您可以參考 [Citrix 白皮書](https://aka.ms/citrix-xenapp-xendesktop-with-asr)。

## <a name="next-steps"></a>後續步驟

[深入瞭解](azure-to-azure-quickstart.md) Azure VM 的嚴重損壞修復。
