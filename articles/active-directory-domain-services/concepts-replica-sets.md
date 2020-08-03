---
title: Azure AD Domain Services 的複本集概念 |Microsoft Docs
description: 瞭解哪些複本集在 Azure Active Directory Domain Services 中，以及它們如何為需要身分識別服務的應用程式提供冗余。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: iainfou
ms.openlocfilehash: 698009ee8a57ed5d30e01376b4f2c63b0a27ead8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505727"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services-preview"></a>Azure Active Directory Domain Services 的複本集概念和功能（預覽）

當您建立 Azure Active Directory Domain Services （Azure AD DS）受控網域時，您會定義唯一的命名空間。 這個命名空間是功能變數名稱（例如*aaddscontoso.com*），然後將兩個網域控制站（dc）部署到您選取的 Azure 區域。 此 Dc 的部署稱為「複本集」。

您可以擴充受控網域，讓每個 Azure AD 租使用者都有一個以上的複本集。 可以將複本集新增至任何支援 Azure AD DS 的 Azure 區域中的任何對等互連虛擬網路。 不同 Azure 區域中的其他複本集，可在 Azure 區域離線時，為繼承應用程式提供地理災難復原。

複本集目前為預覽狀態。

> [!NOTE]
> 複本集不會讓您在單一 Azure 租使用者中部署多個唯一的受控網域。 每個複本集都包含相同的資料。

## <a name="how-replica-sets-work"></a>複本集的工作方式

當您建立受控網域（例如*aaddscontoso.com*）時，會建立初始複本集。 其他複本集共用相同的命名空間和設定。 Azure AD DS 的變更，包括設定、使用者識別和認證、群組、群組原則物件、電腦物件和其他變更，會使用 AD DS 複寫套用至受控網域中的所有複本集。

您會在虛擬網路中建立每個複本集。 每個虛擬網路都必須對等互連至裝載受控網域之複本集的其他每個虛擬網路。 此設定會建立支援目錄複寫的網格網路拓朴。 虛擬網路可以支援多個複本集，前提是每個複本集都在不同的虛擬子網中。

所有複本集都放在相同的 Active Directory 網站中。 因此，所有的變更都會使用站上複寫來進行快速聚合。

> [!NOTE]
> 您無法定義個別的網站，並在複本集之間定義複寫設定。

下圖顯示具有兩個複本集的受控網域。 第一個複本集是使用網域命名空間所建立。 接著會建立第二個複本集：

![包含兩個複本集的範例受控網域圖表](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> 複本集可確保在設定複本集的區域中，驗證服務的可用性。 若要讓應用程式在發生區域性中斷時擁有地理位置冗余，依賴受控網域的應用程式平臺也必須位於另一個區域。
>
> 複本集不會提供應用程式運作所需的其他服務（例如 Azure Vm 或 Azure App 服務）的復原。 其他應用程式元件的可用性設計需要考慮組成應用程式之服務的復原功能。

下列範例顯示具有三個複本集的受控網域，可進一步提供復原並確保驗證服務的可用性。 在這兩個範例中，應用程式工作負載都存在於與受控網域複本集相同的區域中：

![包含三個複本集的範例受控網域圖表](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>部署考量因素

受控網域的預設 SKU 是*企業*SKU，它支援多個複本集。 若要建立其他複本集，如果您變更為*標準*SKU，請將[受控網域升級](change-sku.md)為*Enterprise*或*Premium*。

預覽期間支援的複本集數目上限為四個，包括您在建立受控網域時所建立的第一個複本。

每個複本集的計費是以網域設定 SKU 為基礎。 例如，如果您有使用*企業*SKU 的受控網域，而且有三個複本集，則您的訂用帳戶會針對三個複本集的每小時計費。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-use-my-production-managed-domain-with-this-preview"></a>我可以在此預覽中使用生產受控網域嗎？

複本集是 Azure AD Domain Services 中的公開預覽功能。 您可以使用生產受控網域，但請留意功能仍處於預覽狀態的支援差異。 如需預覽的詳細資訊，請[Azure Active Directory 預覽 SLA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>我可以在訂用帳戶中建立與受控網域不同的複本集嗎？

否。 複本集必須位於與受控網域相同的訂用帳戶中。

### <a name="how-many-replica-sets-can-i-create"></a>我可以建立多少個複本集？

預覽最多隻能有四個複本集，也就是受控網域的初始複本集，再加上三個額外的複本集。

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>使用者和群組資訊如何與我的複本集同步處理？

所有複本集會使用網格虛擬網路對等互連彼此連接。 一個複本集會從 Azure AD 接收使用者和群組更新。 然後，這些變更會透過對等互連網路使用網站間 AD DS 複寫，複寫到其他複本集。

就像內部部署 AD DS，延伸的中斷線上狀態可能會導致複寫中斷。 由於對等互連的虛擬網路無法轉移，因此複本集的設計需求需要完整網狀網路拓朴。

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>在我有複本集之後，如何? 在受控網域中進行變更嗎？

受控網域內的變更與先前的工作一樣。 您可以[使用已加入受控網域的 RSAT 工具來建立及使用管理 VM](tutorial-create-management-vm.md)。 您可以視需要將多個管理 Vm 加入受控網域。

## <a name="next-steps"></a>後續步驟

若要開始使用複本集，請[建立並設定 AZURE AD DS 受控網域][tutorial-create-advanced]。 部署時，請[建立並使用其他複本集][create-replica-set]。

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
