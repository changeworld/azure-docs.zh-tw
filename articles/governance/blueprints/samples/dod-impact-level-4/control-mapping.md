---
title: DoD 影響等級 4 藍圖範例控制
description: 控制 DoD 影響等級 4 藍圖範例的對應。 每個控制項都會對應至一或多個可協助評量的 Azure 原則。
ms.date: 09/17/2020
ms.topic: sample
ms.openlocfilehash: 9ee3214f8cdacd1d70a59bc5a23e4638cadb579d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978402"
---
# <a name="control-mapping-of-the-dod-impact-level-4-blueprint-sample"></a>控制 DoD 影響等級 4 藍圖範例的對應

下列文章會詳細說明 Azure 藍圖的美國國防部影響等級 4 (DoD IL4) 藍圖範例如何對應至 DoD 影響等級 4 的控制。 如需關於控制的詳細資訊，請參閱 [DoD 雲端運算安全性需求指南 (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf)。 國防資訊系統局 (DISA) 是美國國防部 (DoD) 的下轄機構，負責開發和維護 DoD 雲端運算安全性需求指南 (SRG)。 SRG 會針對裝載 DoD 資訊、系統和應用程式的雲端服務提供者 (CSP) 以及 DoD 使用的雲端服務，定義其基準安全性需求。  

下列對應是針對 **DoD 影響等級 4** 的控制。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。 然後，找出並選取 **\[預覽\]：DoD 影響等級 4** 內建原則計畫。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。
> 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/dod-impact-level-4/control-mapping.md) \(英文\)。

## <a name="ac-2-account-management"></a>AC-2 帳戶管理

此藍圖協助您檢閱可能不符合組織帳戶管理需求的帳戶。 此藍圖指派 [Azure 原則](../../../policy/overview.md)定義，使用訂用帳戶與已取代帳戶上的讀取、寫入與擁有者權限來稽核外部帳戶。 檢閱這些原則所稽核的帳戶，您可以採取適當的動作，確保符合帳戶管理需求。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) 帳戶管理 | 角色型配置

Azure 會實作 [Azure 角色型存取控制 (Azure RBAC)](../../../../role-based-access-control/overview.md)，以協助您管理有權存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖還指派了 [Azure 原則](../../../policy/overview.md)定義，用以稽核將 Azure Active Directory 驗證用於 SQL Server 和 Service Fabric 的情形。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。 此外，此藍圖指派了一項 Azure 原則定義來稽核自訂 RBAC 規則的使用情形。 了解自訂 RBAC 規則的實作之處，有助於您確認需求和適當的實作，因為自訂的 RBAC 規則很容易發生錯誤。

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- 稽核自訂 RBAC 規則的使用方式
- Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) 帳戶管理 | 帳戶監視 / 非典型的使用方式

Just-In-Time (JIT) 虛擬機器存取可鎖定 Azure 虛擬機器的連入流量、降低暴露於攻擊的風險，同時能夠視需要輕鬆連線至 VM。 存取虛擬機器的所有 JIT 要求都會都記錄在「活動記錄」中，讓您監視非典型的使用方式。 此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，協助您監視可支援 Just-In-Time 存取但尚未設定的虛擬機器。

- Just-In-Time 網路存取控制應套用在虛擬機器上

## <a name="ac-4-information-flow-enforcement"></a>AC-4 資訊流程強制

跨原始來源資源共用 (CORS) 允許從外部網域對應用程式服務資源提出要求。 Microsoft 建議您只允許必要網域與 API、功能和 Web 應用程式互動。 此藍圖指派 [Azure 原則](../../../policy/overview.md)定義，協助您在 Azure 資訊安全中心監視 CORS 資源存取限制。 了解 CORS 實作可協助您確認會實作資訊流程控制措施。

- CORS 不應允許每項資源存取您的 Web 應用程式

## <a name="ac-5-separation-of-duties"></a>AC-5 權責區分

