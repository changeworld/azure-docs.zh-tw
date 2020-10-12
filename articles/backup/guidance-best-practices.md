---
title: 指導方針和最佳做法
description: 探索將雲端和內部部署工作負載備份至雲端的最佳做法和指導方針
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: f999c568dda6eae60f3060cc4672eccaf06541c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985527"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>將雲端和內部部署工作負載備份到雲端

## <a name="introduction"></a>簡介

Azure 備份透過簡單、安全且符合成本效益的解決方案（需要零基礎結構）全面保護您在 Azure 中的資料資產。 它是針對各種工作負載 Azure's 內建的資料保護解決方案。 它可協助保護您在雲端中執行的任務關鍵性工作負載，並確保您的備份隨時都可供使用，並可大規模地在整個備份資產中進行管理。

### <a name="intended-audience"></a>目標對象

本文的主要目標物件是 IT 和應用程式系統管理員，以及大型和中型組織的實作者，他們想要瞭解 Azure 內建資料保護技術的功能，Azure 備份以及如何有效率地執行更妥善保護您的部署的解決方案。 本文假設您已熟悉核心 Azure 技術、資料保護概念，以及使用備份解決方案的經驗。 本文所涵蓋的指引可讓您更輕鬆地在 Azure 上使用已建立的模式來設計您的備份解決方案，並避免已知的陷阱。

### <a name="how-this-article-is-organized"></a>本文的組織方式

雖然您可以在 Azure 上輕鬆地開始保護基礎結構和應用程式，但當您確保基礎 Azure 資源已正確設定且已以最佳方式使用時，您就可以加速實現價值。 本文將簡短介紹設計考慮和指導方針，以優化 Azure 備份部署的設定。 它會檢查核心元件 (例如，復原服務保存庫、備份原則) 和概念 (例如，治理) 以及如何利用詳細的產品檔連結來思考它們及其功能。

## <a name="architecture"></a>架構

