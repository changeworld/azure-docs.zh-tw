---
title: Azure 序列主控台 | Microsoft Docs
description: 當 SSH 或 RDP 無法使用時，Azure 序列主控台可讓您連線到您的虛擬機器。
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 65679c900baaf0f98a21e4b1f6b1d350c4b945c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074333"
---
# <a name="azure-serial-console"></a>Azure 序列主控台

Azure 入口網站上的序列主控台可讓您針對執行 Linux 或 Windows 的虛擬機器 (VM) 和虛擬機器擴展集執行個體，存取文字型主控台。 這個序列連線會連線至 VM 或虛擬機器擴展集執行個體的 ttyS0 或 COM1 序列埠，提供未依存於網路或作業系統狀態的存取權。 您只能使用 Azure 入口網站來存取序列主控台，而且只允許對 VM 或虛擬機器擴展集具有「參與者」或更高存取角色的使用者進行存取。

序列主控台的運作方式與 VM 和虛擬機器擴展集執行個體相同。 在本文件中，除非另有指示，否則所有提及的 VM 都會隱含虛擬機器擴展集執行個體。

> [!NOTE]
> 序列主控台已在全球 Azure 區域正式推出，且在 Azure Government 中為公開預覽。 目前尚未在「Azure 中國」雲端中提供序列主控台。

## <a name="prerequisites-to-access-the-azure-serial-console"></a>存取 Azure 序列主控台的必要條件
若要在 VM 或虛擬機器擴展集執行個體上執行序列主控台，您需要下列各項：

- 必須為 VM 啟用開機診斷
- 使用密碼驗證的使用者帳戶必須存在於 VM 中。 您可以使用 VM 存取擴充的[重設密碼](../extensions/vmaccess.md#reset-password)功能，建立以密碼為基礎的使用者。 然後，選取 [支援與疑難排解] 區段中的 [重設密碼]。
- 存取序列主控台的 Azure 帳戶必須具有 VM的 [虛擬機器參與者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[開機診斷](boot-diagnostics.md)儲存體帳戶

> [!NOTE]
> 不支援傳統部署。 您的 VM 或虛擬機器擴展集執行個體必須使用 Azure Resource Manager 部署模型。

## <a name="get-started-with-the-serial-console"></a>開始使用序列主控台
只有透過 Azure 入口網站，才能存取虛擬機器和虛擬機器擴展集的序列主控台：

### <a name="serial-console-for-virtual-machines"></a>虛擬機器的序列主控台
VM 的序列主控台非常簡單，只要在 Azure 入口網站的 [支援 + 疑難排解] 區段中，按一下 [序列主控台] 即可。
  1. 開啟 [Azure 入口網站](https://portal.azure.com)。

  1. 導覽至 [所有資源]，並選取一個虛擬機器。 將會開啟該 VM 的概觀頁面。

  1. 向下捲動至 [支援與疑難排解] 區段，然後選取 [序列主控台]。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 序列主控台視窗](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>虛擬機器擴展集的序列主控台
序列主控台適用於虛擬機器擴展集，可在擴展集內的每個執行個體上存取。 您必須先導覽至虛擬機器擴展集的個別執行個體，然後才會看見 [序列主控台] 按鈕。 如果您的虛擬機器擴展集未啟用開機診斷，請務必更新您的虛擬機器擴展集模型，以啟用開機診斷，然後將所有執行個體升級至新的模型，才能存取序列主控台。
  1. 開啟 [Azure 入口網站](https://portal.azure.com)。

  1. 導覽至 [所有資源]，並選取一個虛擬機器擴展集。 虛擬機器擴展集的概觀頁面隨即開啟。

  1. 導覽至 [執行個體]

  1. 選取虛擬機器擴展集執行個體

  1. 從 [支援 + 疑難排解] 區段中，選取 [序列主控台]。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 虛擬機器擴展集序列主控台](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>序列主控台中的 TLS 1.2
序列主控台使用 TLS 1.2 端對端來保護服務內的所有通訊。 序列主控台相依於使用者管理的開機診斷儲存體帳戶，而 TLS 1.2 必須分別為儲存體帳戶進行設定。 具體執行指示位於[此處](../../storage/common/transport-layer-security-configure-minimum-version.md)。

## <a name="advanced-uses-for-serial-console"></a>序列主控台的進階使用
除了對 VM 的主控台存取，您也可以使用 Azure 序列主控台來進行下列動作：
* [將系統要求命令傳送至您的 VM](./serial-console-nmi-sysrq.md)
* [將非遮罩式插斷傳送至您的 VM](./serial-console-nmi-sysrq.md)
* 正常[重新開啟或強制對 VM 進行電源循環](./serial-console-power-options.md)


## <a name="next-steps"></a>後續步驟
您可以在側邊欄取得其他序列主控台文件。
- 如需詳細資訊，請參閱[適用於 Linux VM 的序列主控台](./serial-console-linux.md)。
- 如需詳細資訊，請參閱[適用於 Windows VM 的序列主控台](./serial-console-windows.md)。
