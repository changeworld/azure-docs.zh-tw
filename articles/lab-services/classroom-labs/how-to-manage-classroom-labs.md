---
title: 管理 Azure 實驗室服務中的教室實驗室 | Microsoft Docs
description: 了解如何建立和設定教室實驗室、檢視所有教室實驗室、與實驗室使用者共用註冊連結，或刪除實驗室。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 4c2c02e47059d73e29da705fc9075721e080e636
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701801"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>管理 Azure 實驗室服務中的教室實驗室 
本文說明如何建立和刪除教室實驗室。 它也示範如何檢視實驗室帳戶中的所有教室實驗室。 

## <a name="prerequisites"></a>Prerequisites
若要在實驗室帳戶中設定教室實驗室，您必須是實驗室帳戶中 [實驗室建立者] 角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 實驗室擁有者可以使用下列文章中的步驟，將其他使用者新增至「實驗室建立者」角色：[將使用者新增至實驗室建立者角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。

## <a name="create-a-classroom-lab"></a>建立教室實驗室

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 目前不支援 Internet Explorer 11。 
1. 選取 [登入]，然後輸入您的認證。 選取或輸入**使用者識別碼** (其為實驗室帳戶中具有**實驗室建立者**角色的成員)，然後輸入密碼。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
1. 選取 [新增實驗室]。 
    
    ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-button.png)
1. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定實驗室的**名稱**。 
    1. 選取類別所需的**虛擬機器大小**。 如需可用的大小清單，請參閱 [VM 大小](#vm-sizes)一節。 
    1. 選取您想用於教室實驗室的**虛擬機器映像**。 如果您選取 Linux 映像，您會看到**啟用遠端桌面連線**的選項。 如需詳細資料，請參閱[啟用 Linux 遠端桌面連線](how-to-enable-remote-desktop-linux.md)。

        如果您使用實驗室帳戶擁有者認證進行登入，將會看到為實驗室啟用更多映像的選項。 如需詳細資訊，請參閱[在建立實驗室時啟用映像](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)。
    1. 檢閱頁面上顯示的**每小時總價**。 
    1. 選取 [儲存]。

        ![[新增實驗室] 視窗](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > 如果實驗室帳戶已設為[允許實驗室建立者挑選實驗室位置](allow-lab-creator-pick-lab-location.md)選項，您會看到用以選取實驗室位置的選項。 
4. 在 [虛擬機器認證] 頁面上，指定實驗室中所有 VM 的預設認證。
    1. 指定實驗室中所有 VM 的 [使用者名稱]。
    2. 指定使用者的 [密碼]。 

        > [!IMPORTANT]
        > 請記下使用者名稱和密碼。 它們將不會再次顯示。
    3. 如果您希望學生設定自己的密碼，請停用 [對所有虛擬機器使用相同的密碼] 選項。 此為**選用**步驟。 

        教育者可選擇對實驗室中的所有 VM 使用相同的密碼，或允許學生設定其 VM 的密碼。 根據預設，除了 Ubuntu 以外，所有 Windows 和 Linux 映像都會啟用此設定。 當您選取 **Ubuntu** VM 時，此設定會停用，因此學生會在第一次登入時看見設定密碼的提示。  

        ![[新增實驗室] 視窗](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. 然後，選取 [虛擬機器認證] 頁面上的 [下一步]。 
5. 在 [實驗室原則] 頁面上，執行下列步驟：
    1. 輸入在實驗室的排程時間外所配置給每位使用者的時數 (**每位使用者的配額**)。 
    2. 在 [自動關閉虛擬機器] 選項中，指定是否要在使用者中斷連線時自動關閉 VM。 您也可以指定 VM 在自動關閉之前，應該等候使用者重新連線的時間長度。 如需詳細資訊，請參閱[在中斷連線時啟用自動關閉 VM](how-to-enable-shutdown-disconnect.md)。
    3. 然後，選取 [完成]。 

        ![每位使用者的配額](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. 您應該會看到下列畫面，其中顯示範本 VM 的建立狀態。 在實驗室中建立範本最多需要 20 分鐘。 

    ![範本 VM 的建立狀態](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. 在 [範本] 頁面上，執行下列步驟：這些都是本教學課程的**選擇性**步驟。

    1. 選取 [連線] 以連線至範本 VM。 如果是 Linux 範本 VM，您可選擇要使用 SSH 還是 GUI 遠端桌面進行連線。  若要使用 GUI 遠端桌面，必須進行額外的設定。 如需詳細資訊，請參閱[為 Linux 虛擬機器啟用圖形化遠端桌面](how-to-use-remote-desktop-linux-student.md)。
    1. 選取 [重設密碼] 以重設 VM 的密碼。 
    1. 在您的範本 VM 上安裝並設定軟體。 
    1. **停止** VM。  
    1. 輸入範本的 [描述]
9.  在 [範本] 頁面上，選取工具列上的 [發佈]。 

    ![[發佈範本] 按鈕](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 在您發佈時，即無法取消發佈。 
10. 在 [發佈範本] 頁面上，輸入您要在實驗室中建立的虛擬機器數目，然後選取 [發佈]。 

    ![發佈範本 - VM 的數目](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 您會在頁面上看到範本的**發佈狀態**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 選取左側功能表上的 [虛擬機器] 或選取 [虛擬機器] 圖格，以切換到 [虛擬機器集區] 頁面。 確認您看到處於 [未指派] 狀態的虛擬機器。 這些虛擬機器尚未指派給任何學生。 它們應處於 [已停止] 狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    您可以在此頁面上執行下列工作 (請勿對本教學課程執行這些步驟。 這些步驟僅供參考之用。)： 
    
    1. 若要變更實驗室容量 (實驗室中的 VM 數目)，請選取工具列上的 [實驗室容量]。
    2. 若要一次啟動所有 VM，請選取工具列上的 [全部啟動]。 
    3. 若要啟動特定 VM，請選取 [狀態] 中的向下箭號，然後選取 [啟動]。 您也可以在第一個資料行中選取 VM，然後選取工具列上的 [啟動] 來啟動 VM。                

### <a name="vm-sizes"></a>VM 大小  

| 大小 | 核心 | RAM | 描述 | 
| ---- | ----- | --- | ----------- | 
| 小型 | 2 | 3.5 GB | 此大小最適合用於命令列、開啟網頁瀏覽器、低流量網頁伺服器、小型至中型資料庫。 |
| 中 | 4 | 7 GB | 此大小最適合用於關聯式資料庫、記憶體內部快取及分析 | 
| 中型 (巢狀虛擬化) | 4 | 16 GB | 此大小最適合用於關聯式資料庫、記憶體內部快取及分析。 此大小也支援巢狀虛擬化。 <p>此大小可用於每個學生都需要多個 VM 的案例。 教師可以使用巢狀虛擬化，在虛擬機器內設定一些小型巢狀虛擬機器。 </p> |
| 小型 GPU (計算) | 6 | 56 GB | <p>此大小最適合用於計算密集型和網路密集型應用程式，例如人工智慧和深度學習應用程式。</p><p>當您使用 GPU 映像建立實驗室時，Azure Lab Services 會自動為您安裝及設定所需的 GPU 驅動程式。 </p> | 
| 小型 GPU (視覺化) | 6 | 56 GB | 此大小最適合使用 OpenGL 和 DirectX 這類架構的遠端視覺化、串流、遊戲、編碼。 | 
| 大型 | 8 | 16 GB | 此大小最適合用於需要更快 CPU、更高本機磁碟效能、大型資料庫、大型記憶體快取的應用程式。 |
| 大型 (巢狀虛擬化) | 8 | 32 GB | 此大小最適合用於需要更快 CPU、更高本機磁碟效能、大型資料庫、大型記憶體快取的應用程式。 此大小也支援巢狀虛擬化。 |  
| 中型 GPU (視覺化) | 12 | 112 GB | 此大小最適合使用 OpenGL 和 DirectX 這類架構的遠端視覺化、串流、遊戲、編碼。 | 

> [!NOTE]
> 建立教室實驗室時，您可能不會在清單中看到這些 VM 大小。 此清單會根據實驗室位置的目前容量填入。 如果實驗室帳戶建立者[允許實驗室建立者挑選實驗室位置](allow-lab-creator-pick-lab-location.md)，您可以嘗試為實驗室選擇不同的位置，並查看 VM 大小是否可用。 


## <a name="view-all-classroom-labs"></a>檢視所有教室實驗室
1. 瀏覽至 [Azure 實驗室服務入口網站](https://labs.azure.com)。
2. 選取 [登入]。 選取或輸入**使用者識別碼** (其為實驗室帳戶中具有**實驗室建立者**角色的成員)，然後輸入密碼。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 確認您在所選取的實驗室帳戶中看到所有實驗室。 在實驗室的圖格上，您會看到實驗室中的虛擬機器數目，以及每位使用者的配額 (在排定的時間外)。

    ![所有實驗室](../media/how-to-manage-classroom-labs/all-labs.png)
3. 使用頂端的下拉式清單選取其他的實驗室帳戶。 您會看到所選取實驗室帳戶中的實驗室。 

## <a name="delete-a-classroom-lab"></a>刪除教室實驗室
1. 在實驗室的圖格上，選取角落的三個點 (...)，然後選取 [刪除]。 

    ![刪除按鈕](../media/how-to-manage-classroom-labs/delete-button.png)
3. 在 [刪除實驗室] 對話方塊中，選取 [刪除] 繼續進行刪除。 

## <a name="switch-to-another-classroom-lab"></a>切換到另一個教室實驗室
若要從目前的教室實驗室切換到另一個教室實驗室，請選取頂端實驗室帳戶中的實驗室下拉式清單。

![從頂端的下拉式清單中選取實驗室](../media/how-to-manage-classroom-labs/switch-lab.png)

您也可以使用此下拉式清單中的 [新增實驗室] 建立新的實驗室。 

> [!NOTE]
> 您也可以使用 Az.LabServices PowerShell 模組 (預覽) 來管理實驗室。 如需詳細資訊，請參閱 [GitHub 上的 Az.LabServices 首頁](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

若要切換至不同的實驗室帳戶，請選取實驗室帳戶旁的下拉式清單，然後選取其他實驗室帳戶。 

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)

