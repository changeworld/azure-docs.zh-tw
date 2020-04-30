---
title: 為 Azure Marketplace 建立與 Azure 相容的 VHD
description: 說明如何在 Microsoft Azure Marketplace 中為虛擬機器供應項目建立 VHD。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 642c6964aaad8d6e8750fca67efb11eb3feaf19d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147018"
---
# <a name="create-an-azure-compatible-vhd"></a>建立與 Azure 相容的 VHD

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將您的 Azure 虛擬機器供應專案移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 Azure 虛擬機器技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)中的指示來管理您遷移的供應專案。

本文會詳細說明建立 Microsoft Azure Marketplace 中虛擬機器 (VM) 供應項目虛擬硬碟 (VHD) 的所需步驟。  本文也會涵蓋其他不同層面，例如如何使用遠端桌面通訊協定 (RDP)、選擇 VM 的大小、安裝最新的 Windows 更新以及將 VHD 映像一般化的最佳作法。  下列各節主要著重於以 Windows 為基礎的 VHD；如需更多建立以 Linux 為基礎的 VHD 的詳細資訊，請參閱[經 Azure 背書的 Linux 發佈](../../../virtual-machines/linux/endorsed-distros.md)。 

> [!WARNING]
> 強烈建議您遵循本文中的指示，使用 Azure 建立內有經過預先設定和背書之作業系統的 VM。  如果這與您的解決方案不相容，則可以使用已核准的作業系統來建立及設定內部部署 VM。  接著您可以依照[準備 Windows VHD 或 VHDX 以上傳至 Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，設定並準備上傳。


## <a name="select-an-approved-base"></a>選擇已核准的基礎映像
VM 映像的作業系統 VHD 必須以獲得 Azure 核准的基底映像為基礎，包含 Windows Server 或 SQL Server。
若要開始，請從下列位於 Microsoft Azure 入口網站中的映像選擇一個建立 VM：

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) （Enterprise、Standard、Web）
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) （Enterprise、Standard、Web）

> [!TIP]
> 如果您使用的是最新的 Azure 入口網站或 PowerShell，2014 年 9 月 8 日之後發行的 Windows Server 映像都會獲得核准。

或者，Azure 會提供一組已核准的 Linux 散發套件。  如需目前的清單，請參閱[經 Azure 背書的 Linux 發佈](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。


## <a name="create-vm-in-the-azure-portal"></a>在 Microsoft Azure 入口網站網站中建立 VM 

在 Microsoft [Azure 入口網站](https://ms.portal.azure.com/)中，依照下列步驟建立基礎映像。

1. 利用您想用於發佈 VM 供應方案的 Azure 訂用帳戶之 Microsoft 帳戶和密碼登入入口網站。
2. 建立新的資源群組，並提供您的**資源群組名稱**、**訂用帳戶**以及**資源群組位置**。  如需詳細指引，請參閱[管理資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。
3. 按一下左側功能表中的 [虛擬機器]****，以開啟虛擬機器詳細資料頁面。 
4. 在此新頁面中，按一下 [+ 新增]**** 以開啟 [計算]**** 刀鋒視窗。  如果您在初始畫面上看不到 VM 型別，可以搜尋基底 VM 的名稱，例如：

    ![新 VM 的計算刀鋒視窗](./media/publishvm_014.png)

5. 選取適當的虛擬映像之後，請提供下列值：
   * 在 [基本概念]**** 刀鋒視窗中，輸入虛擬機器的 [名稱]****，長度須介於 1 到 15 個英數字元間。 (此範例使用 `DemoVm009`。)
   * 輸入要用於在 VM 上建立本機帳戶的 [使用者名稱]**** 和強度高的 [密碼]****。  （此處`adminUser`會使用）。 密碼的長度必須是8-123 個字元，且符合下列四個複雜性需求的其中三個：一個小寫字元、一個大寫字元、一個數位和一個特殊字元。 如需詳細資訊，請參閱使用者[名稱和密碼需求](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm)。
   * 選取您先前建立的資源群組 (此處為 `DemoResourceGroup`)。
   * 選取 Azure 資料中心**位置** (此處為 `West US`)。
   * 按一下 [確定]**** 儲存這些值。 

6. 選取要使用下列建議部署的 VM 大小：
   * 如果您打算開發 VHD 內部部署，大小不會造成影響。 請考慮使用其中一個較小的 VM。
   * 如果您打算在 Azure 中開發映像，請考慮使用其中一個建議的 VM 大小做為選取的映像。
   * 關於價格資訊，請參閱入口網站上所顯示的**建議定價層**選取器。 將顯示三個由發行者提供的建議大小。 (在這個案例中，發行者為 Microsoft。)

   ![新 VM 的大小刀鋒視窗](./media/publishvm_015.png)

7. 在 [設定]**** 刀鋒視窗中，將 [使用受控資料箱磁碟]**** 選項設定為 [否]****。  這可讓您以手動方式管理新的 VHD。 （[**設定**] 分頁也可讓您變更儲存體和網路選項的其他變更，例如，選取 [**磁片類型**] 中的 **[Premium （SSD）** ]）。 按一下 **[確定]** 以繼續。

    ![新 VM 的設定刀鋒視窗](./media/publishvm_016.png)

8. 按一下 [摘要] **** 以檢閱您的選擇。 當您看到 [通過驗證]**** 訊息時，請按一下 [確定]****。

    ![新 VM 的摘要刀鋒視窗](./media/publishvm_017.png)

Azure 會開始佈建您所指定的虛擬機器。  您可以按一下左側的 [虛擬機器]**** 索引標籤來追蹤其進度。  建立虛擬機器後，狀態會變更為 [執行中]****。  此時，您可以[連線到新的虛擬機器](./cpp-connect-vm.md)。


## <a name="next-steps"></a>後續步驟

如果您在建立以 Azure 為基礎的新 VHD 時遇到困難，請參閱 [VHD 建立期間的常見問題](./cpp-common-vhd-creation-issues.md)。  否則，接下來您必須[連線至您在 Azure 上建立的 VM](./cpp-connect-vm.md)。 
