---
title: 澳大利亞政府 ISM 受保護藍圖範例概觀
description: 澳大利亞政府 ISM 受保護藍圖範例概觀。 此藍圖範例可協助客戶評定特定 ISO 受保護控制措施。
ms.date: 03/10/2020
ms.topic: sample
ms.openlocfilehash: e2f64943e9db207feb3f59a905cad994553a6cd6
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82595353"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>澳大利亞政府 ISM 受保護藍圖範例控制措施對應

下列文章將詳細說明 Azure 藍圖澳大利亞政府 ISM 受保護藍圖範例如何對應至受 ISM 保護的控制措施。 如需控制措施的詳細資訊，請參閱 [ISM 受保護](https://www.cyber.gov.au/ism)。

以下是 **ISM 受保護**控制措施的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，找出並選取 **\[預覽\]：稽核澳洲政府 ISM (Australian Government ISM) 的受保護 (PROTECTED) 控制措施，並部署特定的 VM 擴充功能，以支援稽核需求**內建原則計劃。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。
> 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md) \(英文\)。


## <a name="location-constraints"></a>位置限制

此藍圖會指派下列 Azure 原則定義，協助您將所有資源和資源群組的部署位置限制為「澳大利亞中部」、「澳大利亞中部 2」、「澳大利亞東部」和「澳大利亞東南部」:

- 允許的位置 (已硬式編碼為「澳大利亞中部」、「澳大利亞中部 2」、「澳大利亞東部」和「澳大利亞東南部」)
- 允許的資源群組位置 (已硬式編碼為「澳大利亞中部」、「澳大利亞中部 2」、「澳大利亞東部」和「澳大利亞東南部」)

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>人員安全性指導方針 - 對系統及其資源的存取

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 已授與系統和其資源存取權的人員是唯一可識別的

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 系統、應用程式和資料存放庫的標準存取權僅限於需要執行其職責的人員

- 應針對您的訂用帳戶指定最多 3 位擁有者
- 應將一個以上的擁有者指派給您的訂用帳戶
- 顯示其中 Administrators 群組包含了任一指定成員的 Windows VM 稽核結果
- 部署必要條件，以稽核其中 Administrator 群組包含任何指定成員的 Windows VM

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 第一次要求和每年或在更短期間進行重新驗證時，會驗證系統、應用程式和資料存放庫的存取使用權限

- 顯示其中 Administrators 群組包含了任一指定成員的 Windows VM 稽核結果
- 部署必要條件，以稽核其中 Administrator 群組包含任何指定成員的 Windows VM

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 系統、應用程式和資料存放庫的存取使用權限僅限於需要執行其職責的人員

- 應針對您的訂用帳戶指定最多 3 位擁有者
- 應將一個以上的擁有者指派給您的訂用帳戶
- 顯示其中 Administrators 群組包含了任一指定成員的 Windows VM 稽核結果
- 部署必要條件，以稽核其中 Administrator 群組包含任何指定成員的 Windows VM
- Just-In-Time 網路存取控制應套用在虛擬機器上

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 共用使用者帳戶的使用受到嚴格控制，而使用這類帳戶的人員則是唯一可識別的

- 顯示其中 Administrators 群組包含了任一指定成員的 Windows VM 稽核結果
- 部署必要條件，以稽核其中 Administrator 群組包含任何指定成員的 Windows VM

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 系統會指派專用的使用權限帳戶給已授與使用權限的使用者，僅供需要存取使用權限的工作使用

- 顯示其中 Administrators 群組包含了任一指定成員的 Windows VM 稽核結果
- 部署必要條件，以稽核其中 Administrator 群組包含任何指定成員的 Windows VM

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 已移除或暫停同一天存取系統、應用程式和資料存放庫的人員權限，不再具有合法的存取要求

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 當系統授與人員系統的暫時存取權時，會將有效的安全性控制項放在適當的位置，以限制他們只能存取其職責所需的資訊

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>系統強化指導方針 - 作業系統強化

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 標準作業環境 (SOEs) 會使用作業系統的最新版本 (N) 或 N-1 版本

- 您應在機器上安裝系統更新
- 應在虛擬機器擴展集上安裝系統更新

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 不需要的作業系統帳戶、軟體、元件、服務和功能已移除或停用

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 應用程式白名單解決方案會在所有伺服器上實作，以將可執行檔、軟體程式庫、指令碼和安裝程式的執行限制為已核准的集合

- 應在虛擬機器上啟用自適性應用程式控制

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 防毒軟體會在工作站和伺服器上實作，並以下列方式設定：啟用簽章型偵測並設為高階、啟用啟發學習型偵測並設定為高階、每日至少檢查一次的貨幣檢查偵測簽章，以及針對所有固定磁碟和抽取式媒體設定的自動和定期掃描

- Microsoft IaaSAntimalware 延伸模組應該部署在 Windows 伺服器上
- 應在虛擬機器擴展集上安裝端點保護解決方案
- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>系統強化指導方針 - 驗證強化

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 系統在授與系統和其資源的存取權給使用者前，會先加以驗證

