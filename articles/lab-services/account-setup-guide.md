---
title: 適用于 Azure 實驗室服務的加速實驗室帳戶設定指南
description: 本指南可協助系統管理員快速設定實驗室帳戶，以便在其學校內使用。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0855e59aced37e50c088cfe89ffeb3d0af9fcdca
ms.sourcegitcommit: 8ad5761333b53e85c8c4dabee40eaf497430db70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148270"
---
# <a name="lab-account-setup-guide"></a>實驗室帳戶設定指南

在第一個步驟中，系統管理員應在您的 Azure 訂用帳戶內設定實驗室帳戶。 實驗室帳戶是教室實驗室的容器，只需要幾分鐘的時間就能完成設定。

## <a name="understand-your-schools-lab-account-requirements"></a>瞭解您學校的實驗室帳戶需求

若要瞭解如何根據您學校的需求來設定實驗室帳戶，您應該考慮這些問題。

### <a name="do-i-have-access-to-an-azure-subscription"></a>我可以存取 Azure 訂用帳戶嗎？

若要建立實驗室帳戶，您需要存取為學校設定的 Azure 訂用帳戶。 您的學校可能會有一或多個訂用帳戶。 您可以使用訂用帳戶來管理所有 Azure 資源和服務（包括實驗室帳戶）的計費和安全性。

### <a name="how-many-lab-accounts-need-to-be-created"></a>需要建立多少實驗室帳戶？

若要快速開始使用，請建立單一實驗室帳戶，稍後視需要建立其他實驗室帳戶。 例如，您的每個部門最終可能會有一個實驗室帳戶。

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>誰應該是實驗室帳戶的擁有者和參與者？

您的系統管理員通常是實驗室帳戶的擁有者和參與者。 他們負責管理適用于實驗室帳戶中所有實驗室的原則。 建立實驗室帳戶的人員會自動成為擁有者。 您可以新增其他擁有者和參與者，通常是來自與您的訂用帳戶相關聯的 Azure Active Directory (Azure AD) 租使用者。 這有助於在實驗室帳戶層級指派擁有者或參與者角色，以協助管理實驗室帳戶。

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

實驗室使用者只會看到可在 Azure 實驗室服務內跨租使用者存取的虛擬機器的單一清單。

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>誰將可以建立和管理實驗室？

您可以選擇讓系統管理員和教職員成員建立及管理實驗室。 這些使用者通常會從與您的訂用帳戶相關聯的 Azure AD 租使用者中 (，) 會指派給實驗室帳戶內的實驗室建立者角色。

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>您是否要讓實驗室建立者能夠儲存可跨實驗室共用的映射？

共用映射庫是您可以用來儲存和共用映射的存放庫。 如果您有數個需要相同映射的類別，實驗室建立者可以建立一次映射，並在實驗室之間共用。 不過，若要開始使用，您不一定需要共用映射庫，因為您稍後可以隨時新增一個映射庫。

如果您對這個問題回答「是」，則需要建立共用映射庫，或將其附加至您的實驗室帳戶。 如果您已經回答「我不知道」，則可以延後此決策，直到稍後為止。

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>您的教室實驗室會使用 Azure Marketplace 中的哪些映射？

Azure Marketplace 提供數百個可讓您啟用的映射，讓實驗室建立者可以使用此映射來建立其實驗室。 某些映射可能包含實驗室已需要的所有專案。 在其他情況下，您可以使用映射做為起點，然後實驗室建立者可以藉由安裝其他應用程式或工具來自訂它。

如果您不知道將需要使用哪些映射，您可以稍後再回來以啟用。 此外，查看可用影像的最佳方式是先建立實驗室帳戶。 這可提供您存取權，讓您可以查看可用影像的清單及其內容。
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>實驗室的虛擬機器是否需要存取其他 Azure 或內部部署資源？

當您設定實驗室帳戶時，您也可以選擇使用虛擬網路對等互連。 若要決定是否需要這樣做，請考慮下列問題：

- **您是否需要提供授權伺服器的存取權？**
  
   如果您打算使用 Azure Marketplace 映射，則會將作業系統授權的成本併入實驗室服務的定價。 因此，您不需要為作業系統本身提供授權。 不過，對於安裝的其他軟體和應用程式，您必須適當地提供授權。

- **實驗室 Vm 是否需要存取其他內部部署資源，例如檔案共用或資料庫？**

   您可以建立虛擬網路，以提供內部部署資源的存取權，通常是使用站對站虛擬網路閘道。 如果您沒有設定虛擬網路，您需要投入更多時間來進行此工作。

- **實驗室 Vm 是否需要存取位於虛擬網路內的其他 Azure 資源？**

   如果您需要存取在虛擬網路內 *未* 受保護的 Azure 資源，您可以透過公用網際網路存取這些資源，而不需進行任何對等互連。

如果您對一或多個問題回答「是」，則必須將實驗室帳戶對等互連到虛擬網路。 如果您已經回答「我不知道」，則可以延後此決策，直到稍後為止。 建立實驗室帳戶之後，您隨時都可以選擇對等互連虛擬網路。

## <a name="set-up-your-lab-account"></a>設定您的實驗室帳戶

瞭解實驗室帳戶的需求之後，您就可以開始進行設定。

1. **建立您的實驗室帳戶。** 如需相關指示，請參閱 [建立實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) 的教學課程。

   當您建立實驗室帳戶時，您可能會發現熟悉相關的 Azure 資源是很有説明的。 如需詳細資訊，請參閱下列文章：

   - [訂用帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [資源群組](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [教室實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [選取區域和位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [資源的命名指導方針](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **將使用者新增至實驗室建立者角色。** 如需相關指示，請參閱 [將使用者新增至實驗室建立者角色](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)。

   此外，如需可指派給將管理實驗室帳戶和實驗室之使用者的不同角色的詳細資訊，請參閱 [管理身分識別的指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)。

3. **連接至對等虛擬網路。** 如需相關指示，請參閱 [使用對等虛擬網路連接實驗室的網路](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)。

   您可能也需要參閱設定 [實驗室 vm 位址範圍的](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)指示。

4. **啟用和審核映射。** 如需相關指示，請參閱 [啟用實驗室建立者的 Azure Marketplace 映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

   若要檢查每個 Azure Marketplace 影像的內容，請選取映射名稱。 例如，下列螢幕擷取畫面顯示 Ubuntu 資料科學 VM 映射的詳細資料：

   ![審核 Azure Marketplace 影像的螢幕擷取畫面](./media/setup-guide/review-marketplace-images.png)

   如果您有連結到實驗室帳戶的共用映射庫，而且您想要讓實驗室建立者共用自訂映射，請完成類似下列螢幕擷取畫面所示的步驟：

   ![在共用映射庫中啟用自訂映射的螢幕擷取畫面](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>後續步驟

- [管理實驗室帳戶](how-to-manage-lab-accounts.md)

- [教室實驗室設定指南](setup-guide.md)
