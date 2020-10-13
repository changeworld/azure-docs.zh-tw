---
title: 在 Azure DevTest Labs 中建立實驗室 | Microsoft Docs
description: 本文將逐步引導您完成使用 Azure 入口網站和 Azure DevTest Labs 建立實驗室的流程。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 09999c5b0187f924f9cfbbc2afad8954adee0fd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481250"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>在 Azure 研測實驗室中建立實驗室
Azure DevTest Labs 中的實驗室是包含一組資源 (例如虛擬機器 (VM)) 的基礎結構，可讓您藉由指定限制和配額更好地管理這些資源。 本文將逐步引導您完成使用 Azure 入口網站來建立實驗室的程序。

## <a name="prerequisites"></a>Prerequisites
若要建立實驗室，您需要：

* Azure 訂用帳戶。 若要深入了解 Azure 購買選項，請參閱[如何購買 Azure](https://azure.microsoft.com/pricing/purchase-options/) 或[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。 您必須是訂用帳戶的擁有者才能建立實驗室。

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中建立實驗室的步驟
下列步驟說明如何使用 Azure 入口網站在 Azure DevTest Labs 中建立實驗室。 

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 從左側的主要功能表中，選取 [所有服務]**** \(位於清單頂端)。 選取 [DEVOPS]**** 區段中 [DevTest Labs]**** 旁邊的 * (星號)。 此動作會將 [DevTest Labs]**** 新增到左側導覽功能表，以便您下次輕鬆存取。 

    ![所有服務 - 選取 DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. 現在，選取左側導覽功能表上的 [DevTest Labs]****。 選取工具列上的 [新增]****。 
   
    ![新增實驗室](./media/devtest-lab-create-lab/add-lab-button.png)
1. 在 [建立 DevTest Labs]**** 頁面上，執行下列動作： 
    1. 輸入實驗室的 **名稱** 。
    2. 選取要與實驗室關聯的 **訂用帳戶** 。
    3. 輸入實驗室之**資源群組的名稱**。 
    4. 選取用來儲存實驗室的**位置**。
    4. 選取 [自動關機] **** 來指定是否要啟用所有實驗室 VM 的自動關閉，以及定義其參數。 自動關閉功能主要是用來節省成本的功能，若您想要讓 VM 自動關閉，便可指定此功能。 您可以按照[在 Azure DevTest Labs 中管理實驗室的所有原則](./devtest-lab-set-lab-policy.md#set-auto-shutdown)一文所述的步驟，在建立實驗室之後變更自動關閉設定。
    1. 如果您想要建立的自訂標記會新增至您將在實驗室中建立的每個資源，請輸入 [標籤]**** 的 [名稱]**** 和 [值]**** 資訊。 標籤很實用，可協助您依照類別管理及組織實驗室資源。 如需標籤的詳細資訊，包括如何在建立實驗室後新增標籤，請參閱[將標籤新增至實驗室](devtest-lab-add-tag.md)。
    6. 選取 [自動化選項]**** 來取得可自動進行設定的 Azure Resource Manager 範本。 
    7. 選取 [建立]****。 您可以監看 [通知]**** 區域，以監控實驗室建立程序的狀態。 
    
        ![建立 DevTest Labs 的實驗室區段](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. 完成時，選取通知中的 [前往資源]****。 或者，您也可以重新整理 [DevTest Labs]**** 頁面，以在實驗室清單中查看新建立的實驗室。  從清單中選取該實驗室。 您會看到實驗室的首頁。 

        ![實驗室的首頁](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>接下來的步驟
建立您的實驗室之後，以下是要考慮的一些後續步驟：

* [安全存取實驗室](devtest-lab-add-devtest-user.md)
* [設定實驗室原則](devtest-lab-set-lab-policy.md)
* [建立實驗室範本](devtest-lab-create-template.md)
* [為您的 Vm 建立自訂構件](devtest-lab-artifact-author.md)
* [將 VM 新增至實驗室](devtest-lab-add-vm.md)

