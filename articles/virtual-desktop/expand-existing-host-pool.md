---
title: 使用新的工作階段主機來擴充現有的主機集區春季 2020-Azure
description: 如何在 Windows 虛擬桌面中使用新的工作階段主機擴充現有的主機集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b23cee72af7f8262fdd552c2d4c26c608b68a1e4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527603"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>使用新的工作階段主機擴充現有的主機集區

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

當您在主機集區中增加使用量時，您可能需要使用新的工作階段主機擴充現有的主機集區，以處理新的負載。

本文將告訴您如何使用新的工作階段主機來擴充現有的主機集區。

## <a name="what-you-need-to-expand-the-host-pool"></a>擴充主機集區所需的內容

開始之前，請確定您已使用下列其中一種方法建立主機集區和工作階段主機虛擬機器（Vm）：

- [Azure 入口網站](./create-host-pools-azure-marketplace.md)
- [使用 PowerShell 建立主機集區](./create-host-pools-powershell.md)

當您第一次建立主機集區和工作階段主機 Vm 時，您也需要下列資訊：

- VM 大小、映射和名稱前置詞
- 網域加入系統管理員認證
- 虛擬網路名稱及子網路名稱

## <a name="add-virtual-machines-with-the-azure-portal"></a>使用 Azure 入口網站新增虛擬機器

若要藉由新增虛擬機器來擴充主機集區：

1. 登入 Azure 入口網站。

2. 搜尋並選取 **Windows 虛擬桌面**。

3. 在畫面左側的功能表中，選取 [**主機**集區]，然後選取您想要新增虛擬機器的主機集區名稱。

4. 從畫面左側的功能表中選取 [**工作階段主機**]。

5. 選取 [ **+ 新增**] 以開始建立您的主機集區。

6. 忽略 [基本] 索引標籤，並改為選取 [ **VM 詳細資料**] 索引標籤。您可以在這裡查看和編輯您想要新增至主機集區的虛擬機器（VM）詳細資料。

7. 選取您想要在其下建立 Vm 的資源群組，然後選取 [區域]。 您可以選擇目前使用的區域或新的區域。

8. 將您想要新增至主機集區的工作階段主機數目輸入**vm**數目。 例如，如果您要將主機集區擴充為五部主機，請輸入**5**。

    >[!NOTE]
    >您無法編輯 Vm 的大小或映射，因為請務必確保主機集區中的所有 Vm 都具有相同的大小。

9. 在 [**虛擬網路資訊**] 中，選取您要加入虛擬機器的虛擬網路和子網。 您可以選取現有電腦目前使用的相同虛擬網路，或選擇更適合您在步驟7中所選取區域的不同。

10. 針對**系統管理員帳戶**，輸入與您所選虛擬網路相關聯的 Active Directory 網域使用者名稱和密碼。 這些認證將用來將虛擬機器加入虛擬網路。

      >[!NOTE]
      >請確定您的系統管理員名稱符合這裡提供的資訊。 而且不會在帳戶上啟用 MFA。

11. 如果您有想要用來分組虛擬機器的標籤，請選取 [**標記**] 索引標籤。 否則，請略過此索引標籤。

12. 選取 [**審查 + 建立**] 索引標籤。請檢查您的選擇，如果一切看起來沒問題，請選取 [**建立**]。

## <a name="next-steps"></a>後續步驟

現在您已擴充現有的主機集區，您可以登入 Windows 虛擬桌面用戶端，在使用者會話中進行測試。 您可以使用下列任何一種用戶端連接到會話：

- [與 Windows 桌面用戶端連線](./connect-windows-7-10.md)
- [與 Web 用戶端連線](./connect-web.md)
- [與 Android 用戶端連線](./connect-android.md)
- [與 macOS 用戶端連線](./connect-macos.md)
- [與 iOS 用戶端連線](./connect-ios.md)
