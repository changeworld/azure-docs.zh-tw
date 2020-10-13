---
title: 使用新的工作階段主機展開現有的主機集區-Azure
description: 如何使用 Windows 虛擬桌面中的新工作階段主機來擴充現有的主機集區。
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b65560a3b10d04887040c4da1e137912810b3095
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929586"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>使用新的工作階段主機展開現有的主機集區

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)。

當您在主機集區中提高使用量時，您可能需要使用新的工作階段主機來擴充現有的主機集區，以處理新的負載。

本文將告訴您如何使用新的工作階段主機來擴充現有的主機集區。

## <a name="what-you-need-to-expand-the-host-pool"></a>擴充主機集區所需的內容

開始之前，請確定您已使用下列其中一種方法，建立主機集區和工作階段主機虛擬機器 (Vm) ：

- [Azure 入口網站](./create-host-pools-azure-marketplace.md)
- [使用 PowerShell 建立主機集區](./create-host-pools-powershell.md)

當您第一次建立主機集區和工作階段主機 Vm 時，您也需要下列資訊：

- VM 大小、映射和名稱前置詞
- 網域加入系統管理員認證
- 虛擬網路名稱及子網路名稱

## <a name="add-virtual-machines-with-the-azure-portal"></a>使用 Azure 入口網站新增虛擬機器

藉由新增虛擬機器來擴充您的主機集區：

1. 登入 Azure 入口網站。

2. 搜尋並選取 **Windows 虛擬桌面**。

3. 在畫面左側的功能表中，選取 [ **主機**集區]，然後選取您想要新增虛擬機器之主機集區的名稱。

4. 從畫面左側的功能表中選取 [ **工作階段主機** ]。

5. 選取 [ **+ 新增** ] 以開始建立您的主機集區。

6. 略過 [基本] 索引標籤，改為選取 [ **VM 詳細資料** ] 索引標籤。您可以在這裡查看和編輯您想要新增至主機集區之虛擬機器 (VM) 的詳細資料。

7. 選取您要在其中建立 Vm 的資源群組，然後選取該區域。 您可以選擇您目前使用的區域或新的區域。

8. 輸入您想要新增至主機集區的工作階段主機數目，使其成為 **Vm 數目**。 例如，如果您要將主機集區擴充為五部主機，請輸入 **5**。

    >[!NOTE]
    >雖然可以編輯 Vm 的映射和前置詞，但如果您的 Vm 在相同的主機集區中有不同的映射，則不建議您進行編輯。 如果您打算從受影響的主機集區中移除具有較舊映射的 Vm，請編輯映射和前置詞。

9. 針對 **虛擬網路資訊**，請選取您想要加入虛擬機器的虛擬網路和子網。 您可以選取現有電腦目前使用的同一個虛擬網路，或選擇更適合您在步驟7中選取之區域的不同虛擬網路。

10. 在 [ **系統管理員帳戶**] 中，輸入與您選取的虛擬網路相關聯的 Active Directory 網域使用者名稱和密碼。 這些認證將用來將虛擬機器加入虛擬網路。

      >[!NOTE]
      >確定您的系統管理員名稱符合此處提供的資訊。 而且帳戶上沒有啟用 MFA。

11. 如果您有想要將虛擬機器群組在一起 **的標記，請選取 [卷** 標] 索引標籤。 否則，請略過此索引標籤。

12. 選取 [ **審核 + 建立** ] 索引標籤。檢查您的選擇，如果一切看起來沒問題，請選取 [ **建立**]。

## <a name="next-steps"></a>後續步驟

現在您已擴充現有的主機集區，您可以登入 Windows 虛擬桌面用戶端，在使用者會話中進行測試。 您可以使用下列任何一個用戶端連線到會話：

- [與 Windows 桌面用戶端連線](./connect-windows-7-10.md)
- [與 Web 用戶端連線](./connect-web.md)
- [與 Android 用戶端連線](./connect-android.md)
- [與 macOS 用戶端連線](./connect-macos.md)
- [與 iOS 用戶端連線](./connect-ios.md)
