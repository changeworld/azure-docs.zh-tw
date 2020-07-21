---
title: 在 Azure DevTest Labs 中設定 Azure Marketplace 映像設定
description: 設定在 Azure DevTest Labs 中建立 VM 時可以使用哪些 Azure Marketplace 映像
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512430"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定 Azure Marketplace 映像設定
DevTest Labs 支援根據 Azure Marketplace 映像來建立 VM，而這取決於您設定 Azure Marketplace 映像使用於實驗室的方式。 本文將說明在實驗室中建立 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。 它可確保您的小組僅能存取所需的 Marketplace 映射。 

## <a name="specify-allowed-images-for-creating-vms"></a>指定建立 Vm 所允許的映射
遵循這些步驟，以指定在建立 VM 時允許的 Azure Marketplace 映射。 

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
3. 從實驗室清單中選取您的實驗室。 
4. 在 [實驗室] 頁面的首頁上，選取 [設定**和原則**]。
5. 在實驗室的 [設定**與原則**] 頁面的 [**虛擬機器基底**] 底下，選取 [ **Marketplace 映射**]。
6. 指定是否要讓所有合格的 Azure Marketplace 映像可用來做為新 VM 的基底。 如果您選取 [是] ****，則實驗室中允許所有符合下列準則的 Azure Marketplace 映像︰
   
   * 映像會建立單一 VM， **而且**
   * 映像會使用 Azure Resource Manager 來佈建 VM， **而且**
   * 映像不需要購買額外的授權方案
     
     如果您不想允許任何映像，或者想要指定可以使用哪些映像，請選取 [否] ****。
     
     ![允許使用所有 Marketplace 映像做為 VM 基底映像的選項](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. 如果您在上一個步驟中選取 [否]****，將會啟用 [允許的映像/全選]**** 核取方塊。 
   您可以將此選項與搜尋方塊一起使用，快速選取或取消選取清單中顯示的所有項目。
   * 藉由核取每個映像的對應核取方塊，個別選取您想要允許來建立 VM 的 Azure Marketplace 映像。
   * 如果您不想在實驗室中允許使用任何 Azure Marketplace 映像，請不要從清單中選取任何項目。
   
     ![您可以指定可使用哪些 Marketplace 映像做為 VM 的基底映像](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>疑難排解
如果您找不到要啟用實驗室的特定映射，請遵循下列步驟： 

- 查看建立計算 VM 時是否可以查看映射。
- 映射可能無法在您使用的訂用帳戶類型中使用。 請洽詢訂用帳戶管理員以取得訂閱的類型（例如： MSDN、免費、隨用隨付，依此類推）。 
- DevTest Labs 中的 Gen 2 映射支援有限。 如果 Gen 1 和 Gen 2 版本均適用於某個映像，在建立 VM 時，DevTest Labs 只會顯示該映像的 Gen 1 版本。 因應措施是在實驗室外部建立自訂 Gen 2 映射，並使用它來建立 VM。 如果只有 Gen 2 版本的映射可用，DevTest Labs 就會支援並顯示在清單中。 
      


## <a name="next-steps"></a>後續步驟
在您設定建立 VM 時允許 Azure Marketplace 映射的方式之後，下一步就是[將 vm 新增至您的實驗室](devtest-lab-add-vm.md)。

