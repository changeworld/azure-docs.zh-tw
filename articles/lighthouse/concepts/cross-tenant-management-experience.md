---
title: 跨租用戶管理體驗
description: Azure 委派的資源管理能提供跨租用戶管理體驗。
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 0e55923e688d1062adc5838a88e8d3202864282a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218396"
---
# <a name="cross-tenant-management-experiences"></a>跨租用戶管理體驗

身為服務提供者的您可以使用 [Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)，從您位於 [Azure 入口網站](https://portal.azure.com)的租用戶內管理多個客戶的 Azure 資源。 大部分的工作和服務都可以對受控租用戶中委派的 Azure 資源執行。 此文章說明一些可讓 Azure 委派的資源管理生效的增強案例。

> [!NOTE]
> Azure 委派的資源管理也可以[在具有多個專屬 Azure AD 租使用者的企業內](enterprise.md)使用，以簡化跨租使用者管理。

## <a name="understanding-customer-tenants"></a>了解客戶租用戶

Azure Active Directory (Azure AD) 租用戶是組織的代表。 它是組織會在註冊 Azure、Microsoft 365 或其他服務，並與 Microsoft 建立關聯性時接收到的 Azure AD 專用執行個體。 每個 Azure AD 租用戶都不同，並與其他 Azure AD 租用戶分開，且具有自己的租用戶識別碼 (GUID)。 如需詳細資訊，請參閱[什麼是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

通常，服務提供者若要為客戶管理 Azure 資源，便必須使用與該客戶的租用戶相關聯的帳戶登入 Azure 入口網站，這要求客戶租用戶中的系統管理員必須為服務提供者建立並管理使用者帳戶。

透過使用 Azure 委派的資源管理，上線程序會在服務提供者的租用戶中指定能存取並管理客戶租用戶中的訂用帳戶、資源群組與資源的使用者。 這些使用者接著便可以使用自己的認證登入 Azure 入口網站。 在 Azure 入口網站內，他們可以管理屬於所有客戶，且其具有存取權的資源。 若要這樣做，請瀏覽 Azure 入口網站中的[我的客戶](../how-to/view-manage-customers.md)頁面，或是在 Azure 入口網站中或透過 API 直接在該客戶的訂用帳戶內容中工作。

Azure 委派的資源管理能為管理多個客戶的資源提供更具彈性的方式，使您不需要在不同的租用戶中登入不同的帳戶。 例如，某個服務提供者可能有三個客戶，並針對他們都具有不同的責任和存取層級，如這裡所示：

![顯示服務提供者責任的三個客戶租用戶](../media/azure-delegated-resource-management-customer-tenants.jpg)

使用 Azure 委派的資源管理，已授權的使用者可以登入服務提供者的租使用者來存取這些資源，如下所示：

![透過其中一個服務提供者租用戶進行管理的客戶資源](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API 和管理工具支援

您可以直接在入口網站中，或是使用 API 與管理工具 (例如 Azure CLI 和 Azure PowerShell)，對委派的資源執行管理工作。 所有現有的 API 都可以在使用委派的資源時使用，但前提是，此功能支援跨租用戶管理，且使用者擁有適當的權限。

Azure PowerShell [get-azsubscription 指令程式](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0)會顯示每個訂用帳戶的**tenantID** ，讓您能夠識別傳回的訂用帳戶屬於您的服務提供者租使用者還是受管理的客戶租使用者。

同樣地，Azure CLI 命令（例如[az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) ）會顯示**homeTenantId**和**managedByTenants**屬性。

> [!TIP]
> 當您使用 Azure CLI 時，如果您沒有看到這些值，請嘗試執行 `az account clear` 並接著 `az login --identity`來清除快取。

我們也提供了一些 Api，專門用來執行 Azure 委派的資源管理工作。 如需詳細資訊，請參閱**參考**一節。

## <a name="enhanced-services-and-scenarios"></a>增強的服務與案例

大部分的工作和服務都可以對受控租用戶中委派的資源執行。 以下是跨租用戶管理適用的一些主要案例。

[適用於伺服器的 Azure Arc (預覽)](../../azure-arc/servers/overview.md)：

- [將 Azure 外部的 Windows Server 或 Linux 電腦連線](../../azure-arc/servers/quickstart-onboard-portal.md)到 Azure 中委派的訂用帳戶和/或資源群組
- 使用 Azure 結構管理已連線的電腦，例如 Azure 原則和標記

[Azure 自動化](../../automation/index.yml)：

- 使用自動化帳戶來存取及處理委派的客戶資源

[Azure 備份](../../backup/index.yml)：

- 備份及還原客戶租用戶中的客戶資料
- 使用 [[備份瀏覽器](../../backup/monitor-azure-backup-with-backup-explorer.md)] 可協助您查看備份專案的作業資訊（包括尚未設定備份的 Azure 資源），以及委派訂閱的監視資訊（作業和警示）。 備份瀏覽器目前僅適用于 Azure VM 資料。
- 跨委派的訂用帳戶使用[備份報告](../../backup/configure-reports.md)來追蹤歷程記錄趨勢、分析備份儲存體耗用量，以及審核備份和還原。

[Azure Kubernetes Service (AKS)](../../aks/index.yml)：

- 管理託管的 Kubernetes 環境並部署及管理客戶租用戶內的容器化應用程式

[Azure 監視器](../../azure-monitor/index.yml)：

- 檢視委派之訂用帳戶的警示，並能夠在所有訂用帳戶之間檢視警示
- 檢視委派之訂用帳戶的活動記錄詳細資料
- 記錄分析：來自多個租用戶中的遠端客戶工作區的查詢資料
- 在客戶租用戶中建立警示，以透過 Webhook 在服務提供者租用戶中觸發自動化 (例如 Azure 自動化 Runbook 或 Azure Functions)

[Azure 原則](../../governance/policy/index.yml) \(部分機器翻譯\)：

- 顯示委派之訂用帳戶內的已指派原則詳細資料的合規性快照集
- 在委派的訂用帳戶內建立及編輯原則定義
- 在委派的訂用帳戶內指派客戶所定義的原則定義
- 客戶會看到由服務提供者所撰寫的原則，以及他們自己撰寫的所有原則
- 可以[補救 deployIfNotExists 或修改客戶租用戶內的指派](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](../../governance/resource-graph/index.yml) \(部分機器翻譯\)：

- 現在會在傳回的查詢結果中包含租用戶識別碼，以讓您識別某個訂用帳戶是否屬於客戶租用戶或服務提供者租用戶

[Azure 資訊安全中心](../../security-center/index.yml)：

- 跨租用戶可見性
  - 監視安全性原則的合規性，並確保所有租使用者資源的安全性涵蓋範圍
  - 在單一檢視中針對多個客戶進行持續性的法規合規性監視
  - 透過安全分數計算對可採取動作的安全性建議進行監視、分級和設定優先權
- 跨租用戶安全性狀態管理
  - 管理安全性原則
  - 透過可採取動作的安全性建議對不符合規範的資源採取動作
  - 收集及儲存安全性相關的資料
- 跨租用戶威脅偵測和保護
  - 跨租使用者的資源偵測威脅
  - 套用進階威脅防護控制措施，例如 Just-In-Time (JIT) VM 存取
  - 透過自適性網路強化來強化網路安全性群組設定
  - 透過自適性應用程式控制來確保伺服器只會執行適當的應用程式與處理序
  - 透過檔案完整性監視 (FIM) 來監視對重要檔案與登錄的變更

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md)：

- 管理客戶租[使用者中的](../../sentinel/multiple-tenants-service-providers.md)Azure Sentinel 資源
- [追蹤攻擊並跨多個客戶租使用者查看安全性警示](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure 服務健康情況](../../service-health/index.yml)：

- 透過 Azure 資源健康情況來監視客戶資源的健康情況
- 追蹤客戶所使用之 Azure 服務的健康情況

[Azure Site Recovery](../../site-recovery/index.yml)：

- 管理客戶租用戶中 Azure 虛擬機器的災害復原選項 (請注意，您無法使用 RunAs 帳戶來複製 VM 擴充功能)

[Azure 虛擬機器](../../virtual-machines/index.yml) \(部分機器翻譯\)：

- 使用虛擬機器擴充功能來在客戶租用戶中的 Azure VM 上提供部署後設定及自動化工作
- 使用開機診斷來針對客戶租用戶中的 Azure VM 進行疑難排解
- 透過客戶租用戶中的序列主控台來存取 VM
- 請注意，您無法使用 Azure Active Directory 來從遠端登入 VM，且您無法將 VM 與 Key Vault 整合以取得用於磁碟加密的密碼、祕密或密碼編譯金鑰

[Azure 虛擬網路](../../virtual-network/index.yml)：

- 部署及管理客戶租用戶內的虛擬網路與虛擬網路介面卡 (vNIC)

支援要求：

- 從 Azure 入口網站中的 [說明 + 支援] 刀鋒視窗開啟委派資源的支援要求 (選取可供委派範圍使用的支援方案)

## <a name="current-limitations"></a>目前的限制
在所有案例中，請留意下列目前限制：

- 由 Azure Resource Manager 所處理的要求可以使用 Azure 委派的資源管理來執行。 這些要求的作業 URI 會以 `https://management.azure.com` 作為開頭。 不過，Azure 委派的資源管理不支援由資源類型（例如 KeyVault 秘密存取或儲存資料存取）的實例所處理的要求。 這些要求的作業 URI 通常會以您執行個體特有的位址作為開頭，例如 `https://myaccount.blob.core.windows.net` 或 `https://mykeyvault.vault.azure.net/`。 此外，後者通常是資料作業，而非管理作業。 
- 角色指派必須使用角色型存取控制 (RBAC) [內建角色](../../role-based-access-control/built-in-roles.md) \(部分機器翻譯\)。 除了「擁有者」或具有 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) \(部分機器翻譯\) 權限的任何內建角色以外，Azure 委派的資源管理目前支援所有內建角色。 只有在[將角色指派給受控識別](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)時，才支援「使用者存取系統管理員」角色的有限用途。  此外，不支援自訂角色與[傳統訂用帳戶管理員角色](../../role-based-access-control/classic-administrators.md) \(部分機器翻譯\)。
- 雖然您可以將使用 Azure Databricks 的訂用帳戶上線，但管理租使用者中的使用者目前無法在委派的訂閱上啟動 Azure Databricks 的工作區。
- 雖然您可以將訂用帳戶和資源群組上線，以進行 Azure 委派的資源管理來鎖定資源，但這些鎖定無法防止管理租用戶中的使用者執行動作。 [拒絕](../../role-based-access-control/deny-assignments.md)保護系統管理資源的指派，例如由 Azure 受控應用程式或 Azure 藍圖（系統指派的拒絕指派）所建立的資源，可防止管理租使用者中的使用者在這些資源上執行動作;不過，在這段時間內，客戶租使用者中的使用者無法建立自己的拒絕指派（使用者指派的拒絕指派）。

## <a name="next-steps"></a>後續步驟

- 將客戶上線至 Azure 委派的資源管理，方法是透過[使用 Azure Resource Manager 範本](../how-to/onboard-customer.md)或[將私人或公開受控服務供應項目發佈至 Azure Marketplace](../how-to/publish-managed-services-offers.md)。
- 前往 Azure 入口網站中的 [我的客戶]，以[檢視及管理客戶](../how-to/view-manage-customers.md)。