- 稽核不受限制的儲存體帳戶網路存取
- Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證
- \[預覽\]：從允許不使用密碼從帳戶遠端連線的 Linux VM 顯示稽核結果
- \[預覽\]：部署必要條件以稽核允許不使用密碼從帳戶遠端連線的 Linux VM
- \[預覽\]：從帳戶沒有密碼的 Linux VM 顯示稽核結果
- \[預覽\]：部署必要條件，以稽核有不具密碼帳戶的 Linux VM

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 使用多重要素驗證來驗證標準使用者

- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 多重要素驗證是用來驗證所有具使用權限的使用者和任何其他信任的位置

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 用於單一要素驗證的複雜密碼至少要有 14 個字元的複雜度，且最理想的是包括 4 個隨機單字

- \[預覽\]：顯示 [安全性設定 - 帳戶原則] 中的 Windows VM 設定稽核結果
- \[預覽\]：部署必要條件，以稽核 [安全性設定 - 帳戶原則] 中的 Windows VM 設定

## <a name="guidelines-for-system-management---system-administration"></a>系統管理指導方針 - 系統管理

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 每次執行使用權限動作時會使用多重要素驗證來驗證使用者

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 管理流量只允許來自用來管理系統和應用程式的網路區域

- Just-In-Time 網路存取控制應套用在虛擬機器上
- 應關閉 API 應用程式的遠端偵錯
- 函式應用程式的遠端偵錯應關閉
- 應關閉 Web 應用程式的遠端偵錯

## <a name="guidelines-for-system-management---system-patching"></a>系統管理指導方針 - 系統修補

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 在廠商、獨立的第三方、系統管理員或使用者在 識別出安全性弱點的 48 小時內，已修補、更新或緩和在應用程式和驅動程式中評估為極端風險的安全性弱點

- 應修復 SQL 資料庫的弱點
- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- \[預覽\]：虛擬機器上應啟用弱點評定
- 應修復虛擬機器擴展集上安全性組態的弱點
- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 在廠商、獨立的第三方、系統管理員或使用者在 識別出安全性弱點的兩週內，已修補、更新或緩和在應用程式和驅動程式中評估為高風險的安全性弱點

- 應修復 SQL 資料庫的弱點
- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- \[預覽\]：虛擬機器上應啟用弱點評定
- 應修復虛擬機器擴展集上安全性組態的弱點
- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 在廠商、獨立的第三方、系統管理員或使用者在 識別出安全性弱點的一個月內，已修補、更新或緩和在應用程式和驅動程式中評估為中等或低風險的安全性弱點

- 應修復 SQL 資料庫的弱點
- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- \[預覽\]：虛擬機器上應啟用弱點評定
- 應修復虛擬機器擴展集上安全性組態的弱點
- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 在廠商、獨立的第三方、系統管理員或使用者在 識別出安全性弱點的 48 小時內，已修補、更新或緩和在作業系統和軔體中評估為極端風險的安全性弱點

- 應修復 SQL 資料庫的弱點
- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- \[預覽\]：虛擬機器上應啟用弱點評定
- 應修復虛擬機器擴展集上安全性組態的弱點
- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 在廠商、獨立的第三方、系統管理員或使用者在 識別出安全性弱點的兩週內，已修補、更新或緩和在作業系統和軔體中評估為高風險的安全性弱點

- 應修復 SQL 資料庫的弱點
- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- \[預覽\]：虛擬機器上應啟用弱點評定
- 應修復虛擬機器擴展集上安全性組態的弱點
- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 在廠商、獨立的第三方、系統管理員或使用者在 識別出安全性弱點的一個月內，已修補、更新或緩和在作業系統和軔體中評估為中等或低風險的安全性弱點

- 應修復 SQL 資料庫的弱點
- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- \[預覽\]：虛擬機器上應啟用弱點評定
- 應修復虛擬機器擴展集上安全性組態的弱點
- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>系統管理指導方針 - 資料備份和還原

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 至少會每天執行一次重要資訊、軟體和組態設定的備份

- 稽核未設定災害復原的虛擬機器

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>系統監視指導方針 - 事件記錄檔和稽核

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 已實作集中化記錄設備，並將系統設定為在每個事件發生後儘快將事件記錄檔儲存至集中式記錄設備

- Azure 訂用帳戶應具有用於活動記錄的記錄設定檔

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 系統會針對作業系統記錄下列事件：存取重要資料和流程、應用程式損毀和任何錯誤訊息、嘗試使用特殊權限、帳戶變更、安全性原則變更、系統組態變更、網域名稱系統 (DNS) 和超文字傳輸通訊協定 (HTTP) 要求、嘗試存取資料和系統資源失敗、服務失敗和重新開機、系統啟動和關機，將資料傳輸至外部媒體、使用者或群組管理、使用特殊權限

