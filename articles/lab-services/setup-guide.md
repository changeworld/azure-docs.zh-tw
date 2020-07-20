---
title: Azure 實驗室服務的加速教室實驗室設定指南
description: 本指南可協助實驗室建立者快速設定實驗室帳戶，以便在學校使用。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad1a679c556316c2d23a713ffa5ac5dfe86cce0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445571"
---
# <a name="classroom-lab-setup-guide"></a>教室實驗室設定指南

向學生發佈實驗室的程序可能長達數小時，視實驗室要建立的虛擬機器 (VM) 數目而定。 至少預留一天的時間設定實驗室，以確保其運作正常，並有足夠的時間可發佈學生的 VM。

## <a name="understand-the-lab-requirements-of-your-class"></a>了解本課程的實驗室需求

在設定新的實驗室之前，您應思考下列問題。

### <a name="what-software-requirements-does-the-class-have"></a>本課程有哪些軟體需求？

根據本課程的學習目標，決定實驗室 VM 需要安裝的作業系統、應用程式和工具。 您有三個設定實驗室 VM 的選項：

- **使用 Azure Marketplace 映像**：Azure Marketplace 提供數百個映像，其可供在建立實驗室時使用。 針對某些類別，這些映像的其中之一可能已經包含課程所需一切內容。

- **建立新的自訂映像**：您可使用 Azure Marketplace 映像作為起點來建立自己的自訂映像，再藉由安裝其他軟體及變更設定來自訂此映像。

- **使用現有的自訂映像**：您可重複使用過去所建立自訂映像，或由其他系統管理員或學校教職員建立的現有映像。 這需要系統管理員設定共用映像庫，這是儲存自訂映像的存放庫。

> [!NOTE]
> 系統管理員負責啟用 Azure Marketplace 映像和自訂映像，以便您可使用這些映像。 與 IT 部門協調，以確保所需的映像已啟用。 您所建立的自訂映像會自動啟用，以供所擁有的實驗室使用。

### <a name="what-hardware-requirements-does-the-class-have"></a>本課程有哪些硬體需求？

有各種計算大小可供選擇：

- 巢狀虛擬化大小，其可供授權學生存取能夠裝載多個巢狀 VM 的 VM。 例如，您可將此計算大小用於網路課程。

- GPU 大小，讓學生可使用電腦密集類型的應用程式。 例如，此選項適合人工智慧和機器學習。

請參閱 [VM 大小調整](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)的指南，以查看可用計算大小的完整清單。

> [!NOTE]
> 視實驗室所屬區域而定，您可能會看到較少的可用計算大小，因為這會因區域而異。 一般而言，建議選取最接近需求的最小計算大小。 使用 Azure 實驗室服務，稍後如有需要，即可使用不同的計算容量來設定新實驗室。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>本課程對外部 Azure 或網路資源的相依性為何？

如果實驗室 VM 需要使用外部資源，例如資料庫、檔案共用或授權伺服器，請與系統管理員協調，以確保實驗室能夠存取這些資源。

若要存取「不受」虛擬網路保護的 Azure 資源，您無需要求系統管理員另行設定。 您可透過公用網際網路存取這些資源。

> [!NOTE]
> 建議考慮是否直接在 VM 上提供資源，以減少實驗室對外部資源的相依性。 例如，為免除從外部資料庫讀取資料的需求，您可直接在 VM 上安裝該資料庫。  

### <a name="how-will-costs-be-controlled"></a>如何控制成本？

實驗室服務使用隨用隨付定價模型，這表示您只需要支付實驗室 VM 執行時的費用。 您有三個控制成本的選項，通常會相互搭配使用：

- **排程**：排程可供自動控制實驗室 VM 的開機和關機時間。
- **配額**：配額能夠控制學生在排程時間以外可存取 VM 的時數。 如果學生在使用 VM 時達到配額，VM 就會自動關機。 除非增加配額，否則學生無法重新開機 VM。
- **自動關機**：啟用時，在學生從遠端桌面通訊協定 (RDP) 工作階段中斷連線後，自動關機設定會指示 Windows VM 在一段特定時間之後自動關機。 此設定預設為停用狀態。  

    > [!NOTE]
    > 目前僅 Windows 提供此設定。

