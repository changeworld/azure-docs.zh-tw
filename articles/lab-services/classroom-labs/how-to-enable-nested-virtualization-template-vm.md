---
title: 在 Azure 實驗室服務的範本 VM 上啟用巢狀虛擬化 | Microsoft Docs
description: 了解如何建立內有多個 VM 的範本 VM。  換句話說，就是在 Azure 實驗室服務的範本 VM 上啟用巢狀虛擬化。
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
ms.openlocfilehash: b7831e554fcca20134d5cf8608481ff0c82eb8a0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591993"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>在 Azure 實驗室服務的範本虛擬機器上啟用巢狀虛擬化

目前，Azure 實驗室服務可讓您在實驗室中設定一個範本虛擬機器，並向每位使用者提供單一複本。 如果您是教授網路、安全性或 IT 課程的授課者，您可能需要為每位學生提供一個環境，在這個環境中，多部虛擬機器可以透過網路彼此通訊。

巢狀虛擬化可讓您在實驗室的範本虛擬機器內建立有多個 VM 的環境。 發佈範本會向實驗室中的每位使用者提供在其內部設定了多個 VM 的虛擬機器。  本文會說明如何在 Azure 實驗室服務的範本機器上設定巢狀虛擬化。

## <a name="what-is-nested-virtualization"></a>什麼是巢狀虛擬化？

巢狀虛擬化可讓您在虛擬機器內建立虛擬機器。 巢狀虛擬化是透過 Hyper-V 來實現的，而且只能在 Windows VM 上使用。

如需有關巢狀虛擬化的詳細資訊，請參閱下列文章：

- [Azure 中的巢狀虛擬化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何在 Azure VM 中啟用巢狀虛擬化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>考量

在設定具有巢狀虛擬化的實驗室之前，請先考量以下幾件事。

- 在建立新實驗室時，請選取 [中型 (巢狀虛擬化)] 或 [大型 (巢狀虛擬化)] 大小作為虛擬機器大小。 這些虛擬機器大小可支援巢狀虛擬化。
- 請選擇可為主機和用戶端虛擬機器提供良好效能的大小。  請記住，在使用虛擬化時，您所選擇的大小不可以只能容納一部機器，而是要能容納主機以及任何同時執行的 Hyper-V 機器。
- 用戶端虛擬機器無法存取 Azure 虛擬網路上的 Azure 資源，例如 DNS 伺服器。
- 主機虛擬機器需要經過設定才能讓用戶端機器具有網際網路連線能力。
- 用戶端虛擬機器已獲得授權而可作為獨立機器。 如需 Microsoft 作業系統和產品授權的相關資訊，請參閱 [Microsoft 授權](https://www.microsoft.com/licensing/default)。 在設定範本機器之前，請先檢查任何其他所用軟體的授權合約。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>在範本 VM 上啟用巢狀虛擬化

本文假設您已建立實驗室帳戶和實驗室。  如需如何建立新實驗室帳戶的詳細資訊，請參閱[用來設定實驗室帳戶的教學課程](tutorial-setup-lab-account.md)。 如需如何建立實驗室的詳細資訊，請參閱[設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)。

>[!IMPORTANT]
>在建立實驗室時，請選取 [大型 (巢狀虛擬化)] 或 [中型 (巢狀虛擬化)] 作為虛擬機器大小。  否則，將無法使用巢狀虛擬化。  

若要連線到範本機器，請參閱[建立和管理教室範本](how-to-create-manage-template.md)。

若要啟用巢狀虛擬化，您需要完成幾項工作。  

- **啟用 Hyper-V 角色**。 必須啟用 Hyper-V 角色，才能在實驗室服務虛擬機器上建立和執行 Hyper-V 虛擬機器。
- **啟用 DHCP**。  實驗室服務虛擬機器已啟用 DHCP 角色時，便可以自動向 Hyper-V 虛擬機器指派 IP 位址。
- **建立 Hyper-V VM 的 NAT 網路**。  NAT 網路會設定為可讓 Hyper-V 虛擬機器能夠存取網際網路。  Hyper-V 虛擬機器可以彼此通訊。

>[!NOTE]
>在實驗室服務 VM 上建立的 NAT 網路，可讓 Hyper-V VM 存取相同實驗室服務 VM 上的網際網路和其他 Hyper-V VM。  Hyper-V VM 將無法存取 Azure 虛擬網路上的 Azure 資源，例如 DNS 伺服器。

上述工作可使用指令碼或 Windows 工具來完成。  如需進一步的詳細資料，請閱讀下列各節。

### <a name="using-script-to-enable-nested-virtualization"></a>使用指令碼來啟用巢狀虛擬化

若要為 Windows Server 2016 或 Windows Server 2019 的巢狀虛擬化使用自動設定，請參閱[使用指令碼在 Azure 實驗室服務中的範本虛擬機器上啟用巢狀虛擬化](how-to-enable-nested-virtualization-template-vm-using-script.md)。 您將使用來自[實驗室服務 Hyper-V 指令碼](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)的指令碼來安裝 Hyper-V 角色。  這些指令碼也會設定網路功能，讓 Hyper-V 虛擬機器能夠存取網際網路。

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>使用 Windows 工具來啟用巢狀虛擬化

若要使用 Windows 角色和系統管理工具來設定 Windows Server 2016 或 Windows Server 2019 的巢狀虛擬化，請參閱[在 Azure 實驗室服務中手動啟用範本虛擬機器的巢狀虛擬化](how-to-enable-nested-virtualization-template-vm-ui.md)。  指示中也會說明如何設定網路功能，讓 Hyper-V 虛擬機器能夠存取網際網路。
