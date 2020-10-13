---
title: 針對驗證和授權問題進行疑難排解-Azure 事件中樞
description: 本文提供 Azure 事件中樞的驗證和授權問題的疑難排解資訊。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cd5f48dfb146a027f0b95b4ddea3dc054a315c6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91566222"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>針對驗證和授權問題進行疑難排解-Azure 事件中樞
[疑難排解連線問題](troubleshooting-guide.md)一文提供針對 Azure 事件中樞的連線問題進行疑難排解的秘訣。 本文提供針對 Azure 事件中樞的驗證和授權問題進行疑難排解的秘訣和建議。 

## <a name="if-you-are-using-azure-active-directory"></a>如果您使用 Azure Active Directory
如果您使用 Azure Active Directory (Azure AD) 以 Azure 事件中樞進行驗證和授權，請確認存取事件中樞的身分識別，是否為位於正確**資源範圍**的正確**Azure 角色** (取用者群組、事件中樞、命名空間、資源群組或訂用帳戶) 的成員。

### <a name="azure-roles"></a>Azure 角色
- [Azure 事件中樞資料擁有](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) 者，以取得事件中樞資源的完整存取權。
- 傳送存取[Azure 事件中樞資料](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)傳送者。
- 接收存取[Azure 事件中樞資料接收者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)。

如需 Schema Registry 內建角色，請參閱 [架構登錄角色](schema-registry-overview.md#role-based-access-control)。

### <a name="resource-scopes"></a>資源範圍
- 取用**者群組**：在此範圍中，角色指派只適用于此實體。 目前，Azure 入口網站不支援將 Azure 角色指派給此層級的安全性主體。 
- **事件中樞**：角色指派適用于事件中樞實體和其下的取用者群組。
- **命名空間**：角色指派橫跨命名空間下的事件中樞整個拓撲，以及與其相關聯的取用者群組。
- **資源群組**：角色指派會套用至資源群組下的所有事件中樞資源。
- **訂**用帳戶：角色指派會套用至訂用帳戶中所有資源群組中的所有事件中樞資源。

如需詳細資訊，請參閱下列文章：

- [使用 Azure Active Directory 來驗證應用程式，以存取事件中樞資源](authenticate-application.md)
- [使用 Azure Active Directory 授權存取事件中樞資源](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>如果您使用共用存取簽章 (SAS) 
如果您使用的是 [SAS](authenticate-shared-access-signature.md)，請遵循下列步驟： 

- 確定您使用的 SAS 金鑰正確無誤。 如果沒有，請使用正確的 SAS 金鑰。
- 確認金鑰具有正確的許可權 (傳送、接收或管理) 。 如果沒有，請使用具有您所需許可權的金鑰。 
- 檢查金鑰是否已過期。 建議您在到期前先更新 SAS。 如果用戶端與事件中樞服務節點之間有時鐘誤差，驗證權杖可能會在用戶端發現之前過期。 目前的執行帳戶最多會有5分鐘的時間扭曲，也就是，用戶端會在權杖到期前的5分鐘內更新。 因此，如果時鐘誤差超過5分鐘，用戶端可能會觀察到間歇性的驗證失敗。
- 如果 **SAS 開始時間** 設定為 [ **現在**]，您可能會在前幾分鐘看到間歇性失敗，因為時鐘誤差 (不同電腦上目前時間的差異) 。 將開始時間設為至少15分鐘，或完全未設定。 同樣的情況也同樣適用于到期時間。 

如需詳細資訊，請參閱下列文章： 

- [使用 (SAS) 的共用存取簽章進行驗證 ](authenticate-shared-access-signature.md)。 
- [使用共用存取簽章來授權事件中樞資源的存取權](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>後續步驟

查看下列文章：

* [針對連線能力問題進行疑難排解](troubleshooting-guide.md)
