---
title: SAP 成功因素屬性參考 |微軟文檔
description: 瞭解成功因素支援的成功因數的哪些屬性-HR 驅動的預配
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522351"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP 成功因素屬性參考

## <a name="supported-successfactors-entities-and-attributes"></a>支援成功因素實體和屬性

下表捕獲以下兩個預配應用支援的 SuccessFactors 屬性清單： 
* [活動目錄使用者預配的成功因素](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Azure AD 使用者預配的成功因素](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | 成功因素實體                  | 成功因素屬性     | 作業類型 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | 每人                              | 人 Id 外部             | 讀取           |
| 2  | 每人                              | 人 Id                     | 讀取           |
| 3  | 每人                              | 佩爾森烏伊德                | 讀取           |
| 4  | 人均                            | displayName                  | 讀取           |
| 5  | 人均                            | firstName                    | 讀取           |
| 6  | 人均                            | gender                       | 讀取           |
| 7  | 人均                            | lastName                     | 讀取           |
| 8  | 人均                            | middleName                   | 讀取           |
| 9  | 人均                            | 首選名稱                | 讀取           |
| 10 | User                                   | 位址Line1                 | 讀取           |
| 11 | User                                   | 位址Line2                 | 讀取           |
| 12 | User                                   | 位址LIne3                 | 讀取           |
| 13 | User                                   | 商務電話                | 讀取           |
| 14 | User                                   | 手機                    | 讀取           |
| 15 | User                                   | city                         | 讀取           |
| 16 | User                                   | country                      | 讀取           |
| 17 | User                                   | 自訂01                     | 讀取           |
| 18 | User                                   | 自訂02                     | 讀取           |
| 19 | User                                   | 自訂03                     | 讀取           |
| 20 | User                                   | 自訂04                     | 讀取           |
| 21 | User                                   | 自訂05                     | 讀取           |
| 22 | User                                   | 自訂06                     | 讀取           |
| 23 | User                                   | 自訂07                     | 讀取           |
| 24 | User                                   | 自訂08                     | 讀取           |
| 25 | User                                   | 自訂09                     | 讀取           |
| 26 | User                                   | 自訂10                     | 讀取           |
| 27 | User                                   | 自訂11                     | 讀取           |
| 28 | User                                   | 自訂12                     | 讀取           |
| 29 | User                                   | 自訂13                     | 讀取           |
| 30 | User                                   | 自訂14                     | 讀取           |
| 31 | User                                   | empId                        | 讀取           |
| 32 | User                                   | homePhone                    | 讀取           |
| 33 | User                                   | 工作家庭                    | 讀取           |
| 34 | User                                   | 暱稱                     | 讀取           |
| 35 | User                                   | state                        | 讀取           |
| 36 | User                                   | timeZone                     | 讀取           |
| 37 | User                                   | username                     | 讀取           |
| 38 | User                                   | 郵遞區號                      | 讀取           |
| 39 | 每部電話                               | 區號                     | 讀取           |
| 40 | 每部電話                               | countryCode                  | 讀取           |
| 41 | 每部電話                               | 擴充功能                    | 讀取           |
| 42 | 每部電話                               | phoneNumber                  | 讀取           |
| 43 | 每部電話                               | 電話類型                    | 讀取           |
| 44 | 每封電子郵件                               | emailAddress                 | 讀取、寫入    |
| 45 | 每封電子郵件                               | 電子郵件類型                    | 讀取           |
| 46 | 恩普就業                          | 第一次工作日期              | 讀取           |
| 47 | 恩普就業                          | 上次日期工作               | 讀取           |
| 48 | 恩普就業                          | userId                       | 讀取           |
| 49 | 恩普就業                          | 是臨時工人           | 讀取           |
| 50 | 恩普約伯                                 | 公司國家             | 讀取           |
| 51 | 恩普約伯                                 | empl狀態                   | 讀取           |
| 52 | 恩普約伯                                 | endDate                      | 讀取           |
| 53 | 恩普約伯                                 | startDate                    | 讀取           |
| 54 | 恩普約伯                                 | jobTitle                     | 讀取           |
| 55 | 恩普約伯                                 | position                     | 讀取           |
| 65 | 恩普約伯                                 | 自訂String13               | 讀取           |
| 56 | 恩普約伯                                 | 經理 Id                    | 讀取           |
| 57 | 恩普伯\.業務單位                   | 業務單位                 | 讀取           |
| 58 | 恩普伯\.業務單位                   | 企業單位Id               | 讀取           |
| 59 | 恩普約\.布公司                        | company                      | 讀取           |
| 60 | 恩普約\.布公司                        | companyId                    | 讀取           |
| 61 | EmpJob\.\.公司註冊國家 | 兩個查爾國家代碼           | 讀取           |
| 62 | 恩普職位\.成本中心                     | costCenter                   | 讀取           |
| 63 | 恩普職位\.成本中心                     | 成本中心Id                 | 讀取           |
| 64 | 恩普職位\.成本中心                     | 成本中心描述        | 讀取           |
| 65 | 恩普約\.布部                     | department                   | 讀取           |
| 66 | 恩普約\.布部                     | departmentId                 | 讀取           |
| 67 | 恩普約\.布部門                       | division                     | 讀取           |
| 68 | 恩普約\.布部門                       | 部門 Id                   | 讀取           |
| 69 | 恩普作業\.代碼                        | 作業代碼                      | 讀取           |
| 70 | 恩普作業\.代碼                        | 工作代碼Id                    | 讀取           |
| 71 | 恩普伯\.位置                       | 位置名稱                 | 讀取           |
| 72 | 恩普伯\.位置                       | 辦公地點位址        | 讀取           |
| 73 | 恩普伯\.位置                       | 辦公地點城市           | 讀取           |
| 74 | 恩普伯\.位置                       | 辦公地點自訂字串4  | 讀取           |
| 75 | 恩普伯\.位置                       | 辦公地點郵遞區號        | 讀取           |
| 76 | 恩普工資\.等級                       | 薪酬等級                     | 讀取           |
| 77 | 恩普就業終止               | 活躍就業計數       | 讀取           |
| 78 | 恩普就業終止               | 最新終止日期        | 讀取           |


## <a name="default-attribute-mapping"></a>預設屬性映射

下表提供了上面列出的 SuccessFactors 屬性和 AD/Azure AD 屬性之間的預設屬性映射。 在 Azure AD 預配應用"映射"邊欄選項卡中，可以修改此預設映射以包括上述清單中的屬性。 

| \# | 成功因素實體                  | 成功因素屬性 | 預設 AD/Azure AD 屬性對應   | 處理注釋                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | 每人                              | 人 Id 外部         | employeeId                              | 用作匹配屬性                                                                   |
| 2  | 每人                              | 佩爾森烏伊德            | \[未映射\-用作源錨點\] | 在初始同步期間，預配服務將人庫id 連結到現有物件 Guid_。  |
| 3  | 人均                            | displayName              | displayName                             | NA                                                                                           |
| 4  | 人均                            | firstName                | givenName                               | NA                                                                                           |
| 5  | 人均                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | User                                   | 位址Line1             | streetAddress                           | NA                                                                                           |
| 7  | User                                   | city                     | l                                       | NA                                                                                           |
| 8  | User                                   | country                  | co                                      | NA                                                                                           |
| 9  | User                                   | state                    | st                                      | NA                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | User                                   | 郵遞區號                  | postalCode                              | NA                                                                                           |
| 12 | 每封電子郵件                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | 恩普約伯                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | 恩普約伯                                 | 經理 Id                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.\.公司註冊國家 | 兩個查爾國家代碼       | c                                       | NA                                                                                           |
| 16 | 恩普約\.布部                     | department               | department                              | NA                                                                                           |
| 17 | 恩普約\.布部門                       | division                 | company                                 | NA                                                                                           |
| 18 | 恩普伯\.位置                       | 辦公地點位址    | streetAddress                           | NA                                                                                           |
| 19 | 恩普伯\.位置                       | 辦公地點郵遞區號    | postalCode                              | NA                                                                                           |
| 20 | 恩普就業終止               | 活躍就業計數   | accountEnabled                          | 如果活動就業統計\0，請禁用該帳戶*。                                           |

