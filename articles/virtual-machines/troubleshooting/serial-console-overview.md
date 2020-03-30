---
title: Azure 串列主控台 |微軟文檔
description: Azure 串列主控台允許您在 SSH 或 RDP 無法接通連接到 VM。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267010"
---
# <a name="azure-serial-console"></a>Azure 串列主控台

Azure 門戶中的串列主控台為運行 Linux 或 Windows 的虛擬機器 （VM） 和虛擬機器縮放集實例提供對基於文本主控台的訪問。 此串列連接連接到 VM 或虛擬機器規模集實例的 ttyS0 或 COM1 序列埠，提供獨立于網路或作業系統狀態的訪問。 串列主控台只能通過使用 Azure 門戶進行訪問，並且僅允許具有"參與者"或更高存取權限的使用者訪問 VM 或虛擬機器規模集。

串列主控台的工作方式與 VM 和虛擬機器縮放集實例的工作方式相同。 在本文檔中，除非另有說明，否則對 VM 的所有提及都將隱式包含虛擬機器規模集實例。

> [!NOTE]
> 串列主控台通常位於全域 Azure 區域中，在 Azure 政府版中提供公共預覽版。 它在 Azure 中國雲中尚不可用。

## <a name="prerequisites-to-access-the-azure-serial-console"></a>訪問 Azure 串列主控台的先決條件
要訪問 VM 或虛擬機器縮放集實例上的串列主控台，您需要以下操作：

- 必須為 VM 啟用啟動診斷
- 使用密碼身份驗證的使用者帳戶必須存在於 VM 中。 您可以使用 VM 訪問擴展的[重置密碼](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)功能創建基於密碼的使用者。 然後，選取 [支援與疑難排解]**** 區段中的 [重設密碼]****。
- 訪問串列主控台的 Azure 帳戶必須具有 VM 和[引導診斷](boot-diagnostics.md)存儲帳戶的[虛擬機器參與者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

> [!NOTE]
> 不支援傳統部署。 VM 或虛擬機器縮放集實例必須使用 Azure 資源管理器部署模型。

## <a name="get-started-with-the-serial-console"></a>開始使用串列主控台
虛擬機器和虛擬機器縮放集的串列主控台只能通過 Azure 門戶訪問：

### <a name="serial-console-for-virtual-machines"></a>虛擬機器串列主控台
VM 的串列主控台與按一下 Azure 門戶中的 **"支援 + 故障排除**"部分中的**串列主控台**一樣簡單。
  1. 打開[Azure 門戶](https://portal.azure.com)。

  1. 導航到**所有資源**並選擇虛擬機器。 將會開啟該 VM 的概觀頁面。

  1. 向下捲動至 [支援與疑難排解]**** 區段，然後選取 [序列主控台]****。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 串列主控台視窗](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>虛擬機器縮放集的串列主控台
串列主控台可用於虛擬機器規模集，可在規模集中的每個實例上訪問。 在看到**串列主控台**按鈕之前，您必須導航到虛擬機器規模集的各個實例。 如果虛擬機器規模集未啟用啟動診斷，請確保更新虛擬機器規模集模型以啟用引導診斷，然後將所有實例升級到新模型以訪問串列主控台。
  1. 打開[Azure 門戶](https://portal.azure.com)。

  1. 導航到**所有資源**並選擇虛擬機器縮放集。 將打開虛擬機器規模集的概覽頁。

  1. 導航到**實例**

  1. 選擇虛擬機器縮放集實例

  1. 在 **"支援 + 故障排除"** 部分中，選擇**串列主控台**。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 虛擬機器規模集串列主控台](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>串列主控台的高級用途
除了對 VM 的主控台訪問外，還可以使用 Azure 串列主控台進行以下操作：
* 向[VM 發送系統請求命令](./serial-console-nmi-sysrq.md)
* 向[VM 發送非遮罩式插斷](./serial-console-nmi-sysrq.md)
* 優雅地[重新開機或強制重新開機 VM](./serial-console-power-options.md)


## <a name="next-steps"></a>後續步驟
側邊欄中提供了其他串列主控台文檔。
- 更多資訊可用於 Linux [VM 的串列主控台](./serial-console-linux.md)。
- 有關詳細資訊，適用于[Windows VM 的串列主控台](./serial-console-windows.md)。
