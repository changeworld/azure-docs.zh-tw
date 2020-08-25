---
title: 管理單位疑難排解和常見問題-Azure Active Directory |Microsoft Docs
description: 調查管理單位，以 Azure Active Directory 中的限制範圍授與許可權。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b48bebe6aa5f9862d5f51fea257c4b7f3057639b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794479"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD 管理單位：疑難排解和常見問題

若要在 Azure Active Directory (Azure AD) 中進行更細微的系統管理控制，您可以將使用者指派至範圍限制為一或多個系統管理單位 Azure AD au (的) 角色。 如需一般工作的 PowerShell 腳本範例，請參閱 [使用管理單位](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)。

## <a name="frequently-asked-questions"></a>常見問題集

**問：為什麼我無法建立管理單位？**

**答：** 只有 *全域管理員* 或特殊 *許可權角色管理員* 可以在 Azure AD 中建立管理單位。 檢查以確定嘗試建立管理單位的使用者已獲指派 *全域管理員* 或特殊 *許可權角色管理員* 角色。

**問：我已將群組新增至管理單位。為什麼群組成員仍不會顯示？**

**答：** 當您將群組新增至管理單位時，不會導致所有群組的成員新增至其中。 使用者必須直接指派給管理單位。

**問：我剛剛新增 (或移除) 管理單位的成員。為什麼成員未顯示 (，或仍在使用者介面中顯示) ？**

**答：** 有時候，處理管理單位的一或多個成員的新增或移除可能需要幾分鐘的時間才會反映在 [ **管理單位** ] 頁面上。 或者，您可以直接前往相關聯的資源屬性，並查看動作是否已完成。 如需有關 au 中使用者和群組的詳細資訊，請參閱 [列出使用者的系統管理單位](roles-admin-units-add-manage-users.md) ，並 [列出群組的管理單位](roles-admin-units-add-manage-groups.md)。

**問：我是系統管理單位上的委派密碼系統管理員。為什麼我無法重設特定使用者的密碼？**

**答：** 身為管理單位的系統管理員，您只能針對指派給管理單位的使用者重設密碼。 請確定密碼重設失敗的使用者屬於您已指派的管理單位。 如果使用者屬於相同的管理單位，但您仍無法重設其密碼，請檢查指派給使用者的角色。 

為了防止權限提高，系統管理單位範圍的系統管理員無法重設指派給具有全組織範圍之角色的使用者密碼。

**問：為何需要管理單位？無法使用安全性群組作為定義範圍的方式嗎？**

**答：** 安全性群組具有現有的用途和授權模型。 例如， *使用者系統管理員*可以管理 Azure AD 組織中所有安全性群組的成員資格。 角色可能會使用群組來管理應用程式（例如 Salesforce）的存取權。 *使用者系統管理員*應該無法管理委派模型本身，如果安全性群組已擴充為支援「資源群組」案例，就會產生此本身。 管理單位（例如 Windows Server Active Directory 中的組織單位）的目的是要提供一種方式來管理範圍廣泛的目錄物件管理。 安全性群組本身可以是資源範圍的成員。 使用安全性群組來定義系統管理員可以管理的安全性群組集合可能會變得令人困惑。

**問：將群組新增至管理單位是什麼意思？**

**答：** 將群組新增至管理單位，會將群組本身帶到任何 *使用者系統管理員* 的管理範圍內，這些系統管理員的範圍也是該管理單位的範圍。 管理單位的使用者系統管理員可以管理群組本身的名稱和成員資格。 它不會授與 *使用者* 管理單位許可權來管理群組使用者的許可權 (例如，將密碼) 重設。 若要授與 *使用者系統管理員* 管理使用者的能力，使用者必須是管理單位的直接成員。

**問：資源 (使用者或群組) 是一個以上管理單位的成員嗎？**

**答：** 是，資源可以是一個以上管理單位的成員。 資源可以由擁有資源許可權的所有全組織和管理單位範圍管理員來管理。

**問： B2C 組織中是否有可用的系統管理單位？**

**答：** 否，B2C 組織無法使用管理單位。

**問：是否支援嵌套的管理單位？**

**答：** 否，不支援嵌套的管理單位。

**問： PowerShell 和圖形 API 是否支援管理單位？**

**答：** 是。 您可以在 [PowerShell Cmdlet 檔](/powershell/module/Azuread/?view=azureadps-2.0-preview) 和 [範例腳本](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)中找到管理單位的支援。 

在 Microsoft Graph 中尋找 [>administrativeunit 資源類型](/graph/api/resources/administrativeunit?view=graph-rest-beta) 的支援。

## <a name="next-steps"></a>後續步驟

- [使用管理單位限制角色的範圍](directory-administrative-units.md)
- [管理管理單位](roles-admin-units-manage.md)