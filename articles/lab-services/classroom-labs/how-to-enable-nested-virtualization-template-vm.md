---
title: 在 Azure 實驗室服務中的範本 VM 上啟用嵌套虛擬化 |微軟文檔
description: 瞭解如何創建包含多個 VM 的範本 VM。  換句話說，在 Azure 實驗室服務中的範本 VM 上啟用嵌套虛擬化。
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502004"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>在 Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化

目前，Azure 實驗室服務使您能夠在實驗室中設置一個範本虛擬機器，並使每個使用者都可以使用單個副本。 如果您是教授網路、安全或 IT 課程，則可能需要為每個學生提供一個環境，使多個虛擬機器可以通過網路相互對話。

嵌套虛擬化使您能夠在實驗室的範本虛擬機器內創建多 VM 環境。 發佈範本將為實驗室中的每個使用者提供一個虛擬機器，其中設置多個 VM。  本文介紹如何在 Azure 實驗室服務中的範本電腦上設置嵌套虛擬化。

## <a name="what-is-nested-virtualization"></a>什麼是嵌套虛擬化？

嵌套虛擬化使您能夠在虛擬機器中創建虛擬機器。 嵌套虛擬化通過 Hyper-V 完成，並且僅在 Windows VM 上可用。

有關嵌套虛擬化的詳細資訊，請參閱以下文章：

- [在 Azure 中嵌套虛擬化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何在 Azure VM 中啟用巢狀虛擬化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>考量

在設置具有嵌套虛擬化的實驗室之前，需要考慮一些事項。

- 創建新實驗室時，為虛擬機器大小選擇 **"中等（嵌套虛擬化）"** 或 **"大型（嵌套虛擬化）"** 大小。 這些虛擬機器大小支援嵌套虛擬化。
- 選擇可同時為主機和用戶端虛擬機器提供良好的性能的大小。  請記住，使用虛擬化時，您選擇的大小必須不僅適合一台電腦，還可用於主機以及必須同時運行的任何用戶端電腦。
- 用戶端虛擬機器將無法訪問 Azure 資源，例如 Azure 虛擬網路上的 DNS 伺服器。
- 主機虛擬機器需要設置，以允許用戶端電腦具有互聯網連接。
- 用戶端虛擬機器被許可為獨立電腦。 有關 Microsoft 作業系統和產品的許可資訊，請參閱[Microsoft 許可](https://www.microsoft.com/licensing/default)。 在設置範本電腦之前，請檢查是否有正在使用的任何其他軟體的授權合約。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>在範本 VM 上啟用巢狀虛擬化

本文假定您已創建實驗室帳戶和實驗室。  有關創建新實驗室帳戶的詳細資訊，請參閱[設置實驗室帳戶的教程](tutorial-setup-lab-account.md)。 有關如何創建實驗室的詳細資訊，請參閱[設置課堂實驗室教程](tutorial-setup-classroom-lab.md)。

>[!IMPORTANT]
>創建實驗室時，為虛擬機器大小選擇 **"大型（嵌套虛擬化）"** 或 **"中型"（嵌套虛擬化）。**  嵌套虛擬化將不起作用。  

要連接到範本電腦，請參閱[創建和管理教室範本](how-to-create-manage-template.md)。

### <a name="using-script-to-enable-nested-virtualization"></a>使用腳本啟用嵌套虛擬化

要將自動設置用於 Windows Server 2016 或 Windows Server 2019 的嵌套虛擬化，請參閱[使用腳本在 Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化](how-to-enable-nested-virtualization-template-vm-using-script.md)。 您將使用[實驗室服務 Hyper-V 腳本中的腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)來安裝 Hyper-V 角色。  這些腳本還將建立網路，以便 Hyper-V 虛擬機器可以進行 Internet 訪問。

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>使用 Windows 工具啟用嵌套虛擬化

使用 Windows 角色和管理工具為 Windows Server 2016 或 Windows Server 2019 設置嵌套虛擬化，請參閱[在 Azure 實驗室服務中的範本虛擬機器上手動啟用嵌套虛擬化](how-to-enable-nested-virtualization-template-vm-ui.md)。  說明還將介紹如何設置網路，以便 Hyper-V 虛擬機器可以進行 Internet 訪問。
