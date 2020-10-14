---
title: 在 Azure DevTest Labs 中建立實驗室 | Microsoft Docs
description: 本文將逐步引導您完成使用 Azure 入口網站和 Azure DevTest Labs 建立實驗室的流程。
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9fa33a59dd35bfe3469f30f2349f8a08c45bd5e3
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058312"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>在 Azure 研測實驗室中建立實驗室

Azure DevTest Labs 中的實驗室是包含一組資源 (例如虛擬機器 (VM)) 的基礎結構，可讓您藉由指定限制和配額更好地管理這些資源。 本文將逐步引導您完成使用 Azure 入口網站來建立實驗室的程序。

## <a name="prerequisites"></a>必要條件

若要建立實驗室，您需要：

* Azure 訂用帳戶。 若要深入了解 Azure 購買選項，請參閱[如何購買 Azure](https://azure.microsoft.com/pricing/purchase-options/) 或[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。 您必須是訂用帳戶的擁有者才能建立實驗室。

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>在數分鐘內開始使用 Azure DevTest Labs

藉由按一下下列連結，您將會傳送至 Azure 入口網站頁面，讓您可以開始在 Azure DevTest Labs 中建立新的實驗室。

[在數分鐘內開始使用 Azure DevTest Labs](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>填寫新帳戶的設定

在 [ **建立 DevTest Labs** ] 頁面上，填入下列設定。

> [!TIP]
> 在每個頁面底部，您會看到一個連結，可讓您 **下載自動化的範本**。

### <a name="basic-settings"></a>基本設定

依預設，您會看到 [ **基本設定** ] 索引標籤。 

![基本設定](./media/devtest-lab-create-lab/basic-settings.png)

填寫下列值：

|名稱|描述|
|---|---|
|**訂用帳戶** | 必要。 選取要與實驗室關聯的 **訂用帳戶** 。|
|**資源群組**| 必要。 輸入實驗室之**資源群組的名稱**。 如果不存在，請建立一個新的。|
|**實驗室名稱**| 必要。 輸入實驗室的 **名稱** 。|
|**位置**|必要。 選取用來儲存實驗室的位置。|
|**公用環境**| 請參閱 [設定和使用公用環境](devtest-lab-configure-use-public-environments.md)。

### <a name="auto-shutdown-settings"></a>自動關機設定

切換至 **自動關機** 頁面以查看其設定。 自動關機可讓您每天在排程的時間自動關閉實驗室中的所有機器。

![自動關機索引標籤](./media/devtest-lab-create-lab/auto-shutdown.png)

在頁面上，您可以啟用 **自動關機** ，並定義所有實驗室 vm 的自動關機參數。 自動關閉功能主要是用來節省成本的功能，若您想要讓 VM 自動關閉，便可指定此功能。 您可以按照[在 Azure DevTest Labs 中管理實驗室的所有原則](./devtest-lab-set-lab-policy.md#set-auto-shutdown)一文所述的步驟，在建立實驗室之後變更自動關閉設定。

### <a name="networking"></a>網路

建立實驗室時，系統會為您建立預設網路。 切換至 [ **網路** ] 索引標籤，即可變更/設定您想要的設定。 例如，選取現有的虛擬網路。

![網路功能索引標籤 ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>標籤

如果您想要建立的自訂標記會新增至您將在實驗室中建立的每個資源，請輸入 [標籤]**** 的 [名稱]**** 和 [值]**** 資訊。 標籤很實用，可協助您依照類別管理及組織實驗室資源。 如需標籤的詳細資訊，包括如何在建立實驗室後新增標籤，請參閱[將標籤新增至實驗室](devtest-lab-add-tag.md)。

![標記索引標籤 ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>檢閱及建立

完成之後，請選取 [ **建立**]。 您可以監看入口網站頁面右上方的 [ **通知** ] 區域，以監視實驗室建立流程的狀態。 

![[建立] 索引標籤](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>完成建立

完成之後，[ **移至資源** ] 按鈕會出現在頁面底部和 [通知] 視窗中。 或者，您也可以重新整理 [DevTest Labs]**** 頁面，以在實驗室清單中查看新建立的實驗室。  

![建立服務](./media/devtest-lab-create-lab/create-2.png)

按 [ **移至資源** ] 按鈕，您將會進入新 DevTest Labs 帳戶的首頁。

![資源](./media/devtest-lab-create-lab/go-to-resource.png)

您也可以在 Azure 入口網站中搜尋 **DevTest Labs** 。 從清單中選取您的新帳戶，然後前往首頁。 

![已建立服務](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>後續步驟

建立您的實驗室之後，以下是要考慮的一些後續步驟：

* [安全存取實驗室](devtest-lab-add-devtest-user.md)
* [設定實驗室原則](devtest-lab-set-lab-policy.md)
* [建立實驗室範本](devtest-lab-create-template.md)
* [為您的 Vm 建立自訂構件](devtest-lab-artifact-author.md)
* [將 VM 新增至實驗室](devtest-lab-add-vm.md)

