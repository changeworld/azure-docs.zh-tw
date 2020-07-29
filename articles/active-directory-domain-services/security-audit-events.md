---
title: 啟用 Azure AD Domain Services 的安全性審核 |Microsoft Docs
description: 瞭解如何在 Azure AD Domain Services 中，啟用安全性審核以集中記錄事件以進行分析和警示
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: c86f98fb20af2cd5ac969867cabfdc5dcb62db54
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039886"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>啟用 Azure Active Directory Domain Services 的安全性審核

Azure Active Directory Domain Services （Azure AD DS）安全性審核可讓 Azure 將安全性事件串流至目標資源。 這些資源包括 Azure 儲存體、Azure Log Analytics 工作區或 Azure 事件中樞。 啟用安全性 audit 事件之後，Azure AD DS 會將所選類別的所有已審核事件傳送至目標資源。

您可以使用 Azure 事件中樞將事件封存至 Azure 儲存體，並將事件串流至安全性資訊和事件管理（SIEM）軟體（或對等專案），或執行您自己的分析，並從 Azure 入口網站使用 Azure Log Analytics 工作區。

> [!IMPORTANT]
> Azure AD DS 安全性審核僅適用于 Azure Resource Manager 為基礎的受控網域。 如需有關如何遷移的詳細資訊，請參閱[從傳統虛擬網路模型將 AZURE AD DS 遷移至 Resource Manager][migrate-azure-adds]。

## <a name="security-audit-destinations"></a>安全性審查目的地

您可以使用 Azure 儲存體、Azure 事件中樞或 Azure Log Analytics 工作區，做為 Azure AD DS 安全性審核的目標資源。 這些目的地可以合併。 例如，您可以使用 Azure 儲存體來封存安全性 audit 事件，但 Azure Log Analytics 工作區可以用來分析和報告短期資訊。

下表概述每個目的地資源類型的案例。

> [!IMPORTANT]
> 您必須先建立目標資源，才能啟用 Azure AD DS 安全性的審核。 您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來建立這些資源。

