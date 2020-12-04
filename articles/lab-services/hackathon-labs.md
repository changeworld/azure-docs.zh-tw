---
title: 使用適用于駭客松的 Azure 實驗室服務
description: 本文說明如何使用 Azure 實驗室服務來建立可供您用來執行駭客松的實驗室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a0917ede4502dcbb59d1a30b7985b06c06975599
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602575"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>為您的下一個駭客松使用 Azure 實驗室服務
Azure 實驗室服務的設計可供輕量且容易使用，讓您可以快速啟動虛擬機器的新實驗室， (Vm) 駭客松。  使用下列檢查清單，以確保您的駭客松盡可能順暢。 這份檢查清單應由您的 IT 部門或教職員完成，負責建立及管理您的駭客松實驗室。 

若要針對您的駭客松使用實驗室服務，請確定您在駭客鬆開始前的幾天內至少建立了實驗室帳戶和實驗室。 此外，請遵循下列指導方針：

## <a name="guidance"></a>指引

- **在最接近參與者的區域或位置中建立實驗室**。 

    若要減少延遲，請在最接近您駭客松參與者的區域中建立您的實驗室。  如果您的參與者位於世界各地，您必須使用最佳判斷來建立集中位置的實驗室。  或者，根據您參與者所在的位置，將駭客松分割為使用多個實驗室。
