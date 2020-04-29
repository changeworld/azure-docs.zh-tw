---
title: Azure 實驗室服務的加速教室實驗室設定指南
description: 本指南可協助實驗室建立者快速地設定實驗室帳戶，以便在其學校內使用。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878097"
---
# <a name="classroom-lab-setup-guide"></a>教室實驗室設定指南

將實驗室發佈至您的學生的程式，最多可能需要數小時的時間，視您在實驗室中建立的虛擬機器（Vm）數目而定。 至少允許一天設定實驗室，以確保其運作正常，並允許足夠的時間發佈學生的 Vm。

## <a name="understand-the-lab-requirements-of-your-class"></a>瞭解您類別的實驗室需求

設定新的實驗室之前，您應該考慮下列問題。

### <a name="what-software-requirements-does-the-class-have"></a>類別有哪些軟體需求？

根據您類別的學習目標，決定需要在實驗室的 Vm 上安裝哪些作業系統、應用程式和工具。 若要設定實驗室 Vm，您有三個選項：

- **使用 Azure Marketplace 映射**： Azure Marketplace 提供數以百計的映射，可供您在建立實驗室時使用。 針對某些類別，這些映射中的其中一個可能已經包含您的類別所需的所有專案。

- **建立新的自訂映射**：您可以建立自己的自訂映射，方法是使用 Azure Marketplace 映射作為起點，並藉由安裝額外的軟體並進行設定變更來加以自訂。

- **使用現有的自訂映射**：您可以重複使用先前建立的現有自訂映射，或是由您學校的其他系統管理員或教職員所建立的。 這需要您的系統管理員設定共用映射庫，這是用來儲存自訂映射的存放庫。

> [!NOTE]
> 您的系統管理員負責啟用 Azure Marketplace 映射和自訂映射，讓您可以使用它們。 與您的 IT 部門協調，以確保您所需的映射已啟用。 您所建立的自訂映射會自動啟用，以便在您擁有的實驗室中使用。

### <a name="what-hardware-requirements-does-the-class-have"></a>類別有哪些硬體需求？

有各種計算大小可供您選擇：

- 嵌套虛擬化大小，讓您可以將學生存取權提供給能夠裝載多個嵌套 Vm 的 VM。 例如，您可以使用此計算大小來進行網路課程。

- GPU 大小，讓您的學生可以使用電腦密集型類型的應用程式。 例如，這種選擇適用于人工智慧和機器學習。

請參閱 VM 重設[大小](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)指南，以查看可用計算大小的完整清單。

> [!NOTE]
> 視您實驗室的區域而定，您可能會看到較少的可用計算大小，因為這會因區域而異。 一般來說，您應該選取最接近您需求的最小計算大小。 使用 Azure 實驗室服務時，您可以在稍後視需要使用不同的計算容量來設定新的實驗室。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>類別對外部 Azure 或網路資源的相依性為何？

如果您的實驗室 Vm 需要使用外部資源，例如資料庫、檔案共用或授權伺服器，請與您的系統管理員協調，以確保您的實驗室能夠存取這些資源。

若要存取*不*受虛擬網路保護的 Azure 資源，您不需要由系統管理員尋求其他設定。 您可以透過公用網際網路存取這些資源。

> [!NOTE]
> 您應該考慮是否可以藉由直接在 VM 上提供資源，來減少實驗室對外部資源的相依性。 例如，若要避免從外部資料庫讀取資料的需求，您可以直接在 VM 上安裝資料庫。  

### <a name="how-will-costs-be-controlled"></a>如何控制成本？

實驗室服務會使用隨用隨付定價模型，這表示您只需針對實驗室 VM 執行的時間付費。 若要控制成本，您有三個通常會搭配使用的選項：

- **排程**：排程可讓您自動控制實驗室的 vm 啟動和關閉的時間。
- **配額**：配額會控制學生在排程的時數以外可存取 VM 的時數。 如果在學生使用配額時達到配額，則會自動關閉 VM。 除非增加配額，否則學生無法重新開機 VM。
- **自動關閉**：啟用時，自動關閉設定會使 Windows vm 在一段特定時間後自動關閉，在學生從遠端桌面通訊協定（RDP）會話中斷連線後。 此設定預設為停用狀態。  

    > [!NOTE]
    > 此設定目前僅存在於 Windows。

