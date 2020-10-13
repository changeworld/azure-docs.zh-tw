---
title: Azure 自動化帳戶驗證概觀
description: 此文章提供 Azure 自動化帳戶驗證的概觀。
keywords: 自動化安全性、安全的自動化、自動化驗證
services: automation
ms.subservice: process-automation
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: bcb5f61c93bd4c3ff7c0f81ae808807f7deb71df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766095"
---
# <a name="automation-account-authentication-overview"></a>自動化帳戶驗證總覽

Azure 自動化可讓您針對 Azure、內部部署以及其他雲端提供者 (例如 Amazon Web Services (AWS)) 的資源自動執行工作。 您可以使用 runbook 將工作自動化，或使用混合式 Runbook 背景工作角色（如果您有可在 Azure 之外管理的商務或操作程式）。 在上述任一環境中工作時，都需要許可權，才能以所需的最基本許可權安全地存取資源。

此文章將介紹 Azure 自動化支援的各種驗證案例，並說明如何根據您要管理的一或多個環境來開始使用。

## <a name="automation-account"></a>自動化帳戶

當您第一次啟動 Azure 自動化時，您必須建立至少一個自動化帳戶。 自動化帳戶可讓您將您的自動化資源、Runbook、資產、設定與其他帳戶中的資源區隔開來。 您可以使用自動化帳戶將資源分成個別的邏輯環境。 例如，您可能會針對開發、生產和內部部署環境各自使用一個帳戶。 Azure 自動化帳戶與 Microsoft 帳戶或您在 Azure 訂用帳戶中建立的帳戶不同。 如需建立自動化帳戶的簡介，請參閱[建立自動化帳戶](automation-quickstart-create-account.md)。

## <a name="automation-resources"></a>自動化資源

每個自動化帳戶的自動化資源都會與單一 Azure 區域相關聯，但該帳戶可管理 Azure 訂用帳戶中的所有資源。 在不同區域中建立自動化帳戶的主要原因是，如果您有需要將資料和資源隔離到特定區域的原則。

在 Azure 自動化中使用 Azure Resource Manager 和 PowerShell Cmdlet 針對資源所執行的所有工作，都必須使用 Azure Active Directory (Azure AD) 組織身分識別的認證型驗證向 Azure 進行驗證。

## <a name="run-as-accounts"></a>執行身分帳戶

Azure 自動化中的執行身份帳戶提供驗證，以管理部署在傳統部署模型上的 Azure Resource Manager 資源或資源。 Azure 自動化中有兩種類型的執行帳戶：

* Azure 執行身分帳戶
* Azure 傳統執行身分帳戶

若要深入瞭解這兩種部署模型，請參閱 [Resource Manager 和傳統部署](../azure-resource-manager/management/deployment-models.md)。

>[!NOTE]
>Azure 雲端方案提供者 (CSP) 訂用帳戶僅支援 Azure Resource Manager 模型。 非 Azure Resource Manager 服務不在方案中。 當您使用 CSP 訂用帳戶時，並不會建立 Azure 傳統執行身分帳戶，但會建立 Azure 執行身分帳戶。 若要深入了解 CSP 訂用帳戶，請參閱 [CSP 訂用帳戶中可用的服務](/azure/cloud-solution-provider/overview/azure-csp-available-services)。

### <a name="run-as-account"></a>執行身分帳戶

Azure 執行身份帳戶會根據 Azure 的 Azure Resource Manager 部署和管理服務來管理 Azure 資源。

當您建立執行帳戶時，它會執行下列工作：

* 建立具有自我簽署憑證的 Azure AD 應用程式、為 Azure AD 中的應用程式建立服務主體帳戶，並為您目前訂用帳戶中的帳戶指派「 [參與者](../role-based-access-control/built-in-roles.md#contributor) 」角色。 您可以將憑證設定變更為擁有者或任何其他角色。 如需詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。

* 在指定的自動化帳戶中，建立名為 `AzureRunAsCertificate` 的自動化憑證資產。 憑證資產會保存 Azure AD 應用程式所使用的憑證私密金鑰。

* 在指定的自動化帳戶中，建立名為 `AzureRunAsConnection` 的自動化連線資產。 連線資產會保存應用程式識別碼、租用戶識別碼、訂閱識別碼，以及憑證指紋。

### <a name="azure-classic-run-as-account"></a>Azure 傳統執行身分帳戶

Azure 傳統執行身份帳戶會根據傳統部署模型來管理 Azure 傳統資源。 您必須是訂用帳戶的共同管理員，才能建立或更新此類型的執行身分帳戶。

當您建立 Azure 傳統執行帳戶時，它會執行下列工作。

* 在訂用帳戶中建立管理憑證。

* 在指定的自動化帳戶中，建立名為 `AzureClassicRunAsCertificate` 的自動化憑證資產。 憑證資產會保存管理憑證所使用的憑證私密金鑰。

* 在指定的自動化帳戶中，建立名為 `AzureClassicRunAsConnection` 的自動化連線資產。 連線資產會保存訂用帳戶名稱、訂用帳戶識別碼，以及憑證資產名稱。

>[!NOTE]
>當您建立自動化帳戶時，依預設並不會建立 Azure 傳統執行身分帳戶。 此帳戶是依照 [管理執行身份帳戶](manage-runas-account.md#create-a-run-as-account-in-azure-portal) 一文所述的步驟來個別建立。

## <a name="service-principal-for-run-as-account"></a>執行身分帳戶的服務主體

執行身分帳戶的服務主體預設沒有讀取 Azure AD 的權限。 如果您想要新增讀取或管理 Azure AD 的權限，您必須在 [API 權限] 下授與服務主體的權限。 若要深入瞭解，請參閱 [新增許可權以存取您的 WEB API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)。

## <a name="role-based-access-control"></a>角色型存取控制

Azure Resource Manager 提供了角色型存取控制來對 Azure AD 使用者帳戶和執行身分帳戶授與允許的動作，並驗證該服務主體。 若要取得有助於開發自動化權限管理模型的進一步資訊，請閱讀 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)一文。

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook 驗證搭配混合式 Runbook 背景工作角色

在資料中心的混合式 Runbook 背景工作角色上或針對其他雲端環境 (例如 AWS) 中的運算服務所執行的 Runbook，不能使用向 Azure 資源進行驗證的 Runbook 通常會使用的相同方法。 這是因為這些資源是在 Azure 外執行，因此需要在自動化中定義自己的安全性認證才能向它們要在本機存取的資源進行驗證。 如需 Runbook 驗證搭配 Runbook 背景工作角色的詳細資訊，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。

對於在 Azure VM 上使用混合式 Runbook 背景工作角色的 Runbook，您可以使用 [Runbook 驗證搭配受控識別](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)來向 Azure 資源進行驗證，而非使用執行身分帳戶。

## <a name="next-steps"></a>後續步驟

* 若要從 Azure 入口網站建立自動化帳戶，請參閱[建立獨立 Azure 自動化帳戶](automation-create-standalone-account.md)。
* 如果您想要使用範本來建立帳戶，請參閱[使用 Azure Resource Manager 範本建立自動化帳戶](quickstart-create-automation-account-template.md)。
* 如需使用 Amazon Web Services 的驗證，請參閱[使用 Amazon Web Services 驗證 Runbook](automation-config-aws-account.md)。