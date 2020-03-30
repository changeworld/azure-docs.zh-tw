---
title: Azure 資訊安全中心的自適性應用程式控制
description: 本文檔可説明您在 Azure 安全中心中使用自我調整應用程式控制項，以對在 Azure 電腦中運行的應用程式進行白名單。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604667"
---
# <a name="adaptive-application-controls"></a>自適性應用程式控制
了解如何利用此逐步解說，在 Azure 資訊安全中心設定應用程式控制。

## <a name="what-are-adaptive-application-controls-in-security-center"></a>什麼是 Azure 資訊安全中心的自適性應用程式控制？
自我調整應用程式控制是 Azure 安全中心的智慧、自動化端到端解決方案，可説明您控制哪些應用程式可以在 Azure 和非 Azure 電腦（Windows 和 Linux）上運行。 除其他好處外，這有助於增強電腦抵禦惡意軟體的能力。 安全中心使用機器學習來分析電腦上運行的應用程式，並以此智慧創建允許清單。 此功能大大簡化了配置和維護應用程式允許清單策略的過程，使您能夠：

- 封鎖執行惡意應用程式的嘗試或提出警示，包括反惡意程式碼解決方案可能遺漏的嘗試。
- 符合您組織規定只能使用授權軟體的安全性原則。
- 避免在您的環境中使用不必要的軟體。
- 避免執行舊版和不支援的應用程式。
- 阻止您的組織不允許的特定軟體工具。
- 讓 IT 能夠透過應用程式使用量來控制敏感性資料的存取。

> [!NOTE]
> 對於非 Azure 和 Linux 電腦，僅在稽核模式下支援自我調整應用程式控制項。

## <a name="how-to-enable-adaptive-application-controls"></a>如何啟用自適性應用程式控制？

自我調整應用程式控制項可説明您定義一組允許在配置的電腦群組中運行的應用程式。 此功能可用於 Azure 和非 Azure Windows（所有版本、經典或 Azure 資源管理器）和 Linux 電腦。 使用以下步驟配置應用程式允許清單：

1. 開啟 [資訊安全中心]**** 儀表板。

