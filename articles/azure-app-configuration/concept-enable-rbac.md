---
title: 使用 Azure Active Directory 來授權 Azure 應用程式組態的存取權
description: 啟用 Azure RBAC 以授權存取您的 Azure 應用程式組態實例
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9c749dbb1cc78bf06dfe665875bc05f75152778b
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862134"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>使用 Azure Active Directory 來授權 Azure 應用程式組態的存取權
除了使用以雜湊為基礎的訊息驗證碼 (HMAC) ，Azure 應用程式組態支援使用 Azure Active Directory (Azure AD) ，來授權應用程式設定實例的要求。  Azure AD 可讓您使用 Azure 角色型存取控制 (Azure RBAC) 將許可權授與安全性主體。  安全性主體可能是使用者、 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) 或 [應用程式服務主體](../active-directory/develop/app-objects-and-service-principals.md)。  若要深入了解角色和角色指派，請參閱[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概觀
安全性主體用來存取應用程式設定資源的要求必須獲得授權。 使用 Azure AD 時，對資源的存取就是兩個步驟的程式：
1. 安全性主體的身分識別已通過驗證，並傳回 OAuth 2.0 權杖。  要求權杖的資源名稱是 `https://login.microsoftonline.com/{tenantID}`，其中 `{tenantID}` 符合服務主體所屬的 Azure Active Directory 租用戶識別碼。
2. 權杖會當做要求的一部分傳遞給應用程式組態服務，以授權指定資源的存取權。

驗證步驟要求應用程式要求在執行階段包含 OAuth 2.0 存取權杖。  如果應用程式是在 Azure 實體內執行，例如 Azure Functions 應用程式、Azure Web 應用程式或 Azure VM，則可以使用受控識別來存取資源。  若要了解如何驗證受控識別對 Azure 應用程式組態所提出的要求，請參閱[使用適用於 Azure 資源的 Azure Active Directory 和受控識別來驗證 Azure 應用程式組態資源的存取權](howto-integrate-azure-managed-service-identity.md)。

授權步驟需要將一或多個 Azure 角色指派給安全性主體。 Azure 應用程式組態提供的 Azure 角色包含應用程式設定資源的許可權集。 指派給安全性主體的角色會決定提供給主體的權限。 如需有關 Azure 角色的詳細資訊，請參閱 [適用于 Azure 應用程式組態的 azure 內建角色](#azure-built-in-roles-for-azure-app-configuration)。 

## <a name="assign-azure-roles-for-access-rights"></a>指派存取權限的 Azure 角色
Azure Active Directory (Azure AD) 透過 [AZURE RBAC (的 azure 角色型存取控制 ](../role-based-access-control/overview.md)，來授與受保護資源的存取權限。

當 Azure 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取範圍限定為應用程式組態資源。 Azure AD 安全性主體可以是使用者或應用程式服務主體，或是 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>適用于 Azure 應用程式組態的 Azure 內建角色
Azure 提供下列 Azure 內建角色，以使用 Azure AD 和 OAuth 來授權存取應用程式設定資料：

- **應用程式組態資料擁有者**：使用此角色可提供應用程式組態資料的讀取/寫入/刪除存取權。 這不會授與應用程式組態資源的存取權。
- **應用程式組態資料讀者**：使用此角色可提供應用程式組態資料的讀取存取權。 這不會授與應用程式組態資源的存取權。
- **參與者**：使用此角色來管理應用程式組態資源。 雖然可以使用存取金鑰來存取應用程式設定資料，但此角色不會使用 Azure AD 來授與資料的直接存取權。
- **讀者**：使用此角色可提供應用程式組態資源的讀取存取權。 這不會授與資源存取金鑰的存取權，也不會授與應用程式組態中所儲存資料的存取權。

> [!NOTE]
> 目前，Azure 入口網站僅支援 HMAC 驗證來存取應用程式設定資料。 不支援 Azure AD 驗證。 因此，Azure 入口網站的使用者需要 *參與者* 角色來取得應用程式設定資源的存取金鑰。 授與 *應用程式設定資料讀取器* 或 *應用程式設定資料擁有* 者角色不會影響透過入口網站的存取。

## <a name="next-steps"></a>後續步驟
深入了解使用[受控識別](howto-integrate-azure-managed-service-identity.md)管理您的應用程式組態服務。
