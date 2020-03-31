---
title: 有關使用 Azure 網站恢復的本地應用的災害復原
description: 描述可以透過 Azure Site Recovery 服務使用災害復原來保護的工作負載。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062829"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>關於內部部署應用程式的災害復原

本文介紹了使用[Azure 網站恢復](site-recovery-overview.md)服務保護災害復原的本地工作負荷和應用。

## <a name="overview"></a>總覽

組織需要業務連續性和災害復原 （BCDR） 策略，以確保工作負載和資料在計畫內和計畫外停機期間的安全以及可用。 並且，恢復到正常的工作條件。

Site Recovery 是一項有助於建立 BCDR 策略的 Azure 服務。 使用 Site Recovery，您可以將應用程式感知的複寫部署到雲端，或部署到次要站台。 您可以使用網站恢復來管理複製、執行災害復原測試以及運行容錯移轉和故障恢復。 你的應用可以在 Windows 或基於 Linux 的電腦、物理伺服器、VMware 或 Hyper-V 上運行。

網站恢復與 Microsoft 應用程式（如 SharePoint、Exchange、動態、SQL Server 和活動目錄）集成。 微軟與 Oracle、SAP 和紅帽等領先供應商密切合作。 您可以根據各應用程式自訂複寫解決方案。

## <a name="why-use-site-recovery-for-application-replication"></a>為何要使用 Site Recovery 進行應用程式複寫？

Site Recovery 可協助您實作應用程式層級的保護和復原，如下所示：

- 與應用無關，並為在受支援的電腦上運行的任何工作負載提供複製。
- 近同步複製，復原點目標 （RPO） 低至 30 秒，可滿足大多數關鍵業務應用的需求。
- 適用於單一或多層式架構應用程式的應用程式一致性快照。
- 與 SQL Server AlwaysOn 集成，並與其他應用程式級複製技術合作。 例如，活動目錄複寫、SQL Alwayson 和交換資料庫可用性組 （DG）。
- 靈活的恢復計畫，使您能夠只需按一下一下即可恢復整個應用程式堆疊，並在計畫中包括外部腳本和手動操作。
- 網站恢復和 Azure 中的高級網路管理，以簡化應用網路要求。 網路管理，例如保留 IP 位址、配置負載平衡以及與 Azure 流量管理器集成以實現低恢復時間目標 （RTO） 網路切換的能力。
- 豐富的自動化程式庫，提供已可用於生產環境的應用程式特定指令碼，這些指令碼可供下載並與復原方案整合。

## <a name="workload-summary"></a>工作負載摘要

Site Recovery 可複寫在支援的機器上執行的任何應用程式。 我們與產品團隊合作，對下表中指定的應用執行其他測試。

| **[工作負載]** |**將 Azure VM 複寫至 Azure** |**將 Hyper-V VM 複寫至次要網站** | **將 Hyper-V VM 複寫至 Azure** | **將 VMware VM 複寫到次要網站** | **將 VMware VM 複寫到 Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory、DNS |是 |是 |是 |是 |是|
| Web 應用程式 (IIS、SQL) |是 |是 |是 |是 |是|
| System Center Operations Manager |是 |是 |是 |是 |是|
| SharePoint |是 |是 |是 |是 |是|
| SAP<br/><br/>將 SAP 網站複寫至非叢集的 Azure |是（由微軟測試） |是（由微軟測試） |是（由微軟測試） |是（由微軟測試） |是（由微軟測試）|
| Exchange (非 DAG) |是 |是 |是 |是 |是|
| 遠端桌面/VDI |是 |是 |是 |是 |是|
| Linux (作業系統和應用程式) |是（由微軟測試） |是（由微軟測試） |是（由微軟測試） |是（由微軟測試） |是（由微軟測試）|
| Dynamics AX |是 |是 |是 |是 |是|
| Windows 檔案伺服器 |是 |是 |是 |是 |是|
| Citrix XenApp 和 XenDesktop |是|N/A |是 |N/A |是 |

## <a name="replicate-active-directory-and-dns"></a>複寫 Active Directory 和 DNS

Active Directory 和 DNS 基礎結構是大多數企業應用程式的必要項目。 在災害復原期間，您需要在恢復工作負載和應用程式之前保護和恢復這些基礎結構元件。

