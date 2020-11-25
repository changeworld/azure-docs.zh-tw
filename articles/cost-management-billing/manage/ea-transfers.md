---
title: Azure Enterprise 傳輸
description: 描述 Azure EA 傳輸
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: dc725839d018c281dc784f5f83b256e62d70884d
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635410"
---
# <a name="azure-enterprise-transfers"></a>Azure Enterprise 傳輸

此文章提供企業傳輸的概觀。

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>將企業帳戶轉移至新的註冊

帳戶轉移會將帳戶擁有者從一個註冊轉移至另一個註冊。 帳戶擁有者下的所有相關訂用帳戶都會轉移至目標註冊。 當您有多個作用中註冊，且只想要移動所選的帳戶擁有者時，請使用帳戶移轉。

本節僅供資訊參考之用，因為企業管理員無法執行動作。 將企業帳戶轉移至新的註冊需要支援要求。

當您將企業帳戶轉移至新的註冊時，請記住下列幾點：

- 只會轉移要求中指定的帳戶。 如果選擇所有帳戶，則會轉移全部的帳戶。
- 來源註冊會將其狀態保持為 [作用中] 或 [已延長]。 您可以繼續使用註冊，直到到期為止。

### <a name="prerequisites"></a>必要條件

當您要求帳戶轉移時，請提供下列資訊：

- 要傳送之帳戶的目標註冊號碼、帳戶名稱和帳戶擁有者電子郵件
- 針對來源註冊，須提供要轉移的註冊號碼和帳戶

帳戶轉移之前要記住的其他幾點：

- 目標和來源註冊都需要 EA 系統管理員的核准
- 如果帳戶轉移不符合您的需求，請考慮註冊轉移。
- 帳戶移轉會移轉與特定帳戶相關的所有服務和訂閱。
- 轉移完成之後，已轉移的帳戶在來源註冊下會顯示為非作用中，而在目標註冊下會顯示為作用中。
- 帳戶會顯示與來源註冊上的有效移轉日期對應的結束日期，並作為目標註冊的開始日期。
- 在有效傳輸日期之前，帳戶所發生的任何使用狀況，都會保留在來源註冊之下。

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>將企業註冊轉移至新的註冊

在下列情況下可考慮進行註冊轉移：

- 目前註冊的預付款期間已結束。
- 註冊處於過期/延長狀態，且已協商新合約。
- 您有多個註冊，而想要將所有帳戶和帳單合併在單一註冊之下。

本節僅供資訊參考之用，因為企業管理員無法執行動作。 將企業註冊轉移至新的註冊需要支援要求。

當您要求將整個企業註冊轉移至另一個註冊時，會發生下列動作：

- 所有 Azure 服務、訂用帳戶、帳戶、部門和整個註冊結構 (包括所有 EA 部門系統管理員) 都會轉移至新的目標註冊。
- 註冊狀態會設定為 [已轉移]。 已轉移的註冊僅限用於報告使用量記錄。
- 您無法將角色或訂用帳戶新增至已轉移的註冊。 已轉移狀態可防止以該註冊產生其他使用量。
- 合約中任何剩餘的 Azure 預付款餘額都會遺失，其中也包括未來的期限。
-    如果您要轉移的註冊有 RI 購買，RI 購買費用將保留在來源註冊中，但所有 RI 權益都將轉移供新註冊使用。
-    舊註冊上已產生的 Marketplace 一次性購買費用和任何每月固定費用，都不會轉移到新的註冊。 依使用量的 Marketplace 費用將會轉移。

### <a name="effective-transfer-date"></a>有效的轉移日期

有效的轉移日期可以是目標註冊的開始日期或之後的日期。

來源註冊使用量會依 Azure 預付款或超額部分收費。 在有效轉移日期之後發生的使用量會轉移至新的註冊，並據以收費。

### <a name="prerequisites"></a>必要條件

當您要求註冊轉移時，請提供下列資訊：

- 針對來源註冊，須提供註冊號碼。
- 針對目標註冊，須提供轉移目的地的註冊號碼和帳戶。
- 針對註冊轉移有效日期，可以是目標註冊開始日期或其之後的日期。 選擇的日期無法影響已發出之超額發票的使用量。

註冊轉移之前要記住的其他幾點：

- 目標和來源註冊 EA 系統管理員都必須核准。
- 如果註冊轉移不符合您的需求，請考慮帳戶轉移。
- 來源註冊狀態會更新為已轉移，且將僅適用於歷史使用量報告用途。

### <a name="azure-prepayment"></a>Azure 預付款

Azure 預付款無法在註冊之間轉移。 Azure 預付款餘額會依約繫結至收訂的註冊。 Azure 預付款不會在帳戶或註冊轉移程序中轉移。

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>帳戶和註冊轉移不會影響任何服務

帳戶或註冊轉移期間不會有任何停機時間。 如果已提供所有必要的資訊，此作業可以在提出要求的同一天完成。

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>將企業訂用帳戶轉移到隨用隨付訂用帳戶

若要將企業訂用帳戶轉移至採隨用隨付費率的個別訂用帳戶，您必須在 Azure 企業版入口網站中建立新的支援要求。 若要建立支援要求，請選取 [說明及支援] 區域中的 [+ 新增支援要求]。

## <a name="change-azure-subscription-or-account-ownership"></a>變更 Azure 訂用帳戶或帳戶擁有權

Azure EA 入口網站可以將訂用帳戶從一個帳戶擁有者轉移至另一個帳戶擁有者。 如需詳細資訊，請參閱[變更 Azure 訂用帳戶或帳戶擁有權](ea-portal-administration.md#change-azure-subscription-or-account-ownership)。

## <a name="subscription-transfer-effects"></a>訂用帳戶轉移效果

當 Azure 訂用帳戶轉移到相同 Azure Active Directory 租用戶中的帳戶時，具有 [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/overview.md) 可管理資源的所有使用者、群組和服務主體都會保留其存取權。

若要檢視訂用帳戶上具有 RBAC 存取權的使用者：

1. 在 Azure 入口網站中，開啟 **訂用帳戶**。
2. 選取要檢視的訂用帳戶，然後選取 [存取控制 (IAM)]。
3. 選取 [角色指派]。 [角色指派] 頁面會列出在訂用帳戶上具有 RBAC 存取權的所有使用者。

如果訂用帳戶轉移到不同 Azure AD 租用戶中的帳戶，則具有 [RBAC](../../role-based-access-control/overview.md) 可管理資源的所有使用者、群組和服務主體都會「失去」其存取權。 雖然 RBAC 存取不存在，但您可以透過安全性機制取得訂用帳戶的存取權，包括：

- 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](../../cloud-services/cloud-services-certs-create.md)。
- 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../../storage/common/storage-account-overview.md)。
- 服務 (例如 Azure 虛擬機器) 的遠端存取認證。

如果接受者需要限制對其 Azure 資源的存取權，則應考慮更新所有與服務相關聯的密碼。 您可以使用下列步驟來更新大部分的資源：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表中，選取 [所有資源]。
3. 選取資源。
4. 在資源頁面上選取 [設定]，即可檢視並更新現有的秘密。

## <a name="next-steps"></a>後續步驟

- 如果您需要 Azure EA 入口網站問題的疑難排解協助，請參閱[針對 Azure EA 入口網站的存取進行疑難排解](ea-portal-troubleshoot.md)。