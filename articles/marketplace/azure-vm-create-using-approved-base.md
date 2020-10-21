---
title: 從核准的基礎 (VM) 建立 Azure 虛擬機器供應專案，Azure Marketplace
description: 瞭解如何從核准的基礎建立虛擬機器 (VM) 供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 34d64d70c657712bb44aa6331b53f014349a0dda
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283898"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>如何使用已核准的基底來建立虛擬機器

本文說明如何使用 Azure 來建立虛擬機器 (VM) ，其中包含預先設定的背書作業系統。 如果這與您的解決方案不相容，您可以使用已核准的作業系統來 [建立和設定內部部署 VM](azure-vm-create-using-own-image.md) ，然後依照 [準備 WINDOWS VHD 或 VHDX 以上傳至 Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)所述，設定並準備進行上傳。

> [!NOTE]
> 開始此程式之前，請先參閱 Azure VM 供應專案的 [技術需求](marketplace-virtual-machines.md#technical-requirements) ，包括虛擬硬碟 (VHD) 需求。

## <a name="select-an-approved-base-image"></a>選取核准的基底映射

選取下列其中一個 Windows 或 Linux 映射做為您的基底。

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、 [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview)、 [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure 會提供一組已核准的 Linux 散發套件。 如需目前的清單，請參閱[經 Azure 背書的 Linux 發佈](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

## <a name="create-vm-on-the-azure-portal"></a>在 Azure 入口網站上建立 VM

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 選取 [虛擬機器]。
3. 選取 [ **+ 新增** ] 以開啟 [ **建立虛擬機器** ] 畫面。
4. 從下拉式清單中選取影像，或選取 **[流覽所有公用和私用映射** ]，以搜尋或流覽所有可用的虛擬機器映射。
5. 若要建立 **第2代** VM，請移至 [ **Advanced** ] 索引標籤，然後選取 [ **Gen 2** ] 選項。

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="選取 [第1代] 或 [第2代]。":::

6. 選取要部署的 VM 大小。

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="選取 [第1代] 或 [第2代]。":::

7. 提供建立 VM 所需的其他詳細資料。
8. 選取 [檢閱 + 建立] 以檢閱選擇。 當 [ **通過驗證** ] 訊息出現時，請選取 [  **建立**]。

Azure 會開始佈建所指定的虛擬機器。 選取左側功能表中的 [ **虛擬機器** ] 索引標籤，以追蹤其進度。 建立之後，虛擬機器的狀態會變更為 [ **正在**執行]。


## <a name="configure-the-vm"></a>設定 VM

本節描述如何針對 Azure VM 進行調整大小、更新及一般化。 若要準備在 Azure Marketplace 部署虛擬機器，則必須完成以下步驟。

### <a name="connect-to-your-vm"></a>連接到您的 VM

請參閱下列檔，以連接到您的 [Windows](../virtual-machines/windows/connect-logon.md) 或 [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM。

### <a name="install-the-most-current-updates"></a>安裝最新的更新

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>執行額外的安全性檢查

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>執行自訂的設定和排程工作

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>後續步驟

- 建議的下一個步驟： [測試您的 VM 映射](azure-vm-image-test.md) ，以確保它符合 Azure Marketplace 發佈需求。 這是選擇性的。
- 如果您未測試 VM 映射，請繼續 [產生 SAS URI](azure-vm-get-sas-uri.md)。
- 如果您在建立新的以 Azure 為基礎的 VHD 時遇到困難，請參閱 [Azure Marketplace 的 VM 常見問題](azure-vm-create-faq.md)。
