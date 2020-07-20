---
title: Azure AD Domain Services 中的傳統部署遷移的優點 |Microsoft Docs
description: 深入瞭解將 Azure Active Directory Domain Services 的傳統部署遷移至 Resource Manager 部署模型的優點
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 837ca3afaf014c41fded09fa8342eed1c13e5dcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84734923"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>從傳統遷移到 Resource Manager 部署模型的優點 Azure Active Directory Domain Services

Azure Active Directory Domain Services （Azure AD DS）可讓您將使用傳統部署模型的現有受控網域遷移至 Resource Manager 部署模型。 使用 Resource Manager 部署模型 Azure AD DS 受控網域提供額外的功能，例如更細緻的密碼原則、audit 記錄和帳戶鎖定保護。

本文概述遷移的優點。 若要開始使用，請參閱將[Azure AD Domain Services 從傳統虛擬網路模型遷移至 Resource Manager][howto-migrate]。

> [!NOTE]
> 在2017中，Azure AD Domain Services 變成可供 Azure Resource Manager 網路中的主機使用。 之後，我們就可以使用 Azure Resource Manager 的現代化功能來建立更安全的服務。 由於 Azure Resource Manager 部署完全取代傳統部署，Azure AD DS 傳統虛擬網路部署將于2023年3月1日淘汰。
>
> 如需詳細資訊，請參閱[官方淘汰通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>遷移的優點

遷移程式會採用使用傳統部署模型的現有受控網域，並移至使用 Resource Manager 部署模型。 當您將受控網域從傳統遷移到 Resource Manager 部署模型時，您可以避免需要將機器重新加入受控網域，或刪除受控網域並從頭建立一個。 在遷移程式結束時，Vm 會繼續加入受控網域。

在遷移之後，Azure AD DS 提供了許多功能，僅適用于使用 Resource Manager 部署模型的網域，如下所示：

* 更細緻[的密碼原則支援][password-policy]。
* Azure AD 和 Azure AD Domain Services 之間的同步處理速度更快。
* 兩個新[的屬性，可從 Azure AD][attributes]  -  *管理員*和*員工 id*進行同步處理。
* 當您[升級 SKU][skus]時，存取較高的網域控制站。
* AD 帳戶鎖定保護。
* [受控網域上的警示電子郵件通知][email-alerts]。
* [使用 azure 活頁簿和 azure 監視器來查看審核記錄和登入活動][workbooks]。
* 在支援的區域中， [Azure 可用性區域][availability-zones]。
* 與其他 Azure 產品（例如[Azure 檔案儲存體][azure-files]、 [HD Insights][hd-insights]和[Windows 虛擬桌面][wvd]）的整合。
* 支援人員可以存取更多遙測資料，並有助於更有效率地進行疑難排解。
* 使用[Azure 受控磁碟][managed-disks]針對受管理網域控制站上的資料進行待用加密。

使用 Resource Manager 部署模型的受控網域，可協助您隨時掌握最新的新功能。 使用傳統部署模型的受控網域不提供新功能。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱將[Azure AD Domain Services 從傳統虛擬網路模型遷移至 Resource Manager][howto-migrate]。

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/windows/managed-disks-overview.md
