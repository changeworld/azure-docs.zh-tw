---
title: SAP SuccessFactors 屬性參考 |Microsoft Docs
description: 瞭解 SuccessFactors-HR 驅動布建支援來自 SuccessFactors 的屬性
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522351"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors 屬性參考

## <a name="supported-successfactors-entities-and-attributes"></a>支援的 SuccessFactors 實體和屬性

下表會捕捉下列兩個布建應用程式所支援的 SuccessFactors 屬性清單： 
* [SuccessFactors 以 Active Directory 的使用者布建](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors 以 Azure AD 的使用者布建](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors 實體                  | SuccessFactors 屬性     | 作業類型 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | 讀取           |
| 2  | PerPerson                              | personId                     | 讀取           |
| 3  | PerPerson                              | perPersonUuid                | 讀取           |
| 4  | PerPersonal                            | displayName                  | 讀取           |
| 5  | PerPersonal                            | firstName                    | 讀取           |
| 6  | PerPersonal                            | gender                       | 讀取           |
| 7  | PerPersonal                            | lastName                     | 讀取           |
| 8  | PerPersonal                            | middleName                   | 讀取           |
| 9  | PerPersonal                            | preferredName                | 讀取           |
| 10 | User                                   | addressLine1                 | 讀取           |
| 11 | User                                   | addressLine2                 | 讀取           |
| 12 | User                                   | addressLIne3                 | 讀取           |
| 13 | User                                   | businessPhone                | 讀取           |
| 14 | User                                   | 行動電話                    | 讀取           |
| 15 | User                                   | city                         | 讀取           |
| 16 | User                                   | country                      | 讀取           |
| 17 | User                                   | custom01                     | 讀取           |
| 18 | User                                   | custom02                     | 讀取           |
| 19 | User                                   | custom03                     | 讀取           |
| 20 | User                                   | custom04                     | 讀取           |
| 21 | User                                   | custom05                     | 讀取           |
| 22 | User                                   | custom06                     | 讀取           |
| 23 | User                                   | custom07                     | 讀取           |
| 24 | User                                   | custom08                     | 讀取           |
| 25 | User                                   | custom09                     | 讀取           |
| 26 | User                                   | custom1-custom10                     | 讀取           |
| 27 | User                                   | custom11                     | 讀取           |
| 28 | User                                   | custom12                     | 讀取           |
| 29 | User                                   | custom13                     | 讀取           |
| 30 | User                                   | custom14                     | 讀取           |
| 31 | User                                   | empId                        | 讀取           |
| 32 | User                                   | homePhone                    | 讀取           |
| 33 | User                                   | jobFamily                    | 讀取           |
| 34 | User                                   | 暱稱                     | 讀取           |
| 35 | User                                   | State                        | 讀取           |
| 36 | User                                   | timeZone                     | 讀取           |
| 37 | User                                   | username                     | 讀取           |
| 38 | User                                   | zipCode                      | 讀取           |
| 39 | PerPhone                               | areaCode                     | 讀取           |
| 40 | PerPhone                               | countryCode                  | 讀取           |
| 41 | PerPhone                               | 擴充功能                    | 讀取           |
| 42 | PerPhone                               | phoneNumber                  | 讀取           |
| 43 | PerPhone                               | phoneType                    | 讀取           |
| 44 | PerEmail                               | emailAddress                 | 讀取、寫入    |
| 45 | PerEmail                               | emailType                    | 讀取           |
| 46 | EmpEmployment                          | firstDateWorked              | 讀取           |
| 47 | EmpEmployment                          | lastDateWorked               | 讀取           |
| 48 | EmpEmployment                          | userId                       | 讀取           |
| 49 | EmpEmployment                          | isContingentWorker           | 讀取           |
| 50 | EmpJob                                 | countryOfCompany             | 讀取           |
| 51 | EmpJob                                 | emplStatus                   | 讀取           |
| 52 | EmpJob                                 | endDate                      | 讀取           |
| 53 | EmpJob                                 | startDate                    | 讀取           |
| 54 | EmpJob                                 | jobTitle                     | 讀取           |
| 55 | EmpJob                                 | position                     | 讀取           |
| 65 | EmpJob                                 | customString13               | 讀取           |
| 56 | EmpJob                                 | managerId                    | 讀取           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | 讀取           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | 讀取           |
| 59 | EmpJob\.公司                        | company                      | 讀取           |
| 60 | EmpJob\.公司                        | companyId                    | 讀取           |
| 61 | EmpJob\.公司\.CountryOfRegistration | twoCharCountryCode           | 讀取           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | 讀取           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | 讀取           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | 讀取           |
| 65 | EmpJob\.部門                     | department                   | 讀取           |
| 66 | EmpJob\.部門                     | departmentId                 | 讀取           |
| 67 | EmpJob\.部門                       | division                     | 讀取           |
| 68 | EmpJob\.部門                       | divisionId                   | 讀取           |
| 69 | EmpJob\.JobCode                        | jobCode                      | 讀取           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | 讀取           |
| 71 | EmpJob\.位置                       | LocationName                 | 讀取           |
| 72 | EmpJob\.位置                       | officeLocationAddress        | 讀取           |
| 73 | EmpJob\.位置                       | officeLocationCity           | 讀取           |
| 74 | EmpJob\.位置                       | officeLocationCustomString4  | 讀取           |
| 75 | EmpJob\.位置                       | officeLocationZipCode        | 讀取           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | 讀取           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | 讀取           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | 讀取           |


## <a name="default-attribute-mapping"></a>預設屬性對應

下表提供上列 SuccessFactors 屬性與 AD/Azure AD 屬性之間的預設屬性對應。 在 Azure AD 布建應用程式的 [對應] 分頁中，您可以修改此預設對應，以包含上述清單中的屬性。 

| \# | SuccessFactors 實體                  | SuccessFactors 屬性 | 預設 AD/Azure AD 屬性對應   | 處理批註                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | 當做比對屬性使用                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[未使用\-對應做為來源錨點\] | 在初始同步處理期間，布建服務會將 personUuid 連結至現有的 objectGuid\。  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | User                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | User                                   | city                     | l                                       | NA                                                                                           |
| 8  | User                                   | country                  | co                                      | NA                                                                                           |
| 9  | User                                   | State                    | st                                      | NA                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | User                                   | zipCode                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.公司\.CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.部門                     | department               | department                              | NA                                                                                           |
| 17 | EmpJob\.部門                       | division                 | company                                 | NA                                                                                           |
| 18 | EmpJob\.位置                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob\.位置                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | 如果 activeEmploymentsCount = 0，請停用 account\。                                           |

