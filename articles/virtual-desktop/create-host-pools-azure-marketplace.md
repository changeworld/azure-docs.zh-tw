---
title: Windows 虛擬桌面主機集區 Azure 入口網站 - Azure
description: 如何使用 Azure 入口網站建立 Windows 虛擬桌面主機集區。
author: Heidilohr
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 268f1c4bff84a2c9ef67c3f6b5749bc8d0b90e85
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735176"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>教學課程：透過 Azure 入口網站建立主機集區

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md)。 您使用 Windows 虛擬桌面 (傳統版) 建立的任何物件，都無法使用 Azure 入口網站來管理。

主機集區是 Windows 虛擬桌面環境中一個或多個相同虛擬機器 (VM) 的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

本文將逐步引導您完成設定程序，以透過 Azure 入口網站建立 Windows 虛擬桌面環境的主機集區。 此方法提供瀏覽器形式的使用者介面，讓您可以在 Windows 虛擬桌面中建立主機集區、建立內含 Azure 訂用帳戶中 VM 的資源群組、將這些 VM 加入至 Azure Active Directory (AD) 網域，以及向 Windows 虛擬桌面註冊 VM。

## <a name="prerequisites"></a>Prerequisites

您必須輸入下列參數，才能建立主機集區：

- VM 映像名稱
- VM 設定
- 網域和網路屬性
- Windows 虛擬桌面主機集區屬性

您也必須知道下列事項：

- 您想要使用的映像來源位在何處。 來自 Azure 資源庫或是自訂映像？
- 您的網域加入認證。

此外，請確定您已註冊 Microsoft.DesktopVirtualization 資源提供者。 如果尚未註冊，請移至 [訂用帳戶]，選取您的訂用帳戶名稱，然後選取 [資源提供者]。 搜尋 DesktopVirtualization、選取 [Microsoft.DesktopVirtualization]，然後選取 [註冊]。

當您使用 Azure Resource Manager 範本建立 Windows 虛擬桌面主機集區時，您可以從 Azure 資源庫、受控映像或非受控映像建立虛擬機器。 如需如何建立 VM 映像的詳細資訊，請參閱[準備要上傳至 Azure 的 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md)，以及[在 Azure 中建立一般化 VM 的受控映像](../virtual-machines/windows/capture-image-resource.md)。

如果您還沒有 Azure 訂用帳戶，請務必在開始遵循這些指示之前，先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="begin-the-host-pool-setup-process"></a>開始進行主機集區設定程序