- **選擇最適合使用需求的計算大小**。

    一般來說，計算大小愈大，虛擬機器的執行速度就愈快。 不過，若要限制成本，您必須根據您的參與者需求選取適當的計算大小。 如需可用計算大小的詳細資訊，請參閱 [系統管理員指南中的 VM](administrator-guide.md#vm-sizing) 調整大小資訊。
- **針對 Linux vm 的遠端桌面連線設定 RDP\SSH**。

    如果您的駭客松使用 Linux Vm，請確定已啟用 [遠端桌面]，讓您的參與者可以使用 RDP (遠端桌面通訊協定) 或 SSH (安全的 shell) 連接到其 Vm。 只有 Linux Vm 需要此步驟，而且必須在建立實驗室時啟用。 此外，針對 RDP，您可能需要在發佈之前，先在範本 VM 上安裝和設定 RDP 伺服器和 GUI 套件。  如需詳細資訊，請參閱 [啟用 Linux 遠端桌面](how-to-enable-remote-desktop-linux.md)的操作指南。

- **安裝和停止 Windows update**。 

    如果您使用的是 Windows 映像，建議您先在實驗室的 [範本 VM](how-to-create-manage-template.md) 上安裝最新的 Windows 更新，然後再將其發佈至建立實驗室的 vm。 基於安全性考慮，以及防止參與者在駭客松期間中斷以安裝更新，這也會導致 Vm 重新開機。 您也可以考慮關閉 Windows 更新以防止任何未來的中斷。 請參閱 [安裝和設定 Windows 更新](how-to-prepare-windows-template.md#install-and-configure-updates)的操作指南。
- **決定學生將如何備份其工作**。 

    每個學生都會在駭客松的存留期內指派虛擬機器。 他們可以將其工作直接儲存至電腦，但建議學生備份其工作，讓他們可以在駭客松結束後存取。 例如，他們應儲存至外部位置，例如 OneDrive、GitHub 等等。 若要使用 OneDrive，您可以選擇在實驗室虛擬機器上自動為學生進行設定。 請參閱 [安裝和設定 OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive)的操作指南。
- **根據參與者數目設定 VM 容量**。 

    確定您實驗室的虛擬機器容量是根據您預期駭客松的參與者數目來設定。 當您發佈範本虛擬機器時，可能需要數小時的時間來建立實驗室中的所有電腦。 這就是為什麼我們建議您在開始駭客松之前，先妥善處理。 如需詳細資訊，請參閱 [更新實驗室容量](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity)的操作指南。

- **決定是否要限制實驗室的存取**。 

    將使用者新增至實驗室時，預設會啟用 [限制存取] 選項。 這項功能需要您先將所有駭客松參與者的電子郵件新增至清單，他們才能使用註冊連結來註冊及存取實驗室。 如果您有不知道參與者在活動之前的駭客松，您可以選擇停用 [限制存取] 選項，讓任何人都能使用註冊連結向實驗室註冊。 如需詳細資訊，請參閱 [新增使用者](how-to-configure-student-usage.md)的操作指南。

- **確認 [排程]、[配額] 和 [自動關機] 設定**。 

    實驗室服務提供數個成本控制項來限制 Vm 的使用量。 但是，如果這些設定的設定不正確，可能會導致實驗室的虛擬機器意外關機。 若要確定已針對您的駭客松適當設定這些設定，請確認下列設定：

    **排程**： [排程](how-to-create-schedules.md) 可讓您自動控制實驗室機器的啟動和關機時間。 依預設，當您建立新的實驗室時，不會設定任何排程。 不過，您應該確定您實驗室的排程是根據對駭客松有意義的結果來設定的。  例如，如果您的駭客松在星期六上午8:00 開始，並在星期日下午5:00 結束，您可以建立排程，在星期六的上午7:30 開始時自動啟動電腦 (大約30分鐘的時間，在) 駭客鬆開始之前30分鐘，然後在星期日關閉下午5:00。 相反地，您也可以決定完全不使用排程。

    **配額**： [配額](how-to-configure-student-usage.md#set-quotas-for-users) 會控制參與者在排程的時間以外可存取虛擬機器的時數。 如果在參與者使用時達到配額，則電腦會自動關機，而參與者將無法重新開機，除非增加配額。 依預設，當您建立實驗室時，配額會設定為10小時。 同樣地，您應該確定要設定配額，讓駭客松有足夠的時間，這在您沒有建立排程的情況下特別重要。

    **自動關機**：啟用時， [自動關機](how-to-enable-shutdown-disconnect.md) 設定會讓 Windows 虛擬機器在一段時間後，在學生從其 RDP 會話中斷連線之後，自動關閉。 根據預設，此設定為停用。

- **設定防火牆設定以允許與實驗室 vm** 的連線。 

    確定您學校或組織的防火牆設定允許使用 RDP\SSH. 連線至實驗室 Vm 如需詳細資訊，請參閱 [設定網路防火牆設定](how-to-configure-firewall-settings.md)的操作指南。

- **在參與者的平板電腦、mac、電腦等上安裝 RDP\SSH 用戶端**。

    駭客松參與者必須在他們將用來連線到實驗室 Vm 的平板電腦或膝上型電腦上安裝 RDP 和/或 SSH 用戶端。 您可以選擇不同的 RDP 或 SSH 用戶端，例如：

    - 適用于 RDP 連線的 Microsoft **遠端桌面連線** 應用程式。 不同類型的平臺（包括 Chromebook 和 [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162)）支援遠端桌面連線應用程式。
    - 使用 SSH 連接到 Linux VM 的[Putty](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) 。
- **確認實驗室虛擬機器**。 

    當您發佈實驗室 Vm 之後，您應該確認它們已正確設定。 您只需要針對其中一個參與者的實驗室虛擬機器進行這項驗證：

    1. 使用 RDP 和/或 SSH 連接。
    2. 開啟您所安裝的每個額外應用程式和工具，以自訂基底虛擬機器映射。
    3. 逐步解說一些基本案例，這些案例代表參與者將執行的活動，以確保 VM 效能根據所選的計算大小而定。

## <a name="on-the-day-of-hackathon"></a>在駭客松日
本節將概述完成駭客松日期的步驟。

1. **啟動實驗室 vm**。

    視您的作業系統而定，您的實驗室電腦最多可能需要30分鐘的時間才能啟動。 因此，請務必在駭客松啟動之前啟動電腦，讓您的參與者不需要等待。 如果您是使用排程，請確定 Vm 會在稍早至少30分鐘內自動啟動。
2. **邀請學生註冊並存取其實驗室虛擬機器**。 

    為您的參與者提供下列資訊，讓參與者可以存取其實驗室 Vm。 

    - 實驗室的註冊連結。 
    - 應該用來連接到電腦的認證。 只有當您的實驗室使用以 Windows 為基礎的映射，而且您已將所有 Vm 設定為使用相同的密碼時，才適用此步驟。
    - 有關參與者如何透過 SSH 將 RDP 和/或至其電腦的指示。

        如需詳細資訊，請參閱將 [邀請傳送給使用者](how-to-configure-student-usage.md?branch=master#send-invitations-to-users) 以及連線 [至 Linux vm](how-to-use-remote-desktop-linux-student.md?branch=master)的操作指南。 

## <a name="next-steps"></a>後續步驟
遵循下列文章中的指示，開始在實驗室中建立實驗室帳戶： [教學課程：使用 Azure 實驗室服務設定實驗室帳戶](tutorial-setup-lab-account.md)。