只有一個 Azure 訂用帳戶擁有者時，無法建立系統管理備援。 相反地，若有過多 Azure 訂用帳戶擁有者，因擁有者帳戶遭到入侵而產生缺口的可能性就會增加。 此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，可稽核 Azure 訂用帳戶的擁有者數目，藉以協助您維護適當數量的 Azure 訂用帳戶擁有者。 這個藍圖還指派 Azure 原則定義，協助您控制 Windows 虛擬機器上 Administrators 群組的成員資格。 管理訂用帳戶擁有者與虛擬機器系統管理員權限可協助您實作適當的權責區分。

- 應針對您的訂用帳戶指定最多 3 位擁有者
- 稽核其中 Administrators 群組包含了任一指定成員的 Windows VM
- 稽核其中 Administrators 群組不包含任何指定成員的 Windows VM
- 部署需求以稽核其中 Administrators 群組包含了任一指定成員的 Windows VM
- 部署需求以稽核其中 Administrators 群組不包含任何指定成員的 Windows VM
- 應將一個以上的擁有者指派給您的訂用帳戶

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) 最小權限 | 使用者權限檢閱

Azure 會實作 [Azure 角色型存取控制 (Azure RBAC)](../../../../role-based-access-control/overview.md)，以協助您管理有權存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核應該優先檢閱的帳戶。 檢閱這些帳戶指標，可協助您確保會實作最低權限控制措施。

- 應針對您的訂用帳戶指定最多 3 位擁有者
- 稽核其中 Administrators 群組包含了任一指定成員的 Windows VM
- 稽核其中 Administrators 群組不包含任何指定成員的 Windows VM
- 部署需求以稽核其中 Administrators 群組包含了任一指定成員的 Windows VM
- 部署需求以稽核其中 Administrators 群組不包含任何指定成員的 Windows VM
- 應將一個以上的擁有者指派給您的訂用帳戶

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) 遠端存取 | 自動化監視 / 控制

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，監視 Azure App Service 應用程式的遠端偵錯已關閉，而且指派原則定義，稽核允許帳戶無需密碼便可從遠端連線的 Linux 虛擬機器，來協助您監視和控制遠端存取。 這個藍圖還指派一個 Azure 原則定義，協助您監視無限制地存取儲存體帳戶。 監視這些指標可協助您確保遠端存取方法符合安全性原則。

- \[預覽\]：稽核允許不使用密碼從帳戶遠端連線的 Linux VM
- \[預覽\]：部署需求以稽核允許不使用密碼從帳戶遠端連線的 Linux VM
- 稽核不受限制的儲存體帳戶網路存取
- 應關閉 API 應用程式的遠端偵錯
- 函式應用程式的遠端偵錯應關閉
- Web 應用程式的遠端偵錯應關閉

## <a name="ac-23-data-mining"></a>AC-23 資料採礦

此藍圖會提供原則定義，以協助您確定已正確啟用資料安全性通知。 此外，此藍圖可確保已在 SQL Server 上設定稽核與進階資料安全性。

- 應在 SQL 伺服器上啟用進階資料安全性
- 應在 SQL 受控執行個體上啟用進階資料安全性
- 在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 應該在 SQL Server 上的進階資料安全性設定上啟用稽核
- 應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知
- 應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知
- SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) 稽核記錄的內容 | 集中管理已計劃的稽核記錄內容

「Azure 監視器」所收集的記錄資料會儲存在 Log Analytics 工作區中，進而啟用集中化設定與管理。 此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核和強制要求在 Azure 虛擬機器上部署 Log Analytics 代理程式，協助您確保會記錄事件。

