---
title: 在 Azure 實驗室服務的範本 VM 上啟用嵌套虛擬化 |Microsoft Docs
description: 瞭解如何在中建立具有多個 Vm 的範本 VM。  換句話說，請在 Azure 實驗室服務的範本 VM 上啟用「嵌套虛擬化」。
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
ms.openlocfilehash: 3c954c4689281838ea8c61c932cdcc3b74bac442
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184668"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>在 Azure 實驗室服務的範本虛擬機器上啟用嵌套虛擬化

目前，Azure 實驗室服務可讓您在實驗室中設定一個範本虛擬機器，並將單一複本提供給每個使用者。 如果您是教授的網路、安全性或 IT 課程，您可能需要為每一位學生提供一個環境，讓多部虛擬機器可以透過網路彼此溝通。

「嵌套虛擬化」可讓您在實驗室的範本虛擬機器內建立多個 VM 環境。 發佈範本會提供實驗室中的每位使用者，並在其中設定多個 Vm 的虛擬機器。  本文說明如何在 Azure 實驗室服務的範本機器上設定嵌套虛擬化。

## <a name="what-is-nested-virtualization"></a>什麼是嵌套的虛擬化？

「嵌套虛擬化」可讓您在虛擬機器中建立虛擬機器。 嵌套虛擬化是透過 Hyper-v 完成，而且只能在 Windows Vm 上使用。

如需有關嵌套虛擬化的詳細資訊，請參閱下列文章：

- [Azure 中的嵌套虛擬化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何在 Azure VM 中啟用巢狀虛擬化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>考量

在設定具有嵌套虛擬化的實驗室之前，以下是幾個要考慮的事項。

- 建立新的實驗室時，請針對虛擬機器大小選取 [**中（嵌套虛擬化）** ] 或 [**大型（嵌套虛擬化）** ] 大小。 這些虛擬機器大小支援嵌套虛擬化。
- 選擇可為主機和用戶端虛擬機器提供良好效能的大小。  請記住，使用虛擬化時，您所選擇的大小必須足以容納一部機器，但主機和任何 Hyper-v 機器同時執行。
- 用戶端虛擬機器無法存取 Azure 虛擬網路上的 Azure 資源，例如 DNS 伺服器。
- 主機虛擬機器需要安裝程式，才能讓用戶端電腦具有網際網路連線能力。
- 用戶端虛擬機器已授權為獨立的機器。 如需 Microsoft 作業系統和產品授權的相關資訊，請參閱[Microsoft 授權](https://www.microsoft.com/licensing/default)。 在設定範本機器之前，請先檢查所使用之任何其他軟體的授權合約。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>在範本 VM 上啟用巢狀虛擬化

本文假設您已建立實驗室帳戶和實驗室。  如需建立新實驗室帳戶的詳細資訊，請參閱[設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。 如需如何建立實驗室的詳細資訊，請參閱[設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)。

>[!IMPORTANT]
>建立實驗室時，請為虛擬機器大小選取 [**大型（嵌套虛擬化）** ] 或 [**中（嵌套虛擬化）** ]。  否則，將無法使用嵌套虛擬化。  

若要連接到範本機器，請參閱[建立和管理教室範本](how-to-create-manage-template.md)。

若要啟用嵌套虛擬化，需要完成幾個工作。  

- **啟用 hyper-v 角色**。 必須啟用 hyper-v 角色，才能在實驗室服務虛擬機器上建立和執行 Hyper-v 虛擬機器。
- **啟用 DHCP**。  當實驗室服務虛擬機器已啟用 DHCP 角色時，可以自動將 IP 位址指派給 Hyper-v 虛擬機器。
- **建立適用于 Hyper-v vm 的 NAT 網路**。  NAT 網路的設定是讓 Hyper-v 虛擬機器能夠存取網際網路。  Hyper-v 虛擬機器可以彼此通訊。

>[!NOTE]
>在實驗室服務 VM 上建立的 NAT 網路，可讓 Hyper-v VM 存取相同實驗室服務 VM 上的網際網路和其他 Hyper-v Vm。  Hyper-v VM 將無法存取 azure 虛擬網路上的 Azure 資源（例如 DNS 伺服器）。

完成以上所列的工作，可以使用腳本或使用 Windows 工具來完成。  如需進一步的詳細資料，請閱讀下列各節。

### <a name="using-script-to-enable-nested-virtualization"></a>使用腳本來啟用嵌套虛擬化

若要使用適用于 Windows Server 2016 或 Windows Server 2019 之嵌套虛擬化的自動安裝，請參閱[使用腳本在 Azure 實驗室服務中的範本虛擬機器上啟用嵌套虛擬化](how-to-enable-nested-virtualization-template-vm-using-script.md)。 您將使用[實驗室服務 hyper-v 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)中的腳本來安裝 hyper-v 角色。  腳本也會設定網路功能，讓 Hyper-v 虛擬機器能夠存取網際網路。

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>使用 Windows 工具來啟用嵌套虛擬化

使用 Windows 角色和系統管理工具設定適用于 Windows Server 2016 或 Windows Server 2019 的嵌套虛擬化，請參閱[在 Azure 實驗室服務中手動啟用範本虛擬機器上的嵌套虛擬化](how-to-enable-nested-virtualization-template-vm-ui.md)。  指示也會涵蓋如何設定網路功能，讓 Hyper-v 虛擬機器可以存取網際網路。