若要開始建立新的主機集區：

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com/)。
   
   >[!NOTE]
   > 如果您要登入 US Gov 入口網站，請改為移至 [https://portal.azure.us/](https://portal.azure.us/)。

2. 在搜尋列中輸入 **Windows 虛擬桌面**，然後尋找並選取 [服務] 底下的 [Windows 虛擬桌面]。

3. 在 [Windows 虛擬桌面] 概觀頁面中，選取 [建立主機集區]。

4. 在 [基本資料] 索引標籤中，從 [專案詳細資料] 底下選取正確的訂用帳戶。

5. 選取 [新建] 來建立新的資源群組，或從下拉式功能表中選取現有的資源群組。

6. 輸入主機集區的唯一名稱

7. 在 [位置] 欄位中，從下拉式功能表中選取您想要建立主機集區的區域。

   與您所選區域相關聯的 Azure 地理位置是將儲存此主機集區中繼資料及其相關物件的位置。 所選區域務必要在儲存服務中繼資料的地理位置內。

     > [!div class="mx-imgBorder"]
     > ![Azure 入口網站的螢幕擷取畫面，其中顯示已選取 美國東部 位置的 位置 欄位。 欄位旁的文字會顯示「中繼資料將儲存在美國東部」。](media/portal-location-field.png)

8. 在 [主機集區類型] 底下，選取您的主機集區是 [個人] 還是 [集區]。

    - 如果您選擇 [個人]，請在 [指派類型] 欄位中選取 [自動] 或 [直接]。

      > [!div class="mx-imgBorder"]
      > ![指派類型 欄位下拉式功能表的螢幕擷取畫面。 使用者已選取 自動。](media/assignment-type-field.png)

9.  如果您選擇 [集區]，請輸入下列資訊：

     - 針對 [最大工作階段限制]，請輸入您想要負載平衡至單一工作階段主機的最大使用者數目。
     - 針對 [負載平衡演算法]，請根據您的使用模式，選擇 [廣度優先] 或 [深度優先]。

       > [!div class="mx-imgBorder"]
       > ![已選取「集區」的指派類型欄位螢幕擷取畫面。 使用者將游標停留在 [負載平衡] 下拉式功能表上的 廣度優先。](media/pooled-assignment-type.png)

10. 完成時，選取 [下一步:虛擬機器 >]。

11. 如果您已建立虛擬機器，而且想要將其與新的主機集區搭配使用，請選取 [否]、選取 [下一步:工作區 >]，並跳至 [工作區資訊](#workspace-information) 區段。 如果您想要建立新的虛擬機器，並將其註冊到新的主機集區，請選取 [是]。

現在您已完成第一個部分，接著讓我們進行設定程序的下一個部分來建立虛擬機器。

## <a name="virtual-machine-details"></a>虛擬機器詳細資料

現在我們已經完成第一個部分，接著您必須設定 VM。

若要在主機集區設定程序中設定您的虛擬機器：

1. 在 [資源群組] 下，選擇您要在其中建立虛擬機器的資源群組。 這可以與您用於主機集區的資源群組不同。

2. 選擇您想要在其中建立虛擬機器的 [虛擬機器位置]。 這可以與您為主機集區選取的區域相同或不同。

3. 接下來，選擇您想要使用的 [虛擬機器大小]。 您可以保留預設大小，或選取 [變更大小] 來變更大小。 如果您選取 [變更大小]，請在出現的視窗中，選擇適合您工作負載的虛擬機器大小。

4. 在 [VM 數目] 底下，提供您想要為主機集區建立的 VM 數目。

    >[!NOTE]
    >設定程序可在設定主機集區時建立最多 400 部 VM，而每個 VM 設定程序會在您的資源群組中建立四個物件。 由於建立程序不會檢查您的訂用帳戶配額，因此請確定您輸入的 VM 數目在資源群組和訂用帳戶的 Azure VM 與 API 限制內。 完成建立主機集區之後，您可以新增更多 VM。

5. 然後請提供 **名稱首碼** 來命名設定程序所建立的虛擬機器。 尾碼則會使用 `-` 與數字 (從 0 開始)。

6. 接下來，選擇用來建立虛擬機器所需的映像。 您可以選擇 [資源庫] 或 [儲存體 Blob]。

    - 如果您選擇 **資源庫**，請從下拉式功能表中選取其中一個建議的映像：

      - Windows 10 企業版的多重工作階段 (1909 版)
      - Windows 10 企業版的多重工作階段 (1909 版) + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 企業版的多重工作階段 (2004 版)
      - Windows 10 企業版的多重工作階段 (2004 版) + Microsoft 365 Apps

     如果您沒有看到想要的映像，請選取 [瀏覽所有映像和磁碟]，這可讓您選取資源庫中的另一個映像，或 Microsoft 及其他發行者所提供的映像。 請確定您選擇的映射是其中一個支援的 [OS 映射](overview.md#supported-virtual-machine-os-images)。

     > [!div class="mx-imgBorder"]
     > ![Marketplace 的螢幕擷取畫面，其中顯示來自 Microsoft 的映像清單。](media/marketplace-images.png)

     您也可以移至 [我的項目]，然後選擇您已經上傳的自訂映像。

     > [!div class="mx-imgBorder"]
     > ![[我的項目] 索引標籤的螢幕擷取畫面。](media/my-items.png)

    - 如果您選擇 [儲存體 Blob]，您可以透過 Hyper-V 或在 Azure VM 上運用您自己的映像組建。 您只需要輸入儲存體 Blob 中的映像位置來作為 URI 即可。

7. 選擇您想要讓 VM 使用的 OS 磁碟類型：標準 SSD、進階 SSD 或標準 HDD。

8. 在 [網路和安全性] 底下，選取您要在放置所建立虛擬機器的 [虛擬網路] 和 [子網路]。 請確定虛擬網路可以連線到網域控制站，因為您必須將虛擬網路內的虛擬機器加入網域。 您所選取之虛擬網路的 DNS 伺服器應該設定為使用網域控制站的 IP。

9. 接下來，選取您是否要讓虛擬機器使用公用 IP。 建議您選取 [否]，因為私人 IP 較安全。

10. 選取您想要的安全性群組類型：**基本**、**進階** 或 **無**。

    如果您選取 [基本]，則必須選取是否要開啟任何輸入連接埠。 如果您選取 [是]，請從標準連接埠清單中選擇，以允許對其進行輸入連線。

    >[!NOTE]
    >為了提高安全性，我們建議您不要開啟公用的輸入連接埠。

    > [!div class="mx-imgBorder"]
    > ![安全性群組頁面的螢幕擷取畫面，其中顯示下拉式功能表中的可用連接埠清單。](media/available-ports.png)

    如果您選擇 [進階]，請選取您已設定的現有網路安全性群組。

11. 然後，請選取您是否要將虛擬機器加入特定網域和組織單位。 如果您選擇 [是]，請指定要加入的網域。 您可以選擇是否要新增特定組織單位來將虛擬機器加入其中。 如果您選擇 [否]，則 VM 將會加入與 [AD 網域加入 UPN] 的尾碼相符的網域。

  - 指定 OU 時，請確定您使用的是完整路徑 (辨別名稱)，而且沒有引號。

12. 在 [系統管理員帳戶] 下，輸入所選取虛擬網路的 Active Directory 網域系統管理員認證。 此帳戶無法啟用多重要素驗證 (MFA)。 加入 Azure Active Directory Domain Services (Azure AD DS) 網域時，該帳戶必須是 Azure AD DC 系統管理員群組的一部分，而且帳戶密碼必須可在 Azure AD DS 中使用。

13. 完成時，選取 [下一步:工作區 >]。

如此一來，我們就可以開始設定您主機集區的下一個階段：將您的應用程式群組註冊到工作區。

## <a name="workspace-information"></a>工作區資訊

根據預設，主機集區設定程序會建立桌面應用程式群組。 為了讓主機集區能夠如預期般執行，您必須將此應用程式群組發佈至使用者或使用者群組，而且您必須向工作區註冊應用程式群組。

若要將桌面應用程式群組註冊至工作區：

1. 選取 [是]。

   如果您選取 [否]，您可以稍後再註冊應用程式群組，但建議您儘快進行工作區註冊，如此一來，您的主機集區才能正常運作。

2. 接下來，選擇您想要建立新的工作區，還是要從現有的工作區中選取。 應用程式群組只能對建立在主機集區所在位置的工作區進行註冊。

3. (選擇性) 您可以選取 [下一步:標籤 >]。

    您可以在這裡新增標籤，以使用中繼資料將物件分組，讓您更輕鬆地進行管理。

4. 當您完成時，選取 [檢閱 + 建立]。

     >[!NOTE]
     >「檢閱+建立」驗證程序不會檢查您的密碼是否符合安全性標準，或是您的架構是否正確，因此您必須自行檢查這任何一項是否有問題。

5. 請檢閱部署的相關資訊，以確定一切看起來都沒有問題。 當您完成時，選取 [建立]。 這會啟動部署程序來建立下列物件：

     - 您的新主機集區。
     - 桌面應用程式群組。
     - 工作區 (如果您選擇建立工作區)。
     - 如果您選擇註冊桌面應用程式群組，則會完成註冊。
     - 虛擬機器 (如果您選擇建立虛擬機器) 會加入網域，並向新的主機集區註冊。
     - Azure 資源管理範本的下載連結 (根據您的設定)。

完成後，您就大功告成了！

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>執行 Azure Resource Manager 範本以佈建新的主機集區

如果您想要使用自動化程序，請改為[下載我們的 Azure Resource Manager 範本](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)以佈建您的新主機集區。

>[!NOTE]
>如果您要使用自動化程序來建置您的環境，您需要最新版的設定 JSON 檔案。 您可以在[這裡](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list)找到 JSON 檔案。

## <a name="next-steps"></a>後續步驟

現在您已建立好主機集區，接著您可以在其中填入 RemoteApp 程式。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](./manage-app-groups.md)