- \[預覽\]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Log Analytics 代理程式部署 - 未列出的 VM 映像 (OS)
- \[預覽\]：稽核適用於 VM 的 Log Analytics 工作區 - 報告不相符
- 為 Linux 虛擬機器擴展集部署 Log Analytics 代理程式
- \[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式
- 為 Windows 虛擬機器擴展集部署 Log Analytics 代理程式
- \[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 稽核處理程序失敗時的回應

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，監視稽核與事件記錄設定。 監視這些設定可提供稽核系統失敗或設定不正確的指標，並協助您採取矯正措施。

- 稽核診斷設定
- 應該在 SQL Server 上的進階資料安全性設定上啟用稽核
- 應在受控執行個體上啟用進階資料安全性
- 應在 SQL 伺服器上啟用進階資料安全性

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) 稽核檢閱、分析和報告 | 集中檢閱與分析

Azure 監視器所收集的記錄資料會儲存在 Log Analytics 工作區中，進而啟用集中化報告與分析。 此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核和強制要求在 Azure 虛擬機器上部署 Log Analytics 代理程式，協助您確保會記錄事件。

- \[預覽\]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Log Analytics 代理程式部署 - 未列出的 VM 映像 (OS)
- \[預覽\]：稽核適用於 VM 的 Log Analytics 工作區 - 報告不相符
- 為 Linux 虛擬機器擴展集部署 Log Analytics 代理程式
- \[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式
- 為 Windows 虛擬機器擴展集部署 Log Analytics 代理程式
- \[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) 稽核檢閱、分析和報告 | 整合 / 掃描和監視功能

此藍圖會提供原則定義，以便利用虛擬機器、虛擬機器擴展集、SQL Database 伺服器與 SQL 伺服器受控執行個體的弱點評定分析來稽核記錄。 這些原則定義也會稽核診斷記錄的設定，以深入解析 Azure 資源內所執行的作業。 這些深入解析可讓您即時檢視已部署資源的安全性狀態，並且可協助您排定補救措施的優先順序。 如需詳細的弱點掃描和監視，建議您也要利用 Azure Sentinel 和 Azure 資訊安全中心。

- \[預覽\]：虛擬機器上應啟用弱點評定
- 弱點評定應於您的 SQL 伺服器上啟用
- 稽核診斷設定
- SQL 受控執行個體上應啟用弱點評定
- 弱點評定應於您的 SQL 伺服器上啟用
- 您應在機器上修復安全性組態的弱點
- 應修復 SQL 資料庫的弱點
- 弱點評量解決方案應修復弱點
- 應修復虛擬機器擴展集上安全性組態的弱點
- \[預覽\]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Log Analytics 代理程式部署 - 未列出的 VM 映像 (OS)

## <a name="au-12-audit-generation"></a>AU-12 稽核產生

此藍圖會提供原則定義，以便稽核並強制要求在 Azure 虛擬機器上部署 Log Analytics 代理程式，以及設定其他 Azure 資源類型的稽核設定。
這些原則定義也會稽核診斷記錄的設定，以深入解析 Azure 資源內所執行的作業。 此外，會在 SQL 伺服器上設定稽核與「進階資料安全性」。

- \[預覽\]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Log Analytics 代理程式部署 - 未列出的 VM 映像 (OS)
- \[預覽\]：稽核適用於 VM 的 Log Analytics 工作區 - 報告不相符
- 為 Linux 虛擬機器擴展集部署 Log Analytics 代理程式
- \[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式
- 為 Windows 虛擬機器擴展集部署 Log Analytics 代理程式
- \[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式
- 稽核診斷設定
- 應該在 SQL Server 上的進階資料安全性設定上啟用稽核
- 應在受控執行個體上啟用進階資料安全性
- 應在 SQL 伺服器上啟用進階資料安全性
- 在 SQL 伺服器上部署進階資料安全性
- 在 SQL 伺服器上部署稽核
- 部署網路安全性群組的診斷設定

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) 稽核產生 | 全系統 / 時間相互相關的稽核線索

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。
此內建原則會要求您指定資源類型陣列，以檢查是否已啟用診斷設定。

- 稽核診斷設定

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) 最少的功能 | 防止程式執行

Azure 資訊安全中心的自適性應用程式控制是智慧型的自動化端對端應用程式允許清單解決方案，可封鎖或防止在虛擬機器上執行特定軟體。 應用程式控制能以強制模式執行，禁止執行未經核准的應用程式。 此藍圖會指派一個 Azure 原則定義，協助您監視已建議但尚未設定允許清單解決方案的虛擬機器。

- 您的電腦應啟用自適性應用程式控制，以定義安全應用程式

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) 最少的功能 | 已授權的軟體 / 加入允許清單

Azure 資訊安全中心的自適性應用程式控制是智慧型的自動化端對端應用程式允許清單解決方案，可封鎖或防止在虛擬機器上執行特定軟體。 應用程式控制可協助您建立虛擬機器的已核准應用程式清單。 此藍圖會指派一個 [Azure 原則](../../../policy/overview.md)定義，協助您監視已建議但尚未設定允許清單解決方案的虛擬機器。

- 您的電腦應啟用自適性應用程式控制，以定義安全應用程式

## <a name="cm-11-user-installed-software"></a>CM-11 使用者安裝的軟體

Azure 資訊安全中心的自適性應用程式控制是智慧型的自動化端對端應用程式允許清單解決方案，可封鎖或防止在虛擬機器上執行特定軟體。 應用程式控制可協助您強制執行軟體限制原則，並監視其合規性。 此藍圖會指派一個 [Azure 原則](../../../policy/overview.md)定義，協助您監視已建議但尚未設定允許清單解決方案的虛擬機器。

- 您的電腦應啟用自適性應用程式控制，以定義安全應用程式

## <a name="cp-7-alternate-processing-site"></a>CP-7 替代處理地點

Azure Site Recovery 會將虛擬機器上執行的工作負載從主要位置複寫到次要位置。 如果主要站台發生中斷，工作負載會容錯移轉到次要位置。 此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，稽核未設定災害復原的虛擬機器。 監視此指標可協助您確保已備妥必要的應變控制。

- 稽核未設定災害復原的虛擬機器

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) 資訊系統備份 | 移轉至替代儲存地點

