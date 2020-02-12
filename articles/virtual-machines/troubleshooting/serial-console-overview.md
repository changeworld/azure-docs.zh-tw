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
ms.openlocfilehash: 8eea568217dc5f47c45433e5fdd755682e322b2f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134058"
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

## <a name="serial-console-rbac-role"></a>序列主控台 RBAC 角色
如先前所述，序列主控台需要 VM 參與者或更高的 VM 或虛擬機器擴展集存取權。 如果您不想將 VM 參與者授與使用者，但仍想要讓使用者存取序列主控台，您可以使用下列角色來執行此動作：

```
{
  "Name": "Serial Console Role",
  "IsCustom": true,
  "Description": "Role for Serial Console Users that provides significantly reduced access than VM Contributor",
  "Actions": [
      "Microsoft.Compute/virtualMachines/*/write",
      "Microsoft.Compute/virtualMachines/*/read",
      "Microsoft.Storage/storageAccounts/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

### <a name="to-create-and-use-the-role"></a>若要建立和使用角色：
*   在已知位置（例如 `~/serialconsolerole.json`）儲存 JSON。
*   使用下列 Az CLI 命令來建立角色定義： `az role definition create --role-definition serialconsolerole.json -o=json`
*   如果您需要更新角色，請使用下列命令： `az role definition update --role-definition serialconsolerole.json -o=json`
*   角色會顯示在入口網站的存取控制（IAM）中（可能需要幾分鐘的時間才能傳播）
*   您可以將使用者新增至 VM，以及具有自訂角色角色的開機診斷儲存體帳戶
    *   請注意，使用者必須被授與 VM 上的自訂角色*和*開機診斷儲存體帳戶


## <a name="advanced-uses-for-serial-console"></a>序列主控台的 Advanced 使用
除了對 VM 的主控台存取，您也可以使用 Azure 序列主控台來進行下列動作：
* 將[系統要求命令傳送至您的 VM](./serial-console-nmi-sysrq.md)
* 將[非遮罩式插斷傳送至您的 VM](./serial-console-nmi-sysrq.md)
* 正常[重新開機或強制對 VM 進行電源迴圈](./serial-console-power-options.md)


## <a name="next-steps"></a>後續步驟
您可以在提要欄位中取得其他序列主控台檔。
- 如需詳細資訊，請查看適用于[Linux vm 的序列主控台](./serial-console-linux.md)。
- 如需詳細資訊，請查看適用于[Windows vm 的序列主控台](./serial-console-windows.md)。
