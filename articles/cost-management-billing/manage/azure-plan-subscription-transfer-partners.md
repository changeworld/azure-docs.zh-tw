---
title: 將 Azure 訂用帳戶從一個合作夥伴轉移至另一個合作夥伴 (預覽)
description: 本文可協助您了解在傳輸 Azure 訂用帳戶的帳單擁有權之前和之後需要知道的事項。
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554191"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>將 Azure 訂用帳戶從一個合作夥伴轉移至另一個合作夥伴 (預覽)

本文可協助您了解在傳輸 Azure 訂用帳戶的帳單擁有權之前和之後需要知道的事項。 若要在 Azure 方案下將 Azure 訂用帳戶從一個 Microsoft 合作夥伴轉移到另一個合作夥伴 ，您必須連絡合作夥伴。 合作夥伴會傳送有關如何開始的指示給您。 在傳輸程序完成後，您的訂用帳戶的帳單擁有權就會變更。

## <a name="user-access"></a>使用者存取

使用 Azure 角色型存取控制 (RBAC) 為現有使用者、群組或服務主體指派的存取權，在轉換期間不受影響。 [Azure RBAC](../../role-based-access-control/overview.md) 可協助您管理可存取 Azure 資源的人員、這些人員如何使用資源，以及其可存取的區域。 您的新合作夥伴不會透過訂用帳戶傳輸，為您的資源提供任何 RBAC 存取權。 您先前的合作夥伴會保留其 RBAC 存取權。

因此，請務必移除舊合作夥伴的 Azure RBAC 存取權，並新增新合作夥伴的存取權。 如需提供新合作夥伴存取權的詳細資訊，請參閱[什麼是 Azure 角色型存取控制 (Azure RBAC)？](../../role-based-access-control/overview.md) 如需移除先前合作夥伴 RBAC 存取權的詳細資訊，請參閱[移除角色指派](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment)。

此外，您的新合作夥伴不會自動[代表管理員 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 取得您訂用帳戶的管理員存取權。 您的合作夥伴必須透過 AOBO，才能代表您管理 Azure 訂用帳戶。 如需有關如何提供新合作夥伴 AOBO 存取權的詳細資訊，請參閱[將 Azure 訂用帳戶的帳單擁有權轉移至另一個帳戶](billing-subscription-transfer.md)。

## <a name="stop-a-transfer"></a>停止轉移

收到已提交轉移要求的確認後，**如果不想繼續轉移**，可以使用下列其中一個選項 。

- 如果您目前的夥伴尚未接受要求，可以要求新的合作夥伴取消其啟動的轉移要求 (當狀態為擱置中時)。
- 要求目前的合作夥伴在收到轉移要求時不採取任何動作。 若沒有您目前合作夥伴的同意，則無法繼續轉移。 要求將會過期。
- 您可以「移除與新合作夥伴的轉銷商關係」。 此動作會移除您移動訂用帳戶的能力， 同時會立即取消要求。

您也可以使用 [Microsoft 法律](https://www.microsoft.com/legal/)網站上的任何選項，尋求說明、回報不法情事或可疑活動的資訊。 回報疑慮的選項位於合規性與道德之下。

## <a name="next-steps"></a>後續步驟

- 如需提供新合作夥伴 RBAC 存取權，請參閱[什麼是 Azure 角色型存取控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- 如需提供新合作夥伴 AOBO 存取權，請參閱[將 Azure 訂用帳戶的帳單擁有權轉移至另一個帳戶](billing-subscription-transfer.md)。