此藍圖會指派 Azure 原則定義，以便透過電子方式將組織的系統備份資訊轉移至替代儲存地點。 如需進行儲存體中繼資料的實體出貨，請考慮使用 Azure 資料箱。

- 應為儲存體帳戶啟用異地備援儲存體
- 應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份
- 應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份
- 應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份
- 應為 Azure SQL Database 啟用長期異地備援備份

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) 識別與驗證 (組織使用者) | 特殊權限帳戶的網路存取

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核具有擁有者和/或寫入權限且未啟用多重要素驗證的帳戶，協助您限制和控制特殊權限存取。 多重要素驗證有助於確保帳戶即使有部分驗證資訊遭到入侵，仍然安全無虞。 藉由監視未啟用多重要素驗證的帳戶，您將可識別較可能遭到入侵的帳戶。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) 識別與驗證 (組織使用者) | 非特殊權限帳戶的網路存取

此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，稽核具有讀取權限且未啟用多重要素驗證的帳戶，協助您限制和控制存取。 多重要素驗證有助於確保帳戶即使有部分驗證資訊遭到入侵，仍然安全無虞。 藉由監視未啟用多重要素驗證的帳戶，您將可識別較可能遭到入侵的帳戶。

- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA

## <a name="ia-5-authenticator-management"></a>IA-5 驗證器管理

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核允許帳戶無需密碼便可從遠端連線和/或在 passwd 檔案設定不正確權限的 Linux 虛擬機器。 此藍圖還指派了原則定義，稽核 Windows 虛擬機器的密碼加密類型搭配。 監視這些指標可協助您確保系統驗證器符合貴組織的識別與驗證原則。

- \[預覽\]：稽核密碼檔權限未設為 0644 的 Linux VM
- \[預覽\]：稽核有不具密碼帳戶的 Linux VM
- \[預覽\]：稽核未使用可逆加密來儲存密碼的 Windows VM
- \[預覽\]：部署需求以稽核密碼檔權限未設為 0644 的 Linux VM
- \[預覽\]：部署需求以稽核有不具密碼帳戶的 Linux VM
- \[預覽\]：部署需求以稽核未使用可逆加密來儲存密碼的 Windows VM

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) 驗證器管理 | 密碼型驗證

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核未強制執行最低強度和其他密碼需求的 Windows 虛擬機器，以協助您強制要求使用強式密碼。 了解有哪些虛擬機器違反密碼強度原則可協助您採取矯正措施，以確保所有虛擬機器使用者帳戶的密碼都符合貴組織的密碼原則。

