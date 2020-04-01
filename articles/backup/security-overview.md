---
title: 安全功能概述
description: 瞭解 Azure 備份中的安全功能,這些功能可説明您保護備份數據並滿足企業的安全需求。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 91a0f29862b0c9c35e562c143e28ebbc6c39cf94
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423180"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure 備份中安全功能概述

保護數據可以採取的最重要步驟之一是擁有可靠的備份基礎架構。 但是,確保以安全的方式備份數據,並確保備份始終受到保護,這一點同樣重要。 Azure 備份為備份環境提供了安全性 - 數據在傳輸過程中和靜止時。 本文列出了 Azure 備份中的安全功能,這些功能可説明您保護備份數據並滿足企業的安全需求。

## <a name="management-and-control-of-identity-and-user-access"></a>身份與使用者存取的管理及控制

Azure 備份使您能夠使用[Azure 基於角色的訪問控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)管理細粒度訪問。 RBAC 允許您在團隊中隔離職責,並僅授予完成工作所需的用戶訪問許可權。

Azure 備份提供了三個內建角色來控制備份管理操作:

* 備份參與者 - 建立和管理備份,但刪除回復服務保管庫和授予其他人存取權限
* 備份操作者 ─ 參與者除刪除備份與管理備份原則的一切
* 備份讀取器 - 查看所有備份管理的權限

詳細瞭解[基於角色的存取控制項以管理 Azure 備份](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

Azure 備份在服務中內置了多個安全控件,以防止、檢測和回應安全漏洞。 詳細瞭解[Azure 備份 的安全控制項](https://docs.microsoft.com/azure/backup/backup-security-controls)。

## <a name="separation-between-guest-and-azure-storage"></a>來賓儲存和 Azure 儲存之間的分離

使用 Azure 備份(包括在 VM 備份中包括虛擬機備份和 SQL 和 SAP HANA),備份數據儲存在 Azure 儲存中,並且來賓無法直接存取備份儲存或其內容。  使用虛擬機備份時,備份快照創建和存儲由 Azure 結構完成,其中來賓除了靜默應用程式一致的備份工作負載外,沒有其他參與。  使用 SQL 和 SAP HANA,備份擴展可以臨時訪問以寫入特定 Blob。  這樣,即使在受危害的環境中,來賓也不能篡改或刪除現有備份。

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM 備份不需要網路連線

Azure VM 的備份需要將數據從虛擬機器的磁碟轉移到恢復服務保管庫。 但是,所有必需的通信和數據傳輸僅在 Azure 骨幹網路上進行,而無需訪問虛擬網路。 因此,備份放置在安全網路中的 Azure VM 不需要您允許訪問任何 IP 或 FQDN。

## <a name="private-endpoints-for-azure-backup"></a>用於 Azure 備份的專用終結點

現在,您可以使用[專用終結點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)安全地將數據從虛擬網路中的伺服器備份到恢復服務保管庫。 專用終結點使用來自 VNET 位址空間的 IP 來存取保管庫,因此您無需將虛擬網路公開給任何公共 IP。 專用終結點可用於備份和還原在 Azure VM 中運行的 SQL 和 SAP HANA 資料庫。 它還可用於使用 MARS 代理的本地伺服器。

>[!NOTE]
> 此功能當前可用性有限。 如果您有興趣使用專用終結點進行 Azure 備份,請填寫[此調查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)並[傳送電子郵件給我們](mailto:azbackupnetsec@microsoft.com)。 使用此功能的功能須經 Azure 備份服務批准。

## <a name="encryption-of-data-in-transit-and-at-rest"></a>傳輸與靜態資料的加密

加密可保護您的數據,並説明您履行組織安全和合規性承諾。 在 Azure 中,Azure 儲存和保管庫之間傳輸的數據受 HTTPS 保護。 此資料會保持在 Azure 中樞網路上。

* 備份數據使用Microsoft管理的金鑰自動加密。 您還可以使用存儲在 Azure 金鑰保管庫中[的客戶託管密鑰](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys)在恢復服務保管庫中加密備份的託管磁碟 VM。 無需執行任何顯式操作來啟用此加密。 它適用於備份到恢復服務保管庫的所有工作負載。

* Azure 備份支援使用 Azure 磁碟加密 (ADE) 加密其作業系統/數據磁碟的 Azure VM 的備份和恢復。 [瞭解有關加密 Azure VM 和 Azure 備份的更多](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)詳細資訊。

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>保護備份資料免受意外刪除

Azure 備份提供安全功能,即使在刪除后也能幫助保護備份數據。 使用軟刪除,如果使用者刪除 VM 的備份,備份數據將再保留 14 天,從而可以恢復該備份專案,不會丟失任何數據。 在「軟刪除」狀態下額外保留備份數據 14 天不會給客戶帶來任何成本。 [瞭解此軟刪除的更多](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)。

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>監測和警報可疑活動

Azure 備份提供[內置監視和警報功能](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor),用於查看和配置與 Azure 備份相關的事件的操作。 [備份報告](https://docs.microsoft.com/azure/backup/configure-reports)是追蹤使用方式、審核備份和還原以及識別不同粒度級別的關鍵趨勢的一站式目標。 使用 Azure 備份的監視和報告工具可以在任何未經授權的、可疑或惡意活動發生後立即提醒您。

## <a name="security-features-to-help-protect-hybrid-backups"></a>安全功能,協助保護混合備份

Azure 備份服務使用 Microsoft Azure 恢複服務 (MARS) 代理從本地電腦備份和還原檔、資料夾以及卷或系統狀態到 Azure。 MARS 現在提供安全功能來幫助保護混合備份。 這些功能包括：

* 每當執行重要作業 (如變更複雜密碼) 時，就會多一道驗證。 這項驗證用來確保只有具備有效 Azure 認證的使用者，才能執行這類作業。 [詳細瞭解防止攻擊的功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)。

* 已刪除的備份數據將從刪除之日起再保留 14 天。 這可確保數據在給定時間段內的可恢復性,因此即使發生攻擊,也會丟失數據。 而且會維護更多的復原點，以防範資料損毀。 [瞭解有關恢復已刪除的備份數據的更多。](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data)

## <a name="compliance-with-standardized-security-requirements"></a>符合標準化的安全要求

為了説明組織遵守有關收集和使用個人數據的國家、區域和行業特定要求,Microsoft Azure & Azure 備份提供了一套全面的認證和證明。 [檢視合規性認證清單](compliance-offerings.md)

## <a name="next-steps"></a>後續步驟

* [安全功能,幫助保護使用 Azure 備份的雲工作負載](backup-azure-security-feature-cloud.md)
* [利用安全性功能協助保護使用 Azure 備份的混合式備份](backup-azure-security-feature.md)
