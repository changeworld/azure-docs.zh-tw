---
title: Azure 受控 HSM 存取控制
description: 管理 Azure 受管理 HSM 和金鑰的存取權限。 涵蓋受控 HSM 的驗證與授權模型，以及如何保護您的 Hsm。
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a21d0db383e8c563f0b187061a95ac818dd2a4f0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994546"
---
# <a name="managed-hsm-access-control"></a>受控 HSM 存取控制

> [!NOTE]
> Key Vault 資源提供者支援兩種資源類型：保存 **庫** 和 **受管理的 hsm**。 本文所述的存取控制僅適用于 **受管理的 hsm**。 若要深入瞭解受控 HSM 的存取控制，請參閱 [使用 Azure 角色型存取控制來提供 Key Vault 金鑰、憑證和秘密的存取權](../general/rbac-guide.md)。

Azure Key Vault 受控 HSM 是保護加密金鑰的雲端服務。 因為這項資料是敏感且商務關鍵性的，所以您必須只允許已獲授權的應用程式和使用者存取受管理的 Hsm，以保護對這些 Hsm 的存取。 本文提供受控 HSM 存取控制模型的總覽。 它會說明驗證和授權，並說明如何保護對受控 Hsm 的存取。

## <a name="access-control-model"></a>存取控制模型

受控 HSM 的存取權可透過兩個介面來控制： **管理平面** 和 **資料平面**。 管理平面是您管理 HSM 本身的位置。 此平面中的作業包括建立和刪除受管理的 hsm，以及抓取受管理的 HSM 屬性。 資料平面是您使用受管理的 HSM 中所儲存資料的位置，也就是 HSM 支援的加密金鑰。 您可以新增、刪除、修改和使用金鑰來執行密碼編譯作業、管理角色指派來控制金鑰的存取、建立完整的 HSM 備份、還原完整備份，以及從資料平面介面管理安全性網域。