- \[預覽\]：稽核允許重複使用前 24 個舊密碼的 Windows 虛擬機器
- \[預覽\]：稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：稽核最小密碼存留期不是 1 天的 Windows VM
- \[預覽\]：稽核未啟用密碼複雜度設定的 Windows VM
- \[預覽\]：稽核最小密碼長度未限制為 14 個字元的 Windows 虛擬機器
- \[預覽\]：稽核未使用可逆加密來儲存密碼的 Windows VM
- \[預覽\]：部署需求以稽核允許重複使用前 24 個舊密碼的 Windows VM
- \[預覽\]：部署需求以稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：部署需求以稽核最小密碼存留期不是 1 天的 Windows VM
- \[預覽\]：部署需求以稽核未啟用密碼複雜度設定的 Windows VM
- \[預覽\]：部署需求以稽核最小密碼長度未限制為 14 個字元的 Windows VM
- \[預覽\]：部署需求以稽核未使用可逆加密來儲存密碼的 Windows VM

## <a name="ir-6-2-incident-reporting--vulnerabilities-related-to-incidents"></a>IR-6 (2) 事件報告 | 與事件相關的弱點

此藍圖會提供原則定義，以便利用虛擬機器、虛擬機器擴展集和 SQL Server 的弱點評定分析來稽核記錄。 這些深入解析可讓您即時檢視已部署資源的安全性狀態，並且可協助您排定補救措施的優先順序。

- 應修復虛擬機器擴展集上安全性組態的弱點
- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- 應修復 SQL 資料庫的弱點

## <a name="ra-5-vulnerability-scanning"></a>RA-5 弱點掃描

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，監視 Azure 資訊安全中心內的作業系統弱點、SQL 弱點與虛擬機器弱點，以協助您管理資訊系統弱點。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。 此藍圖還指派原則定義，針對 SQL 伺服器稽核並強制套用「進階資料安全性」。 「進階資料安全性」包含弱點評定和進階威脅防護功能，可協助您了解所部署資源的弱點。

- 應在受控執行個體上啟用進階資料安全性
- 應在 SQL 伺服器上啟用進階資料安全性
- 在 SQL 伺服器上部署進階資料安全性
- 應修復虛擬機器擴展集上安全性組態的弱點
- 應修復虛擬機器上安全性組態的弱點
- 應修復 SQL 資料庫的弱點
- 弱點評量解決方案應修復弱點

## <a name="sc-5-denial-of-service-protection"></a>SC-5 阻斷服務保護

Azure 的分散式阻斷服務 (DDoS) 標準層提供基本服務層以外的額外功能與防護功能。 這些額外的功能包括「Azure 監視器」整合以及能夠檢閱攻擊後風險降低報告。 此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，稽核是否已啟用 DDoS 標準層。 了解服務層之間的功能差異，協助您為 Azure 環境選取解決阻斷服務保護的最佳解決方案。

- 應啟用 DDoS 保護標準

## <a name="sc-7-boundary-protection"></a>SC-7 界限保護

此藍圖藉由指派 [Azure 原則](../../../policy/overview.md)定義在 Azure 資訊安全中心監視網路安全性群組強化建議，以協助您管理和控制系統界限。 「Azure 資訊安全中心」會分析網際網路面向虛擬機器的流量模式，並提供網路安全群組規則建議，以降低潛在的受攻擊面。
此外，此藍圖還指派原則定義，以監視未受保護的端點、應用程式與儲存體帳戶。 不受防火牆保護的端點和應用程式，以及存取不受限制的儲存體帳戶，都可能允許資訊系統內所包含的資訊受到非預期的存取。

- 應強化與虛擬機器互動的網際網路網路安全性群組規則
- 應限制透過網際網路面向端點存取
- 應在 IaaS 上強化 Web 應用程式的 NSG 規則
- 稽核不受限制的儲存體帳戶網路存取

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) 界限保護 | 存取點

Just-In-Time (JIT) 虛擬機器存取可鎖定 Azure 虛擬機器的連入流量、降低暴露於攻擊的風險，同時能夠視需要輕鬆連線至 VM。 JIT 虛擬機器存取可協助您限制 Azure 中資源的外部連線數目。 此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，協助您監視可支援 Just-In-Time 存取但尚未設定的虛擬機器。

