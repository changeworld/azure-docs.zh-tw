---
title: Azure 實驗室服務的加速課堂實驗室設置指南
description: 本指南可説明實驗室建立者快速設置實驗室帳戶，供其學校內使用。
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
ms.openlocfilehash: d58e11551c2857c269e8985e81f84138f6d389ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370815"
---
# <a name="classroom-lab-setup-guide"></a>課堂實驗室設置指南

根據將在實驗室中創建的虛擬機器 （VM） 數量，向學生髮布實驗室的過程最多可能需要幾個小時。  您應該給自己至少一天時間來設置一個實驗室，以確保實驗室正常工作，並留出足夠的時間發佈學生的 VM。

## <a name="understand-your-classs-lab-requirements"></a>瞭解您班級的實驗室要求

在設置新實驗室之前，應考慮以下問題：

**該類有哪些軟體要求？**

根據課堂的學習目標，您應該決定哪些作業系統、應用程式、工具等需要在實驗室的 VM 上安裝。   要設置實驗室 VM，有三個選項：

- **使用 Azure 應用商店映射**– 應用商店提供了數百個映射，您可以在創建實驗室時使用這些圖像。  對於某些類，市場映射可能已包含類所需的一切。

- **創建新的自訂映射**- 您可以使用市場映射作為起點，並通過安裝其他軟體、更改配置等來自訂映射。

- **使用現有的自訂映射**- 您可以重用您以前創建或由學校其他管理員或教職員工創建的現有自訂映射;這要求管理員配置共用映射庫，這是用於保存自訂映射的存儲庫。

> [!NOTE]
> 管理員負責啟用應用商店和自訂映射，以便您可以使用它們;您需要與 IT 部門協調，以確保啟用所需的映射。  您創建的自訂映射將自動啟用，以便在您擁有的實驗室中使用。

**該類有哪些硬體要求？**

有多種計算大小可供選擇，包括：

- 嵌套虛擬化大小，以便您可以允許學生訪問能夠承載多個嵌套 VM 的 VM;例如，此計算大小通常用於網路課程。

- GPU 大小，以便學生可以使用電腦密集型類型的應用程式，例如人工智慧和機器學習。

請參閱有關 VM[大小調整](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)的指南，查看可用計算大小的完整清單。

> [!NOTE]
> 根據實驗室將在其中創建的區域，您可能會看到可用的計算大小更少，因為區域會有所不同。  我們的一般建議是選擇最接近您需求的最小計算大小。  使用 Azure 實驗室服務，可以根據需要稍後設置具有不同計算容量的新實驗室。

**類對外部 Azure 或網路資源具有哪些依賴關係？**

如果實驗室 VM 需要使用外部資源（如資料庫、檔共用、許可伺服器等），則需要與管理員協調以確保實驗室有權訪問這些資源。

對於訪問*不受*虛擬網路保護的 Azure 資源，則可以通過公共 Internet 訪問這些資源，而無需管理員進行任何其他配置。

> [!NOTE]
> 應考慮是否可以通過在 VM 上直接提供資源來減少實驗室對外部資源的依賴關係。  例如，為了消除從外部資料庫讀取資料的需要，可以直接在 VM 上安裝資料庫。  

**如何控制成本？**

實驗室服務使用即付即用定價模型，這意味著您只為實驗室 VM 運行時付費。  為了控制成本，您有三個選項通常同時使用：

- **計畫**- 計畫允許您自動控制實驗室的 VM 何時啟動和關閉。
- **配額**- 配額控制學生將在計畫時數之外訪問 VM 的小時數。  如果在學生使用配額時達到配額，則 VM 將自動關閉，除非增加配額，否則學生無法重新開機 VM。
- **自動關機**- 啟用後，自動關閉設置會導致學生斷開 RDP 會話後一段時間後自動關閉。  此設定預設為停用狀態。  

    > [!NOTE]
    > 此設置當前僅存在於 Windows 中。

