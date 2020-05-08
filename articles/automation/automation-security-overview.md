---
title: Azure 自動化中的驗證簡介
description: 本文概述 Azure 自動化中的自動化安全性和自動化帳戶可用的不同驗證方法。
keywords: 自動化安全性、安全的自動化、自動化驗證
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 3d3dbaad18f6acbe1ddf17d81f54e4232c838dd7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787408"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Azure 自動化中的驗證簡介

Azure 自動化可讓您針對 Azure、內部部署以及其他雲端提供者 (例如 Amazon Web Services (AWS)) 的資源自動執行工作。 為了讓 Runbook 執行其必要動作，其必須有權能以訂用帳戶內的最少必要權限，安全地存取資源。

本文將涵蓋 Azure 自動化支援的各種驗證案例，以及如何根據您需要管理的環境或環境開始著手。  

## <a name="automation-account-overview"></a>自動化帳戶概觀

當您第一次啟動 Azure 自動化時，您必須建立至少一個自動化帳戶。 自動化帳戶可讓您將您的自動化資源 (Runbook、資產、組態) 與其他自動化帳戶中包含的資源區隔開來。 您可以使用自動化帳戶將資源分成個別的邏輯環境。 例如，您可能會針對開發、生產和內部部署環境各自使用一個帳戶。 Azure 自動化帳戶與 Microsoft 帳戶或您在 Azure 訂用帳戶中建立的帳戶不同。

每個自動化帳戶的自動化資源都會與單一 Azure 區域相關聯，但自動化帳戶可管理訂用帳戶中的所有資源。 在不同區域建立自動化帳戶的主要原因會是，若您擁有需要區隔資料和資源到特定區域的原則時。

在 Azure 自動化中使用 Azure Resource Manager 和 Azure Cmdlet 針對資源所執行的工作，皆必須使用 Azure Active Directory 組織身分識別的認證型驗證向 Azure 進行驗證。 中的執行身分帳戶 Azure 自動化提供使用 Azure Cmdlet 在 Azure 中管理資源的驗證。 當您建立執行身分帳戶時，它會在 Azure Active Directory （AD）中建立新的服務主體使用者，並在訂用帳戶層級將參與者角色指派給此使用者。 針對在 Azure 虛擬機器上使用混合式 Runbook 背景工作角色的 runbook，您可以使用[Runbook 驗證搭配受控](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)識別，而不是執行身分帳戶來驗證您的 Azure 資源。

執行身分帳戶的服務主體預設沒有讀取 Azure AD 的許可權。 如果您想要新增讀取或管理 Azure AD 的許可權，您必須在 [ **API 許可權**] 下授與服務主體的許可權。 若要深入瞭解，請參閱[新增存取 Web api 的許可權](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

Azure Resource Manager 提供了角色型存取控制來對 Azure AD 使用者帳戶和執行身分帳戶授與允許的動作，並驗證該服務主體。 若要取得有助於開發自動化權限管理模型的進一步資訊，請閱讀 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)一文。  

在您資料中心的混合式 Runbook 背景工作角色上執行的 runbook 或其他雲端環境（例如 AWS）中的計算服務，無法使用通常用於向 Azure 資源進行驗證的 runbook 所使用的相同方法。 這是因為這些資源是在 Azure 外執行，因此需要在自動化中定義自己的安全性認證才能向它們要在本機存取的資源進行驗證。 如需使用 runbook worker 進行 runbook 驗證的詳細資訊，請參閱[驗證混合式 runbook 背景工作角色的](automation-hrw-run-runbooks.md)runbook。 

## <a name="next-steps"></a>後續步驟

* [從 Azure 入口網站建立自動化帳戶](automation-create-standalone-account.md)。

* [使用 Azure Resource Manager 範本建立自動化帳戶](automation-create-account-template.md)。

* [使用 Amazon Web Services 進行驗證（AWS）](automation-config-aws-account.md)。