您可以使用 Site Recovery，對 Active Directory 和 DNS 建立一個完整的自動化災害復原方案。 例如，要將 SharePoint 和 SAP 從主網站容錯移轉到輔助網站，可以設置恢復計畫，該計畫首先在活動目錄上失敗。 然後，使用其他特定于應用的恢復計畫來故障接管依賴于 Active Directory 的其他應用。

[詳細瞭解](site-recovery-active-directory.md)活動目錄和 DNS 的災害復原。

## <a name="protect-sql-server"></a>保護 SQL Server

SQL Server 為本地資料中心中的許多業務應用提供了資料服務基礎。 網站恢復可與 SQL Server HA/DR 技術一起使用，以保護使用 SQL Server 的多層企業應用。

Site Recovery 提供：

- 針對 SQL Server 提供簡單且符合成本效益的災害復原解決方案。 將多個版本的 SQL Server 獨立伺服器和叢集複寫至 Azure 或次要網站。
- 整合 SQL AlwaysOn 可用性群組，利用 Azure Site Recovery 復原方案管理容錯移轉和容錯回復。
- 應用程式中各層 (包括 SQL Server 資料庫) 的端對端復原方案。
- _通過將_SQL Server 轉換為 Azure 中較大的 IaaS 虛擬機器大小，通過網站恢復擴展 SQL Server 以達到峰值負載。
- 簡單的 SQL Server 災害復原測試。 您可以測試容錯移轉，以分析資料並執行相容性檢查，但不會影響您的生產環境。

瞭解有關 SQL 伺服器災害復原[的更多](site-recovery-sql.md)資訊。

## <a name="protect-sharepoint"></a>保護 SharePoint

Azure Site Recovery 可協助保護 SharePoint 部署，如下所示：

- 排除對災害復原之待命伺服器陣列的需要和相關聯的基礎結構成本。 使用網站恢復將整個伺服器場（Web、應用和資料庫層）複製到 Azure 或輔助網站。
- 簡化應用程式部署和管理作業。 部署到主網站的更新將自動複製。 在容錯移轉和恢復輔助網站中的伺服器場後，更新可用。 降低與保持待命伺服器場最新相關的管理複雜性和成本。
- 藉由建立類似生產的複本隨選複本環境進行測試和偵錯，以簡化 SharePoint 應用程式開發和測試。
- 藉由使用 Site Recovery 將 SharePoint 部署移轉至 Azure，來簡化雲端轉換。

[詳細瞭解](site-recovery-sharepoint.md)SharePoint 的災害復原。

## <a name="protect-dynamics-ax"></a>保護 Dynamics AX

Azure Site Recovery 可協助保護您的 Dynamics AX ERP 解決方案，方法如下：

- 管理整個 Dynamics AX 環境（Web 和 AOS 層、資料庫層、SharePoint）對 Azure 或輔助網站的複製。
- 簡化 Dynamics AX 部署到雲端 (Azure) 的移轉。
- 藉由建立類似生產的複本隨選進行測試和偵錯，以簡化 Dynamics AX 應用程式開發和測試。

[瞭解有關](site-recovery-dynamicsax.md)動態 AX 災害復原的更多詳細資訊。

## <a name="protect-remote-desktop-services"></a>保護遠端桌面服務

遠端桌面服務 （RDS） 支援虛擬桌面基礎結構 （VDI）、基於會話的桌面和應用程式，允許使用者在任何地方工作。

使用 Azure 網站恢復，您可以複製以下服務：

- 將託管或非託管池虛擬桌面複製到輔助網站。
- 將遠端應用程式和會話複製到輔助網站或 Azure。

下表顯示了複製選項：

| **Rds** |**將 Azure VM 複寫至 Azure** | **將 Hyper-V VM 複寫至次要網站** | **將 Hyper-V VM 複寫至 Azure** | **將 VMware VM 複寫到次要網站** | **將 VMware VM 複寫到 Azure** | **將實體伺服器複寫到次要網站** | **將實體伺服器複寫到 Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **集區化虛擬桌面 (非受控)** |否|是 |否 |是 |否 |是 |否 |
| **集區化虛擬桌面 (受控但不含 UPD)** |否|是 |否 |是 |否 |是 |否 |
| **遠端應用程式和桌面工作階段 (不含 UPD)** |是|是 |是 |是 |是 |是 |是 |

[詳細瞭解](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure)RDS 的災害復原。

## <a name="protect-exchange"></a>保護 Exchange

