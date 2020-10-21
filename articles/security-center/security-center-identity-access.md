---
title: 在 Azure 資訊安全中心監視身分識別和存取 | Microsoft Docs
description: 了解如何在 Azure 資訊安全中心使用身分識別和存取功能，來監視使用者的存取活動和身分識別相關問題。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 88ab04eb69be0f9f765e6f95a0ea1194189d823a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341257"
---
# <a name="monitor-identity-and-access"></a>監視身分識別和存取

安全性周邊已從網路周邊發展到身分識別周邊。 在這項開發中，安全性比保護您的網路更少，而且更進一步瞭解如何管理您的應用程式、資料和使用者的安全性。

藉由監視與身分識別相關的活動和設定，您可以在事件發生之前採取主動式動作，或採取回應動作來停止嘗試的攻擊。

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>資訊安全中心提供哪些身分識別和存取保護？ 

Azure 資訊安全中心有兩個專屬的安全性控制措施可確保您符合組織的身分識別和安全性需求： 

 - **管理存取權和許可權** -我們鼓勵您採用 [最低許可權存取模型](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) ，並確保您只授與使用者執行其作業所需的存取權。 此控制項也包含執行 [角色型存取控制 (RBAC) ](../role-based-access-control/overview.md) 來控制資源存取權的建議。
 
 - **啟用 mfa** -啟用 [mfa](https://www.microsoft.com/security/business/identity/mfa) 後，您的帳戶會更安全，使用者仍可使用單一登入驗證幾乎任何應用程式。

### <a name="example-recommendations-for-identity-and-access"></a>身分識別和存取的範例建議

您可能會在資訊安全中心的 **建議** 頁面上看到這兩個控制項的建議範例：

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應針對您的訂用帳戶指定最多 3 位擁有者
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 已淘汰的帳戶應從您的訂用帳戶中移除 (已淘汰的帳戶是不再需要的帳戶，且已封鎖無法 Azure Active Directory 登入) 

> [!TIP]
> 如需這些建議以及您可能會在這些控制項中看到的其他建議的詳細資訊，請參閱身分 [識別和存取建議](recommendations-reference.md#recs-identity)。

### <a name="limitations"></a>限制

資訊安全中心的身分識別和存取保護有一些限制：

- 具有600以上帳戶的訂用帳戶無法使用身分識別建議。 在這種情況下，這些建議會列在「無法使用的評量」底下。
- 雲端解決方案提供者 (CSP) 合作夥伴的管理員代理程式無法使用身分識別建議。
- 身分識別建議無法識別以特殊許可權身分識別管理 (PIM) 系統管理的帳戶。 如果您是使用 PIM 工具，則可能會在 [ **管理存取和許可權** ] 控制項中看到不正確的結果。

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a> (MFA) 和 Azure Active Directory 的多重要素驗證 

需要有 [Azure Active Directory (AD) 租用戶權限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)才能啟用 MFA。

- 如果您有進階版的 AD，請使用[條件式存取](../active-directory/conditional-access/concept-conditional-access-policy-common.md)來啟用 MFA。
- 如果您使用的是 AD free edition，請啟用[Azure Active Directory 檔](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)中所述的**安全性預設值**。

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>找出不具多重要素驗證的帳戶 (MFA) 已啟用

若要查看哪些帳戶未啟用 MFA，請使用下列 Azure Resource Graph 查詢。 此查詢會傳回所有狀況不良的資源，也就是「在訂用帳戶上具有擁有者許可權的帳戶上應啟用 MFA」的建議帳戶。 

1. 開啟 **Azure Resource Graph Explorer**。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="啟動 Azure Resource Graph Explorer * * 建議頁面" :::

1. 輸入下列查詢，然後選取 [ **執行查詢**]。

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData`屬性會針對未強制執行 MFA 的帳戶，顯示帳戶物件識別碼的清單。 

    > [!NOTE]
    > 帳戶會顯示為物件識別碼而非帳戶名稱，以保護帳戶持有者的隱私權。

> [!TIP]
> 或者，您可以使用安全中心的 REST API 方法 [評定-Get](/rest/api/securitycenter/assessments/get)。


## <a name="next-steps"></a>後續步驟
若要深入瞭解適用于其他 Azure 資源類型的建議，請參閱下列文章：

- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)