---
title: 使用 Azure 實驗室服務的實驗室來設定專案負責人
description: 瞭解如何設定實驗室，讓專案組長以類別的方式教授。
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024614"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>以類別的方式為專案負責人設定實驗室

[專案負責人的 (PLTW) ](https://www.pltw.org/) 是一種非贏利組織， &ndash; 在美國的電腦科學、工程和生物醫學科學中提供 PreK 12 的課程。  在每個 PLTW 類別中，學生都使用各種軟體應用程式作為實際操作學習體驗的一部分。  許多軟體應用程式在某些情況下都需要快速的 CPU 或 GPU。  本文說明如何設定適用于下列 PLTW 類別的實驗室，這些類別通常是在成績 9 12 的學生提供 &ndash; ：

- **設計設計簡介**

    學生推出了工程設計程式，其中包括使用 [Autodesk 發明者電腦輔助設計 (CAD) ](https://www.autodesk.com/products/inventor/new-features) 軟體進行3d 模型化。

- **工程原則**
    
    學生瞭解工程機制、結構化和材質的強度，以及自動化。  此類別會使用軟體，例如 [MD 實體](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf)、 [西部橋樑設計](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)工具和 [北美洲的軍隊模擬](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf)。

- **民事工程和架構**

    學生可以使用 [Autodesk Revit](https://www.autodesk.com/products/revit/overview) 架構設計軟體，來學習建立和網站的設計和開發， (model.bim) 。

- **電腦整合式製造**

    學生探索牽涉到機器人和自動化的新式製造流程。   在此課程中，學生使用 [Autodesk 發明者 CAD](https://www.autodesk.com/products/inventor/new-features) 和 [Autodesk 發明者電腦輔助製造 (凸輪) ](https://www.autodesk.com/products/inventor-cam/overview) 軟體。 

- **數位電路**

    學生會使用 [國家儀器 Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) 模擬和線路設計軟體，來研究電子邏輯電路和裝置。

- **工程設計與開發**

    學生藉由將所呈現的研究、設計和測試結合到工程師的面板，來提供端對端解決方案。  在此課程中，學生使用 [Autodesk 發明者 CAD](https://www.autodesk.com/products/inventor/new-features) 軟體。

- **電腦科學基本概念**

    系統會介紹學生的計算概念和工具。  它們是從區塊型程式設計開始，然後使用程式碼撰寫環境（例如 [VEXcode V5 區塊](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf)）移至以文字為基礎的編碼。

- **電腦科學原則**
    
    學生利用[Microsoft Visual Studio 程式碼開發環境](https://code.visualstudio.com/)，以[Python](https://www.python.org/)成長其程式設計專長。 

- **電腦科學 A**

    學生藉由學習行動應用程式開發，將其程式設計能力擴充到此類別。  在此類別中，他們會使用 Microsoft Visual Studio 程式[代碼開發環境](https://code.visualstudio.com/)來學習[JAVA](https://www.java.com/) 。  學生也會使用可讓他們執行和測試行動應用程式程式碼的模擬器。  如需如何在 Azure 實驗室服務中設定模擬器的詳細資訊，請 [洽詢 Azure 實驗室服務](mailto:AzLabsCOVIDSupport@microsoft.com)。

如需類別軟體的完整清單，請移至每個類別的 [PLTW 網站](https://www.pltw.org/pltw-software) 。

## <a name="lab-configuration"></a>實驗室組態

若要開始設定 PLTW 的實驗室，您需要 Azure 訂用帳戶和實驗室帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 

取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱 [設定實驗室帳戶](./tutorial-setup-lab-account.md)。 您也可以使用現有的實驗室帳戶。

設定實驗室帳戶之後，您應該為學校所提供的每個 PLTW 類別會話建立個別的實驗室。  我們也建議您為每一種類型的 PLTW 類別建立個別的映射。  如需有關如何結構您的實驗室和映射的詳細資訊，請參閱 [從實體實驗室移至 Azure 實驗室服務](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)的 blog 文章。

### <a name="lab-account-settings"></a>實驗室帳戶設定

依照下表所述，啟用您的實驗室帳戶設定。 如需如何啟用 Azure Marketplace 映射的詳細資訊，請參閱 [指定可供實驗室建立者使用的 Azure Marketplace 映射](./specify-marketplace-images.md)。

| 實驗室帳戶設定 | Instructions |
| -------------------- | ----- |
| Marketplace 映像 | 啟用 Windows 10 專業版映射，以在您的實驗室帳戶中使用。 |

<br>

### <a name="lab-settings"></a>實驗室設定
我們建議針對 PLTW 類別使用的虛擬機器 (VM) 大小，取決於您的學生在類別中執行的工作負載類型。  針對較早列出的類別，我們建議使用小型 GPU (視覺效果) 和大型 VM 大小。 當您設定 PLTW 類別的實驗室時，請參閱下表中的指導方針：

| 實驗室設定 | 值和描述 | 類別建議 |
| ------------ | ------------------ | --- |
| 虛擬機器大小 | **小型 GPU (視覺化)**<br>最適用于遠端視覺效果、串流、遊戲，以及使用 OpenGL 和 DirectX 等架構的編碼。 | 針對下列 PLTW 類別，我們建議使用此大小：民事工程和架構、數位電子設備、電腦整合式製造，以及工程設計與開發。
| 虛擬機器大小 | **大型**<br>最適合需要更快的 Cpu、更佳的本機磁片效能、大型資料庫和大型記憶體快取的應用程式。 | 針對下列 PLTW 類別，我們建議使用此大小：設計設計、工程原則、電腦科學基本概念、電腦科學原則，以及電腦科學 A 的簡介。 |

<br>

### <a name="license-server"></a>授權伺服器
上述 PLTW 類別中使用的大部分軟體都 *不* 需要授權伺服器的存取權。  但是，如果您打算針對下列軟體使用 Autodesk 網路授權模型，則需要存取授權伺服器：
-   Revit
-   發明家
-   發明者凸輪

若要搭配 Autodesk software 使用網路授權，PLTW 提供在授權伺服器上安裝 Autodesk Network License Manager 的 [詳細步驟](https://www.pltw.org/pltw-software) 。  此授權伺服器通常位於您的內部部署網路中，或裝載于 azure 虛擬機器 (VM) 在 Azure 虛擬網路內。

設定授權伺服器之後，您必須將虛擬網路與您的[實驗室帳戶](./tutorial-setup-lab-account.md)[對等](./how-to-connect-peer-virtual-network.md)互連。 您必須在建立實驗室 *之前先* 進行網路對等互連，讓實驗室 vm 可以存取授權伺服器，反之亦然。

Autodesk 產生的授權檔，內嵌授權伺服器的 MAC 位址。  如果您決定使用 Azure VM 來裝載您的授權伺服器，請務必確定授權伺服器的 MAC 位址不會變更。 如果 MAC 位址變更，您將需要重新產生您的授權檔案。 若要避免您的 MAC 位址變更，請執行下列動作：

- 為裝載授權伺服器的 Azure VM[設定靜態私人 IP 和 MAC 位址](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address)。
- 請務必在具有足夠 VM 容量的區域或位置中，設定實驗室帳戶和授權伺服器的虛擬網路，如此您就不需要在稍後將這些資源移至新的區域或位置。

如需詳細資訊，請參閱 [將授權伺服器設定為共用資源](./how-to-create-a-lab-with-shared-resource.md)。

### <a name="template-machine"></a>範本電腦
PLTW 所需的部分安裝檔案很龐大。 當您將檔案下載至實驗室範本 VM 時，可能需要很長的時間才能複製。

我們建議您在實體環境中建立 PLTW 映射，而不是將安裝檔案下載到範本電腦，並在該處安裝所有專案。  然後，您可以將自訂映射匯入共用映射庫，讓您可以使用它們來建立您的實驗室。  如需詳細資訊，請參閱 [將自訂映射上傳至共用映射庫](./upload-custom-image-shared-image-gallery.md)。

當您遵循此建議時，請注意設定實驗室的主要工作：

1. 在您的實體環境中，建立類別的映射。

    a.  使用 PLTW 的詳細步驟來下載安裝檔案，以及安裝所需的軟體。

    > [!NOTE]    
    > 當您安裝 Autodesk 應用程式時，您要安裝這些應用程式的電腦必須能夠與您的授權伺服器通訊。 Autodesk 安裝精靈將會提示您指定裝載授權伺服器之電腦的電腦名稱稱。  如果您要在 Azure VM 上裝載授權伺服器，您可能需要等候在實驗室範本 VM 上安裝 Autodesk，讓安裝精靈可以存取您的授權伺服器。

    b.  [安裝和設定](./how-to-prepare-windows-template.md#install-and-configure-onedrive) 您學校可能會使用的 OneDrive 或其他備份選項。
    
    c.  [安裝和設定 Windows 更新](./how-to-prepare-windows-template.md#install-and-configure-updates)。

1.  將自訂映射上傳至 [您的實驗室帳戶所附加的共用映射庫](./how-to-attach-detach-shared-image-gallery.md)。

1.  建立實驗室，然後選取您在上一個步驟中上傳的自訂映射。

1.  建立實驗室之後，請啟動並聯機至範本 VM，以驗證映射是否如預期般運作。

1.  最後，發佈範本 VM 以建立學生的 Vm。

## <a name="student-devices"></a>學生裝置
學生可以從 Windows 電腦、Mac 和 Chromebook 連接到其實驗室 Vm。 如需相關指示，請參閱：

- [從 Windows 連線](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [從 Mac 連接](./connect-virtual-machine-mac-remote-desktop.md)
- [從 Chromebook 連接](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>成本
讓我們來討論 PLTW 類別的範例成本估計值。  這項預估不包含執行授權伺服器或使用共用映射庫的成本。 假設您有25個學生的類別，每個學生都有20小時的排程類別時間。  每位學生也有額外的10個配額小時可用於排程類別時間以外的家庭作業或指派。  以下是預估成本：

- **大型 VM**

    25名學生 &times; (20 個排程時間 + 10 個配額時數) &times; 70 個實驗室單位 &times; 每小時 0.01 = 美元525.00

- **小型 GPU (視覺效果)**

    25名學生 &times; (20 個排程時間 + 10 個配額時數) &times; 160 個實驗室單位 &times; 每小時 0.01 = 美元1200.00

> [!IMPORTANT] 
> 成本預估僅供範例之用。  如需最新的定價資訊，請參閱 [Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

> [!NOTE] 
> 許多 PLTW 類別會使用透過瀏覽器存取的應用程式，例如 MIT 應用程式發明者。  這些以瀏覽器為基礎的應用程式不需要快速的 CPU 或 GPU，而且您可以從任何具有網際網路連線的裝置存取這些應用程式。  當學生使用這些類型的應用程式時，建議他們在其實體裝置上改為使用瀏覽器，而不是在其實驗室 VM 上使用瀏覽器。 學生只能針對需要快速 CPU 或 GPU 的應用程式使用其實驗室 VM，以協助降低成本。

## <a name="next-steps"></a>下一步

當您設定實驗室時，請參閱下列文章：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users) 
