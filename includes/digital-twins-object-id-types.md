---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76268280"
---
`objectIdType` (或 **物件識別碼類型**) 係指給予角色的身分識別類型。 除了 `DeviceId` 和 `UserDefinedFunctionId` 類型之外，物件識別碼類型還會對應至 Azure Active Directory 物件的屬性。

下表包含 Azure Digital Twins 中支援的物件識別碼類型：

| 類型 | 描述 |
| --- | --- |
| UserId | 會為使用者指派角色。 |
| deviceId | 會為裝置指派角色。 |
| DomainName | 會為網域名稱指派角色。 每個具有指定網域名稱的使用者，都會有對應角色的存取權限。 |
| TenantId | 會為租用戶指派角色。 每個屬於指定 Azure AD 租用戶識別碼的使用者，都會有對應角色的存取權限。 |
| ServicePrincipalId | 會為服務主體物件識別碼指派角色。 |
| UserDefinedFunctionId | 會為使用者定義函式 (UDF) 指派角色。 |