### <a name="how-will-students-save-their-work"></a>學生如何儲存作業？

每位學生都會獲指派專用的 VM，供其在實驗室存留期間使用。 其可選擇：

- 直接儲存到 VM。
- 儲存到外部位置，例如 OneDrive 或 GitHub。

您可設定 OneDrive 自動供學生在自己的實驗室 VM 上使用。

> [!NOTE]
> 為確保學生可繼續存取儲存在實驗室以外的作業，建議學生在課程結束後，將作業儲存到外部存放庫。

### <a name="how-will-students-connect-to-their-vm"></a>學生如何連線到自己的 VM？

針對 Windows VM 的 RDP，建議學生使用 [Microsoft 遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。 遠端桌面用戶端支援 Mac、Chromebook 和 Windows。

使用 Linux VM 的學生可使用 SSH 或 RDP。 為讓學生能使用 RDP 連線，您必須安裝並設定必要的 RDP 和 GUI 套件。

## <a name="set-up-your-lab"></a>設定實驗室

了解課程實驗室的需求之後，即可開始設定實驗室。 請遵循本節的連結，了解如何設定實驗室。

1. **建立實驗室。** 如需相關指示，請參閱[建立教室實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)的教學課程。

    > [!NOTE]
    > 如果課程需要巢狀的虛擬化，請參閱[啟用巢狀虛擬化](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)中的步驟。

1. **自訂映像與發佈實驗室 VM。** 連線至稱為範本 VM 的特殊 VM。 請參閱下列指南中的步驟：
    - [建立及管理範本 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [使用共用映像庫](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > 如果使用的是 Windows，建議另行參閱[準備 Windows 範本 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template) 中的指示。 這些指示包括供學生使用的 OneDrive 和 Office 設定步驟。

1. **管理 VM 集區和容量。** 您可視課程需求，輕鬆放大或縮小 VM 容量。 請記住，增加 VM 容量可能需要數小時，因為這牽涉到設定新的 VM。 請參閱[設定和管理 VM 集區](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)中的步驟。

1. **新增與管理實驗室使用者**。 若要將使用者新增至實驗室，請參閱下列教學課程中的步驟：
   - [將使用者新增至實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [傳送邀請電子郵件給使用者](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    如需學生可使用的帳戶類型相關資訊，請參閱[學生帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts) (機器翻譯)。
  
1. **設定成本控制項。** 您可設定排程、配額和自動關機，以控制實驗室成本。 請參閱下列教學課程：

   - [設定排程](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > 視所安裝的作業系統類型而定，VM 啟動可能需要幾分鐘。 為確保實驗室 VM 在排程時間內隨時可用，建議提前 30 分鐘啟動 VM。

   - [設定使用者的配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) (機器翻譯) 和 [為特定使用者設定額外的配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users) (機器翻譯)
  
   - [在中斷連線時啟用 VM 的自動關機](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > 排程和配額不適用於範本 VM，但可套用自動關機設定。 
        > 
        > 範本 VM 在建立實驗室時即已建立，但不會啟動。 您可啟動範本 VM、連線到範本 VM，然後安裝實驗室的任何必要軟體，然後發佈範本 VM。 當發佈範本 VM 時，如果尚未套用自動關機，則系統會自動關機。 
        > 
        > 範本 VM 會在執行時產生**費用**，因此當不需要範本 VM 執行時，請確定其已關機。 


1. **使用儀表板。** 如需指示，請參閱[使用實驗室的儀表板](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard) (機器翻譯)。

    > [!NOTE]
    > 儀表板中所顯示預估成本是可預期學生使用實驗室的最大成本。 例如，學生未使用的配額時數「不」收費。 估計成本*不會*反映使用範本 VM、共用映射資源庫，或實驗室建立者啟動使用者電腦時的任何費用。

## <a name="next-steps"></a>後續步驟

- [追蹤教室實驗的使用情況](tutorial-track-usage.md)
  
- [存取教室實驗室](tutorial-connect-virtual-machine-classroom-lab.md)