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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: cc937589b2fc4f394b44cf6890a352d770751d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502029"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>管理 Azure 實驗室服務中的教室實驗室 
本文說明如何建立和刪除教室實驗室。 它也示範如何檢視實驗室帳戶中的所有教室實驗室。 

## <a name="prerequisites"></a>Prerequisites
若要在實驗室帳戶中設定教室實驗室，您必須是實驗室帳戶中 [實驗室建立者]**** 角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 實驗室擁有者可以使用下列文章中的步驟，將其他使用者新增至「實驗室建立者」角色：[將使用者新增至實驗室建立者角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。

## <a name="create-a-classroom-lab"></a>建立教室實驗室

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 IE1 還不受支援。 
2. 選取 [登入]****，然後輸入您的認證。 選取或輸入**使用者識別碼** (其為實驗室帳戶中具有**實驗室建立者**角色的成員)，然後輸入密碼。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 選取 [新增實驗室]****。 
    
    ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. 在 [新增實驗室]**** 視窗中，執行下列動作： 
    1. 指定實驗室的**名稱**。 
    2. 選擇類所需的**虛擬機器的大小**。 有關可用大小的清單，請參閱 VM[大小](#vm-sizes)部分。 
    3. 選擇要用於課堂實驗室的**虛擬機器映射**。 如果您選取 Linux 映像，您會看到可啟用遠端桌面連接的選項。 如需詳細資料，請參閱[啟用 Linux 遠端桌面連線](how-to-enable-remote-desktop-linux.md)。
    4. 查看頁面上顯示**的每小時總價**。 
    6. 選取 [儲存]****。

        ![[新增實驗室] 視窗](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > 如果實驗室帳戶配置為[允許實驗室建立者選擇實驗室位置](allow-lab-creator-pick-lab-location.md)選項，您將看到一個選項，用於選擇實驗室的位置。 
4. 在 [虛擬機器認證]**** 頁面上，指定實驗室中所有 VM 的預設認證。
    1. 指定實驗室中所有 VM 的 [使用者名稱]****。
    2. 指定使用者的 [密碼]****。 

        > [!IMPORTANT]
        > 請記下使用者名稱和密碼。 它們將不會再次顯示。
    3. 如果您希望學生設置自己的密碼，請**禁用"對所有虛擬機器使用相同的密碼**"選項。 此為**選用**步驟。 

        教師可以選擇對實驗室中的所有 VM 使用相同的密碼，或者允許學生為其 VM 設置密碼。 預設情況下，此設置已針對除 Ubuntu 以外的所有 Windows 和 Linux 映射啟用。 選擇**Ubuntu** VM 時，此設置將被禁用，因此學生首次登錄時將提示他們設置密碼。  

        ![[新增實驗室] 視窗](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. 然後，在**虛擬機器憑據**頁上選擇 **"下一步**"。 
5. 在 **"實驗室策略"** 頁上，執行以下步驟：
    1. 輸入為實驗室計畫時間以外的每個使用者分配的小時數（**每個使用者的配額**）。 
    2. 對於 **"自動關閉虛擬機器"** 選項，請指定是否要在使用者斷開連接時自動關閉 VM。 您還可以指定 VM 應等待使用者重新連接的時間，然後再自動關閉。 有關詳細資訊，請參閱[在斷開連接時啟用 VM 的自動關閉](how-to-enable-shutdown-disconnect.md)。
    3. 然後，選擇 **"完成**"。 

        ![每位使用者的配額](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. 您應該會看到下列畫面，其中顯示範本 VM 的建立狀態。 在實驗室中建立範本最多需要 20 分鐘。 

    ![範本 VM 的建立狀態](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. 在 **"範本"** 頁上，執行以下步驟：這些步驟對於本教程是**可選**的。

    2. 選取 [連線]**** 以連線至範本 VM。 如果是 Linux 範本 VM，您可選擇是否要使用 SSH 或 RDP 連線 (若已啟用 RDP)。
    1. 選取 [重設密碼]**** 以重設 VM 的密碼。 
    1. 在您的範本 VM 上安裝並設定軟體。 
    1. **停止**VM。  
    1. 輸入範本的 [描述]****
10. 在 [範本]**** 頁面上，選取工具列上的 [發佈]****。 

    ![[發佈範本] 按鈕](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 在您發佈時，即無法取消發佈。 
8. 在 [發佈範本]**** 頁面上，輸入您要在實驗室中建立的虛擬機器數目，然後選取 [發佈]****。 

    ![發佈範本 - VM 的數目](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 您會在頁面上看到範本的**發佈狀態**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 選取左側功能表上的 [虛擬機器] 或選取 [虛擬機器] 圖格，以切換到 [虛擬機器集區]**** 頁面。 確認您看到處於 [未指派]**** 狀態的虛擬機器。 這些虛擬機器尚未指派給任何學生。 它們應處於 [已停止]**** 狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    您可以在此頁面上執行下列工作 (請勿對本教學課程執行這些步驟。 這些步驟僅供參考之用。)： 
    
    1. 若要變更實驗室容量 (實驗室中的 VM 數目)，請選取工具列上的 [實驗室容量]****。
    2. 若要一次啟動所有 VM，請選取工具列上的 [全部啟動]****。 
    3. 若要啟動特定 VM，請選取 [狀態]**** 中的向下箭號，然後選取 [啟動]****。 您也可以在第一個資料行中選取 VM，然後選取工具列上的 [啟動]**** 來啟動 VM。                

### <a name="vm-sizes"></a>VM 大小  

| 大小 | 核心 | RAM | 描述 | 
| ---- | ----- | --- | ----------- | 
| 小型 | 2 | 3.5 GB | 此大小最適合命令列、打開 Web 瀏覽器、低流量 Web 服務器、中小型資料庫。 |
| 中 | 4 | 7 GB | 此大小最適合關係資料庫、記憶體緩存和分析 | 
| 中等（嵌套虛擬化） | 4 | 16 GB | 此大小最適合關係資料庫、記憶體緩存和分析。 此大小還支援嵌套虛擬化。 <p>此大小可用於每個學生需要多個 VM 的情況。 教師可以使用嵌套虛擬化在虛擬機器內設置幾個小型嵌套虛擬機器。 </p> |
| 大型 | 8 | 32 GB | 此大小最適合需要更快 CPU、更好的本地磁片性能、大型資料庫和大型記憶體緩存的應用程式。 此大小還支援嵌套虛擬化 |  
| 小型 GPU（視覺化） | 6 | 56 GB | 此大小最適合使用 OpenGL 和 DirectX 等框架進行遠端視覺化、資料流、遊戲和編碼。 | 
| 小型 GPU（計算） | 6 | 56 GB | 此大小最適合計算密集型和網路密集型應用，如人工智慧和深度學習應用。 | 
| 中型 GPU（視覺化） | 12 | 112 GB | 此大小最適合使用 OpenGL 和 DirectX 等框架進行遠端視覺化、資料流、遊戲和編碼。 | 

> [!NOTE]
> 使用 GPU 映射創建實驗室時，Azure 實驗室服務會自動安裝和配置必要的 GPU 驅動程式。  

## <a name="view-all-classroom-labs"></a>檢視所有教室實驗室
1. 瀏覽至 [Azure 實驗室服務入口網站](https://labs.azure.com)。
2. 選取 [登入]****。 選取或輸入**使用者識別碼** (其為實驗室帳戶中具有**實驗室建立者**角色的成員)，然後輸入密碼。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 確認您在所選取的實驗室帳戶中看到所有實驗室。 在實驗室的磁貼上，您可以看到實驗室中的虛擬機器數和每個使用者的配額（超出計畫時間）。

    ![所有實驗室](../media/how-to-manage-classroom-labs/all-labs.png)
3. 使用頂端的下拉式清單選取其他的實驗室帳戶。 您會看到所選取實驗室帳戶中的實驗室。 

## <a name="delete-a-classroom-lab"></a>刪除教室實驗室
1. 在實驗室的磁貼上，選擇角中的三個點 （...），然後選擇 **"刪除**"。 

    ![刪除按鈕](../media/how-to-manage-classroom-labs/delete-button.png)
3. 在 **"刪除實驗室"** 對話方塊中，選擇 **"刪除**"以繼續刪除。 

## <a name="switch-to-another-classroom-lab"></a>切換到另一個教室實驗室
若要從目前的教室實驗室切換到另一個教室實驗室，請選取頂端實驗室帳戶中的實驗室下拉式清單。

![從頂端的下拉式清單中選取實驗室](../media/how-to-manage-classroom-labs/switch-lab.png)

您還可以使用此下拉清單中**的"新建"實驗室**創建新實驗室。 

> [!NOTE]
> 您還可以使用 Az.LabServices PowerShell 模組（預覽版）來管理實驗室。 有關詳細資訊，請參閱[GitHub 上的 Az.LabServices 主頁](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

要切換到其他實驗室帳戶，請選擇實驗室帳戶旁邊的下拉清單，然後選擇其他實驗室帳戶。 

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)

