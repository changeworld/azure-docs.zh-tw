---
title: Microsoft Azure 的客戶加密箱
description: Microsoft Azure 客戶加密箱的技術總覽，可在 Microsoft 可能需要存取客戶資料時，提供雲端提供者存取的控制權。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 09/09/2020
ms.openlocfilehash: 5c24bd80721f626e38dcb886e89231c0b86056df
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650971"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure 的客戶加密箱

> [!NOTE]
> 若要使用這項功能，您的組織必須擁有具有基本**開發人員**層級的[Azure 支援方案](https://azure.microsoft.com/support/plans/)。

適用于 Microsoft Azure 的客戶加密箱會提供一個介面，讓客戶可以檢查及核准或拒絕客戶資料存取要求。 當 Microsoft 工程師需要在支援要求期間存取客戶資料時，就會使用此功能。

本文涵蓋如何起始、追蹤和儲存客戶加密箱要求，以供稍後的評論和審核之用。

客戶加密箱現已正式推出，且目前已啟用對虛擬機器的遠端桌面存取。

## <a name="supported-services-and-scenarios-in-preview"></a>預覽中支援的服務和案例

下列服務目前為客戶加密箱的預覽狀態：

- API 管理
- Azure App Service
- 適用於 MySQL 的 Azure 資料庫
- Azure Databricks
- Azure Synapse Analytics
- 認知服務
- Container Registry
- Azure Data Factory
- 適用於 PostgreSQL 的 Azure 資料庫
- Azure Kubernetes Service
- Azure 資料箱
- HDInsight
- 函數
- Azure 儲存體
- Azure SQL DB
- Azure 資料總管
- 虛擬機器 (現在也涵蓋記憶體傾印和受控磁片的存取權) 
- Azure 訂用帳戶轉移

若要為您的組織啟用這些預覽供應專案的客戶加密箱，請註冊 [Azure 公開預覽客戶加密箱](https://aka.ms/customerlockbox/insiderprogram)。

## <a name="supported-services-and-scenarios-in-general-availability"></a>正式推出的支援服務和案例

下列服務和案例目前已正式推出客戶加密箱。

### <a name="remote-desktop-access-to-virtual-machines"></a>虛擬機器的遠端桌面存取

客戶加密箱目前已啟用對虛擬機器的遠端桌面存取要求。 支援下列工作負載：
- 平臺即服務 (PaaS) -Azure 雲端服務 (web 角色和背景工作角色) 
- 基礎結構即服務 (IaaS) -Windows 和 Linux (僅 Azure Resource Manager) 
- 虛擬機器擴展集-Windows 和 Linux

> [!NOTE]
> 客戶加密箱不支援 IaaS 傳統實例。 如果您的工作負載是在 IaaS 傳統實例上執行，建議您將它們從傳統遷移至 Resource Manager 部署模型。 如需指示，請參閱 [平臺支援將 IaaS 資源從傳統遷移至 Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)。

#### <a name="detailed-audit-logs"></a>詳細的 audit 記錄

針對牽涉到遠端桌面存取的案例，您可以使用 Windows 事件記錄檔來檢查 Microsoft 工程師所採取的動作。 請考慮使用 Azure 資訊安全中心收集您的事件記錄檔，並將資料複製到您的工作區以進行分析。 如需詳細資訊，請參閱 [Azure 資訊安全中心中的資料收集](../../security-center/security-center-enable-data-collection.md)。

## <a name="workflow"></a>工作流程

下列步驟概述客戶加密箱要求的一般工作流程。

1. 組織中的某人對於其 Azure 工作負載有問題。

2. 當這個人員針對問題進行疑難排解，但無法修正問題時，他們會從 [Azure 入口網站](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)開啟支援票證。 票證會指派給 Azure 客戶支援工程師。

3. Azure 支援工程師會審核服務要求，並決定解決問題的後續步驟。

4. 如果支援工程師無法使用標準工具和遙測進行問題的疑難排解，下一步是使用即時 (JIT) 存取服務來要求更高的許可權。 此要求可以來自原始支援工程師。 或者，它可以來自不同的工程師，因為問題已呈報給 Azure DevOps 團隊。

5. Azure 工程師提交存取要求之後，即時服務會評估納入考慮因素的要求，例如：
    - 資源的範圍
    - 要求者是否為隔離的身分識別或使用多重要素驗證
    - 許可權層級

    根據 JIT 規則，此要求也可能包含來自內部 Microsoft 核准者的核准。 例如，核准者可能是客戶支援負責人或 DevOps 管理員。

6. 當要求需要直接存取客戶資料時，就會起始客戶加密箱要求。 例如，遠端桌面存取客戶的虛擬機器。

    要求現在處於 **客戶通知** 的狀態，正在等候客戶的核准，再授與存取權。

7. 在客戶組織中，擁有 Azure 訂用帳戶 [擁有者角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) 的使用者會收到來自 Microsoft 的電子郵件，通知他們有擱置的存取要求。 針對客戶加密箱要求，這位人員是指定的核准者。

    範例電子郵件：

    ![Azure 客戶加密箱-電子郵件通知](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 電子郵件通知會提供 Azure 入口網站中 **客戶加密箱** 分頁的連結。 使用此連結時，指定的核准者會登入 Azure 入口網站，以查看其組織針對客戶加密箱的任何擱置要求：

    ![Azure 客戶加密箱-登陸頁面](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   要求會保留在客戶佇列中四天。 在這段時間之後，存取要求會自動到期，而不會授與 Microsoft 工程師的存取權。

9. 若要取得擱置要求的詳細資料，指定的核准者可以從 **擱置要求**中選取加密箱要求：

    ![Azure 客戶加密箱-查看擱置的要求](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 指定的核准者也可以選取 **服務要求識別碼** ，以查看原始使用者所建立的支援票證要求。 這項資訊會提供 Microsoft 支援服務參與原因的內容，以及回報問題的歷程記錄。 例如：

    ![Azure 客戶加密箱-查看支援票證要求](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 審核要求之後，指定的核准者會選取 [ **核准** ] 或 [ **拒絕**]：

    ![Azure 客戶加密箱-選取 [核准] 或 [拒絕]](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    作為選取專案的結果：
    - **核准**：存取權授與 Microsoft 工程師。 授與存取權的預設期間為八小時。
    - **拒絕**： Microsoft 工程師的提高存取要求遭到拒絕，因此不會採取進一步的動作。

基於審核目的，在此工作流程中採取的動作會記錄在 [客戶加密箱要求記錄](#auditing-logs)檔中。

## <a name="auditing-logs"></a>稽核記錄

客戶加密箱記錄會儲存在活動記錄中。 在 [Azure 入口網站中，選取 [ **活動記錄** ] 以查看與客戶加密箱要求相關的審核資訊。 您可以篩選特定動作，例如：
- **拒絕加密箱要求**
- **建立加密箱要求**
- **核准加密箱要求**
- **加密箱要求到期**

例如：

![Azure 客戶加密箱-活動記錄](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>客戶加密箱與 Azure 安全性基準測試整合

我們在 Azure 安全性基準測試中引進了新的主控項 ([3.13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) ，其中涵蓋客戶加密箱適用性。 客戶現在可以利用基準測試客戶加密箱服務的適用性。

## <a name="exclusions"></a>排除

在下列工程支援案例中，不會觸發客戶加密箱要求：

- Microsoft 工程師需要進行超出標準作業程式的活動。 例如，在非預期或無法預期的案例中復原或還原服務。

- Microsoft 工程師會在進行疑難排解時存取 Azure 平臺，並不慎存取客戶資料。 例如，Azure 網路小組會執行在網路裝置上產生封包捕獲的疑難排解。 但是，如果客戶在傳輸時將資料加密，則工程師無法讀取資料。

## <a name="next-steps"></a>接下來的步驟

如果所有客戶的 [Azure 支援方案](https://azure.microsoft.com/support/plans/) 都具有基本等級的 **開發人員**，客戶加密箱會自動提供。

當您有合格的支援方案時，您不需要採取任何動作來啟用客戶加密箱。 如果您需要此動作來進行從組織中的某個人提出的支援票證，則會由 Microsoft 工程師起始客戶加密箱要求。