- Just-In-Time 網路存取控制應套用在虛擬機器上

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) 界限保護 | 外部電信服務

Just-In-Time (JIT) 虛擬機器存取可鎖定 Azure 虛擬機器的連入流量、降低暴露於攻擊的風險，同時能夠視需要輕鬆連線至 VM。 JIT 虛擬機器存取可加快存取要求與核准程序，協助您管理流量原則的例外。 此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，協助您監視可支援 Just-In-Time 存取但尚未設定的虛擬機器。

- Just-In-Time 網路存取控制應套用在虛擬機器上

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) 傳輸機密性和完整性 | 密碼編譯或替代實體保護

此藍圖藉由指派 [Azure 原則](../../../policy/overview.md)定義來協助您監視針對通訊協定所實作的密碼編譯機制，進而協助您保護傳輸資訊的機密和完整性。 確保通訊受到適當加密，可協助您符合貴組織的需求，或保護資訊免於遭受未經授權的揭露和修改。

- API 應用程式應只可經由 HTTPS 存取
- 稽核未使用安全通訊協定的 Windows 網頁伺服器
- 部署需求以稽核未使用安全通訊協定的 Windows 網頁伺服器
- 函式應用程式應只可經由 HTTPS 存取
- 應該只允許對 Redis Cache 的安全連線
- 應啟用儲存體帳戶的安全傳輸
- Web 應用程式應只可經由 HTTPS 存取

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) 待用資訊的保護 | 密碼編譯保護

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，強制執行特定的密碼編譯控制措施及稽核弱式密碼編譯設定的使用，協助您強制執行密碼編譯控制措施的使用原則，以保護待用資訊。 了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則定義會要求加密 Data Lake Storage 帳戶、要求在 SQL 資料庫進行透明資料加密，以及稽核 SQL 資料庫、虛擬機器磁碟與自動化帳戶變體是否缺少加密。

- 應在受控執行個體上啟用進階資料安全性
- 應在 SQL 伺服器上啟用進階資料安全性
- 在 SQL 伺服器上部署進階資料安全性
- 部署 SQL DB 透明資料加密
- 應在虛擬機器上套用磁碟加密
- Data Lake Store 帳戶需要加密
- 應在 SQL 資料庫上啟用透明資料加密

## <a name="si-2-flaw-remediation"></a>SI-2 缺陷補救

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，用以監視 Azure 資訊安全中心內的遺漏系統更新、作業系統弱點、SQL 弱點和虛擬機器弱點，以協助您管理資訊系統缺陷。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。 此藍圖還指派一個原則定義，確保修補虛擬機器擴展集的作業系統。

- 虛擬機器擴展集需要自動 OS 映像修補
- 應在虛擬機器擴展集上安裝系統更新
- 應在虛擬機器上安裝系統更新
- 應修復虛擬機器擴展集上安全性組態的弱點
- 應修復虛擬機器上安全性組態的弱點
- 應修復 SQL 資料庫的弱點
- 弱點評量解決方案應修復弱點

## <a name="si-02-06-flaw-remediation--removal-of-previous-versions-of-software--firmware"></a>SI-02 (06) 缺陷補救 | 移除舊版的軟體/韌體

此藍圖會指派原則定義，以協助您確定應用程式會使用最新版的 HTTP、Java、PHP、Python 和 TLS。 此藍圖也會指派原則定義以確保 Kubernetes 服務會升級為其不易受攻擊的版本。

- 確定 'HTTP' 在用來執行 API 應用程式時，版本是最新的
- 確定 'HTTP' 在用來執行函式應用程式時，版本是最新的
- 確定 'HTTP' 在用來執行 Web 應用程式時，版本是最新的
- 確定 'Java' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 'Java' 在作為函式應用程式的一部分時，版本是最新的
- 確定 'Java' 在作為 Web 應用程式的一部分時，版本是最新的
- 確定 'PHP' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 'PHP' 在作為 Web 應用程式的一部分時，版本是最新的
- 確定 'Python' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 'Python' 在作為函式應用程式的一部分時，版本是最新的
- 確定 'Python' 在作為 Web 應用程式的一部分時，版本是最新的
- 您的 API 應用程式應使用最新的 TLS 版本
- 您的函式應用程式應使用最新的 TLS 版本
- 您的 Web 應用程式應使用最新的 TLS 版本
- Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本

