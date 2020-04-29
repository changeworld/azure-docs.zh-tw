---
title: 使用 Azure Active Directory 授權存取 Azure 應用程式組態
description: 啟用 RBAC 以授權存取您的 Azure 應用程式組態實例
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472618"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>使用 Azure Active Directory 授權存取 Azure 應用程式組態
Azure 應用程式組態支援使用 Azure Active Directory （Azure AD）來授權應用程式組態實例的要求。  Azure AD 可讓您使用角色型存取控制（RBAC）將許可權授與安全性主體。  安全性主體可能是使用者或[應用程式服務主體](../active-directory/develop/app-objects-and-service-principals.md)。  若要深入瞭解角色和角色指派，請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概觀
安全性主體（使用者或應用程式）為了存取應用程式組態資源所提出的要求必須獲得授權。  有了 Azure AD，對資源的存取是兩個步驟的程式。
1. 安全性主體的身分識別已通過驗證，並傳回 OAuth 2.0 權杖。  要求權杖的資源名稱會`https://login.microsoftonline.com/{tenantID}` `{tenantID}`與服務主體所屬的 Azure Active Directory 租使用者識別碼相符。
2. 權杖會當做要求的一部分傳遞給應用程式組態服務，以授權存取指定的資源。

驗證步驟要求應用程式要求在執行時間包含 OAuth 2.0 存取權杖。  如果應用程式是在 Azure 實體內執行，例如 Azure Functions 應用程式、Azure Web 應用程式或 Azure VM，則可以使用受控識別來存取資源。  若要瞭解如何驗證受控識別所提出的要求以 Azure 應用程式組態，請參閱[使用 Azure 資源的 Azure Active Directory 和受控識別來驗證 Azure 應用程式組態資源的存取權](howto-integrate-azure-managed-service-identity.md)。

授權步驟需要將一個或多個 RBAC 角色指派給安全性主體。 Azure 應用程式組態提供 RBAC 角色，其中包含應用程式組態資源的許可權集。 指派給安全性主體的角色會決定提供給主體的許可權。 如需 RBAC 角色的詳細資訊，請參閱[Azure 應用程式組態的內建 RBAC 角色](#built-in-rbac-roles-for-azure-app-configuration)。 

## <a name="assign-rbac-roles-for-access-rights"></a>指派存取權限的 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。

當 RBAC 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取範圍限定為應用程式組態資源。 Azure AD 的安全性主體可能是使用者、應用程式服務主體或[Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Azure 應用程式組態的內建 RBAC 角色
Azure 提供下列內建 RBAC 角色，以使用 Azure AD 和 OAuth 來授權應用程式組態資料的存取：

- Azure 應用程式組態資料擁有者：使用此角色可提供應用程式組態資源的讀取/寫入存取權。
- Azure 應用程式組態資料讀取器：使用此角色可授與應用程式組態資源的讀取權限。
- 參與者：使用此角色可讓系統管理員存取服務，而不需授與應用程式組態實例中所儲存之資料的存取權。

## <a name="next-steps"></a>後續步驟
深入瞭解如何使用[受控](howto-integrate-azure-managed-service-identity.md)識別來管理您的應用程式組態服務。