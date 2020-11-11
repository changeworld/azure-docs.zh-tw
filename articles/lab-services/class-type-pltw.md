---
title: 使用 Azure 實驗室服務的實驗室來設定專案負責人
description: 瞭解如何設定實驗室，讓專案組長以類別的方式教授。
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 8585d09759319eef04da5ed68fec603cfa390093
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496679"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>以類別的方式為專案負責人設定實驗室

[專案負責人的 (PLTW) ](https://www.pltw.org/) 是一種非贏利組織，在美國的電腦科學、工程和生物醫學科學中提供 PreK-12 的課程。  在每個 PLTW 類別中，學生都使用各種軟體應用程式作為實際操作學習體驗的一部分。  許多軟體應用程式都需要快速的 CPU 或某些情況下的 GPU。  本文說明如何設定下列 PLTW 類別的實驗室，這些類別通常會提供給成績為9-12 的學生：

- **設計設計簡介**

    學生推出了工程設計程式，其中包括使用 [Autodesk 的發明者電腦輔助設計 (CAD) ](https://www.autodesk.com/products/inventor/new-features) 軟體進行3d 模型化。

- **工程原則**
    
    學生瞭解工程機制、structural\material 強度和自動化。  此類別會使用軟體，例如 [MD 實體](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf)、 [西部橋樑設計](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)工具和 [北美洲的軍隊模擬](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf)。

- **民事工程和架構**

    您可以使用 [Autodesk 的 Revit](https://www.autodesk.com/products/revit/overview) 架構設計軟體，為3d 建立資訊模型化的學生建立和網站設計和開發 (model.bim) 。

- **電腦整合式製造**

    學生探索牽涉到機器人和自動化的新式製造流程。   在此課程中，學生使用 [Autodesk 的發明者 CAD](https://www.autodesk.com/products/inventor/new-features) 和 [Autodesk 的發明者電腦輔助製造 (凸輪) ](https://www.autodesk.com/products/inventor-cam/overview) 軟體。 

- **數位電路**

    學生將使用 [全國儀器的 Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) 模擬和線路設計軟體，來研究電子邏輯電路和裝置。

- **工程設計與開發**

    學生參與了端對端解決方案，可將其呈現的研究、設計和測試結合到工程師的面板上。  在此課程中，學生使用 [Autodesk 的發明者 CAD](https://www.autodesk.com/products/inventor/new-features) 軟體。

- **電腦科學基本概念**

    系統會介紹學生的計算概念和工具。  它們是從以區塊為基礎的程式設計開始，然後使用 [VEXcode V5 區塊](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf)等程式碼撰寫環境，以使用以文字為基礎的編碼。

- **電腦科學原則**
    
    學生使用[Microsoft 的 Visual Studio Code 開發環境](https://code.visualstudio.com/)，以[Python](https://www.python.org/)成長其程式設計專長。 

- **電腦科學 A**

    藉由學習行動應用程式開發，學生就能將其程式設計專長成長到此類別。  在此類別中，他們會使用[Microsoft 的 Visual Studio Code 開發環境](https://code.visualstudio.com/)來學習[JAVA](https://www.java.com/) 。  學生也會使用可讓他們執行和測試行動應用程式程式碼的模擬器。  如需如何在 Azure Labs 中設定模擬器的詳細資訊，請洽詢我們 azlabspilot@microsoft.com 。

請參閱 PLTW 的網站，以取得每個類別的 [完整軟體清單](https://www.pltw.org/pltw-software) 。

## <a name="lab-configuration"></a>實驗室組態
若要設定 PLTW 的實驗室，您需要 Azure 訂用帳戶和實驗室帳戶才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立一個[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱 [如何設定實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)的教學課程。 您也可以使用現有的實驗室帳戶。

擁有實驗室帳戶之後，您應該為學校所提供的 PLTW 類別的每個會話建立個別的實驗室。  我們也建議您為每一種類型的 PLTW 類別建立個別的映射。  如需有關如何結構您的實驗室和影像的詳細資訊，請閱讀 blog 文章： [從實體實驗室移至 Azure 實驗室服務](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)。

### <a name="lab-account-settings"></a>實驗室帳戶設定
針對實驗室帳戶啟用下表所述的設定。 如需有關如何啟用 marketplace 映射的詳細資訊，請參閱有關 [如何指定實驗室建立者可用之 marketplace 映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)的文章。

| 實驗室帳戶設定 | Instructions |
| -------------------- | ----- |
| Marketplace 映像 | 啟用 Windows 10 專業版映射，以在您的實驗室帳戶中使用。 |

### <a name="lab-settings"></a>實驗室設定
我們建議針對 PLTW 類別使用的 VM 大小取決於您的學生在類別中執行的工作負載類型。  針對上述類別，我們建議使用大型和小型 GPU (視覺效果) VM 大小。  為您的 PLTW 類別設定實驗室時，請參閱下表中的指導方針。

| 實驗室設定 | 值/指示 |
| ------------ | ------------------ |
|虛擬機器大小| **小型 GPU (視覺效果)** 。  此 VM 最適合用來進行遠端視覺效果、串流、遊戲、使用 OpenGL 和 DirectX 等架構的編碼。  針對下列 PLTW 類別，我們建議使用此大小：民事工程和架構、數位設備、電腦整合式製造;和工程設計與開發。
|虛擬機器大小| **龐大** 。  此大小最適合用於需要更快 CPU、更高本機磁碟效能、大型資料庫、大型記憶體快取的應用程式。  針對下列 PLTW 類別，我們建議使用此大小：設計設計、工程原則、電腦科學基本概念、電腦科學原則，以及電腦科學 A 的簡介。

### <a name="licensing-server"></a>授權伺服器
上述 PLTW 類別中使用的大部分軟體 *_都不需要_* 存取授權伺服器。  但是，如果您打算針對下列軟體使用 Autodesk 的網路授權模型，您將需要存取授權伺服器：
-   Revit
-   發明家
-   發明者凸輪

若要搭配使用網路授權與 Autodesk 的軟體，PLTW 提供在授權伺服器上安裝 Autodesk 之授權管理員的 [詳細步驟](https://www.pltw.org/pltw-software) 。  此授權伺服器通常位於您的內部部署網路中，或是裝載于 azure 虛擬機器上的 Azure 虛擬機器 (VM) 在 Azure 虛擬網路 (VNet) 中。

設定授權伺服器之後，您必須將 [VNet 對等](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) 互連至您的 [實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)。 必須執行網路對等互連 _before * 建立實驗室，讓實驗室虛擬機器可以存取授權伺服器，反之亦然。

Autodesk 產生的授權檔案會內嵌授權伺服器的 MAC 位址。  如果您決定使用 Azure VM 來裝載授權伺服器，請務必確定授權伺服器的 MAC 位址不會變更。   否則，當 MAC 位址變更時，就必須重新產生您的授權檔案。  遵循這些秘訣，以防止您的 MAC 位址變更：

- 為裝載授權伺服器的 Azure VM[設定靜態私人 IP 和 MAC 位址](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource#static-private-ip-and-mac-address)。
- 請確定您已在具有足夠 VM 容量的 region\location 中設定實驗室帳戶和授權伺服器的 VNet，如此您就不需要在稍後將這些資源移至新的 region\location。

此外，如需詳細資訊，請參閱有關 [如何設定授權伺服器作為共用資源](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource) 的文章。

### <a name="template-machine"></a>範本電腦
您需要 PLTW 的一些安裝檔案很大，而且在您將其下載至實驗室的範本機器時，需要較長的時間才能複製。

我們建議您在實體環境中建立 PLTW 映射，而不是將安裝檔案下載到範本電腦，並在該處安裝所有專案。  然後，您可以將映射匯入共用映射庫，讓您可以使用這些自訂映射來建立您的實驗室。  如需詳細資訊，請參閱下列文章： [將自訂映射上傳至共用映射庫](https://docs.microsoft.com/azure/lab-services/upload-custom-image-shared-image-gallery)。

遵循這項建議，以下是設定實驗室的主要工作：

1. 在您的實體環境中，建立類別的映射。

    a.  使用 PLTW 的詳細步驟來下載安裝檔案和安裝所需的軟體。

    > [!NOTE]    
    > 當您安裝 Autodesk 的應用程式時，您要安裝 Autodesk 的電腦必須能夠與您的授權伺服器通訊 (Autodesk 的安裝精靈會提示您指定) 上裝載授權伺服器之電腦的電腦名稱稱。  如果您要在 Azure VM 上裝載授權伺服器，您可能需要等候在實驗室的範本電腦上安裝 Autodesk，讓 Autodesk 的安裝精靈可以存取您的授權伺服器

    b.  [安裝和設定 OneDrive](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-onedrive) (或您學校可能會使用的其他備份選項) 。
    
    c.  [安裝和設定 Windows 更新](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-updates)。

1.  將自訂映射上傳至 [您的實驗室帳戶所附加的共用映射庫](https://docs.microsoft.com/azure/lab-services/how-to-attach-detach-shared-image-gallery)。

1.  建立實驗室，然後選取您在上一個步驟上傳的自訂映射。

1.  建立實驗室之後，請啟動並聯機到範本電腦，以驗證映射是否如預期般運作。

1.  最後，發佈範本電腦以建立學生的 Vm。

## <a name="student-devices"></a>學生裝置
您的學生可以從 Windows\Mac 電腦和 Chromebook 連線至其實驗室 Vm。  以下是每個選項的指示連結：

- [從 Windows 連線](https://docs.microsoft.com/azure/lab-services/how-to-use-classroom-lab#connect-to-the-vm)
- [從 Mac 連接](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-mac-remote-desktop)
- [從 Chromebook 連接](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-chromebook-remote-desktop)

## <a name="cost"></a>成本
讓我們來討論上述 PLTW 類別的可能成本預估。  此預估不包含執行授權伺服器或使用共用映射庫的成本。  我們將使用25名學生的課程。  排程的類別時間有20小時。  此外，每位學生都可以針對排定的課程時間以外的作業或指派，取得10小時的配額。  針對 **大型** 和 **小型 GPU (視覺效果)** 大小，請參閱下列成本估計值。

- **大型 VM**

    25名學生 x (20 個排程時間 + 10 個配額時數) x 70 實驗室單位 x 0.01 美元/小時 = 525.00 美元

- **小型 GPU (視覺化)**

    25名學生 x (20 個排程時間 + 10 個配額時數) x 160 實驗室單位 x 0.01 美元/小時 = 1200.00 美元

> [!IMPORTANT] 
> 成本預估僅供範例之用。  如需定價的最新詳細資訊，請參閱 [Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

> [!NOTE] 
> 許多 PLTW 類別會使用透過瀏覽器存取的應用程式，例如 MIT 應用程式發明者。  這些以瀏覽器為基礎的應用程式不需要快速的 CPU 或 GPU，而且可以從任何具有網際網路連線的裝置存取。  當學生使用這些類型的應用程式時，建議他們在其實體裝置上使用瀏覽器，而不是在其實驗室 Vm 上使用瀏覽器。  這可協助您只針對需要快速 CPU 或 GPU 的應用程式使用實驗室 Vm，以減少成本。

## <a name="next-steps"></a>後續步驟
接下來是設定任何實驗室的一般步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)。 