若要在任一平面存取受控 HSM，所有呼叫端都必須有適當的驗證和授權。 驗證會建立呼叫者的身分識別。 授權則會判斷呼叫者可以執行哪些作業。 呼叫端可以是 Azure Active Directory 使用者、群組、服務主體或受控識別中定義的任何一個 [安全性主體](../../role-based-access-control/overview.md#security-principal) 。

這兩個平面都會使用 Azure Active Directory 進行驗證。 針對授權，他們使用不同的系統，如下所示
- 管理平面使用 Azure 角色型存取控制--Azure RBAC--建置於 Azure 上的授權系統 Azure Resource Manager 
- 資料平面會使用受控 hsm 層級 RBAC (受控 HSM 本機 RBAC) --在受控 HSM 層級實行並強制執行的授權系統。

建立受控 HSM 時，要求者也會提供資料平面系統管理員清單， (所有 [安全性主體](../../role-based-access-control/overview.md#security-principal) 都) 支援。 只有這些系統管理員能夠存取受管理的 HSM 資料平面，以執行重要作業，以及管理 (受控 HSM 本機 RBAC) 的資料平面角色指派。

這兩個平面的許可權模型都會使用相同的語法 (RBAC) ，但會在不同的層級強制執行，而且角色指派會使用不同的範圍。 管理平面 RBAC 會由 Azure Resource Manager 強制執行，而資料平面 RBAC 則由受控 HSM 本身強制執行。

> [!IMPORTANT]
> 授與安全性主體管理平面對受控 HSM 的存取權，並不會授與他們存取資料平面的任何存取權，以存取金鑰或資料層角色指派管理的 HSM 本機 RBAC) 。 這項隔離的設計是為了避免不小心地展開影響存取儲存在受控 HSM 中之金鑰的許可權。

例如，訂用帳戶管理員 (因為訂用帳戶中的所有資源都有「參與者」許可權) 可以在其訂用帳戶中刪除受控 HSM，但如果他們沒有透過受控 HSM 本機 RBAC 授與的資料平面存取權，則他們無法存取金鑰或管理管理 HSM 中的角色指派，以授與自己或其他人對資料平面的存取權。

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 驗證

當您在 Azure 訂用帳戶中建立受控 HSM 時，它會自動與訂用帳戶的 Azure Active Directory 租使用者建立關聯。 這兩個平面中的所有呼叫者都必須在此租使用者中註冊，並進行驗證以存取受管理的 HSM。

應用程式會在呼叫任一平面之前，使用 Azure Active Directory 進行驗證。 應用程式可以根據應用程式類型來使用任何 [支援的驗證方法](../../active-directory/develop/authentication-scenarios.md) 。 應用程式會取得平面中的資源權杖以取得存取權。 視 Azure 環境而定，資源會是管理或資料平面中的端點。 應用程式會使用權杖，並將 REST API 要求傳送至受管理的 HSM 端點。 若要深入了解，請參閱[整個驗證流程](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

針對這兩個平面使用單一驗證機制有幾個優點：

- 組織可以集中控制其組織內所有受控 Hsm 的存取權。
- 如果使用者離開，他們會立即失去組織中所有受控 Hsm 的存取權。
- 組織可以使用 Azure Active Directory 中的選項來自訂驗證，例如啟用多重要素驗證以增加安全性。

## <a name="resource-endpoints"></a>資源端點

安全性主體會透過端點來存取平面。 這兩個平面的存取控制各自獨立運作。 若要授與應用程式存取權以使用受控 HSM 中的金鑰，您可以使用受控 HSM 本機 RBAC 來授與資料平面存取權。 若要授與使用者對受控 HSM 資源的存取權，以建立、讀取、刪除、移動受管理的 Hsm，以及編輯您使用 Azure RBAC 的其他屬性和標籤。

下表顯示管理和資料平面的端點。

| 存取&nbsp;平面 | 存取端點 | 作業 | 存取控制機制 |
| --- | --- | --- | --- |
| 管理平面 | **全域：**<br> management.azure.com:443<br> | 建立、讀取、更新、刪除和移動受控 Hsm<br>設定受控 HSM 標記 | Azure RBAC |
| 資料平面 | **全域：**<br> &lt;hsm 名稱 &gt; . vault.azure.net:443<br> | **金鑰**：解密、加密、<br> 解除包裝、包裝、驗證、簽署、取得、列出、更新、建立、匯入、刪除、備份、還原、清除<br/><br/> **資料平面角色管理 (受控 HSM 本機 RBAC) * * *：列出角色定義、指派角色、刪除角色指派、定義自訂角色 <br/> <br/> **備份/還原 **：備份、還原、檢查狀態備份/還原作業 <br/> <br/> **安全性網域 * *：下載並上傳安全性網域 | 受控 HSM 本機 RBAC |
|||||
## <a name="management-plane-and-azure-rbac"></a>管理平面和 Azure RBAC

在管理平面中，您會使用 Azure RBAC 來授權呼叫者可以執行的作業。 在 RBAC 模型中，每個 Azure 訂用帳戶都有 Azure Active Directory 的實例。 您可以對來自該目錄的使用者、群組和應用程式授與存取權。 授與存取權即可在 Azure 訂用帳戶中管理使用 Azure Resource Manager 部署模型的資源。 若要授與存取權，請使用 [Azure 入口網站](https://portal.azure.com/)、[Azure CLI](../../cli-install-nodejs.md)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx)。

您會在資源群組中建立金鑰保存庫，並使用 Azure Active Directory 來管理存取權。 您可以對使用者或群組授與在資源群組中管理金鑰保存庫的能力。 您可以藉由指派適當的 RBAC 角色，來授與特定範圍層級的存取權。 若要對使用者授與管理金鑰保存庫的權限，您可以在特定範圍對使用者指派預先定義的 `key vault Contributor` 角色。 您可以對 RBAC 角色指派下列範圍層級：

- **管理群組**：在訂用帳戶層級指派的 RBAC 角色，會套用至該管理群組中的所有訂用帳戶。
- 訂用帳戶：在訂用帳戶層級指派的 RBAC 角色，會套用至該訂用帳戶內的所有資源群組和資源。
- **資源群組**：在資源群組層級指派的 RBAC 角色，會套用至該資源群組內的所有資源。
- **特定資源**：針對特定資源指派的 RBAC 角色，則會套用至該資源。 在此情況下，資源會是特定的金鑰保存庫。

有數個預先定義的角色。 如果預先定義的角色不符合您的需求，您可以定義您自己的角色。 如需詳細資訊，請參閱[RBAC：內建角色](../../role-based-access-control/built-in-roles.md)。

## <a name="data-plane-and-managed-hsm-local-rbac"></a>資料平面和受控 HSM 本機 RBAC

您可以藉由指派角色來授與安全性主體存取權，以執行特定的金鑰作業。 針對每個角色指派，您必須指定要套用指派的角色和範圍。 針對受控 HSM 本機 RBAC，有兩個範圍可供使用。

- **"/" 或 "/keys"**： HSM 層級範圍。 在此範圍指派角色的安全性主體可針對受管理的 HSM 中)  (金鑰的所有物件，執行角色中定義的作業。
- **"/keys/ &lt; key-name &gt; "**：索引鍵層級範圍。 在此範圍指派角色的安全性主體，只能針對指定之金鑰的所有版本執行此角色中定義的作業。

## <a name="next-steps"></a>下一步

- 如需系統管理員的入門教學課程，請參閱 [什麼是受控 HSM？](overview.md)。
- 如需角色管理教學課程，請參閱 [受控 HSM 本機 RBAC](role-management.md)
- 如需受管理的 HSM 記錄之使用記錄的詳細資訊，請參閱 [受控 hsm 記錄](logging.md)。