- \[預覽\]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- \[預覽\]：稽核 VMSS 中的記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- \[預覽\]：稽核適用於 VM 的 Log Analytics 工作區 - 報告不相符
- 稽核診斷設定

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 系統會針對資料庫記錄下列事件：存取特別重要的資訊、加入新的使用者，特別是有權限的使用者、包含註解的任何查詢、包含多個內嵌查詢的任何查詢、任何查詢或資料庫警示或失敗、嘗試提高使用權限、嘗試存取的行為 (無論成功與否)、資料庫結構變更、使用者角色或資料庫權限變更、資料庫管理員動作、資料庫登入和登出、資料修改、使用可執行命令

- 應在 SQL 伺服器上啟用進階資料安全性
- 稽核診斷設定
- 應在 SQL 受控執行個體上啟用進階資料安全性

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>系統監視指導方針 - 弱點管理

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 弱點評估和滲透測試是由具備適當技能的人員在系統部署之前，或系統的重大變更後，至少每年執行一次，或在系統擁有者指定的情況下進行

- 應修復 SQL 資料庫的弱點
- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- \[預覽\]：虛擬機器上應啟用弱點評定
- 應修復虛擬機器擴展集上安全性組態的弱點
- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點

## <a name="guidelines-for-database-systems-management---database-servers"></a>資料庫系統管理指導方針 - 資料庫伺服器

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 資料庫伺服器的硬碟使用完整磁碟加密進行加密

- 應在虛擬機器上套用磁碟加密
- 應在 SQL 資料庫上啟用透明資料加密

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 資料庫伺服器與 Web 應用程式之間傳達的資訊已加密

- 應該只允許對 Redis Cache 的安全連線
- 應啟用儲存體帳戶的安全傳輸
- 從未使用安全通訊協定的 Windows 網頁伺服器顯示稽核結果
- 部署必要條件以稽核未使用安全通訊協定的 Windows 網頁伺服器

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>資料庫系統管理指導方針 - 資料庫管理系統軟體

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 預設資料庫管理員帳戶已停用、重新命名或變更了密碼

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 資料庫管理員具有唯一且可識別的帳戶

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 資料庫管理員帳戶不會在不同的資料庫之間共用

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 資料庫系統管理員帳戶僅供系統管理工作使用，並使用標準資料庫帳戶與資料庫進行一般用途的互動

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 資料庫管理員存取權僅限於定義的角色，而不是具有預設管理權限或具有所有權限的帳戶

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>使用加密的指導方針 - 加密基本概念

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 用於待用資料的加密軟體會執行完整磁碟加密，或部分加密，其中的存取控制只允許寫入加密的分割區

- 應在虛擬機器上套用磁碟加密

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>使用加密的指導方針 - 傳輸層安全性

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 只會使用最新版本的 TLS

- 您的 API 應用程式應使用最新的 TLS 版本
- 您的 Web 應用程式應使用最新的 TLS 版本
- 您的函式應用程式應使用最新的 TLS 版本
- 部署必要條件以稽核未使用安全通訊協定的 Windows 網頁伺服器
- 從未使用安全通訊協定的 Windows 網頁伺服器顯示稽核結果

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>資料傳輸和內容篩選指導方針 - 內容篩選

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 所有內容均會執行防毒軟體掃描 (使用多個不同的掃描引擎)

- Microsoft IaaSAntimalware 延伸模組應該部署在 Windows 伺服器上
- 應在虛擬機器擴展集上安裝端點保護解決方案
- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>資料傳輸和內容篩選指導方針 - Web 應用程式開發

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 所有 Web 應用程式內容都是使用 HTTPS 以獨佔方式提供

- 函式應用程式應只可經由 HTTPS 存取
- API 應用程式應只可經由 HTTPS 存取
- Web 應用程式應只可經由 HTTPS 存取
- 應該只允許對 Redis Cache 的安全連線

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 網頁瀏覽器型的安全性控制項會針對 Web 應用程式實作，以協助保護 Web 應用程式及其使用者

- CORS 不應允許每項資源存取您的 Web 應用程式

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>網路管理指導方針 - 網路設計和組態

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 網路會實作網路存取控制，以防止未經授權網路裝置的連線

- 稽核不受限制的儲存體帳戶網路存取

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 系統會實作網路存取控制，以將網路區段內和之間的流量限制為僅供商務用途所需的流量

- 應使用網路安全性群組保護網際網路對應的虛擬機器
- 稽核不受限制的儲存體帳戶網路存取
- 應在網際網路對應虛擬機器中套用自適性網路強化建議

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>網路管理指導方針 - 線上服務的服務持續性

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 會與服務提供者討論拒絕服務的攻擊防護和風險降低策略，特別是能夠承受拒絕服務攻擊的能力、客戶可能因阻絕服務的攻擊所需支付的任何成本、在阻絕服務攻擊時通知客戶或關閉其線上服務的閾值、在阻絕服務的攻擊期間可採取的預先核准動作、與上游提供者之間的拒絕服務的攻擊預防安排以便儘可能在最上游封鎖惡意流量

- 應啟用 DDoS 保護標準


> [!NOTE]
> 特定 Azure 原則定義的可用性可能因 Azure Government 和其他國家雲端而異。 

## <a name="next-steps"></a>後續步驟

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。