![Azure 備份架構](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>工作負載

Azure 備份可在內部部署和雲端)  (各種工作負載的資料保護。 它是 Azure 中安全可靠的內建資料保護機制。 它可以順暢地調整跨多個工作負載的保護，而不會有任何額外的管理負擔。 您也可以透過 PowerShell、CLI、Azure Resource Manager 範本和 REST Api 來啟用此 (的多個自動化通道。 ) 

* **可擴充、耐用且安全的儲存體-** Azure 備份使用具有內建安全性和高可用性功能的可靠 Blob 儲存體。 您可以為您的備份資料選擇 LRS、GRS 或 RA GRS 的儲存體。  

* **原生工作負載整合-** Azure 備份提供與 Azure 工作負載的原生整合， (Vm、SAP Hana、Azure Vm 中的 SQL 甚至 Azure 檔案儲存體) ，而不需要您管理自動化或基礎結構來部署代理程式、撰寫新的腳本或布建儲存體。

### <a name="data-plane"></a>資料平面

* **自動化存放裝置管理** – Azure 備份自動布建和管理備份資料的儲存體帳戶，以確保隨著備份資料的成長調整規模。

* **惡意刪除保護–** 透過備份的虛刪除，防止任何意外和惡意的備份刪除備份。 已刪除的備份資料會免費儲存14天，並允許從這個狀態復原。

* **安全加密的備份-** Azure 備份可確保您的備份資料以安全的方式儲存，並利用 Azure 平臺的內建安全性功能，例如 RBAC 和加密。

* **備份資料生命週期管理-** Azure 備份會自動清除較舊的備份資料，以符合保留原則。 您也可以將資料從操作儲存體分層到保存庫儲存體。

### <a name="management-plane"></a>管理平面

* **存取控制** -保存庫 (復原服務和備份保存庫) 提供管理功能，並可透過 Azure 入口網站、備份中心、保存庫儀表板、SDK、CLI 和 REST api 來存取。 它也是 RBAC 界限，可讓您選擇只將備份的存取許可權制為已授權的備份管理員。

* **原則管理** –每個保存庫內的 Azure 備份原則會定義何時應該觸發備份，以及需要保留多久的時間。 您也可以管理這些原則，並將其套用到多個專案。

* **監視和報告** – Azure 備份與 Log Analytics 整合，也提供透過活頁簿查看報表的功能。

* **快照集管理** – Azure 備份取得某些 Azure 原生工作負載 (vm 和 Azure 檔案儲存體) 的快照集，並可管理這些快照集，並允許從這些快照集進行快速還原。 此選項可大幅減少將資料復原到原始儲存體的時間。

## <a name="vault-considerations"></a>保存庫考慮

Azure 備份使用保存庫 (復原服務和備份保存庫) 來協調及管理備份。 其也會使用保存庫儲存已備份的資料。 有效的保存庫設計可協助組織建立結構來組織及管理 Azure 中的備份資產，以支援您的業務優先順序。 建立保存庫時，請考慮下列指導方針：  

### <a name="align-to-subscription-design-strategy"></a>配合訂用帳戶設計策略

由於保存庫的範圍是訂用帳戶，因此請符合您的保存庫設計，以符合訂用帳戶設計策略，例如根據特定應用程式或服務，或在應用程式原型程式碼中分隔訂閱的 *應用程式類別策略* 。 如需詳細資訊，請參閱這篇[文章](/azure/cloud-adoption-framework/decision-guides/subscriptions/)。

### <a name="single-or-multiple-vault"></a>單一或多個保存庫

您可以使用單一保存庫或多個保存庫來組織及管理您的備份。 請參考下列指引：

* 如果您的工作負載全都由單一訂用帳戶和單一資源管理，則您可以使用單一保存庫來監視和管理您的備份資產。

* 如果您的工作負載分散到多個訂用帳戶，您可以建立多個保存庫，每個訂用帳戶一或多個。
  * 備份中心可讓您擁有單一窗格，以管理與備份相關的所有工作。 若要[深入瞭解]()，請參閱。
  * 您可以使用活頁簿範本自訂您的觀點。 Backup Explorer 是 Azure Vm 的其中一個範本。 若要[深入瞭解](monitor-azure-backup-with-backup-explorer.md)，請參閱。
  * 如果您需要跨保存庫的一致原則，您可以使用 Azure 原則將備份原則傳播到多個保存庫。 您可以撰寫自訂的 [Azure 原則定義](../governance/policy/concepts/definition-structure.md) ，以使用 [' deployifnotexists '](../governance/policy/concepts/effects.md#deployifnotexists) 效果將備份原則傳播到多個保存庫。 您可以指派 [此 Azure 原則](../governance/policy/assign-policy-portal.md) 定義至 (訂用帳戶或 RG) 的特定範圍，以便將「備份原則」資源部署到 Azure 原則指派範圍內的所有復原服務保存庫。 備份原則的設定 (例如備份頻率、保留等等) 應該由使用者指定為 Azure 原則指派中的參數。

* 當您的組織使用量增加時，您可能會想要在訂用帳戶之間移動工作負載，原因如下：依備份原則、合併保存庫、在較低的冗余上取捨，以節省成本 (從 GRS 移至 LRS) 。  Azure 備份支援跨 Azure 訂用帳戶或相同訂用帳戶內的另一個資源群組移動復原服務保存庫。 若要[深入瞭解](backup-azure-move-recovery-services-vault.md)，請參閱。

### <a name="review-default-settings"></a>檢閱預設設定

在保存庫中設定備份之前，請先檢查儲存體複寫類型和安全性設定的預設設定，以符合您的需求。

* 依預設，*儲存體複寫類型*會設定為異地冗余 (GRS) 。 設定備份之後，[修改] 選項就會停用。 請 [遵循下列步驟](backup-create-rs-vault.md#set-storage-redundancy) 來檢查和修改設定。

* 預設會在新建立的保存庫上啟用虛*刪除*，以防止備份資料遭到意外或惡意刪除。 請 [遵循下列步驟](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 來檢查和修改設定。

* *跨區域還原* 可讓您在次要區域（即 azure 配對的區域）中還原 azure vm。 此選項可讓您進行演練，以符合審核或合規性需求，並在主要區域發生嚴重損壞時還原 VM 或其磁片。 CRR 是適用于任何 GRS 保存庫的加入宣告功能。 若要[深入瞭解](backup-create-rs-vault.md#set-cross-region-restore)，請參閱。

* 在完成保存庫設計之前，請先參閱保存 [庫支援矩陣](backup-support-matrix.md#vault-support) ，以瞭解可能影響或限制您的設計選擇的因素。

## <a name="backup-policy-considerations"></a>備份原則考慮

Azure 備份原則有兩個元件： *排程* (何時進行備份) 和 *保留* (保留備份) 的時間長度。 您可以根據所備份的資料類型、RTO/RPO 需求、作業或法規合規性需求和工作負載 (類型（例如 VM、資料庫、檔案) ）來定義原則。 若要[深入瞭解](backup-architecture.md#backup-policy-essentials)，請參閱。

建立備份原則時，請考慮下列指導方針：

### <a name="schedule-considerations"></a>排程考慮

* 請考慮將需要相同排程開始時間、頻率及保留設定的 Vm 群組在單一原則內。

* 確定備份排定的開始時間是在非尖峰的生產應用程式時間期間。

* 若要散發備份流量，請考慮在一天中的不同時間備份不同的 Vm，並確定時間不會重迭。

### <a name="retention-considerations"></a>保留考慮

* 短期保留期可以是「分鐘」或「每日」。 「每週」、「每月」或「每年」備份點的保留期稱為長期保留。

* 長期保留：
  * 規劃 (合規性需求) -如果您事先知道資料是從目前時間起的需要年，請使用長期保留。
  * 未規劃的 (隨選需求) -如果您事先不知道，您可以視需要使用特定的自訂保留設定 (這些自訂保留設定不受原則設定) 的影響。

* 使用自訂保留的隨選備份-如果您需要建立未透過備份原則排程的備份，您可以使用隨選備份。 這有助於讓備份不符合您的排程備份或進行細微的備份 (例如，每天執行多個 IaaS VM 備份，因為排程的備份每日只允許一個備份) 。 請務必注意，在 [排定的原則] 中定義的保留原則不會套用至隨選備份。

### <a name="optimize-backup-policy"></a>優化備份原則

* 當您的商務需求變更時，您可能需要延長或縮短保留期限。 當您這樣做時，您可以預期會發生下列情況：  
  * 如果延長保留期，就會標示現有的復原點，並依據新原則加以保留。
  * 如果縮短保留期，復原點會標示為在下次清除作業中剪除，然後再刪除。
  * 所有保留點都適用最新的保留規則， (不包括) 的隨選保留點。 因此，如果保留期限是延伸 (例如100天) ，則在進行備份時，會在保留縮減 (例如100天到七天) ，則會根據最後指定的保留期限來保留所有備份資料 (也就是7天) 。

* Azure 備份可讓您彈性地 *停止保護和管理您的備份*：
  * *停止保護並保留備份資料*。 如果您要淘汰或解除委任您的資料來源 (VM、應用程式) ，但需要保留資料以供進行審核或合規性，則您可以使用此選項來停止所有未來的備份作業以保護您的資料來源，並保留已備份的復原點。 然後您可以還原或繼續 VM 保護。
  * *停止保護並刪除備份資料*。 此選項將會停止所有未來的備份作業以保護您的 VM，並刪除所有復原點。 您將無法還原 VM，也無法使用 [繼續備份] 選項。

  * 如果您繼續保護 (的資料來源已停止，且保留資料) ，則會套用保留規則。 任何過期的復原點會在排程的時間) 移除 (。

* 完成原則設計之前，請務必留意可能影響您設計選擇的下列因素。
  * 備份原則的範圍是保存庫。
  * 每個原則的專案數目有限制 (例如，100 Vm) 。 若要調整規模，您可以使用相同或不同的排程來建立重複的原則。
  * 您無法選擇性地刪除特定的復原點。
  * 您無法完全停用已排程的備份，並讓資料來源處於受保護的狀態。 您可以使用原則來設定的最不頻繁備份，就是每週排程備份一次。 替代方法是使用 [保留資料] 停止保護，並在每次您想要進行備份時啟用保護、進行隨選備份，然後關閉保護，但保留備份資料。 若要[深入瞭解](backup-azure-manage-vms.md#stop-protecting-a-vm)，請參閱。

## <a name="security-considerations"></a>安全性考量

為了協助保護您的備份資料並符合企業的安全性需求，Azure 備份提供機密性、完整性和可用性保證，以防止刻意攻擊和您的寶貴資料和系統濫用。 針對您的 Azure 備份解決方案，請考慮下列安全性指導方針：

### <a name="authentication-and-authorization"></a>驗證與授權

* Azure 角色型存取控制 (Azure RBAC) 可讓您更細緻的存取管理、小組內的職責隔離，以及僅授與執行其工作所需的使用者存取權。 若要[深入瞭解](backup-rbac-rs-vault.md)，請參閱。

* Azure 備份提供三個內建角色來控制備份管理作業：備份參與者、操作員和讀者。 若要[深入瞭解](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)，請參閱。

* Azure 備份在服務內建數個安全性控制項，以防止、偵測及回應安全性弱點 (深入瞭解) 

* 復原服務保存庫所使用的儲存體帳戶是隔離的，使用者無法存取這些帳戶以進行任何惡意用途。 僅允許透過 Azure 備份管理作業 (例如，還原) 進行存取。

### <a name="encryption-of-data-in-transit-and-at-rest"></a>加密傳輸中資料和待用資料

加密可保護您的資料安全，並協助您符合組織安全性和合規性承諾。

* 在 Azure 中，會透過 HTTPS 保護 Azure 儲存體與保存庫之間的傳輸中資料。 此資料會保留在 Azure 網路中。

* 備份資料會使用 Microsoft 受控金鑰自動加密。 或者，您也可以使用自己的金鑰，也稱為 [客戶管理的金鑰](encryption-at-rest-with-cmk.md)。

* Azure 備份支援備份和還原已使用 Azure 磁碟加密 (ADE) 加密其作業系統/資料磁碟的 Azure VM。 若要[深入瞭解](backup-azure-vms-encryption.md)，請參閱。

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>保護不小心刪除的備份資料

Azure 備份提供安全性功能，即使在刪除之後，也能協助保護備份資料。 使用虛刪除時，如果使用者刪除 VM 的備份 (、SQL Server 資料庫、Azure 檔案共用、SAP Hana 資料庫) 備份資料會保留14天，以允許復原該備份專案，而不會遺失資料。 「虛刪除」狀態中的額外14天備份資料保留期不會產生任何費用。 若要[深入瞭解](backup-azure-security-feature-cloud.md)，請參閱。

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>可疑活動的監視與警示

Azure 備份提供內建的監視和警示功能，以檢視和設定與 Azure 備份相關的事件動作。 若要[深入瞭解](security-overview.md#monitoring-and-alerts-of-suspicious-activity)，請參閱。

### <a name="security-features-to-help-protect-hybrid-backups"></a>安全性功能有助於保護混合式備份

Azure 備份服務使用 Microsoft Azure 復原服務 (MARS) 代理程式，將檔案、資料夾，以及磁碟區或系統狀態，從內部部署電腦備份與還原至 Azure。 MARS 現在提供了安全性功能：從 Azure 備份下載之後，在上傳和解密之前加密的複雜密碼，已刪除的備份資料會保留額外的14天（從刪除日期算起）和重要作業 (例如 變更複雜密碼) 只能由具有有效 Azure 認證的使用者執行。 若要[深入瞭解](backup-azure-security-feature.md)，請參閱。

## <a name="network-considerations"></a>網路考量

Azure 備份需要將資料從您的工作負載移到復原服務保存庫。 Azure 備份提供數個功能，可保護備份資料免于不慎遭到意外的 (，例如網路) 攔截式攻擊。 請參考下列指引：

### <a name="internet-connectivity"></a>網際網路連線

* *AZURE VM 備份* -儲存體和 Azure 備份服務之間的所有必要通訊和資料傳輸都是在 azure 網路內進行，而不需要存取您的虛擬網路。 因此，放在安全網路內的 Azure Vm 備份不需要允許存取任何 Ip 或 Fqdn。

* *AZURE vm 上的 SAP Hana 資料庫、AZURE vm 上的 SQL Server 資料庫* -需要連線到 Azure 備份服務、Azure 儲存體和 Azure Active Directory。 這可以藉由使用私人端點，或允許存取所需的公用 IP 位址或 FQDN 來達成。 若不允許正確連線到所需的 Azure 服務，可能會導致資料庫探索、設定備份、執行備份和還原資料等作業失敗。 如需使用 NSG 標籤、Azure 防火牆和 HTTP Proxy 時的完整網路指導方針，請參閱這些 [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) 和 [SAP Hana](./backup-azure-sap-hana-database.md#establish-network-connectivity) 文章。

* *混合* 式-MARS (Microsoft Azure 復原服務) 代理程式需要針對所有重要作業（安裝、設定、備份及還原）進行網路存取。 MARS 代理程式可以透過 [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) 連接到 Azure 備份服務，方法是使用公用對等互連 (適用于舊線路) 和 Microsoft 對等互連，使用 [私人端點](install-mars-agent.md#private-endpoints) 或透過 [適當的存取控制的 proxy/防火牆](install-mars-agent.md#verify-internet-access)。

### <a name="private-endpoints-for-azure-backup"></a>Azure 備份的私人端點

Azure [私人端點](../private-link/private-endpoint-overview.md) 是一種網路介面，可讓您以私人且安全的方式連接到 Azure Private Link 的服務。 Azure 備份可讓您使用私人端點，從復原服務保存庫安全地備份和還原資料。

* 當您啟用保存庫的私人端點時，它們只會用於備份和還原 Azure VM 中的 SQL 和 SAP Hana 工作負載，以及 MARS 代理程式備份。  您也可以使用保存庫進行其他工作負載的備份， (不需要) 的私人端點。 除了使用 MARS 代理程式備份 SQL 和 SAP Hana 工作負載和備份之外，也會在 Azure VM 備份的情況下，使用私用端點來執行檔案復原。 若要[深入瞭解](private-endpoints.md#recommended-and-supported-scenarios)，請參閱。

* Azure Active Directory 目前不支援私人端點。 因此，當您在 Azure Vm 中執行資料庫的備份，並使用 MARS 代理程式進行備份時，必須允許來自安全網路的輸出存取，才能使用 Azure Active Directory 所需的 Ip 和 Fqdn。 您也可以使用 NSG 標籤和 Azure 防火牆標籤來允許存取 Azure AD （適用）。 若要深入瞭解 [必要條件](./private-endpoints.md#before-you-start)，請參閱。

## <a name="governance-considerations"></a>治理考量

Azure 中的治理主要是以 [Azure 原則](../governance/policy/overview.md) 和 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)來執行。 [Azure 原則](../governance/policy/overview.md)可讓您建立、指派和管理原則定義，以強制執行資源的規則。 此功能可確保這些資源能夠符合公司的標準規範。 針對您的 Azure 資源和其他雲端服務提供者，[Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)可讓您追蹤雲端使用量和費用。 此外，下列工具（例如 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/) 和 [Azure Advisor](../advisor/advisor-overview.md)  ）在成本管理程式中扮演重要角色。

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure 備份透過內建 Azure 原則支援兩個主要案例

* 確定新建立的商務關鍵性機器會自動以正確的保留設定進行備份。 Azure 備份提供內建原則 (使用 Azure 原則) ，可指派給訂用帳戶或資源群組內指定位置中的所有 Azure Vm。 將此原則指派給指定的範圍時，在該範圍中建立的所有新 Vm 都會自動設定為備份至相同位置和訂用帳戶中現有的保存庫。 使用者可以指定要與備份的 Vm 相關聯的保存庫和保留原則。 若要[深入瞭解](backup-azure-auto-enable-backup.md)，請參閱。

* 確定新建立的保存庫已啟用診斷，以支援報告。 通常，每個保存庫都會手動新增診斷設定，是一項繁瑣的工作。 此外，任何新建立的保存庫都必須啟用診斷設定，以便您可以查看此保存庫的報表。 為了簡化使用 Log Analytics 作為目的地)  (大規模診斷設定的建立，Azure 備份提供內建的 Azure 原則。 此原則會將 LA 診斷設定新增到每個訂用帳戶或資源群組中的所有保存庫。 下列各節提供如何使用此原則的指示。 若要[深入瞭解](azure-policy-configure-diagnostics.md)，請參閱。

### <a name="azure-backup-cost-considerations"></a>Azure 備份成本考慮

Azure 備份服務的功能可讓您彈性地管理成本，並仍能滿足您的 BCDR (商務持續性和嚴重損壞修復) 商務需求。 請參考下列指引：

* 使用定價計算機，藉由調整各種杠杆來評估和優化成本。 [請於此處深入了解](azure-backup-pricing.md)

* Backup Explorer：使用 Backup Explorer 或備份報表來瞭解並優化備份儲存體成長、停止非關鍵性工作負載或已刪除 Vm 的備份。 您可以從備份的觀點來取得整個資產的匯總視圖。 這不僅包括備份專案的資訊，還包含未設定進行備份的資源。 這可確保您永遠不會錯過在不斷成長的資產中保護重要資料，而且您的備份已針對非關鍵性工作負載或已刪除的工作負載進行優化。

* 優化備份原則
  * 根據工作負載原型優化排程和保留設定 (例如，任務關鍵性、非關鍵性) 
  * 優化立即還原的保留設定
  * 選擇適當的備份類型以符合需求，同時取得支援的備份類型 (完整、累加、記錄、差異) Azure 備份考慮的工作負載。

* 選擇性備份磁片：排除磁片 (預覽功能) 提供有效率且符合成本效益的選項，可選擇性地備份重要資料。 例如，當您不想要備份連接至 VM 的其餘磁片時，請只備份一個磁片。 當您有多個備份解決方案時，這也很有用。 例如，當您使用工作負載備份解決方案來備份資料庫或資料時 (SQL Server Azure VM) 備份中的資料庫，而且您想要針對選取的磁片使用 Azure VM 層級備份。

* Azure 備份會取得 Azure Vm 的快照集，並將其儲存在磁片上，以加速建立復原點，並加快還原作業的速度。 這稱為「立即還原」。 根據預設，立即還原快照集會保留兩天。 這項功能可減少還原時間，讓這些快照集的還原作業。 它可減少從保存庫轉換和複製資料所需的時間。 因此，您將看見儲存體成本會隨著在這段期間內取得的快照集而增減。 若要[深入瞭解](backup-instant-restore-capability.md#configure-snapshot-retention)，請參閱。

* 依預設，Azure 備份保存庫的儲存體複寫類型會設定為異地冗余 (GRS) 。 保護專案之後，就無法變更此選項。 異地冗余儲存體 (GRS) 提供比本地冗余儲存體 (LRS) 更高的資料持久性層級，可讓您選擇使用跨區域還原和成本。 檢查較低成本和更高資料耐久性之間的取捨，並決定最適合您的案例。 [請於此處深入了解](backup-create-rs-vault.md#set-storage-redundancy)

* 如果您要保護在 VM 和 VM 本身內執行的工作負載，請檢查是否需要此雙重保護。

## <a name="monitoring-and-alerting-considerations"></a>監視和警示考慮

做為備份使用者或系統管理員，您應該能夠監視所有備份解決方案，並在重要案例中收到通知。 本節將詳細說明 Azure 備份服務所提供的監視和通知功能。

### <a name="monitoring"></a>監視

* Azure 備份針對作業（例如設定備份、備份、還原、刪除備份等等）提供 **內建的作業監視功能** 。 這是以保存庫為範圍，適用于監視單一保存庫。 若要[深入瞭解](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)，請參閱。

* 如果您需要大規模監視操作活動， **Backup Explorer** 會提供整個備份資產的匯總視圖，以提供詳細的向下切入分析和疑難排解。 它是內建的 Azure 監視器活頁簿，可提供單一的集中位置，協助您在 Azure、跨租使用者、位置、訂用帳戶、資源群組和保存庫的整個備份資產上監視作業活動。 若要[深入瞭解](monitor-azure-backup-with-backup-explorer.md)，請參閱。
  * 您可以使用它來識別未針對備份設定的資源，並確保您不會錯過在不斷成長的資產中保護重要資料。
  * 儀表板會提供過去七天的操作活動， (最大) 。 如果您需要保留此資料，您可以匯出為 Excel 檔案並加以保留。
  * 如果您是 Azure Lighthouse 使用者，您可以跨多個租使用者查看資訊，以啟用無界限監視。

* 如果您需要保留和查看長期的營運活動，請使用 **報表**。 備份系統管理員的常見需求是根據長時間內的資料，取得備份的見解。 這類解決方案的使用案例包括：
  * 配置和預測已取用的雲端儲存體。
  * 稽核備份和還原。
  * 找出不同細微性層級的主要趨勢。

* 此外，
  * 您可以) 至 **Log Analytics** 工作區，將資料 (例如，工作、原則等等。 這會啟用 Azure 監視器記錄的功能，以便將資料與 Azure 監視器所收集的其他監視資料相互關聯、將多個 Azure 訂用帳戶和租使用者的記錄專案合併到一個位置以進行分析，並使用記錄查詢來執行複雜的分析，並取得記錄專案的深入見解。 若要[深入瞭解](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)，請參閱。
  * 您可以將資料傳送至事件中樞，以將專案傳送到 Azure 外部，例如，協力廠商 SIEM (安全性資訊和事件管理) 或其他 log analytics 解決方案。 若要[深入瞭解](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs)，請參閱。
  * 如果您想要保留記錄資料超過90天進行審核、靜態分析或備份，您可以將資料傳送至 Azure 儲存體帳戶。 如果您只需要保留您的事件90天或更少的時間，您就不需要將封存設定至儲存體帳戶，因為 Azure 平臺中的活動記錄事件會保留90天。 [深入了解](../azure-monitor/platform/activity-log.md#send-to--azure-storage)。

### <a name="alerting"></a>警示

* 警示主要是取得取得相關動作的通知方法。 [備份警示] 區段會顯示 Azure 備份服務產生的警示。

* Azure 備份透過電子郵件，為失敗、警告和重要作業提供 **內建的警示** 通知機制。 您可以指定要在產生警示時收到通知的個別電子郵件地址或通訊群組清單。 您也可以選擇是否要收到每個個別警示的通知，或以每小時摘要將其分組，然後獲得通知。
  * 這些警示是由服務所定義，可提供有限案例的支援-備份/還原失敗、停止保護，並保留資料/停止保護與刪除資料等等。 若要[深入瞭解](backup-azure-monitoring-built-in-monitor.md#alert-scenarios)，請參閱。
  * 如果執行了破壞性作業（例如停止保護與刪除資料），則會引發警示，並將電子郵件傳送給訂用帳戶擁有者、系統管理員和共同管理員，即使 **沒有** 針對復原服務保存庫設定通知也是一樣。
  * 某些工作負載可能會產生高頻率的失敗 (例如，每隔15分鐘 SQL Server) 。 為了避免在每次失敗時引發警示，會合並警示。 若要[深入瞭解](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts)，請參閱。
  * 內建警示無法自訂，而且僅限於 Azure 入口網站中定義的電子郵件。

* 如果您需要 **建立自訂警示** (例如，成功的作業警示) 然後使用 Log Analytics。 在 Azure 監視器中，您可以在 Log Analytics 工作區建立自己的警示。 混合式工作負載 (DPM/MABS) 也可以將資料傳送至 LA，並使用 LA 在 Azure 備份支援的工作負載之間提供常見的警示。

* 您也可以透過內建的復原服務保存庫 **活動記錄**來取得通知。 不過，它支援有限的案例，並不適用于排程備份之類的作業，這會比使用活動記錄更適合資源記錄檔。 若要深入瞭解這些限制，以及您可以如何使用 Log Analytics 工作區來大規模監視和警示，以針對受 Azure 備份保護的所有工作負載，請參閱這 [篇文章](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)。

## <a name="next-steps"></a>後續步驟

建議您閱讀下列文章，以瞭解使用 Azure 備份的起點：

* [Azure 備份總覽](backup-overview.md)
* [常見問題集](backup-azure-backup-faq.md)
