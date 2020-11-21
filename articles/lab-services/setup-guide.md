---
title: 適用于 Azure 實驗室服務的加速實驗室設定指南
description: 如果您是實驗室建立者，本指南可協助您在您的學校快速設定實驗室帳戶。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021723"
---
# <a name="lab-setup-guide"></a>實驗室設定指南

在本指南中，您將瞭解如何為您學校的學生建立實驗室。

將實驗室發佈至學生的流程最多可能需要數小時的時間。 設定時間的數量取決於您要在實驗室中建立 (Vm) 的虛擬機器數目。 至少允許一天，以確保實驗室正常運作，並允許足夠的時間發佈學生的 Vm。

## <a name="understand-the-lab-requirements-of-your-class"></a>了解本課程的實驗室需求

在設定新的實驗室之前，您應思考下列問題。

### <a name="what-software-requirements-does-the-class-have"></a>本課程有哪些軟體需求？

當您決定要在實驗室 Vm 上安裝的作業系統、應用程式和工具時，請參閱您類別的學習目標。 您有三個設定實驗室 VM 的選項：

- **使用 Azure Marketplace 映像**：Azure Marketplace 提供數百個映像，其可供在建立實驗室時使用。 針對某些類別，這些映像的其中之一可能已經包含課程所需一切內容。

- **建立新的自訂映射**：您可以使用 Azure Marketplace 映射作為起點，建立自己的自訂映射。 然後您可以藉由安裝其他軟體並進行設定變更來自訂它。

- **使用現有的自訂映射**：您可以重複使用先前建立的自訂映射，或您學校中其他系統管理員或教職員所建立的映射。 若要使用自訂映射，您的系統管理員必須設定共用映射庫。  共用映射庫是用來儲存自訂映射的存放庫。

> [!NOTE]
> 系統管理員負責啟用 Azure Marketplace 映像和自訂映像，以便您可使用這些映像。 與您的 IT 部門協調，以確保您需要的映射已啟用。 您所建立的自訂映像會自動啟用，以供所擁有的實驗室使用。

### <a name="what-hardware-requirements-does-the-class-have"></a>本課程有哪些硬體需求？

您可以從各種不同的計算大小中選擇：

- **嵌套虛擬化大小**：可讓學生存取可裝載多個嵌套 VM 的 VM。 例如，您可以使用此計算大小來進行網路或道德攻擊類別。

- **GPU 大小**：讓您的學生使用大量電腦的應用程式類型。 例如，這項選擇通常用於人工智慧和機器學習。

