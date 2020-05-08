---
title: Microsoft Azure 的客戶加密箱
description: Microsoft Azure 的客戶加密箱技術總覽，當 Microsoft 可能需要存取客戶資料時，可讓您控制雲端提供者的存取權。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: 5330c751aaa3fcbd5c7fc268e4a4de08d336d474
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735431"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure 的客戶加密箱

> [!NOTE]
> 若要使用這項功能，您的組織必須具有最低**開發人員**層級的[Azure 支援計畫](https://azure.microsoft.com/support/plans/)。

Microsoft Azure 的客戶加密箱會提供介面，讓客戶審查及核准或拒絕客戶資料存取要求。 在 Microsoft 工程師必須在支援要求期間存取客戶資料的情況下，才會使用此功能。

本文涵蓋如何起始、追蹤和儲存客戶加密箱要求，以供日後審查和審核。

客戶加密箱現在已正式運作，且目前已啟用對虛擬機器的遠端桌面存取。

## <a name="workflow"></a>工作流程

下列步驟概述客戶加密箱要求的一般工作流程。

1. 組織中的某人有其 Azure 工作負載的問題。

2. 在此人員對問題進行疑難排解，但無法加以修正之後，他們會從[Azure 入口網站](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)開啟支援票證。 票證會指派給 Azure 客戶支援工程師。

3. Azure 支援工程師會審查服務要求，並判斷解決此問題的下一個步驟。

4. 如果支援工程師無法使用標準工具和遙測來對問題進行疑難排解，下一步就是使用及時（JIT）存取服務來要求更高的許可權。 此要求可以來自原始支援工程師。 或者，它可以來自不同的工程師，因為問題已呈報給 Azure DevOps 小組。

5. 當 Azure 工程師提交存取要求之後，及時服務會評估將要求納入考慮的因素，例如：
    - 資源的範圍
    - 要求者是否為隔離的身分識別或使用多重要素驗證
    - 許可權層級

    根據 JIT 規則，此要求也可能包含來自內部 Microsoft 核准者的核准。 例如，核准者可能是客戶支援組長或 DevOps Manager。

6. 當要求需要直接存取客戶資料時，就會起始客戶加密箱要求。 例如，遠端桌面存取客戶的虛擬機器。

    要求現在會在**客戶通知**狀態，等待客戶的核准，然後再授與存取權。

7. 在客戶組織中，具有 Azure 訂用帳戶擁有者[角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)的使用者會收到來自 Microsoft 的電子郵件，通知他們有擱置的存取要求。 對於客戶加密箱要求，此人員是指定的核准者。

    範例電子郵件：

    ![Azure 客戶加密箱-電子郵件通知](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 電子郵件通知會提供 Azure 入口網站中 [**客戶加密箱**] 分頁的連結。 使用此連結時，指定的核准者會登入 Azure 入口網站，以查看其組織客戶加密箱的任何擱置要求：

    ![Azure 客戶加密箱-登陸頁面](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   要求會保留在客戶佇列中四天。 在這段時間之後，存取要求會自動到期，且不會授與 Microsoft 工程師的任何存取權。

9. 若要取得待決要求的詳細資料，指定的核准者可以從**擱置要求**中選取加密箱要求：

    ![Azure 客戶加密箱-查看擱置中的要求](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 指定的核准者也可以選取**服務要求識別碼**，以查看原始使用者所建立的支援票證要求。 這項資訊會提供 Microsoft 支援服務參與原因的內容，以及所回報問題的歷程記錄。 例如：

    ![Azure 客戶加密箱-查看支援票證要求](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 在審查要求之後，指定的核准者會選取 [**核准**] 或 [**拒絕**]：

    ![Azure 客戶加密箱-選取 [核准] 或 [拒絕]](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    做為選取專案的結果：
    - **核准**：授與 Microsoft 工程師的存取權。 授與存取權的預設期間為八小時。
    - **拒絕**： Microsoft 工程師的提高許可權存取要求會遭到拒絕，且不會採取進一步的動作。

基於審核目的，在此工作流程中採取的動作會記錄在[客戶加密箱要求記錄](#auditing-logs)中。

## <a name="auditing-logs"></a>稽核記錄

客戶加密箱記錄會儲存在活動記錄中。 在 [Azure 入口網站中，選取 [**活動記錄**] 以查看與客戶加密箱要求相關的審核資訊。 您可以篩選特定的動作，例如：
- **拒絕加密箱要求**
- **建立加密箱要求**
- **核准加密箱要求**
- **加密箱要求過期**

例如：

![Azure 客戶加密箱-活動記錄](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>支援的服務和案例已正式運作

下列服務和案例目前已正式推出客戶加密箱。

### <a name="remote-desktop-access-to-virtual-machines"></a>虛擬機器的遠端桌面存取

客戶加密箱目前已啟用對虛擬機器的遠端桌面存取要求。 支援下列工作負載：
- 平臺即服務（PaaS）-Azure 雲端服務（web 角色和背景工作角色）
- 基礎結構即服務（IaaS）-Windows 和 Linux （僅限 Azure Resource Manager）
- 虛擬機器擴展集-Windows 和 Linux

> [!NOTE]
> 客戶加密箱不支援 IaaS 傳統實例。 如果您的工作負載在 IaaS 傳統實例上執行，建議您將它們從傳統遷移到 Resource Manager 部署模型。 如需指示，請參閱[平臺支援將 IaaS 資源從傳統遷移至 Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)。

#### <a name="detailed-audit-logs"></a>詳細的審核記錄

對於牽涉到遠端桌面存取的案例，您可以使用 Windows 事件記錄來檢查 Microsoft 工程師所採取的動作。 請考慮使用 Azure 資訊安全中心來收集事件記錄檔，並將資料複製到您的工作區進行分析。 如需詳細資訊，請參閱[Azure 資訊安全中心中的資料收集](../../security-center/security-center-enable-data-collection.md)。

## <a name="supported-services-and-scenarios-in-preview"></a>預覽中支援的服務和案例

下列服務目前在客戶加密箱的預覽狀態：

- Azure 儲存體

- Azure SQL DB

- Azure 資料總管

- 虛擬機器（現在也涵蓋記憶體傾印和受控磁片的存取權）

- Azure 訂用帳戶傳輸

若要為您的組織啟用這些預覽供應專案的客戶加密箱，請註冊[Azure 公開預覽客戶加密箱](https://aka.ms/customerlockbox/insiderprogram)。


## <a name="exclusions"></a>排除

在下列工程支援案例中，不會觸發客戶加密箱要求：

- Microsoft 工程師必須執行超出標準作業程式的活動。 例如，在非預期或無法預期的案例中復原或還原服務。

- Microsoft 工程師會在進行疑難排解時存取 Azure 平臺，且不小心可以存取客戶資料。 例如，Azure 網路小組會執行在網路裝置上產生封包捕捉的疑難排解。 不過，如果客戶在傳輸時加密資料，工程師就無法讀取資料。

## <a name="next-steps"></a>後續步驟

客戶加密箱會自動提供給具有最基本**開發人員**之[Azure 支援方案](https://azure.microsoft.com/support/plans/)的所有客戶。

當您有合格的支援方案時，您不需要採取任何動作就能啟用客戶加密箱。 如果需要此動作來進行從組織中的人員提出的支援票證，則 Microsoft 工程師會起始客戶加密箱要求。