**學生將如何保存他們的工作？**

每個學生都分配了自己的 VM，這些 VM 在實驗室的存留期內分配給他們。  他們可以選擇：

- 直接保存到 VM。
- 保存到外部存儲庫，如 OneDrive、GitHub 等。

要使用 OneDrive，您可以選擇為其實驗室 VM 上的學生自動設定此功能。  下文提供了這方面的其他資訊。

> [!NOTE]
> 為了確保學生能夠繼續訪問實驗室外（包括課結束後）保存的工作，我們建議學生將工作保存到外部存儲庫。

**學生如何連接到他們的 VM？**

對於 Windows VM 的 RDP，我們建議學生使用[Microsoft 遠端桌面用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。  遠端桌面用戶端支援 Mac、Chromebook 和 Windows。

對於 Linux VM，學生可以使用 SSH 或 RDP。   要使用 RDP 進行連接，您負責安裝和配置必要的 RDP 和 GUI 包。  詳情如下。

## <a name="set-up-your-lab"></a>設定實驗室

瞭解課堂實驗室的要求後，即可設置它。  請按照本節中的連結瞭解如何設置實驗室。

1. **建立實驗室**

   有關[創建教室實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)的說明，請參閱教程。

    > [!NOTE]
    > 如果您的類需要嵌套虛擬化，請參閱說明指南中顯示[啟用嵌套虛擬化](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)的步驟。

1. **自訂映射併發布實驗室 VM**

    要自訂映射並在實驗室中發佈 VM，請連接到稱為範本 VM 的特殊 VM;請連接到名為範本 VM 的特殊 VM。請參閱以下指南的步驟：
    - [創建和管理範本 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [使用共用圖像庫](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > 如果使用 Windows，還應參考操作指南中[用於準備 Windows 範本 VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)的說明。  這些說明包括設置 OneDrive 和 Office 供學生使用的步驟。

1. **管理 VM 池和容量**

   您可以根據需要輕鬆擴展或減少 VM 容量。  請記住，增加 VM 容量可能需要幾個小時，因為這涉及到設置新的 VM。  請參閱設置[和管理 VM 池](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)的說明指南中的步驟。

1. **新增和管理實驗室使用者**

   要將使用者添加到實驗室，請參閱以下教程中的步驟：
   - [將使用者新增至實驗室](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [向使用者發送邀請](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    此外，有關學生可以使用的帳戶類型的資訊，請參閱以下文章：
    - [學生帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **設置成本控制**

    要控制實驗室的成本，請設置計畫、配額和自動關閉;請參閱以下教程中的說明：

   - [設置計畫](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > 根據已安裝的作業系統類型，VM 可能需要幾分鐘才能啟動。  為確保實驗室 VM 在預定時間內準備就緒，我們建議您提前 30 分鐘啟動 VM，以確保 VM 已運行並可供使用。

   - [為使用者設置配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)並為[特定使用者設置其他配額](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quota-for-a-specific-user)
  
   - [在中斷連線時啟用自動關機](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > 計畫、配額和自動關機*不適用於*範本 VM。  因此，必須確保在不使用範本 VM 時關閉該範本 VM，否則將繼續產生成本。  此外，預設情況下，當您創建實驗室時，範本 VM 將自動啟動，因此您需要確保立即完成實驗室設置並關閉範本 VM。

1. **使用儀表板**

    有關[使用實驗室儀表板](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)的說明，請參閱操作指南。

    > [!NOTE]
    > 儀表板中顯示的估計成本是學生使用實驗室時可以期望的最大成本。  例如，您的學生*不會*向您收取未使用的配額小時費。  估計成本*不*反映使用範本 VM 或共用映射庫的任何費用。

## <a name="next-steps"></a>後續步驟

查看下列文章：

- [追蹤教室實驗的使用情況](tutorial-track-usage.md)
  
- [存取教室實驗室](tutorial-connect-virtual-machine-classroom-lab.md)
