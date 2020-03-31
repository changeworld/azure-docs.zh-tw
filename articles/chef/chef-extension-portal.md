---
title: 從 Azure 入口網站安裝 Chef 用戶端
description: 了解如何從 Azure 入口網站部署及設定您的 Chef 用戶端
keywords: azure, chef, devops, 用戶端, 安裝, 入口網站
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586354"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>從 Azure 入口網站安裝 Chef 用戶端
您可以從 Azure 入口網站直接將 Chef 用戶端擴充功能新增至 Linux 或 Windows 機器。 這篇文章會逐步引導您完成使用新 Linux 虛擬機器的程序。

## <a name="prerequisites"></a>Prerequisites

- **Azure 訂閱**：如果沒有 Azure 訂閱，請先創建一個[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。"

- **Chef**：如果您沒有作用中 Chef 帳戶，請註冊[免費試用版託管 Chef](https://manage.chef.io/signup)。 若要依照本文章中的指示，您需要 Chef 帳戶的下列值：
  - organization_validation 金鑰
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>在新的 Linux 虛擬機器上安裝 Chef 擴充功能
在本節中，首先您會使用 Azure 入口網站建立 Linux 機器。 在過程中，您也會看到如何在新的虛擬機器上安裝 Chef 擴充功能。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 從左邊的功能表上，選取 [虛擬機器]**** 選項。 如果 [虛擬機器]**** 選項未顯示，請選取 [所有服務]****，然後選取 [虛擬機器]****。

1. 在 [虛擬機器]**** 索引標籤上，選取 [新增]****。

    ![在 Azure 入口網站中新增新的虛擬機器](./media/chef-extension-portal/add-vm.png)

1. 在 [計算]**** 索引標籤上，選取所需的作業系統。 針對這個示範，請選取 **[Ubuntu Server]**。

1. 在 **[Ubuntu Server]** 索引標籤上，選取 **[Ubuntu Server 16.04 LTS]**。

    ![建立 Ubuntu 虛擬機器時，指定您需要的版本](./media/chef-extension-portal/ubuntu-server-version.png)

1. 在 **[Ubuntu Server 16.04 LTS]** 索引標籤上，選取 [建立]****。

    ![Ubuntu 會提供其產品的額外資訊](./media/chef-extension-portal/create-vm.png)

1. 在 [建立虛擬機器]**** 索引標籤上，選取 [基本]****。

1. 在 [基本]**** 索引標籤上，指定下列值，然後選取 [確定]****。

   - **名稱** - 輸入新虛擬機器的名稱。
   - **虛擬機器磁碟類型** - 針對儲存體磁碟類型指定 **SSD** 或 **HDD**。 如需 Azure 上虛擬機器磁碟類型的詳細資訊，請參閱[選取磁碟類型](../virtual-machines/windows/disks-types.md)一文。
   - **使用者名稱** - 輸入使用者名稱，系統已在虛擬機器上授與此使用者系統管理員權限。
   - **驗證類型** - 選取 [密碼]****。 您也可以選取 **SSH 公開金鑰**，並提供 SSH 公開金鑰值。 針對本示範的目的 (以及在快照集中)，會選取 [密碼]****。
   - **密碼**和**確認密碼** - 請輸入使用者的密碼。
   - **使用 Azure Active Directory 進行登入** - 請選取 [停用]****。
   - **訂用帳戶** - 如果您有多個 Azure 訂用帳戶，請選取所需的訂用帳戶。
   - **資源群組** - 請輸入資源群組的名稱。
   - **位置** - 選取 [美國東部]****。

     ![用來建立虛擬機器的 [基本] 索引標籤](./media/chef-extension-portal/add-vm-basics.png)

1. 請在 [選擇大小]**** 索引標籤上，選取虛擬機器的大小，然後選取 [選取]****。

1. 在 [設定]**** 索引標籤上，系統會根據您在上一個索引標籤中選取的值，為您填入大部分的值。 選擇**擴展**。

     ![擴充功能會透過 [設定] 索引標籤新增至虛擬機器](./media/chef-extension-portal/add-vm-select-extensions.png)

1. 請在 [擴充功能]**** 索引標籤上，選取 [新增擴充功能]****。

     ![請選取 [新增擴充功能] 以將擴充功能新增至虛擬機器](./media/chef-extension-portal/add-vm-add-extension.png)

1. 請在 [新增資源]**** 索引標籤上，選取 [Linux Chef 擴充功能 (1.2.3)]****。

     ![Chef 具有 Linux 和 Windows 虛擬機器的擴充功能](./media/chef-extension-portal/select-linux-chef-extension.png)

1. 請在 [Linux Chef 擴充功能]**** 索引標籤上，選取 [建立]****。

1. 請在 [安裝擴充功能]**** 索引標籤上，指定下列值，然後選取 [確定]****。

    - **Chef 伺服器 URL** - 請輸入包含組織名稱的 Chef 伺服器 URL，例如 *https://api.chef.io/organization/mycompany*。
    - **Chef 節點名稱**- 輸入 Chef 節點名稱。
    - **運行清單**- 輸入添加到電腦的 Chef 運行清單。 此值可以留空。
    - **驗證用戶端名稱**- 輸入 Chef 驗證用戶端名稱。 例如， `tarcher-validator`.
    - **驗證金鑰** - 選取檔案，其中包含啟動載入您的機器時使用的驗證金鑰。
    - **用戶端設定檔**- 為 Chef-用戶端選擇設定檔。 此值可以留空。
    - **Chef 用戶端版本** - 請輸入要安裝的 Chef 用戶端版本。 此值可以留空，從而安裝最新版本。
    - **SSL 驗證模式** - 請選取 [無]**** 或 [對等]****。 在示範中我們選取 [無]**。
    - **Chef 環境** - 請輸入這個節點所屬的 Chef 環境。 此值可以留空。
    - **加密資料包金鑰**- 選擇包含此電腦需要訪問的加密資料包的機密的檔。 此值可以留空。
    - **Chef 伺服器 SSL 憑證**- 選擇分配給 Chef 伺服器的 SSL 憑證。 此值可以留空。

      ![在 Linux 虛擬機器上安裝 Chef 伺服器](./media/chef-extension-portal/install-extension.png)

1. 顯示 **"擴展"** 選項卡時，選擇 **"確定**"。

1. 顯示 **"設置"** 選項卡時，選擇 **"確定**"。

1. 顯示 **"創建**"選項卡時，您將看到所選和輸入的選項的摘要。 驗證資訊以及**使用條款**，然後選擇 **"創建**"。

建立及部署具有 Chef 擴充功能的虛擬機器程序完成時，通知會指出作業成功或失敗。 此外，新虛擬機器的資源頁面一旦建立，就會在 Azure 入口網站中自動開啟。

![在 Linux 虛擬機器上安裝 Chef 伺服器](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [使用 Chef 在 Azure 上建立 Windows 虛擬機器](chef-automation.md)