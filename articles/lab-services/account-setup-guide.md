---
title: 適用于 Azure 實驗室服務的加速實驗室帳戶設定指南
description: 本指南可協助系統管理員快速設定實驗室帳戶，以便在其學校內使用。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659857"
---
# <a name="lab-account-setup-guide"></a>實驗室帳戶設定指南
若要設定您的 Azure 實驗室服務環境，系統管理員必須先在您的 Azure 訂用帳戶中設定 **實驗室帳戶** 。 實驗室帳戶是您實驗室的容器，只需要幾分鐘的時間就能完成設定。

本指南包含三個區段：
-  第一個區段著重于設定實驗室帳戶 *前* 必須完成的必要條件。
-  第二節提供規劃實驗室帳戶設定的指引。
-  第三節提供設定實驗室帳戶的逐步指示。

## <a name="prerequisites-for-setting-up-your-lab-account"></a>設定實驗室帳戶的必要條件
本節將概述您在設定實驗室帳戶之前必須完成的必要條件。

### <a name="obtain-an-azure-subscription"></a>取得 Azure 訂用帳戶
若要建立實驗室帳戶，您需要存取為學校設定的 Azure 訂用帳戶。 您的學校可能會有一或多個訂用帳戶。 您可以使用訂用帳戶來管理所有 Azure 資源和服務（包括實驗室帳戶）的計費和安全性。  Azure 訂用帳戶通常是由您的 IT 部門管理。  如需詳細資訊，請參閱下列主題：
 - [系統管理員指南-訂用帳戶](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>估計您所需的 Vm 數目和 VM 大小
您將需要預估 (Vm) 的虛擬機器數目，以及您學校需要的 [vm 大小](./administrator-guide.md#vm-sizing) 。  閱讀下列 blog 文章，以取得如何結構 labs\images. 的指引  此 blog 文章也可協助您決定您需要的 Vm 數目和 VM 大小：
- [從實體實驗室移至 Azure 實驗室服務](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

此外，請參閱這篇文章，其中說明如何結構實驗室的其他指導方針：
- [系統管理員指南-實驗室](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>瞭解訂用帳戶 VM 限制和區域 VM 容量
一旦您估計實驗室的 Vm 數目和 VM 大小，您需要：

- 確定您的 Azure 訂用帳戶容量限制允許 Vm 數目和您打算在實驗室中使用的 VM 大小。

- 在具有足夠可用 VM 容量的區域內建立您的實驗室帳戶。

若要深入瞭解，請閱讀下列 blog 文章： [VM 訂用帳戶限制和區域容量](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)。

### <a name="decide-how-many-lab-accounts-to-create"></a>決定要建立的實驗室帳戶數量

若要快速開始使用，請在自己的資源群組中建立單一實驗室帳戶。  稍後，您可以視需要建立額外的實驗室帳戶 (和資源群組) 。 例如，您最終可能會有一個實驗室帳戶和每個部門的資源群組，做為清楚分隔成本的方式。  請參閱下列文章，以深入瞭解實驗室帳戶、資源群組和分隔成本：
- [系統管理員指南-資源群組](./administrator-guide.md#resource-group)
- [系統管理員指南-實驗室帳戶](./administrator-guide.md#lab-account) 
- [Azure 實驗室服務的成本管理](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>規劃實驗室帳戶的設定

若要規劃實驗室帳戶的設定，您應該考慮下列問題。

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>誰應該是實驗室帳戶的擁有者和參與者？

   您學校的 IT 系統管理員通常是實驗室帳戶的擁有者和參與者。 他們負責管理適用于實驗室帳戶內所有實驗室的原則。 建立實驗室帳戶的人員會自動成為擁有者。 您可以從與訂用帳戶相關聯的 Azure Active Directory (AD) 租使用者中新增其他擁有者和參與者。 如需實驗室帳戶擁有者和參與者角色的詳細資訊，請參閱：
   -  [系統管理員指南-管理身分識別](./administrator-guide.md#manage-identity)。

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   實驗室使用者只會看到可在 Azure 實驗室服務內跨租使用者存取的虛擬機器的單一清單。

### <a name="who-will-be-allowed-to-create-labs"></a>誰將可建立實驗室？

   您可以選擇讓 IT 和教職員成員建立實驗室。 當使用者建立實驗室時，系統會自動將他們指派為實驗室的擁有者。  若要建立實驗室，使用者通常會從與您的訂用帳戶相關聯的 Azure AD 租使用者中 () 必須指派給實驗室帳戶中的實驗室建立者角色。  如需實驗室建立者角色的詳細資訊，請參閱：
   -  [系統管理員指南-管理身分識別](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>誰將可擁有及管理實驗室？

   您也可以選擇讓 IT 和教職員成員 own\manage 實驗室， *而不* 讓他們能夠建立實驗室。  在此情況下，來自訂用帳戶 Azure AD 租使用者的使用者會被指派現有實驗室的擁有者或參與者。  如需實驗室的擁有者和參與者角色的詳細資訊，請參閱：
   - [系統管理員指南-管理身分識別](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>是否要儲存可以跨實驗室共用的映射？
共用映射庫是您可以用來儲存和共用映射的存放庫。 針對需要相同映射的類別，實驗室建立者可以建立映射，然後將它匯出至共用映射庫。  將映射匯出至共用映射庫之後，就可以使用它來建立新的實驗室。

此外，您可能會想要在實體環境中建立映射，然後將其匯入至共用映射庫。  如需此程式的詳細資訊，請閱讀下列 blog 文章： 
- [將自訂映射匯入共用映射庫](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

如果您決定需要使用共用映射庫，您必須建立共用映射庫，或將其附加至您的實驗室帳戶。 此外，您可以在稍後將此決策延後，因為它可以在任何時間附加至實驗室帳戶。  如需共用映射庫的詳細資訊，請參閱：
- [系統管理員指南-共用映射庫](./administrator-guide.md#shared-image-gallery)
- [系統管理員指南-共用映射庫定價](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>您的實驗室會使用 Azure Marketplace 中的哪些映射？
Azure Marketplace 提供數百個可讓您啟用的映射，讓實驗室建立者可以使用此映射來建立其實驗室。 某些映射可能包含實驗室已需要的所有專案。 在其他情況下，您可以使用映射做為起點，然後實驗室建立者可以藉由安裝其他應用程式或工具來自訂它。

如果您不知道所需的映射，您可以稍後再回來啟用。 此外，查看可用影像的最佳方式是先建立實驗室帳戶。 這可讓您存取，讓您可以查看可用影像的清單及其內容。  如需 marketplace 映射的詳細資訊，請參閱：
- [指定實驗室建立者可用的 Marketplace 映像](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>實驗室的 Vm 是否需要有其他 Azure 或內部部署資源的存取權？
當您設定實驗室帳戶時，您也可以將實驗室帳戶與虛擬網路（ (VNet) ）對等互連。  請記住，您的 VNet 和實驗室帳戶都必須位於相同的區域。  若要決定您是否需要對等互連，請考慮下列案例：

- **授權伺服器的存取權**
  
   當您使用 Azure Marketplace 映射時，作業系統授權的成本會與實驗室服務的定價配套。 不過，您不需要為作業系統本身提供授權。 不過，對於安裝的其他軟體和應用程式，您必須適當地提供授權。  若要存取授權伺服器：
   - 您可以選擇連接至內部部署授權伺服器。  連接至內部部署授權伺服器需要額外的設定。
   - 另一個更快設定的選項是建立您在 Azure VM 上裝載的授權伺服器。  Azure VM 位於您與實驗室帳戶相同的虛擬網路內。

- **存取其他內部部署資源，例如檔案共用或資料庫**

   您可以建立 VNet 來提供內部部署資源的存取權，通常是使用站對站虛擬網路閘道。 設定這類環境需要額外的時間。

- **存取位於 VNet 之外的其他 Azure 資源**

   如果您需要存取在 VNet 內 *未* 受保護的 Azure 資源，您可以透過公用網際網路存取這些資源，而不需進行任何對等互連。

如需虛擬網路的詳細資訊，請參閱：
- [架構基礎-虛擬網路](./classroom-labs-fundamentals.md#virtual-network)
- [如何連接至虛擬網路](./how-to-connect-peer-virtual-network.md)
- [如何使用 Azure 實驗室服務中的共用資源建立實驗室](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>設定您的實驗室帳戶

完成規劃之後，您就可以開始設定您的實驗室帳戶。  這些相同的步驟適用于使用小組來設定實驗室 [Azure 實驗室服務](./lab-services-within-teams-overview.md)。

1. **建立您的實驗室帳戶。** 如需相關指示，請參閱 [建立實驗室帳戶](./tutorial-setup-lab-account.md#create-a-lab-account) 的教學課程。
   
    如需有關命名的詳細指引，請參閱下列文章：

   - [資源的命名指導方針](./administrator-guide.md#naming)

2. **將使用者新增至實驗室建立者角色。** 如需相關指示，請參閱 [將使用者新增至實驗室建立者角色](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。


3. **連接至對等虛擬網路。** 如需相關指示，請參閱 [使用對等虛擬網路連接實驗室的網路](./how-to-connect-peer-virtual-network.md)。

   您可能也需要參閱設定 [實驗室 vm 位址範圍的](./how-to-configure-lab-accounts.md)指示。

4. **啟用和審核映射。** 如需相關指示，請參閱 [啟用實驗室建立者的 Azure Marketplace 映射](./specify-marketplace-images.md)。

   若要檢查每個 Azure Marketplace 影像的內容，請選取映射名稱。 例如，下列螢幕擷取畫面顯示 Ubuntu 資料科學 VM 映射的詳細資料：

   ![審核 Azure Marketplace 影像的螢幕擷取畫面](./media/setup-guide/review-marketplace-images.png)

   如果共用映射庫已連結至您的實驗室帳戶，而且您想要讓實驗室建立者共用自訂映射，請完成類似下列螢幕擷取畫面所示的類似步驟：

   ![在共用映射庫中啟用自訂映射的螢幕擷取畫面](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>後續步驟

設定實驗室環境的常見步驟如下：

- [管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [實驗室設定指南](setup-guide.md)