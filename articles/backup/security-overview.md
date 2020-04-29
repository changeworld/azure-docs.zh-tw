---
title: 安全性功能總覽
description: 瞭解 Azure 備份中的安全性功能，協助您保護您的備份資料，並符合您企業的安全性需求。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585825"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure 備份中的安全性功能總覽

保護您的資料最重要的步驟之一，就是擁有可靠的備份基礎結構。 但請務必確保您的資料會以安全的方式備份，而且您的備份會隨時受到保護。 Azure 備份為您的備份環境提供安全性，這兩者都是在您的資料傳輸中和靜止時。 本文列出 Azure 備份中的安全性功能，可協助您保護您的備份資料，並符合企業的安全性需求。

## <a name="management-and-control-of-identity-and-user-access"></a>身分識別和使用者存取的管理與控制

Azure 備份可讓您使用[Azure 角色型存取控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)來管理更細緻的存取權。 RBAC 可讓您區隔小組內的職責，僅授與使用者執行其工作所需的存取權數量。

Azure 備份提供三個內建角色來控制備份管理作業：

* 備份參與者-建立和管理備份，但刪除復原服務保存庫並將存取權授與其他人
* Backup Operator-參與者除了移除備份和管理備份原則以外的所有專案
* 備份讀取者-查看所有備份管理作業的許可權

深入瞭解[以角色為基礎的存取控制來管理 Azure 備份](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

Azure 備份有數個內建于服務中的安全性控制，以防止、偵測及回應安全性弱點。 深入瞭解[Azure 備份的安全性控制](https://docs.microsoft.com/azure/backup/backup-security-controls)。

## <a name="separation-between-guest-and-azure-storage"></a>來賓與 Azure 儲存體之間的分隔

在 VM 備份中包含虛擬機器備份和 SQL 和 SAP Hana 的 Azure 備份，備份資料會儲存在 Azure 儲存體中，而且來賓無法直接存取備份儲存體或其內容。  在虛擬機器備份中，建立和儲存備份快照集是由 Azure 網狀架構所完成，其中的來賓除了停止應用程式一致備份的工作負載之外，也不會有任何其他參與。  使用 SQL 和 SAP Hana 時，備份延伸模組會暫時存取以寫入特定的 blob。  如此一來，即使在遭入侵的環境中，來賓也無法修改或刪除現有的備份。

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM 備份不需要網際網路連線

Azure Vm 的備份需要將資料從虛擬機器的磁片移至復原服務保存庫。 不過，所有必要的通訊和資料傳輸只會發生在 Azure 骨幹網路上，而不需要存取您的虛擬網路。 因此，放在受保護網路中的 Azure Vm 備份不會要求您允許存取任何 Ip 或 Fqdn。

## <a name="private-endpoints-for-azure-backup"></a>Azure 備份的私人端點

您現在可以使用[私人端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)，從虛擬網路內部的伺服器將資料安全地備份到復原服務保存庫。 私人端點會針對您的保存庫使用 VNET 位址空間中的 IP，因此您不需要向任何公用 Ip 公開虛擬網路。 私人端點可用於備份和還原您的 SQL 和 SAP Hana 在 Azure Vm 中執行的資料庫。 它也可以用於使用 MARS 代理程式的內部部署伺服器。

>[!NOTE]
> 這項功能目前的可用性有限。 如果您有興趣使用 Azure 備份的私用端點，請填寫[這份問卷](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)並寄送[電子郵件給我們](mailto:azbackupnetsec@microsoft.com)。 使用這項功能的功能，可能會受到 Azure 備份服務的核准。

## <a name="encryption-of-data-in-transit-and-at-rest"></a>傳輸中和待用資料的加密

加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 在 Azure 中，Azure 儲存體與保存庫之間傳輸中的資料會受到 HTTPS 保護。 此資料會保持在 Azure 中樞網路上。

* 備份資料會使用 Microsoft 管理的金鑰自動加密。 您也可以使用儲存在 Azure Key Vault 中的[客戶管理金鑰](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys)，加密復原服務保存庫中已備份的受控磁片 vm。 您不需要採取任何明確的動作，即可啟用此加密。 其適用于備份到復原服務保存庫的所有工作負載。

* Azure 備份支援備份和還原已使用 Azure 磁碟加密（ADE）加密其 OS/資料磁片的 Azure Vm。 [深入瞭解加密的 Azure vm 和 Azure 備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>保護不小心刪除的備份資料

Azure 備份提供安全性功能，可在刪除之後協助保護備份資料。 使用虛刪除時，如果使用者刪除 VM 的備份，備份資料會保留14個額外的天數，以便在不遺失資料的情況下復原該備份專案。 在「虛刪除」狀態中，備份資料的額外14天保留期不會對客戶產生任何費用。 [深入瞭解虛刪除](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)。

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>可疑活動的監視與警示

Azure 備份提供[內建的監視和警示功能](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)，可針對與 Azure 備份相關的事件來查看及設定動作。 [備份報表](https://docs.microsoft.com/azure/backup/configure-reports)可作為追蹤使用狀況的一次性目的地、備份和還原的審核，以及識別不同資料細微性層級的重要趨勢。 使用 Azure 備份的監視和報告工具，可在發生任何未經授權、可疑或惡意的活動時，向您發出警示。

## <a name="security-features-to-help-protect-hybrid-backups"></a>協助保護混合式備份的安全性功能

Azure 備份服務會使用 Microsoft Azure 復原服務（MARS）代理程式來備份和還原從內部部署電腦到 Azure 的檔案、資料夾和磁片區或系統狀態。 MARS 現在提供安全性功能，以協助保護混合式備份。 這些功能包括：

* 每當執行重要作業 (如變更複雜密碼) 時，就會多一道驗證。 這項驗證用來確保只有具備有效 Azure 認證的使用者，才能執行這類作業。 [深入瞭解防止攻擊的功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)。

* 刪除的備份資料會保留一天，從刪除日期起算。 這可確保在指定的時間週期內資料的復原能力，因此即使發生攻擊，也不會遺失資料。 而且會維護更多的復原點，以防範資料損毀。 [深入瞭解如何復原已刪除的備份資料](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data)。

* 針對使用 Microsoft Azure 復原服務（MARS）代理程式進行備份的資料，在上傳至 Azure 備份之前，會使用複雜密碼來確保資料已加密，而且只有在從 Azure 備份下載之後才會解密。 複雜密碼詳細資料僅適用于建立複雜密碼的使用者，以及使用它設定的代理程式。 服務不會傳輸或共用任何內容。 這可確保資料的完整安全性，如同不慎公開的任何資料（例如網路上的攔截式攻擊）在沒有複雜密碼的情況下無法使用，而且不會在網路上傳送複雜密碼。

## <a name="compliance-with-standardized-security-requirements"></a>符合標準化的安全性需求

為了協助組織符合控管個人資料收集和使用的國家、地區和業界特定需求，Microsoft Azure & Azure 備份提供一組完整的認證和證明。 [查看合規性認證的清單](compliance-offerings.md)

## <a name="next-steps"></a>後續步驟

* [協助保護使用 Azure 備份之雲端工作負載的安全性功能](backup-azure-security-feature-cloud.md)
* [利用安全性功能協助保護使用 Azure 備份的混合式備份](backup-azure-security-feature.md)
