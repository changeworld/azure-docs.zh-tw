---
title: 管理單位管理（預覽） - Azure AD |微軟文檔
description: 使用管理單位在 Azure Active Directory 中進行更細微的權限委派
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028419"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure 活動目錄中的管理單位管理（預覽版）

本文介紹 Azure 活動目錄 （Azure AD） 中的管理單位。 管理單位是 Azure AD 資源，可以是其他 Azure AD 資源的容器。 在此預覽版本中，這些資源僅能是使用者。 例如，管理單位範圍的使用者帳戶管理員可以更新設定檔資訊、重置密碼，並僅為其管理單元中的使用者分配許可證。

可以使用管理單位對使用者子集委派管理許可權，並將策略應用於使用者子集。 可以使用管理單位將許可權委派給區域管理員或在細微性級別設置策略。

## <a name="deployment-scenario"></a>部署案例

行政單位在具有獨立部門的組織中非常有用。 以一所由許多自治學校（商學院、工程學院等）組成的大型大學為例，這些學校都有自己的 IT 管理員，他們控制訪問、管理使用者和為其學校設置策略。 中央管理員可以為商學院創建一個管理單位，並且僅由商學院的學生和員工填充。 然後，中央管理員可以將商學院 IT 人員添加到作用域角色，該角色僅授予商學院管理單元中的 Azure AD 使用者管理許可權。

## <a name="license-requirements"></a>授權需求

要使用管理單位，需要為每個管理單位管理員提供 Azure 活動目錄高級許可證。有關詳細資訊，請參閱使用[Azure AD 高級版入門](../fundamentals/active-directory-get-started-premium.md)。

## <a name="managing-administrative-units"></a>管理管理單位

在此預覽版本中，創建和管理管理單位的唯一方法是使用 Windows PowerShell Cmdlet 的 Azure 活動目錄模組[，如使用管理單位](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)中所述

有關軟體要求和安裝 Azure AD 模組的詳細資訊，以及有關 Azure AD 模組 Cmdlet 以管理管理管理單元（包括語法、參數說明和示例）的參考資訊，請參閱[Azure 活動目錄 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

## <a name="next-steps"></a>後續步驟

[Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)
