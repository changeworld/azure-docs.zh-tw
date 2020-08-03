---
title: 針對驗證和授權問題進行疑難排解-Azure 事件中樞
description: 本文提供有關使用 Azure 事件中樞進行驗證和授權問題疑難排解的資訊。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 6fca0c29c20e2bfe0c3ec64e3068b61bd5147bdb
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502098"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>針對驗證和授權問題進行疑難排解-Azure 事件中樞
針對連線[問題進行疑難排解](troubleshooting-guide.md)一文提供針對 Azure 事件中樞的連線問題進行疑難排解的秘訣。 本文提供針對 Azure 事件中樞的驗證和授權問題進行疑難排解的秘訣和建議。 

## <a name="if-you-are-using-azure-active-directory"></a>如果您使用 Azure Active Directory
如果您使用 Azure Active Directory （Azure AD）來驗證和授權 Azure 事件中樞，請確認存取事件中樞的身分識別是正確的**資源範圍**（取用者群組、事件中樞、命名空間、資源群組或訂用帳戶）的正確**Azure 角色**的成員。

### <a name="rbac-roles"></a>RBAC 角色
- [Azure 事件中樞資料擁有](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)者，以完整存取事件中樞資源。
- Azure 事件中樞傳送存取的[資料寄件者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)。
- 接收存取的[Azure 事件中樞資料接收器](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)。

### <a name="resource-scopes"></a>資源範圍
- 取用**者群組**：在此範圍中，角色指派只會套用至此實體。 目前，Azure 入口網站不支援將 RBAC 角色指派給此層級的安全性主體。 
- **事件中樞**：角色指派會套用到事件中樞實體和其下的取用者群組。
- **命名空間**：角色指派會跨越命名空間下的整個事件中樞拓撲，以及與其相關聯的取用者群組。
- **資源群組**：角色指派會套用至資源群組下的所有事件中樞資源。
- **訂**用帳戶：角色指派會套用至訂用帳戶中所有資源群組內的所有事件中樞資源。

如需詳細資訊，請參閱下列文章：

- [使用 Azure Active Directory 來驗證應用程式，以存取事件中樞資源](authenticate-application.md)
- [使用 Azure Active Directory 授權事件中樞資源的存取權](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>如果您使用共用存取簽章（SAS）
如果您使用[SAS](authenticate-shared-access-signature.md)，請遵循下列步驟： 

- 請確定您使用的 SAS 金鑰是正確的。 如果不是，請使用正確的 SAS 金鑰。
- 確認金鑰具有正確的許可權（傳送、接收或管理）。 如果沒有，請使用具有所需許可權的金鑰。 
- 檢查金鑰是否已過期。 我們建議您在到期前更新 SAS。 如果用戶端與事件中樞服務節點之間有時鐘誤差，驗證權杖可能會在用戶端發現之前過期。 目前的執行帳戶時鐘誤差最長為5分鐘，也就是用戶端會在權杖到期前的5分鐘後將其續約。 因此，如果時鐘誤差大於5分鐘，用戶端可能會觀察到間歇的驗證失敗。
- 如果 [ **SAS 開始時間**] 設定為 [**現在**]，您可能會看到因為時鐘誤差（在不同電腦上目前時間的差異）而導致的前幾分鐘發生間歇性失敗。 將 [開始時間] 設定為在過去至少15分鐘，或完全未設定。 相同的一般也適用于到期時間。 

如需詳細資訊，請參閱下列文章： 

- [使用共用存取簽章（SAS）進行驗證](authenticate-shared-access-signature.md)。 
- [使用共用存取簽章授權事件中樞資源的存取權](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>後續步驟

查看下列文章：

* [疑難排解連線問題](troubleshooting-guide.md)