1. 在左側窗格中，選取位於 [進階雲端防禦]**** 之下的 [自適性應用程式控制]****。

    [![防禦](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

[自適性應用程式控制]**** 頁面隨即出現。

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

[VM 群組]**** 區段包含三個索引標籤：

* **已設定**：內含已設定應用程式控制之 VM 的群組清單。
* **建議**：建議採用應用程式控制的群組清單。 資訊安全中心會使用機器學習服務，根據 VM 是否以一致的方式執行相同的應用程式來識別適合採用應用程式控制的 VM。
* **無建議**：內含無任何應用程式控制建議之 VM 的群組清單。 例如，其上的應用程式一直改變且尚未達到穩定狀態的 VM。

> [!NOTE]
> 資訊安全中心會使用專屬群集演算法來建立 VM 群組，以確保類似的 VM 取得最佳建議應用程式控制原則。
>
>

### <a name="configure-a-new-application-control-policy"></a>設定新的應用程式控制原則

1. 為具有應用程式控制建議的組清單選擇 **"推薦**"選項卡：

   ![建議](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   此清單包括：

   - **組名稱**：訂閱和組的名稱
   - **VM 和電腦**：組中的虛擬機器數
   - **狀態**： 建議的狀態
   - **嚴重性**：建議的嚴重性級別

2. 按一下群組以開啟 [建立應用程式控制規則]**** 選項。

   [![應用程式控制規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. 在 [選取 VM]**** 中，檢閱建議的 VM 清單，並取消選取任何不想套用應用程式允許清單原則的 VM。 接下來，您會看到兩份清單：

   - **建議應用程式**：此群組中 VM 上經常使用的應用程式清單，建議您允許執行。
   - **更多應用程式**：此群組中 VM 上較不常使用或稱為「易受利用」(詳情請參閱下文) 的應用程式清單，建議您加以檢閱。

4. 檢閱每份清單中的應用程式，並取消選取您不想套用的應用程式。 每份清單都包括：

   - **名稱**：應用程式的憑證資訊或完整路徑
   - **檔案類型**：應用程式檔案類型。 這可以是 EXE、指令碼、MSI 或這些類型的任何排列。
   - **漏洞 ：** 警告圖示指示攻擊者是否可以使用特定應用程式繞過應用程式允許清單。 建議您在核准之前檢閱這些應用程式。
   - **使用者**：允許執行應用程式的建議使用者

5. 一旦完成您的選擇，請選取 [建立]****。 <br>
   選擇"創建"後，Azure 安全中心會在 Windows 伺服器 （AppLocker） 上可用的內置應用程式允許清單解決方案之上自動創建相應的規則。

> [!NOTE]
> - 資訊安全中心會依賴至少兩週的資料，以建立基準，並且在每個虛擬機器群組填入唯一建議。 資訊安全中心標準層的新客戶預期會有標準行為，也就是他們的虛擬機器群組一開始會出現在 [不推薦]** 索引標籤底下。
> - 資訊安全中心的自適性應用程式控制不支援已由 GPO 或本機安全性原則啟用 AppLocker 原則的 VM。
> -  最佳的安全性做法如下：資訊安全中心一律嘗試為選取為允許的應用程式建立發行者規則，且只有在應用程式沒有發行者資訊 (也就是未簽署) 時，才會針對特定應用程式的完整路徑建立路徑規則。
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>編輯和監視已設定應用程式控制的群組

1. 要編輯和監視配置的應用程式允許清單策略的組，請返回到**自我調整應用程式控制項**頁面，並在**VM 組**下選擇 **"配置**"：

   ![群組](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   此清單包括：

   - **組名稱**：訂閱和組的名稱
   - **VM 和電腦**：組中的虛擬機器數
   - **模式**：稽核模式將記錄運行不在允許清單中的應用程式的嘗試;強制將不允許應用程式運行，除非它們位於允許清單中
   - **警示**：目前所有的違規情形

2. 按一下群組以在 [編輯應用程式控制原則]**** 頁面中進行變更。

   ![保護](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. 在 [保護模式]**** 之下，您可以選取下列選項：

   - **稽核**：在此模式中，應用程式控制解決方案不會強制執行規則，而只會稽核受保護虛擬機器上的活動。 此模式建議用於下列情況：您想在封鎖要在目標 VM 中執行的應用程式之前，先觀察整體行為。
   - **強制**：在此模式中，應用程式控制解決方案會強制執行規則，並確定不得執行的應用程式會遭到封鎖。

   > [!NOTE]
   > -  [強制執行]**** 保護模式已停用，直到進一步通知為止。
   > - 如先前所述，根據預設，新的應用程式控制原則一律設定為「稽核」** 模式。 
   >

4. 在 [原則擴充功能]**** 底下，新增想允許的任何應用程式路徑。 添加這些路徑後，安全中心將更新應用程式允許在所選 VMS 組中的 VM 上的 VM 上的 VM 上的清單策略，並針對這些應用程式創建適當的規則，以及已有的規則。

5. 檢閱 [最近的警示] **** 區段中列出的目前違規情形。 按下每一行以重新導向到 Azure 資訊安全中心內的 [警示] **** 頁面，並檢視 Azure 資訊安全中心在相關聯 VM 上偵測到的所有警示。
   - **警示**：已記錄的任何違規情形。
   - **VM 數目**：具有此警示類型的虛擬機器數目。

6. 在 [發行者允許清單規則]、[路徑允許清單規則] 和 [雜湊允許清單規則] 底下，可以根據規則集合類型，查看群組中的 VM 上目前設定了哪些應用程式允許清單規則。************ 在每個規則中，您可以看到：：

   - **規則**：特定的參數，AppLocker 會根據這些參數來檢查應用程式，判斷是否允許執行應用程式。
   - **檔案類型**：特定規則所涵蓋的檔案類型。 這可以是下列任一項：EXE、指令碼、MSI 或這些檔案類型的任何排列。
   - **使用者**：使用者的名稱或數目，這些使用者可以執行應用程式允許清單規則所涵蓋的應用程式。

   ![列入允許清單規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. 若要刪除該特定規則或編輯允許的使用者，請按一下每一行結尾的三個點。

8. 對**自適性應用程式控制**原則進行變更之後，請按一下 [儲存]****。

### <a name="not-recommended-list"></a>不建議使用的清單

資訊安全中心只會針對執行一組穩定應用程式的虛擬機器，建議應用程式允許清單原則。 如果相關 VM 上的應用程式一直變更，則不會產生建議。

![建議](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

此清單包含：
- **組名稱**：訂閱和組的名稱
- **VM 和電腦**：組中的虛擬機器數

Azure 資訊安全中心也可讓您對非建議的 VM 群組定義應用程式允許清單原則。 遵循先前所述的相同準則，同樣對這些群組設定應用程式允許清單原則。

## <a name="move-a-vm-from-one-group-to-another"></a>將 VM 從一個組移動到另一個組

 將 VM 從一個組移動到另一個組時，應用於它的應用程式控制策略將更改為您移動到的組的設置。 您還可以將 VM 從配置的組移動到未配置的組，這將導致刪除以前應用於 VM 的任何應用程式控制策略。

 1. 在 **"自我調整應用程式控制項"** 頁（從 **"配置"** 選項卡中），按一下當前要移動的 VM 所屬的組。
1. 按一下 **"配置的 VM 和電腦**"。
1. 按一下 VM 行中的三個點以移動，然後按一下"**移動**"。 **將打開"將電腦移動到不同的組**"視窗。

    ![保護](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. 選擇要將 VM 移動到的組，然後按一下 **"移動電腦**"，然後按一下"**保存**"。

    ![保護](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> 按一下"**移動電腦"** 後，請務必按一下"**保存**"。 如果不按一下 **"保存**"，則不會移動電腦。

## <a name="next-steps"></a>後續步驟
在本文中，您學習了如何使用 Azure 安全中心中的自我調整應用程式控制項對在 Azure 和非 Azure VM 中運行的應用程式進行白名單。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [管理和回應 Azure 安全中心中的安全警報](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何在資訊安全中心管理警示，以及回應安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同類型的安全性警示。
* [Azure 安全中心故障排除指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何針對資訊安全中心的常見問題進行疑難排解。
* [Azure 安全博客](https://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。
