---
title: 使用者流程的 API 連接器程式碼範例 - Azure AD
description: 自助式註冊流程 (適用於 Azure Active Directory 外部身分識別) 中的 API 連接器程式碼範例。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87906861"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>適用於外部身分識別自助式註冊的範例

下列表格提供程式碼範例的連結，這些程式碼範例可讓您使用 [API 連接器](api-connectors-overview.md)，在您的自助式註冊使用者流程中運用 Web API。

## <a name="api-connector-azure-function-quickstarts"></a>API 連接器 Azure Function 快速入門

| 範例                                                                                                                          | 描述                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | 此 .NET Core Azure Function 範例示範如何將註冊限制為特定的租用戶網域，並驗證使用者提供的資訊。 |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | 此 Node.js Azure Function 範例示範如何將註冊限制為特定的租用戶網域，並驗證使用者提供的資訊。  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | 此 Python Azure Function 範例示範如何將註冊限制為特定的租用戶網域，並驗證使用者提供的資訊。    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>自訂核准工作流程

| 範例 | 描述 |
|--------| ----------- |
| [手動核准工作流程](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | 這個範例示範端對端核准工作流程如何管理在自助式註冊中建立來賓使用者帳戶的作業 |

## <a name="identity-verification"></a>身分識別驗證

| 範例                                                                                                            | 描述                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | 這個範例示範如何使用 API 連接器與 IDology 整合，在進行自助式註冊時驗證使用者身分識別。 |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | 這個範例示範如何使用 API 連接器與 Experian 整合，在進行自助式註冊時驗證使用者身分識別。 |