### <a name="how-will-students-save-their-work"></a>學生會如何儲存其工作？

每個學生都會獲指派自己的 VM，在實驗室的存留期間指派給他們。 他們可以選擇：

- 直接儲存到 VM。
- 儲存至外部位置，例如 OneDrive 或 GitHub。

您可以在實驗室 Vm 上自動設定學生專用的 OneDrive。

> [!NOTE]
> 為確保您的學生可以繼續存取實驗室以外的已儲存工作，並在類別結束後，建議學生將其工作儲存至外部存放庫。

### <a name="how-will-students-connect-to-their-vm"></a>學生如何連接到其 VM？

針對 RDP 到 Windows Vm，我們建議學生使用[Microsoft 遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。 遠端桌面用戶端支援 Mac、Chromebook 和 Windows。

針對 Linux Vm，學生可以使用 SSH 或 RDP。 若要讓學生使用 RDP 連線，您必須安裝並設定必要的 RDP 和 GUI 套件。

## <a name="set-up-your-lab"></a>設定實驗室

瞭解類別實驗室的需求之後，您就可以開始設定。 請遵循本節中的連結，以瞭解如何設定您的實驗室。

1. **建立實驗室。** 如需相關指示，請參閱[建立教室實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)的教學課程。

    > [!NOTE]
    > 如果您的類別需要嵌套虛擬化，請參閱[啟用嵌套虛擬化](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)中的步驟。

1. **自訂映射併發布實驗室 Vm。** 連接至名為範本 VM 的特殊 VM。 請參閱下列指南中的步驟：
    - [建立和管理範本 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [使用共用映像資源庫](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > 如果您使用的是 Windows，您也應該參閱[準備 windows 範本 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)中的指示。 這些指示包括設定 OneDrive 和 Office 的步驟，讓您的學生使用。

1. **管理 VM 集區和容量。** 您可以視您的類別需求輕鬆地相應增加或減少 VM 容量。 請記住，增加 VM 容量可能需要數小時的時間，因為這牽涉到設定新的 Vm。 請參閱[設定和管理 VM 集](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)區中的步驟。

1. **新增和管理實驗室使用者。** 若要將使用者新增至您的實驗室，請參閱下列教學課程中的步驟：
   - [將使用者新增至實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [傳送邀請給使用者](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    如需學生可以使用之帳戶類型的相關資訊，請參閱[學生帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)。
  
1. **設定成本控制項。** 若要控制實驗室的成本，請設定排程、配額和自動關閉。 請參閱下列教學課程：

   - [設定排程](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > 根據您已安裝的作業系統類型而定，VM 可能需要幾分鐘的時間才能啟動。 若要確保實驗室 VM 已準備好在您的排程時間內使用，建議您事先啟動30分鐘的 Vm。

   - [設定使用者的配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)，並[為特定使用者設定額外的配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [在中斷連線時啟用自動關機](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > 「排程」、「配額」和「自動關機」不適用於範本 VM。 因此，您必須確保在未使用範本 VM 時將它關閉。 否則，它會繼續產生成本。 此外，根據預設，當您建立實驗室時，範本 VM 會自動啟動。 請確定您立即完成實驗室的設定，並關閉範本 VM。

1. **使用 [儀表板]。** 如需指示，請參閱[使用實驗室的儀表板](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)。

    > [!NOTE]
    > 儀表板中顯示的預估成本是您可預期學生使用實驗室的最大成本。 例如，您的學生*不*會向您收取未使用配額時數的費用。 估計的成本*不會*反映使用範本 VM 或共用映射資源庫的任何費用。

## <a name="next-steps"></a>後續步驟

- [追蹤教室實驗的使用情況](tutorial-track-usage.md)
  
- [存取教室實驗室](tutorial-connect-virtual-machine-classroom-lab.md)