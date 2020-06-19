---
title: Azure 自動化帳戶驗證概觀
description: 此文章提供 Azure 自動化帳戶驗證的概觀。
keywords: 自動化安全性、安全的自動化、自動化驗證
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2b9f705c73e667f34e46fdeed3c80af1e65fb12
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830101"
---
# <a name="automation-account-authentication-overview"></a>自動化帳戶驗證總覽

Azure 自動化可讓您針對 Azure、內部部署以及其他雲端提供者 (例如 Amazon Web Services (AWS)) 的資源自動執行工作。 您可以使用 Runbook 來自動化您的工作，或使用混合式 Runbook 背景工作角色 (如果您有非 Azure 工作要管理)。 任一環境都需要權限，才能以 Azure 訂用帳戶內所需的最低權限來安全地存取資源。

此文章將介紹 Azure 自動化支援的各種驗證案例，並說明如何根據您要管理的一或多個環境來開始使用。

## <a name="automation-account"></a>自動化帳戶 

當您第一次啟動 Azure 自動化時，您必須建立至少一個自動化帳戶。 自動化帳戶可讓您將您的自動化資源、Runbook、資產、設定與其他帳戶中的資源區隔開來。 您可以使用自動化帳戶將資源分成個別的邏輯環境。 例如，您可能會針對開發、生產和內部部署環境各自使用一個帳戶。 Azure 自動化帳戶與 Microsoft 帳戶或您在 Azure 訂用帳戶中建立的帳戶不同。 如需建立自動化帳戶的簡介，請參閱[建立自動化帳戶](automation-quickstart-create-account.md)。

## <a name="automation-resources"></a>自動化資源

每個自動化帳戶的自動化資源都會與單一 Azure 區域相關聯，但該帳戶可管理 Azure 訂用帳戶中的所有資源。 在不同區域中建立自動化帳戶的主要原因是，如果您有需要將資料和資源隔離到特定區域的原則。

在 Azure 自動化中使用 Azure Resource Manager 和 PowerShell Cmdlet 針對資源所執行的所有工作，都必須使用 Azure Active Directory (Azure AD) 組織身分識別的認證型驗證向 Azure 進行驗證。 

## <a name="run-as-account"></a>執行身分帳戶

Azure 自動化中的執行身分帳戶可用來提供驗證，以使用 PowerShell Cmdlet 來管理 Azure 資源。 當您建立執行身分帳戶時，其會在 Azure AD 中建立新的服務主體使用者，並在訂用帳戶層級將參與者角色指派給這位使用者。 對於在 Azure VM 上使用混合式 Runbook 背景工作角色的 Runbook，您可以使用 [Runbook 驗證搭配受控識別](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)來向 Azure 資源進行驗證，而非使用執行身分帳戶。

## <a name="service-principal-for-run-as-account"></a>執行身分帳戶的服務主體

執行身分帳戶的服務主體預設沒有讀取 Azure AD 的權限。 如果您想要新增讀取或管理 Azure AD 的權限，您必須在 [API 權限] 下授與服務主體的權限。 若要深入了解，請參閱[新增存取 Web API 的權限](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="role-based-access-control"></a>角色型存取控制

Azure Resource Manager 提供了角色型存取控制來對 Azure AD 使用者帳戶和執行身分帳戶授與允許的動作，並驗證該服務主體。 若要取得有助於開發自動化權限管理模型的進一步資訊，請閱讀 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)一文。  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook 驗證搭配混合式 Runbook 背景工作角色 

在資料中心的混合式 Runbook 背景工作角色上或針對其他雲端環境 (例如 AWS) 中的運算服務所執行的 Runbook，不能使用向 Azure 資源進行驗證的 Runbook 通常會使用的相同方法。 這是因為這些資源是在 Azure 外執行，因此需要在自動化中定義自己的安全性認證才能向它們要在本機存取的資源進行驗證。 如需 Runbook 驗證搭配 Runbook 背景工作角色的詳細資訊，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。 

## <a name="next-steps"></a>後續步驟

* 若要從 Azure 入口網站建立自動化帳戶，請參閱[建立獨立 Azure 自動化帳戶](automation-create-standalone-account.md)。
* 如果您想要使用範本來建立帳戶，請參閱[使用 Azure Resource Manager 範本建立自動化帳戶](automation-create-account-template.md)。
* 如需使用 Amazon Web Services 的驗證，請參閱[使用 Amazon Web Services 驗證 Runbook](automation-config-aws-account.md)。