| 目標資源 | 狀況 |
|:---|:---|
|Azure 儲存體| 當您的主要需求是儲存安全性 audit 事件以供封存之用時，應該使用此目標。 其他目標可以用於封存用途，不過這些目標提供的功能超出主要的封存需求。 <br /><br />啟用 Azure AD DS 安全性 audit 事件之前，請先[建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)。|
|Azure 事件中心| 當您的主要需求是與其他軟體（例如資料分析軟體或 & 事件管理（SIEM）軟體的安全性資訊）共用安全性 audit 事件時，應該使用此目標。<br /><br />啟用 Azure AD DS 安全性審核事件之前，請先[使用 Azure 入口網站建立事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics 工作區| 當您的主要需求是直接分析並查看來自 Azure 入口網站的安全審核時，應該使用此目標。<br /><br />啟用 Azure AD DS 安全性審核事件之前，請先[在 Azure 入口網站中建立 Log Analytics 工作區。](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>使用 Azure 入口網站啟用安全性 audit 事件

若要使用 Azure 入口網站啟用 Azure AD DS 安全性 audit 事件，請完成下列步驟。

> [!IMPORTANT]
> Azure AD DS 安全性審核不會追溯。 您無法捕獲或重新執行過去的事件。 Azure AD DS 只能傳送在啟用安全性審核之後發生的事件。

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 在 Azure 入口網站頂端，搜尋並選取 [ **Azure AD Domain Services**]。 選擇您的受控網域，例如 aaddscontoso.com**。
1. 在 [Azure AD DS] 視窗中，選取左側的 [**診斷設定**]。
1. 預設不會設定診斷。 若要開始使用，請選取 [**新增診斷設定**]。

    ![新增 Azure AD Domain Services 的診斷設定](./media/security-audit-events/add-diagnostic-settings.png)

1. 輸入診斷設定的名稱，例如 [ *aadds-審核*]。

    核取您想要的 [安全性] audit 目的地的核取方塊。 您可以從 Azure 儲存體帳戶、Azure 事件中樞或 Log Analytics 工作區中進行選擇。 這些目的地資源必須已經存在於您的 Azure 訂用帳戶中。 您無法在此嚮導中建立目的地資源。

    ![啟用所需的目的地和 audit 事件種類以進行捕獲](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure 儲存體**
        * 選取 [封存**至儲存體帳戶**]，然後選擇 [**設定**]。
        * 選取您想要用來封存安全性 audit 事件的**訂**用**帳戶和儲存體帳戶**。
        * 準備好時，選擇 **[確定]**。
    * **Azure 事件中樞**
        * 選取 [**串流至事件中樞**]，然後選擇 [**設定**]。
        * 選取**訂**用帳戶和**事件中樞命名空間**。 如有需要，也請選擇**事件中樞名稱**，然後選取 [**事件中樞原則名稱**]。
        * 準備好時，選擇 **[確定]**。
    * **Azure 記錄分析工作區**
        * 選取 [**傳送至 Log analytics**]，然後選擇您要用來儲存安全性 audit 事件的**訂**用帳戶和**Log Analytics 工作區**。

1. 選取您要包含在特定目標資源中的記錄類別。 如果您將 audit 事件傳送至 Azure 儲存體帳戶，您也可以設定保留原則，以定義保留資料的天數。 預設值為*0*會保留所有資料，而且不會在一段時間後輪替事件。

    您可以在單一設定中，為每個目標資源選取不同的記錄類別。 這種功能可讓您選擇要為 Log Analytics 保留的記錄類別，以及記錄您想要封存的類別（例如）。

1. 完成時，請選取 [**儲存**] 以認可您的變更。 儲存設定之後，目標資源就會開始收到 Azure AD DS 安全性 audit 事件。

## <a name="enable-security-audit-events-using-azure-powershell"></a>使用 Azure PowerShell 啟用安全性 audit 事件

若要使用 Azure PowerShell 啟用 Azure AD DS 安全性 audit 事件，請完成下列步驟。 如有需要，請先[安裝 Azure PowerShell 模組，並連接到您的 Azure 訂](/powershell/azure/install-az-ps)用帳戶。

> [!IMPORTANT]
> Azure AD DS 安全性審核不會追溯。 您無法捕獲或重新執行過去的事件。 Azure AD DS 只能傳送在啟用安全性審核之後發生的事件。

1. 使用[disconnect-azaccount](/powershell/module/Az.Accounts/Connect-AzAccount) Cmdlet 向您的 Azure 訂用帳戶進行驗證。 出現提示時，請輸入您的帳號憑證。

    ```azurepowershell
    Connect-AzAccount
    ```

1. 建立安全性 audit 事件的目標資源。

    * **Azure 儲存體**  - [使用 Azure PowerShell 建立儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure 事件中樞**  - [使用 Azure PowerShell 建立事件中樞](../event-hubs/event-hubs-quickstart-powershell.md)。 您可能也需要使用[AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) Cmdlet 來建立授權規則，以將 Azure AD DS 許可權授與事件中樞*命名空間*。 授權規則必須包含「**管理**」、「**接聽**」和「**傳送**」許可權。

        > [!IMPORTANT]
        > 請確定您已在事件中樞命名空間上設定授權規則，而不是事件中樞本身。

    * **Azure 記錄分析工作區**  - [建立具有 Azure PowerShell 的 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace-posh.md)。

1. 使用[get-azresource](/powershell/module/Az.Resources/Get-AzResource) Cmdlet，取得 Azure AD DS 受控網域的資源識別碼。 建立名為 $aadds 的變數 *。* 要保存值的 ResourceId：

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. 使用[set-azdiagnosticsetting 指令程式](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting)來設定 Azure 診斷設定，以使用目標資源來 Azure AD Domain Services 安全性 audit 事件。 在下列範例中，變數 *$aadds。* 在上一個步驟中會使用 ResourceId。

    * **Azure 儲存體**-將*storageAccountId*取代為您的儲存體帳戶名稱：

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure 事件中樞**-將*eventHubName*取代為您的事件中樞名稱，並使用您的授權規則識別碼來*eventHubRuleId* ：

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure 記錄分析工作區**-以 Log Analytics 工作區的識別碼取代*workspaceId* ：

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>使用 Azure 監視器查詢和查看安全性 audit 事件

記錄分析工作區可讓您使用 Azure 監視器和 Kusto 查詢語言，來查看和分析安全性 audit 事件。 這種查詢語言是專為使用容易閱讀的語法提供強大分析功能的唯讀用途所設計。 如需開始使用 Kusto 查詢語言的詳細資訊，請參閱下列文章：

* [Azure 監視器檔](https://docs.microsoft.com/azure/azure-monitor/)
* [開始使用 Azure 監視器 Log Analytics](../azure-monitor/log-query/get-started-portal.md)
* [開始使用 Azure 監視器中的查詢](../azure-monitor/log-query/get-started-queries.md)
* [建立和共用 Log Analytics 資料的儀表板](../azure-monitor/learn/tutorial-logs-dashboards.md)

下列查詢範例可用於開始分析來自 Azure AD DS 的安全性 audit 事件。

### <a name="sample-query-1"></a>範例查詢1

查看過去七天內的所有帳戶鎖定事件：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>範例查詢2

在2020年6月3日上午9點，查看所有帳戶鎖定事件（*4740*） 2020年6月10日午夜，以日期和時間遞增排序：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>範例查詢3

針對名為 user 的帳戶，在七天前查看帳戶登入事件（從現在開始）：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>範例查詢4

從現在開始，針對名為 user 且嘗試使用錯誤密碼登入的帳戶，從過去七天內查看帳戶登入事件（*0xC0000006a*）：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>範例查詢5

從現在開始，針對嘗試登入但帳戶已鎖定的帳戶（*0xC0000234*），在七天前查看帳戶登入事件：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>範例查詢6

從現在開始，針對所有鎖定的使用者所發生的所有登入嘗試，在七天前查看帳戶登入事件的數目：

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Audit 事件類別目錄

Azure AD DS 安全性審核與傳統 AD DS 網域控制站的傳統審核一致。 在混合式環境中，您可以重複使用現有的 audit 模式，以便在分析事件時使用相同的邏輯。 根據您需要進行疑難排解或分析的案例而定，不同的 audit 事件類別目錄必須設為目標。

下列是可用的 audit 事件類別：

| 審核類別名稱 | Description |
|:---|:---|
| 帳戶登入|審核會嘗試驗證網域控制站或本機安全性帳戶管理員（SAM）上的帳戶資料。</p>登入和登出原則設定和事件會追蹤存取特定電腦的嘗試。 此類別中的設定和事件著重于所使用的帳戶資料庫。 此類別包括下列子類別：<ul><li>[稽核認證驗證](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerbero 驗證的新功能](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[稽核 Kerberos 服務票證作業](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[稽核其他登入/登出事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 帳戶管理|會對使用者和電腦帳戶和群組進行審核。 此類別包括下列子類別：<ul><li>[稽核應用程式群組管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[稽核電腦帳戶管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[稽核通訊群組管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[審核其他帳戶管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[稽核安全性群組管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[稽核使用者帳戶管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 詳細資料追蹤|會審核該電腦上個別應用程式和使用者的活動，並瞭解電腦的使用方式。 此類別包括下列子類別：<ul><li>[稽核 DPAPI 活動](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Audit PNP 活動](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[稽核程序建立](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[稽核程序終止](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[稽核 RPC 事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 目錄服務存取|審核會嘗試存取和修改 Active Directory Domain Services （AD DS）中的物件。 這些 audit 事件只會記錄在網域控制站上。 此類別包括下列子類別：<ul><li>[稽核詳細目錄服務複寫](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[稽核目錄服務存取](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[稽核目錄服務變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[稽核目錄服務複寫](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 登入-登出|審核會嘗試以互動方式或透過網路登入電腦。 這些事件適用于追蹤使用者活動，以及識別網路資源的潛在攻擊。 此類別包括下列子類別：<ul><li>[稽核帳戶鎖定](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[稽核使用者/裝置宣告](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[稽核 IPsec 延伸模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[稽核群組成員資格](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[稽核 IPsec 主要模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[稽核 IPsec 快速模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[稽核登出](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[稽核登入](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[稽核網路原則伺服器](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[稽核其他登入/登出事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[稽核特殊登入](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|物件存取| 審核會嘗試存取網路或電腦上的特定物件或物件類型。 此類別包括下列子類別：<ul><li>[稽核已產生的應用程式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[稽核憑證服務](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[稽核詳細檔案共用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[稽核檔案共用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Audit 檔案系統](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[稽核篩選平台連線](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[稽核篩選平台封包丟棄](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Audit Handle 操作](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Audit 核心物件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[稽核其他物件存取事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Audit Registry](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Audit 卸除式存放裝置](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[稽核 SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[稽核中央存取原則階段](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|原則變更|會對本機系統或網路上的重要安全性原則進行審核。 原則通常是由系統管理員所建立，以協助保護網路資源。 監視變更或嘗試變更這些原則，可能是網路安全性管理的重要層面。 此類別包括下列子類別：<ul><li>[稽核稽核原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[稽核驗證原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[稽核授權原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[稽核篩選平台原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[稽核 MPSSVC 規則層級原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[審核其他原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|許可權使用| 會在一或多個系統上，對特定許可權的使用進行審核。 此類別包括下列子類別：<ul><li>[稽核非機密特殊權限使用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[稽核機密特殊權限使用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[稽核其他特殊權限使用事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|系統| 會針對不包含在其他類別中的電腦，以及有潛在安全性含意的電腦，進行系統層級變更的審核。 此類別包括下列子類別：<ul><li>[稽核 IPSec 驅動程式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[稽核其他系統事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[稽核安全性狀態變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[稽核安全性系統延伸](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[稽核系統整合性](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>每個類別的事件識別碼

 當特定動作觸發可審核的事件時，Azure AD DS 安全性審核會記錄下列事件識別碼：

| 事件類別目錄名稱 | 事件識別碼 |
|:---|:---|
|帳戶登入安全性|4767、4774、4775、4776、4777|
|帳戶管理安全性|4720，4722，4723，4724，4725，4726，4727，4728，4729，4730，4731，4732，4733，4734，4735，4737，4738，4740，4741，4742，4743，4754，4755，4756，4757，4758，4764，4765，4766，4780，4781，4782，4793，4798，4799，5376|
|詳細資料追蹤安全性|None|
|DS 存取安全性|5136、5137、5138、5139、5141|
|登入登出安全性|4624、4625、4634、4647、4648、4672、4675、4964|
|物件存取安全性|None|
|原則變更安全性|4670，4703，4704，4705，4706，4707，4713，4715，4716，4717，4718，4719，4739，4864，4865，4866，4867，4904，4906，4911，4912|
|許可權使用安全性|4985|
|系統安全性|4612、4621|

## <a name="next-steps"></a>後續步驟

如需 Kusto 的特定資訊，請參閱下列文章：

* Kusto 查詢語言的[總覽](/azure/kusto/query/)。
* [Kusto 教學](/azure/kusto/query/tutorial)課程，讓您熟悉查詢的基本概念。
* 協助您瞭解新方式來查看資料的[範例查詢](/azure/kusto/query/samples)。
* Kusto[最佳作法](/azure/kusto/query/best-practices)，以優化您的查詢是否成功。

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
