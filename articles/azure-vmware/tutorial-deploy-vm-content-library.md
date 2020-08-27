---
title: 教學課程：建立內容庫以在 Azure VMware Solution 中部署 VM
description: 在這個 Azure VMware Solution 教學課程中，您會建立內容庫以在 Azure VMware 解決方案私人雲端中部署 VM。
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 3abaafac0dbd6f3537d2ca30a093627230780eb5
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750548"
---
# <a name="tutorial-create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>教學課程：建立內容庫以在 Azure VMware Solution 中部署 VM

內容庫會以程式庫項目的形式來儲存和管理內容。 單一程式庫項目是由您用來部署虛擬機器 (VM) 的一或多個檔案所組成。 
 
在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 建立內容庫
> * 將 ISO 映像上傳至內容庫
> * 在內容庫中使用 ISO 部署 VM

## <a name="prerequisites"></a>必要條件

需要使用 NSX-T 邏輯交換器區段和受控 DHCP 服務，才能完成本教學課程。  如需詳細資訊，請參閱[如何在 Azure VMWare 解決方案預覽中管理 DHCP](manage-dhcp.md)。

## <a name="create-a-content-library"></a>建立內容庫

1. 從內部部署 vSphere 用戶端，選取 [功能表] > [內容庫]。

   ![選取 [功能表] -> [內容庫]](./media/content-library/vsphere-menu-content-libraries.png)

1. 按一下 [新增] 按鈕建立新的內容庫。

   ![按一下 [新增] 按鈕建立新的內容庫。](./media/content-library/create-new-content-library.png)

1. 指定名稱並確認 vCenter 伺服器的 IP 位址，然後選取 [下一步]。

   ![指定您選擇的名稱和附註，然後選取 [下一步]。](./media/content-library/new-content-library-step1.png)

1. 選取 [本機內容庫] 然後選取 [下一步]。

   ![在此範例中，我們將建立本機內容庫，然後選取 [下一步]。](./media/content-library/new-content-library-step2.png)

1. 選取將儲存內容庫的資料存放區，然後選取 [下一步]。

   ![選取您想要裝載內容庫的資料存放區，然後選取 [下一步]。](./media/content-library/new-content-library-step3.png)

1. 檢閱並確認內容庫設定，然後選取 [完成]。

   ![確認您的設定，然後選取 [完成]。](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>將 ISO 映像上傳至內容庫

既然已建立內容庫，您可以新增 ISO 映像來將 VM 部署至私人雲端叢集。 

1. 從 vSphere 用戶端，選取 [功能表] > [內容庫]。

1. 以滑鼠右鍵按一下要用於新 ISO 的內容庫，然後選取 [匯入項目]。

1. 執行下列其中一項動作來匯入來源的程式庫項目，然後選取 [匯入]：
   1. 選取 [URL]，並提供下載 ISO 的 URL。

   1. 選取 [本機檔案] 以從本機系統上傳。

   > [!TIP]
   > 您可以選擇性地定義目的地的自訂項目名稱和附註。

1. 開啟程式庫，然後選取 [其他類型] 索引標籤，確認已成功上傳您的 ISO。


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>將 VM 部署至私人雲端叢集

1. 從 vSphere 用戶端，選取 [功能表] > [主機和叢集]。

1. 在左面板中，展開樹狀結構並選取叢集。

1. 選取 [動作] > [新增虛擬機器]。

1. 完成精靈的操作，視需要修改設定。

1. 選取 [新增 CD/DVD 光碟機] > [用戶端裝置] > [內容庫 ISO 檔案]。

1. 選取上一節中所上傳的 ISO，然後選取 [確定]。

1. 選取 [連線] 核取方塊，以便在開機時掛接 ISO。

1. 選取 [新增網路] > [選取] 下拉式清單 > [瀏覽]。

1. 選取 [邏輯交換器 (區段)] 然後選取 [確定]。

1. 修改任何其他硬體設定，然後選取 [下一步]。

1. 確認設定，然後選取 [完成]。


## <a name="next-steps"></a>後續步驟

如果您打算使用混合式雲端擴充功能 (HCX) 將 VM 工作負載遷移至私人雲端，請使用[安裝適用於 Azure VMware Solution 的 HCX](hybrid-cloud-extension-installation.md)程序。

<!-- LINKS - external-->

<!-- LINKS - internal -->
