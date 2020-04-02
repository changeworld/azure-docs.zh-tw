---
title: 修復 Azure AD 網域服務中不匹配的目錄錯誤 |微軟文件
description: 瞭解不符合的目錄錯誤的含義以及如何在 Azure AD 網域服務中解決它
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 7a74d5e609c615bb6b973eceebb144c58e46f7cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519465"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>解決現有 Azure AD Domain Services 受控網域的不相符目錄錯誤

如果 Azure 活動目錄域服務 (Azure AD DS) 託管域顯示租戶錯誤不匹配,則在解析之前無法管理託管域。 如果將基礎 Azure 虛擬網路移動到其他 Azure AD 目錄,則會發生此錯誤。

本文解釋錯誤發生的原因以及如何解決它。

## <a name="what-causes-this-error"></a>導致此錯誤的原因是什麼?

當 Azure AD DS 託管域和虛擬網路屬於兩個不同的 Azure AD 租戶時,會發生不匹配的目錄錯誤。 例如,您可能有一個 Azure AD DS 託管域,稱為*aaddscontoso.com,* 該域在 Contoso 的 Azure AD 租戶中運行。 但是,託管域的 Azure 虛擬網路是 Fabrikam Azure AD 租戶的一部分。

Azure 使用基於角色的存取控制 (RBAC) 來限制對資源的訪問。 在 Azure AD 租戶中啟用 Azure AD DS 時,憑據哈希將同步到託管域。 此操作要求您是 Azure AD 目錄的租戶管理員,並且必須控制對憑據的訪問。 要將資源部署到 Azure 虛擬網路並控制流量,必須在部署 Azure AD DS 的虛擬網路上具有管理許可權。

RBAC 要一致地安全地訪問 Azure AD DS 使用的所有資源,託管域和虛擬網路必須屬於同一 Azure AD 租戶。

下列規則適用於資源管理員環境：

- Azure AD 目錄有多個 Azure 訂用帳戶。
- Azure 訂用帳戶有多個資源，例如虛擬網路。
- Azure AD 目錄已啟用單一 Azure AD Domain Services 受控網域。
- Azure AD Domain Services 受控網域可以在屬於相同 Azure AD 租用戶內之任何 Azure 訂用帳戶的虛擬網路上啟用。

### <a name="valid-configuration"></a>有效設定

在以下範例部署方案中,Contoso Azure AD DS 託管域在 Contoso Azure AD 租戶中啟用。 託管域部署在屬於 Contoso Azure AD 租戶擁有的 Azure 訂閱的虛擬網路中。 託管域和虛擬網路都屬於同一個 Azure AD 租戶。 此示例配置有效且完全支援。

![使用同一 Azure AD 租戶的託管域和虛擬網路部分的有效 Azure AD DS 租戶設定](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>不相符租用戶設定

在此範例中部署方案中,Contoso Azure AD DS 託管域在 Contoso Azure AD 租戶中啟用。 但是,託管域部署在屬於 Fabrikam Azure AD 租戶擁有的 Azure 訂閱的虛擬網路中。 託管域和虛擬網路屬於兩個不同的 Azure AD 租戶。 此示例配置是不匹配的租戶,不受支援。 必須將虛擬網路移動到與託管域相同的 Azure AD 租戶。

![不相符租用戶設定](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>解決不符合的租戶錯誤

以下兩個選項解決了不符合的目錄錯誤:

* 從現有 Azure AD 目錄[中移除 Azure AD DS 託管域](delete-aadds.md)。 在您使用的虛擬網路相同的 Azure AD 目錄中[建立取代的 Azure AD DS 託管網域](tutorial-create-instance.md)。 準備就緒后,將以前加入到已刪除域的所有電腦加入重新創建的託管域。
* 將包含虛擬網路的[Azure 訂閱移動到](../cost-management-billing/manage/billing-subscription-transfer.md)與 Azure AD 託管域相同的 Azure AD 目錄。

## <a name="next-steps"></a>後續步驟

有關 Azure AD DS 故障排除問題的詳細資訊,請參閱[故障排除指南](troubleshoot.md)。