如需有關選取適當 VM 大小的指引，請參閱：
- [VM 大小調整](./administrator-guide.md#vm-sizing)
- [從實體實驗室移至 Azure 實驗室服務](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> 因為計算大小可用性會因區域而異，所以您的實驗室可能會有較少的大小。 一般而言，您應該選取適合您需求的最小計算大小。 使用 Azure 實驗室服務時，您可以稍後視需要設定有更多計算容量的新實驗室。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>本課程對外部 Azure 或網路資源的相依性為何？
您的實驗室 Vm 可能需要存取外部資源，例如資料庫、檔案共用或授權伺服器。  若要允許您的實驗室 Vm 使用外部資源，請與您的 IT 系統管理員協調。

> [!NOTE]
> 您應該考慮是否可以直接在 VM 上提供網路資源，以減少實驗室對外部資源的相依性。 例如，為免除從外部資料庫讀取資料的需求，您可直接在 VM 上安裝該資料庫。  

### <a name="how-will-you-control-costs"></a>您將如何控制成本？
實驗室服務會使用隨用隨付定價模型，這表示您只需支付實驗室 VM 執行的時間。 若要控制成本，請使用下列任何或所有選項：

- **排程**：使用排程，自動控制實驗室 vm 的啟動和關閉時間。
- **配額**：使用配額來控制學生可以在排程的時間以外存取 VM 的時數。  當學生使用 VM 並達到配額時，VM 會自動關機。  除非您增加配額，否則學生無法重新開機 VM。
- **自動關機**：當您啟用自動關機設定時，Windows vm 會在學生從遠端桌面通訊協定 (RDP) 會話中斷連線後自動關閉。 根據預設，此設定為停用。

如需控制成本的詳細資訊，請參閱：
- [預估成本](./cost-management-guide.md#estimate-the-lab-costs)
- [管理成本](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>學生如何儲存作業？
在實驗室的存留期內，會將 VM 指派給每個個別的學生。 學生可以儲存其工作：

- 至 VM。
- 至外部位置，例如 OneDrive 或 GitHub。 您可設定 OneDrive 自動供學生在自己的實驗室 VM 上使用。

> [!NOTE]
> 為確保您的學生在實驗室之外仍能繼續存取其儲存的工作，且在類別結束之後，建議您將其工作儲存至外部存放庫。

### <a name="how-will-students-connect-to-their-vms"></a>學生將如何連線至其 Vm？
針對 Windows Vm 的 RDP 連線，建議學生使用 [Microsoft 遠端桌面用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。 遠端桌面用戶端支援 Mac、Chromebook 和 Windows 裝置。

針對 Linux Vm，學生可以使用安全殼層 (SSH) 或 RDP 通訊協定。 若要讓學生使用 RDP 進行連線，您必須安裝和設定必要的 RDP 和圖形化使用者介面， (GUI) 套件。

### <a name="will-students-also-use-microsoft-teams"></a>學生也會使用 Microsoft 小組嗎？
Azure 實驗室服務與 Microsoft 團隊整合，讓教職員成員可以在小組中建立及管理實驗室。  同樣地，學生可以在小組中存取他們的實驗室。

如需詳細資訊，請參閱 [Microsoft 小組中的 Azure 實驗室服務](./lab-services-within-teams-overview.md)。

## <a name="set-up-your-lab"></a>設定實驗室

了解課程實驗室的需求之後，即可開始設定實驗室。 若要深入瞭解，請遵循本節中的連結。 也提供在小組中設定實驗室的指示。

1. **建立實驗室**。 請參閱下列教學課程：
    - [建立教室實驗室](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [在小組中建立實驗室](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > 如果您的類別需要嵌套虛擬化，請參閱 [啟用嵌套虛擬化](./how-to-enable-nested-virtualization-template-vm.md)。

1. **自訂映射及發佈實驗室 vm**。 若要連接到名為範本 VM 的特殊 VM，請參閱：
    - [建立及管理範本 VM](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [使用共用映像庫](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > 如果您使用的是 Windows，也請參閱 [設定 windows 範本 VM](./how-to-prepare-windows-template.md)。 這些指示包括為學生設定 OneDrive 和 Microsoft Office 的步驟。

1. **管理 VM 集區和容量**。 您可視課程需求，輕鬆放大或縮小 VM 容量。 請記住，增加 VM 容量可能需要數小時的時間，因為正在設定新的 Vm。 查看下列文章：
    - [設定和管理 VM 集區](./how-to-set-virtual-machine-passwords.md)
    - [在小組的實驗室服務中管理 VM 集區](./how-to-manage-vm-pool-within-teams.md)

1. **新增和管理實驗室使用者**。 若要將使用者新增至您的實驗室，請參閱：
   - [將使用者新增至實驗室](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [傳送邀請電子郵件給使用者](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [管理小組中的實驗室服務使用者清單](./how-to-manage-user-lists-within-teams.md)

    如需學生可使用之帳戶類型的相關資訊，請參閱 [學生帳戶](./how-to-configure-student-usage.md#student-accounts)。
  
1. **設定成本控制項**。 若要設定排程、建立配額，並啟用自動關機，請參閱下列教學課程：

   - [設定排程](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > 視您所安裝的作業系統而定，VM 可能需要幾分鐘的時間才能啟動。 為確保實驗室 VM 可在您的排程時間內準備好使用，建議您提前30分鐘的時間啟動。

   - [為使用者設定配額](./how-to-configure-student-usage.md#set-quotas-for-users) ，並 [為特定使用者設定額外的配額](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [在中斷連線時啟用 VM 的自動關機](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > 排程和配額不適用於範本 VM，但會套用自動關機設定。 
        > 
        > 當您建立實驗室時，會建立範本 VM，但不會啟動。 您可以啟動範本 VM、與其連線、安裝實驗室的任何先決條件軟體，然後發佈它。 當您發佈範本 VM 時，如果您未手動完成，它會自動關機。 
        > 
        > 範本 Vm 在執行時會產生 *費用* ，因此請確定範本 vm 在您不需要執行時關機。

    - [建立及管理小組中的實驗室服務排程](./how-to-create-schedules-within-teams.md) 

1. **使用儀表板**。 如需相關指示，請參閱 [使用教室實驗室儀表板](./use-dashboard.md)。

    > [!NOTE]
    > 儀表板上顯示的預估成本是您可預期學生實驗室使用的最大成本。 例如，學生未使用的配額時數「不」收費。 估計成本 *不會* 反映使用範本 VM、共用映射庫，或實驗室建立者啟動使用者電腦時的任何費用。

## <a name="next-steps"></a>下一步

作為管理實驗室的一部分，請參閱下列文章：
- [追蹤教室實驗室的使用方式](tutorial-track-usage.md)  
- [存取教室實驗室](tutorial-connect-virtual-machine-classroom-lab.md)