## <a name="si-3-malicious-code-protection"></a>SI-3 惡意程式碼防護

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以在「Azure 資訊安全中心」監視虛擬機器缺少的 Endpoint Protection，並在 Windows 虛擬機器上強制執行 Microsoft 反惡意程式碼軟體解決方案，協助您管理 Endpoint Protection，包括惡意程式碼防護。

- 為 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組
- 應在虛擬機器擴展集上安裝端點保護解決方案
- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) 惡意程式碼防護 | 集中管理

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以在「Azure 資訊安全中心」監視虛擬機器缺少的 Endpoint Protection，協助您管理 Endpoint Protection，包括惡意程式碼防護。 「Azure 資訊安全中心」提供集中管理與報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。

- 應在虛擬機器擴展集上安裝端點保護解決方案
- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection

## <a name="si-4-information-system-monitoring"></a>SI-4 資訊系統監視

此藍圖可稽核並強制執行所有 Azure 資源的記錄和資料安全性，協助您監視系統。 具體來說，指派的原則會稽核並強制部署 Log Analytics 代理程式，以及 SQL 資料庫、儲存體帳戶與網路資源的增強式安全性設定。 這些功能可協助您偵測異常行為和攻擊的指標，以便採取適當動作。

- \[預覽\]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Log Analytics 代理程式部署 - 未列出的 VM 映像 (OS)
- \[預覽\]：稽核適用於 VM 的 Log Analytics 工作區 - 報告不相符
- 為 Linux 虛擬機器擴展集部署 Log Analytics 代理程式
- \[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式
- 為 Windows 虛擬機器擴展集部署 Log Analytics 代理程式
- \[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式
- 應在受控執行個體上啟用進階資料安全性
- 應在 SQL 伺服器上啟用進階資料安全性
- 在 SQL 伺服器上部署進階資料安全性
- 在儲存體帳戶部署進階威脅防護
- 在 SQL 伺服器上部署稽核
- 於虛擬網路建立時部署網路監看員
- 在 SQL 伺服器上部署威脅偵測
- 允許的位置
- 允許資源群組的位置

## <a name="si-4-12-information-system-monitoring--automated-alerts"></a>SI-4 (12) 資訊系統監視 | 自動警示

此藍圖會提供原則定義，以協助您確定已正確啟用資料安全性通知。 此外，此藍圖可確保已啟用 Azure 資訊安全中心的標準定價層。 請注意，標準定價層會為網路及虛擬機器啟用威脅偵測，在 Azure 資訊安全中心提供威脅情報、異常偵測及行為分析。

- 應已針對高嚴重性警示啟用傳送給訂用帳戶擁有者的電子郵件通知
- 應為您的訂用帳戶提供安全性連絡人電子郵件地址 
- 應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知 
- 應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知 
- 應為您的訂用帳戶提供安全性連絡人電話號碼
- SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- 應選取資訊安全中心標準定價層

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) 資訊系統監視 | 分析流量 / 掩飾外洩

Azure 儲存體的進階威脅防護可偵測儲存體帳戶中異常而且可能有害的存取或攻擊意圖。 防護警示包含異常存取模式、異常擷取/上傳以及可疑的儲存體活動。 這些指標可以協助您偵測掩飾資訊外洩的情況。

- 在儲存體帳戶部署進階威脅防護

> [!NOTE]
> 特定 Azure 原則定義的可用性可能因 Azure Government 和其他國家雲端而異。 

## <a name="next-steps"></a>後續步驟

您已檢閱過 DoD 影響等級 4 藍圖的控制項對應，接下來請瀏覽下列文章以了解藍圖及如何部署此範例：

> [!div class="nextstepaction"]
> [DoD 影響等級 4 藍圖 - 概觀](./index.md)
> [DoD 影響等級 4 藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。