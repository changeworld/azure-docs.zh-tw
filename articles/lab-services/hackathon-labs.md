---
title: 使用適用于駭客松的 Azure 實驗室服務
description: 本文說明如何使用 Azure 實驗室服務來建立可用於執行駭客松的實驗室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8727fad6f37d1365983127a1a514e87e3a5003b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445877"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>使用 Azure 實驗室服務進行下一個駭客松
Azure 實驗室服務的設計是輕量且便於使用，因此您可以快速啟動新的虛擬機器（Vm）實驗室來進行駭客松。  使用下列檢查清單，確保您的駭客松盡可能順暢。 此檢查清單應由負責建立及管理駭客松實驗室的 IT 部門或教職員完成。 

若要針對您的駭客松使用實驗室服務，請確定實驗室帳戶和實驗室至少會在駭客鬆開始前的幾天內建立。 此外，請遵循下列指導方針：

## <a name="guidance"></a>指引

- **在最接近參與者的區域或位置中建立實驗室**。 

    若要減少延遲，請在最接近您駭客松參與者的區域中建立您的實驗室。  如果您的參與者位於世界各地，您必須使用最佳的判斷來建立集中的實驗室。  或者，根據您參與者所在的位置，分割駭客松以使用多個實驗室。
- **選擇最適合使用需求的計算大小**。

    一般來說，計算大小愈大，虛擬機器的執行速度就越快。 不過，若要限制成本，您必須根據您的參與者需求來選取適當的計算大小。 如需可用計算大小的詳細資訊，請參閱[系統管理員指南中的 VM 調整大小資訊](administrator-guide.md#vm-sizing)。
- **設定 RDP\SSH 以進行與 Linux vm 的遠端桌面**連線。

    如果您的駭客松使用 Linux Vm，請確定已啟用遠端桌面，讓您的參與者可以使用 RDP （遠端桌面通訊協定）或 SSH （安全 shell）來連線到其 Vm。 只有 Linux Vm 需要此步驟，而且必須在建立實驗室時啟用。 此外，對於 RDP，您可能需要先在範本 VM 上安裝和設定 RDP 伺服器和 GUI 套件，然後再發佈。  如需詳細資訊，請參閱[啟用適用于 Linux 的遠端桌面](how-to-enable-remote-desktop-linux.md)的操作指南。

- **安裝和停止 Windows 更新**。 

    如果您使用 Windows 映像，建議您先在實驗室的[範本 VM](how-to-create-manage-template.md)上安裝最新的 Windows 更新，然後再將其發佈以建立實驗室的 vm。 基於安全性目的，以及防止參與者在駭客松安裝更新期間中斷，這也會導致 Vm 重新開機。 您也可以考慮關閉 Windows 更新以防止任何未來的中斷。 請參閱[安裝和設定 Windows 更新](how-to-prepare-windows-template.md#install-and-configure-updates)的操作指南。
- **決定學生要如何備份其工作**。 

    在駭客松的存留期內，每個學生都有指派一部虛擬機器。 他們可以直接將其工作儲存至電腦，但建議學生備份其工作，讓他們在駭客松結束後能夠存取。 例如，他們應該儲存到外部位置，例如 OneDrive、GitHub 等等。 若要使用 OneDrive，您可以選擇自動為實驗室虛擬機器上的學生進行設定。 請參閱[安裝和設定 OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive)的操作指南。
- **根據參與者數目設定 VM 容量**。 

    確定您的實驗室虛擬機器容量是根據您在駭客松中預期的參與者數目來設定。 當您發佈範本虛擬機器時，可能需要幾個小時的時間，才能在實驗室中建立所有電腦。 這就是為什麼我們建議您在開始駭客松的同時，才這麼做。 如需詳細資訊，請參閱[更新實驗室容量](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity)的操作指南。

- **決定是否要限制實驗室存取**。 

    將使用者新增至實驗室時，預設會啟用 [限制存取] 選項。 這項功能需要您先將所有駭客松參與者的電子郵件新增到清單中，才能使用註冊連結來註冊和存取實驗室。 如果您有不知道參與者在事件之前的駭客松，您可以選擇停用 [限制存取] 選項，讓任何人都能使用註冊連結向實驗室註冊。 如需詳細資訊，請參閱[新增使用者](how-to-configure-student-usage.md#add-users-to-a-lab)的操作指南。

- **確認 [排程]、[配額] 和 [autoshutdown] 設定**。 

    Lab Services 提供數個成本控制來限制 Vm 的使用量。 不過，如果這些設定不正確，可能會導致實驗室的虛擬機器意外關閉。 若要確定已針對您的駭客松適當地設定這些設定，請確認下列設定：

    **排程**：[排程](how-to-create-schedules.md)可讓您自動控制實驗室的機器何時啟動和關機。 根據預設，當您建立新的實驗室時，不會設定排程。 不過，您應該確保實驗室的排程是根據對您駭客松有意義的來設定。  例如，如果您的駭客松是從星期六上午8:00 開始，並于下午5:00 結束，則您可以建立一個排程，以便在星期六的上午7:30 （駭客鬆開始前30分鐘）自動啟動電腦，並將其關閉于星期天下午5:00。 相反地，您也可以決定不使用排程。

    **配額**：[配額](how-to-configure-student-usage.md#set-quotas-for-users)會控制參與者在排程的時數以外，可以存取虛擬機器的時數。 如果在參與者使用配額的同時，電腦會自動關閉，除非增加配額，否則參與者將無法重新開機。 根據預設，當您建立實驗室時，配額會設定為10小時。 同樣地，您應該務必設定配額，讓它能夠有足夠的時間來進行駭客松，如果您尚未建立排程，這就特別重要。

    **Autoshutdown**：啟用時， [Autoshutdown](how-to-enable-shutdown-disconnect.md)設定會使 Windows 虛擬機器在一段時間後，在學生從其 RDP 會話中斷連線後自動關閉。 此設定預設為停用狀態。

- **設定防火牆設定，以允許連接到實驗室 vm**。 

    請確定您學校或組織的防火牆設定允許使用 RDP\SSH. 連線到實驗室 Vm 如需詳細資訊，請參閱[設定網路防火牆設定](how-to-configure-firewall-settings.md)的操作指南。

- 將**RDP\SSH 用戶端安裝在參與者的平板電腦、mac、電腦**等上。

    駭客松參與者必須將 RDP 和/或 SSH 用戶端安裝在其用來連線到實驗室 Vm 的平板電腦或膝上型電腦上。 您可以選擇不同的 RDP 或 SSH 用戶端，例如：

    - 適用于 RDP 連線的 Microsoft**遠端桌面連線**應用程式。 不同類型的平臺（包括 Chromebook 和[Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162)）支援遠端桌面連線應用程式。
    - 使用 SSH 連接至 Linux VM 的[Putty](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) 。
- **確認實驗室虛擬機器**。 

    一旦您已發佈實驗室 Vm，您應該確認它們已正確設定。 您只需要對其中一個參與者的實驗室虛擬機器執行這項驗證：

    1. 使用 RDP 和/或 SSH 進行連接。
    2. 開啟您已安裝的每個額外應用程式和工具，以自訂基底虛擬機器映射。
    3. 逐步解說幾個基本案例，其中代表參與者將做的活動，以確保 VM 效能根據選取的計算大小而足夠。

## <a name="on-the-day-of-hackathon"></a>在駭客松日
本節概述完成駭客松日期的步驟。

1. **啟動實驗室 vm**。

    視您的作業系統而定，您的實驗室電腦最多可能需要30分鐘的時間才能啟動。 因此，請務必在駭客松啟動之前啟動機器，讓您的參與者不必等待。 如果您使用的是排程，請確定 Vm 至少會在前30分鐘自動啟動。
2. **邀請學生註冊並存取其實驗室虛擬機器**。 

    提供您的參與者下列資訊，讓參與者可以存取其實驗室 Vm。 

    - 實驗室的註冊連結。 
    - 應該用來連接到電腦的認證。 只有當您的實驗室使用以 Windows 為基礎的映射，而且您已將所有 Vm 設定為使用相同的密碼時，才適用此步驟。
    - 有關參與者如何將 RDP 和/或到其電腦的指示。

        如需詳細資訊，請參閱傳送[邀請給使用者](how-to-configure-student-usage.md?branch=master#send-invitations-to-users)和連線[到 Linux vm](how-to-use-remote-desktop-linux-student.md?branch=master)的操作指南。 

## <a name="next-steps"></a>後續步驟
遵循下列文章中的指示，開始在教室實驗室中建立實驗室帳戶：[教學課程：使用 Azure Lab Services 設定實驗室帳戶](tutorial-setup-lab-account.md)。