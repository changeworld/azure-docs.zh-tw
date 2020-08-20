---
title: Azure AD Domain Services 中傳統部署遷移的優點 |Microsoft Docs
description: 深入瞭解將傳統部署 Azure Active Directory Domain Services 遷移至 Resource Manager 部署模型的優點
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 93dcc1202c08be905cf08513f38e79a8a7674e01
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650122"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>從傳統遷移至 Resource Manager 部署模型的優點 Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) 可讓您將使用傳統部署模型的現有受控網域遷移至 Resource Manager 部署模型。 使用 Resource Manager 部署模型 Azure AD DS 受控網域可提供更細緻的密碼原則、audit 記錄和帳戶鎖定保護等額外功能。

本文概述遷移的優點。 若要開始使用，請參閱將 [Azure AD Domain Services 從傳統虛擬網路模型遷移到 Resource Manager][howto-migrate]。

> [!NOTE]
> 在2017中，Azure AD Domain Services 可用於 Azure Resource Manager 網路中的主機。 從那時開始，我們可以使用 Azure Resource Manager 的新式功能來建立更安全的服務。 因為 Azure Resource Manager 部署完全取代傳統部署，所以 Azure AD DS 傳統虛擬網路部署將于2023年3月1日淘汰。
>
> 如需詳細資訊，請參閱 [官方淘汰通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>遷移優點

遷移程式會使用現有的受控網域，此網域使用傳統部署模型，並移至使用 Resource Manager 部署模型。 當您將受控網域從傳統遷移至 Resource Manager 部署模型時，您不需要將機器重新加入受控網域或刪除受控網域，並從頭建立一個。 Vm 會在遷移過程結束時繼續加入受控網域。

在遷移之後，Azure AD DS 提供許多功能，這些功能僅適用于使用 Resource Manager 部署模型的網域，如下所示：

* 更[細緻的密碼原則支援][password-policy]。
* Azure AD 與 Azure AD Domain Services 之間的同步處理速度更快。
* [從 Azure AD][attributes]  -  *管理員*和擁有者同步處理的*employeeID*兩個新屬性。
* 當您 [升級 SKU][skus]時，可存取支援較高的網域控制站。
* AD 帳戶鎖定保護。
* [您受控網域上警示的電子郵件通知][email-alerts]。
* [使用 azure 活頁簿和 azure 監視器來查看 audit 記錄和登入活動][workbooks]。
* 在支援的區域中， [Azure 可用性區域][availability-zones]。
* 與其他 Azure 產品（例如 [Azure 檔案儲存體][azure-files]、 [HD Insights][hd-insights]和 [Windows 虛擬桌面][wvd]）整合。
* 支援人員可以存取更多遙測，並且有助於更有效率地進行疑難排解。
* 使用 [Azure 受控磁碟][managed-disks] 針對受控網域控制站上的資料進行靜態加密。

使用 Resource Manager 部署模型的受控網域可協助您隨時掌握最新的新功能。 使用傳統部署模型的受控網域無法使用新功能。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱將 [Azure AD Domain Services 從傳統虛擬網路模型遷移到 Resource Manager][howto-migrate]。

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
[managed-disks]: ../virtual-machines/managed-disks-overview.md
