---
title: Azure 實驗室服務的加速實驗室帳戶設置指南
description: 本指南可説明管理員快速設置實驗室帳戶,供其學校使用。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879576"
---
# <a name="lab-account-setup-guide"></a>實驗室帳戶設置指南

作為第一步,管理員應在 Azure 訂閱中設置實驗室帳戶。 實驗室帳戶是教室實驗室的容器,只需幾分鐘即可設置。

## <a name="understand-your-schools-lab-account-requirements"></a>瞭解您學校的實驗室帳戶要求

要瞭解如何根據學校的需求配置實驗室帳戶,應考慮這些問題。

### <a name="do-i-have-access-to-an-azure-subscription"></a>我有權訪問 Azure 訂閱嗎?

要創建實驗室帳戶,您需要訪問為學校配置的 Azure 訂閱。 您的學校可能有一個或多個訂閱。 您可以使用訂閱來管理所有 Azure 資源和服務的計費和安全,包括實驗室帳戶。

### <a name="how-many-lab-accounts-need-to-be-created"></a>需要創建多少個實驗室帳戶?

要快速入門,請創建單個實驗室帳戶,然後根據需要創建其他實驗室帳戶。 例如,您可能最終每個部門有一個實驗室帳戶。

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>誰應該是實驗室帳戶的擁有者和貢獻者?

管理員通常是實驗室帳戶的所有者和參與者。 他們負責管理適用於實驗室帳戶中包含的所有實驗室的策略。 創建實驗室帳戶的人員將自動成為擁有者。 您可以添加其他擁有者和參與者,通常來自與訂閱關聯的 Azure 活動目錄 (Azure AD) 租戶。 通過在實驗室帳戶級別分配所有者或參與者角色來説明管理實驗室帳戶非常有用。

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>誰將被允許創建和管理實驗室?

您可以選擇讓管理員和教職員工創建和管理實驗室。 這些使用者(通常來自與您的訂閱關聯的 Azure AD 租戶)被分配到實驗室帳戶中的實驗室建立者角色。

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>是否要使實驗室建立者能夠保存可在實驗室之間共用的圖像?

共用映射庫是可用於保存和共用映射的儲存庫。 如果您有多個類需要相同的圖像,實驗室建立者可以創建映射一次,並在實驗室之間共用它。 但是,要開始,您不一定需要共用圖像庫,因為您以後可以隨時添加一個。

如果您對此問題回答"是",則需要創建共用圖像庫或將共用圖像庫附加到實驗室帳戶。 如果你回答"我不知道",你可以推遲這個決定,直到以後。

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>您的教室應用商店將使用哪些映射?

Azure 應用商店提供了數百個映射,您可以啟用這些映射,以便實驗室建立者可以使用該映射創建其實驗室。 某些圖像可能包含實驗室已經需要的所有內容。 在其他情況下,您可以使用圖像作為起點,然後實驗室建立者可以通過安裝其他應用程式或工具對其進行自定義。

如果您不知道需要使用哪些圖像,則以後可以隨時返回以啟用它們。 此外,查看哪些圖像可用的最佳方式是首先創建實驗室帳戶。 這為您提供了訪問許可權,以便您可以查看可用圖像及其內容的清單。
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>實驗室的虛擬機是否需要訪問其他 Azure 或本地資源?

設置實驗室帳戶時,還可以選擇與虛擬網路對等。 要確定是否需要這樣做,請考慮以下問題:

- **您是否需要提供對許可伺服器的訪問許可權?**
  
   如果計劃使用 Azure 應用商店映射,操作系統許可證的成本將捆綁到實驗室服務的定價中。 因此,您無需為操作系統本身提供許可證。 但是,對於已安裝的其他軟體和應用程式,您確實需要提供相應的許可證。

- **實驗室 VM 是否需要訪問其他本地資源(如檔案共享或資料庫)?**

   創建虛擬網路以提供對本地資源的訪問許可權,通常通過使用網站到網站的虛擬網路閘道。 如果未配置虛擬網路,則需要為此投入更多時間。

- **實驗室 VM 是否需要造訪位於虛擬網路內的其他 Azure 資源?**

   如果需要造訪虛擬網路中*未*受保護的 Azure 資源,則可以通過公共 Internet 訪問這些資源,而無需進行任何對等互連。

如果您對一個或多個問題回答"是",則需要將實驗室帳戶與虛擬網路對等。 如果你回答"我不知道",那麼你可以推遲這個決定,直到以後。 創建實驗室帳戶后,始終可以選擇對虛擬網路進行對等。

## <a name="set-up-your-lab-account"></a>設定實驗室帳戶

了解實驗室帳戶的要求后,即可將其設置。

1. **創建實驗室帳戶。** 有關[創建實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)的說明,請參閱教程。

   創建實驗室帳戶時,您可能會發現熟悉所涉及的 Azure 資源會很有説明。 如需詳細資訊，請參閱下列文章：

   - [訂用帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [資源群組](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [課堂實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [選擇區域與位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [具資源指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **將使用者添加到實驗室建立者角色。** 有關說明,請參閱[將使用者添加到實驗室建立者角色](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)。

   此外,有關可以分配給將管理實驗室帳戶和實驗室的使用者的不同角色的詳細資訊,請參閱[有關管理標識的指南](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)。

3. **連接到對等虛擬網路。** 有關說明,請參閱[將實驗室網路與對等虛擬網路連接](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)。

   您可能還需要參考有關[配置實驗室 VM 位址範圍](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)的說明。

4. **啟用和查看圖像。** 有關說明,請參閱[為實驗室建立者啟用 Azure 應用商店映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

   要查看每個 Azure 應用商店映像的內容,請選擇映像名稱。 例如,以下螢幕截圖顯示了 Ubuntu 資料科學 VM 映像的詳細資訊:

   ![審核 Azure 應用商店映像的螢幕截圖](../media/setup-guide/review-marketplace-images.png)

   如果您有一個共用影像庫附加到實驗室帳戶,並且希望允許自定義圖像由實驗室建立者共用,請完成類似於以下螢幕截圖中所示的步驟:

   ![在分享影像庫中啟用自訂影像的螢幕擷取](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>後續步驟

- [管理實驗室帳戶](how-to-manage-lab-accounts.md)

- [課堂實驗室設置指南](setup-guide.md)
