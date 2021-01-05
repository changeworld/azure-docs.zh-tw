---
title: 透過 Azure Active Directory 授與存取權
description: 本文提供使用 Azure Active Directory 授權存取 Azure SignalR Service 資源的相關資訊。
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797478"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>使用 Azure Active Directory 授權存取 Azure SignalR Service 資源
Azure SignalR Service 支援使用 Azure Active Directory (Azure AD) 來授權 Azure SignalR Service 資源的要求。 使用 Azure AD 時，您可以使用角色型存取控制 (RBAC) ，將許可權授與安全性主體（可能是使用者或應用程式服務主體）。 若要深入瞭解角色和角色指派，請參閱 [瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>總覽
當安全性主體 (應用程式) 嘗試存取 Azure SignalR Service 資源時，要求必須獲得授權。 使用 Azure AD，對資源的存取是兩個步驟的程序。 

 1. 首先，會驗證安全性主體的身分識別，並傳回 OAuth 2.0 權杖。 要求權杖的資源名稱是 `https://signalr.azure.com/` 。
 2. 接下來，權杖會作為要求的一部分傳遞給 Azure SignalR Service，以授權存取指定的資源。

驗證步驟要求應用程式要求在執行階段包含 OAuth 2.0 存取權杖。 如果您的中樞伺服器是在 azure 實體中執行，例如 Azure VM、虛擬機器擴展集或 Azure 函式應用程式，則可以使用受控識別來存取資源。 若要瞭解如何向 Azure SignalR Service 驗證受控識別所提出的要求，請參閱 [使用 Azure 資源的 Azure Active Directory 和受控識別來驗證 Azure SignalR Service 資源的存取權](authenticate-managed-identity.md)。 

授權步驟需要將一或多個 RBAC 角色指派給安全性主體。 Azure SignalR Service 提供包含 Azure SignalR 資源許可權集的 RBAC 角色。 指派給安全性主體的角色會決定主體將擁有的許可權。 如需 RBAC 角色的詳細資訊，請參閱 [適用于 Azure SignalR Service 的 Azure 內建角色](#azure-built-in-roles-for-azure-signalr-service)。 

未在 Azure 實體內執行的 SignalR Hub 伺服器也可以使用 Azure AD 授權。 若要瞭解如何要求存取權杖，並使用它來授權 Azure SignalR Service 資源的要求，請參閱 [使用應用程式的 Azure AD 驗證對 Azure SignalR Service 的存取](authenticate-application.md)。 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>適用于 Azure SignalR Service 的 Azure 內建角色

- [SignalR 應用程式伺服器]
- [SignalR Service 讀者]
- [SignalR Service 擁有者]

## <a name="assign-rbac-roles-for-access-rights"></a>指派存取權限的 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure SignalR Service 會定義一組 Azure 內建角色，其中包含一組用來存取 Azure SignalR Service 的常用許可權，而且您也可以定義用來存取資源的自訂角色。

當 RBAC 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取權可以設定為訂用帳戶層級、資源群組或任何 Azure SignalR Service 資源的範圍。 Azure AD 的安全性主體可能是使用者、應用程式或 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-roles-for-azure-signalr-service"></a>Azure SignalR Service 的內建角色
Azure 提供下列 Azure 內建角色，以使用 Azure AD 和 OAuth 來授權存取 Azure SignalR Service 資源：

### <a name="signalr-app-server"></a>SignalR 應用程式伺服器

您可以使用此角色來授與存取權，以取得用來簽署用戶端權杖的暫時存取金鑰。

### <a name="signalr-serverless-contributor"></a>SignalR 無伺服器參與者

使用此角色來授與存取權，以便直接從 Azure SignalR Service 取得用戶端權杖。

## <a name="next-steps"></a>後續步驟

請參閱下列相關文章：

- [驗證具有 Azure AD 的應用程式以存取 Azure SignalR Service](authenticate-application.md)
- [使用 Azure AD 來驗證受控識別，以存取 Azure SignalR Service](authenticate-managed-identity.md)