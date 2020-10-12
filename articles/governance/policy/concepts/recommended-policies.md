---
title: Azure 服務的建議原則
description: 說明如何尋找及套用 Azure 服務（例如 Azure 虛擬機器）的建議原則。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 919d09a569fd950ab2061ba3452a4a940d25cb5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89447769"
---
# <a name="recommended-policies-for-azure-services"></a>Azure 服務的建議原則

剛 Azure 原則的客戶通常會尋找一般原則定義來管理和管理其資源。 Azure 原則的 **建議原則** 會提供要開始的一般原則定義的焦點清單。 針對支援的資源， **建議的原則** 體驗內嵌在該資源的入口網站體驗中。

如需其他 Azure 原則內建，請參閱 [Azure 原則內建定義](../samples/built-in-policies.md)。

## <a name="azure-virtual-machines"></a>Azure 虛擬機器

適用于[Azure 虛擬機器](../../../virtual-machines/index.yml)的**建議原則**位於虛擬機器的 [**總覽**] 頁面和 [**功能**] 索引標籤底下。在_Azure 原則_卡片中，選取「未設定」或「指派的 #」文字，以開啟具有建議原則的側邊窗格。 任何已指派給虛擬機器所屬範圍的原則定義都會變成灰色。選取要套用到此虛擬機器的建議原則，然後選取 [ **指派原則** ] 以建立每個原則的指派。

當組織透過 [組織資源和資源](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)階層的方式達到成熟度時，建議您將這些原則指派從每個資源一次轉換成訂用帳戶或 [管理群組](../../management-groups/index.yml) 層級。

### <a name="azure-virtual-machines-recommended-policies"></a>Azure 虛擬機器建議的原則

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核未設定災害復原的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |稽核未設定災害復原的虛擬機器。 若要深入了解災害復原，請造訪 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[稽核不是使用受控磁碟的 VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |此原則會稽核不是使用受控磁碟的 VM |稽核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此原則有助於在所有虛擬機器都啟用 Azure 備份服務時進行稽核。 Azure 備份是符合成本效益的一鍵備份解決方案，能夠簡化資料復原，且較其他雲端備份服務易於啟用。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱[了解原則效果](./effects.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。