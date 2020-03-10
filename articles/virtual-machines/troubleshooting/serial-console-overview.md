---
title: Azure 序列主控台 |Microsoft Docs
description: 當 SSH 或 RDP 無法使用時，Azure 序列主控台可讓您連接到您的 VM。
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
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381710"
---
# <a name="azure-serial-console"></a>Azure 序列主控台

Azure 入口網站中的序列主控台可讓您存取執行 Linux 或 Windows 的虛擬機器（Vm）和虛擬機器擴展集實例的文字型主控台。 此序列連線會連線到 VM 或虛擬機器擴展集實例的 ttyS0 或 COM1 序列埠，並提供與網路或作業系統狀態無關的存取權。 您只能使用 Azure 入口網站來存取序列主控台，而且只允許對 VM 或虛擬機器擴展集具有「參與者」或更高版本存取角色的使用者。

序列主控台的運作方式與 Vm 和虛擬機器擴展集實例相同。 在本檔中，除非另有指示，否則所有提及的 Vm 都會隱含地包含虛擬機器擴展集實例。

> [!NOTE]
> 序列主控台已在全球 Azure 區域正式推出，且在 Azure Government 中為公開預覽。 Azure 中國雲端尚未提供此功能。

## <a name="prerequisites-to-access-the-azure-serial-console"></a>存取 Azure 序列主控台的必要條件
若要存取您 VM 或虛擬機器擴展集實例上的序列主控台，您將需要下列各項：

- 必須為 VM 啟用開機診斷
- 使用密碼驗證的使用者帳戶必須存在於 VM 中。 您可以使用 VM 存取延伸模組的 [[重設密碼](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)] 功能來建立密碼型使用者。 然後，選取 [支援與疑難排解] 區段中的 [重設密碼]。
- 存取序列主控台的 Azure 帳戶必須同時具有 VM 和[開機診斷](boot-diagnostics.md)儲存體帳戶的「[虛擬機器參與者」角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

> [!NOTE]
> 不支援傳統部署。 您的 VM 或虛擬機器擴展集實例必須使用 Azure Resource Manager 部署模型。

## <a name="get-started-with-the-serial-console"></a>開始使用序列主控台
Vm 和虛擬機器擴展集的序列主控台只能透過 Azure 入口網站進行存取：

### <a name="serial-console-for-virtual-machines"></a>虛擬機器的序列主控台
Vm 的序列主控台非常簡單，只要在 Azure 入口網站的 [**支援 + 疑難排解**] 區段中按一下**序列主控台**即可。
  1. 開啟 [Azure 入口網站](https://portal.azure.com)。

  1. 流覽至 [**所有資源**]，然後選取虛擬機器。 將會開啟該 VM 的概觀頁面。

  1. 向下捲動至 [支援與疑難排解] 區段，然後選取 [序列主控台]。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 序列主控台視窗](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>虛擬機器擴展集的序列主控台
序列主控台適用于虛擬機器擴展集，可在擴展集內的每個實例上存取。 您必須先流覽至虛擬機器擴展集的個別實例，才會看到 [**序列主控台**] 按鈕。 如果您的虛擬機器擴展集未啟用開機診斷，請務必更新您的虛擬機器擴展集模型以啟用開機診斷，然後將所有實例升級至新的模型，才能存取序列主控台。
  1. 開啟 [Azure 入口網站](https://portal.azure.com)。

  1. 流覽至 [**所有資源**]，然後選取虛擬機器擴展集。 虛擬機器擴展集的 [總覽] 頁面隨即開啟。

  1. 流覽至**實例**

  1. 選取虛擬機器擴展集實例

  1. 從 [**支援 + 疑難排解**] 區段中，選取 [**序列主控台**]。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 虛擬機器擴展集序列主控台](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>序列主控台的 Advanced 使用
除了對 VM 的主控台存取，您也可以使用 Azure 序列主控台來進行下列動作：
* 將[系統要求命令傳送至您的 VM](./serial-console-nmi-sysrq.md)
* 將[非遮罩式插斷傳送至您的 VM](./serial-console-nmi-sysrq.md)
* 正常[重新開機或強制對 VM 進行電源迴圈](./serial-console-power-options.md)


## <a name="next-steps"></a>後續步驟
您可以在提要欄位中取得其他序列主控台檔。
- 如需詳細資訊，請查看適用于[Linux vm 的序列主控台](./serial-console-linux.md)。
- 如需詳細資訊，請查看適用于[Windows vm 的序列主控台](./serial-console-windows.md)。