Site Recovery 協助保護 Exchange 的方式如下所示：

- 對於小型 Exchange 部署 (例如單一或獨立式伺服器)，Site Recovery 可以複寫並容錯移轉至 Azure 或次要網站。
- 對於大型部署，Site Recovery 會與 Exchange DAG 整合。
- Exchange DAG 是企業中 Exchange 災害復原的建議解決方案。  Site Recovery 復原方案可以包含 DAG，以協調網站之間的 DAG 容錯移轉。

要瞭解有關災害復原交換的更多，請參閱交換[DAG](/Exchange/high-availability/database-availability-groups/database-availability-groups)和[Exchange 災害復原](/Exchange/high-availability/disaster-recovery/disaster-recovery)。

## <a name="protect-sap"></a>保護 SAP

使用 Site Recovery 保護您的 SAP 部署，如下所示：

- 藉由將元件複寫到 Azure，啟用在內部部署環境執行之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
- 藉由將元件複寫到另一個 Azure 資料中心，啟用執行 Azure 之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
- 使用 Site Recovery 將您的 SAP 部署移轉至 Azure，來簡化雲端移轉。
- 藉由建立隨選生產複本來測試 SAP 應用程式，簡化 SAP 專案升級、測試和原型設計。

[詳細瞭解](site-recovery-sap.md)SAP 的災害復原。

## <a name="protect-internet-information-services"></a>保護互聯網資訊服務

使用網站恢復保護您的互聯網資訊服務 （IIS） 部署，如下所示：

Azure Site Recovery 提供災害復原，方法為將環境中的重要元件複寫至冷遠端站台或類似 Microsoft Azure 的公用雲端。 由於具有 Web 服務器和資料庫的虛擬機器已複製到恢復網站，因此無需對設定檔或證書進行單獨的備份。 取決於容錯移轉後之環境變數的應用程式對應和繫結，可以透過指令碼整合到災害復原計劃進行更新。 虛擬機器僅在容錯移轉期間在恢復網站上啟動。 Azure 網站恢復還通過提供以下功能，説明您協調端到端容錯移轉：

- 排序各層中的虛擬機器啟動與關機。
- 添加腳本，以便在虛擬機器啟動後更新應用程式依賴項和綁定。 指令碼也可用來更新 DNS 伺服器指向復原站台。
- 通過映射主網路和恢復網路並使用容錯移轉後不需要更新的腳本，將 IP 位址分配給虛擬機器容錯移轉前。
- 能夠為多個 Web 應用程式進行一鍵式容錯移轉，從而消除災難期間的混亂範圍。
- 能夠在 DR 鑽研的隔離環境中測試復原計劃。

[瞭解有關](site-recovery-iis.md)IIS 災害復原的更多詳細資訊。

## <a name="protect-citrix-xenapp-and-xendesktop"></a>保護 Citrix XenApp 和 XenDesktop

使用 Site Recovery 保護您的 Citrix XenApp 和 XenDesktop 部署，如下所示：

- 啟用 Citrix XenApp 和 XenDesktop 部署的保護。 將不同的部署層複製到 Azure：活動目錄、DNS 伺服器、SQL 資料庫伺服器、Citrix 交付控制器、StoreFront 伺服器、XenApp 主控形狀 （VDA）、Citrix XenApp 許可證伺服器。
- 使用 Site Recovery 將您的 Citrix XenApp 和 XenDesktop 部署移轉至 Azure，來簡化雲端移轉。
- 藉由建立類似生產的隨選複本來進行測試和偵錯，以簡化 Citrix XenApp/XenDesktop 測試。
- 此解決方案僅適用于 Windows Server 虛擬桌面，不適用於用戶端虛擬桌面。 Azure 中尚不支援用戶端虛擬桌面進行許可。 [深入了解](https://azure.microsoft.com/pricing/licensing-faq/)如何在 Azure 中進行用戶端/伺服器桌面的授權。

[詳細瞭解](site-recovery-citrix-xenapp-and-xendesktop.md)Citrix XenApp 和 XenDesktop 部署的災害復原情況。 或者，您可以參考[Citrix 白皮書](https://aka.ms/citrix-xenapp-xendesktop-with-asr)。

## <a name="next-steps"></a>後續步驟

[詳細瞭解](azure-to-azure-quickstart.md)Azure VM 的災害復原。
