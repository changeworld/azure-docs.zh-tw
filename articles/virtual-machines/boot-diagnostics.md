---
title: Azure 開機診斷
description: Azure 開機診斷和受控開機診斷的總覽
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 1dcefefe02d91506c494cdf91e75ca951ccf43bb
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365462"
---
# <a name="azure-boot-diagnostics"></a>Azure 開機診斷

開機診斷是 Azure 虛擬機器 (VM) 的偵錯工具，可讓您診斷 VM 開機失敗。 開機診斷可讓使用者藉由收集序列記錄資訊和螢幕擷取畫面，來觀察其 VM 的狀態。

## <a name="boot-diagnostics-storage-account"></a>開機診斷儲存體帳戶
在 Azure 入口網站中建立 VM 時，預設會啟用開機診斷。 建議的開機診斷體驗是使用受控儲存體帳戶，因為它在建立 Azure VM 時，會大幅改善效能。 這是因為將使用 Azure 受控儲存體帳戶，以移除建立新的使用者儲存體帳戶來儲存開機診斷資料所需的時間。

替代的開機診斷體驗是使用使用者管理的儲存體帳戶。 使用者可以建立新的儲存體帳戶或使用現有的儲存體帳戶。 

> [!IMPORTANT]
> 開機診斷資料 blob (，其中包含記錄檔和快照集映射) 會儲存在受控儲存體帳戶中。 客戶只需支付 blob 所用的 Gib，而不是磁片的布建大小。 快照計量會用於受控儲存體帳戶的計費。 由於受管理的帳戶是在標準 LRS 或標準 ZRS 上建立，因此客戶只需支付其診斷資料 blob 大小的每月 $ 0.05/GB。 如需此定價的詳細資訊，請參閱 [受控磁片定價](https://azure.microsoft.com/pricing/details/managed-disks/)。 客戶會看到此費用與 VM 資源 URI 相關聯。 

## <a name="boot-diagnostics-view"></a>開機診斷視圖
位於虛擬機器分頁中的 [開機診斷] 選項位於 Azure 入口網站的 [ *支援與疑難排解* ] 區段下。 選取 [開機診斷] 將會顯示幕幕快照和序列記錄檔資訊。 序列記錄檔包含核心訊息，而螢幕擷取畫面則是 Vm 目前狀態的快照集。 根據 VM 是否正在執行 Windows 或 Linux，判斷預期的螢幕擷取畫面。 針對 Windows，使用者會看到桌面背景，而針對 Linux，使用者會看到登入提示。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 開機診斷的螢幕擷取畫面":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 開機診斷的螢幕擷取畫面":::

## <a name="enable-managed-boot-diagnostics"></a>啟用受控開機診斷 
您可以透過 Azure 入口網站、CLI 和 ARM 範本來啟用 Managed 開機診斷。 尚不支援透過 PowerShell 啟用。 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>使用 Azure 入口網站啟用 managed 開機診斷
在 Azure 入口網站中建立 VM 時，預設設定為使用受管理的儲存體帳戶啟用開機診斷。 若要查看此情況，請在建立 VM 時流覽至 [ *管理* ] 索引標籤。 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="在 VM 建立期間啟用 managed 開機診斷的螢幕擷取畫面。":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>使用 CLI 啟用受控開機診斷
Azure CLI 2.12.0 和更新版本支援使用受管理的儲存體帳戶開機診斷。 如果您未輸入儲存體帳戶的名稱或 URI，將會使用受管理的帳戶。 如需詳細資訊和程式碼範例，請參閱 [開機診斷的 CLI 檔](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest&preserve-view=true)。

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>使用 Azure Resource Manager (ARM) 範本啟用受控開機診斷
API 版本2020-06-01 之後的所有專案都支援受控開機診斷。 如需詳細資訊，請參閱 [開機診斷實例查看](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics)。

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>限制
- 開機診斷僅適用于 Azure Resource Manager Vm。
- 受控開機診斷不支援使用非受控 OS 磁片的 Vm。
- 開機診斷不支援 premium 儲存體帳戶，如果用於開機診斷的高階儲存體帳戶，使用者將會 `StorageAccountTypeNotSupported` 在啟動 VM 時收到錯誤。 
- Resource Manager API 版本 "2020-06-01" 和更新版本支援受控儲存體帳戶。
- Azure 序列主控台目前與用於開機診斷的受控儲存體帳戶不相容。 深入瞭解 [Azure 序列主控台](./troubleshooting/serial-console-overview.md)。
- 入口網站僅支援搭配使用開機診斷與適用于單一實例 Vm 的受控儲存體帳戶。

## <a name="next-steps"></a>後續步驟

深入瞭解 [Azure 序列主控台](./troubleshooting/serial-console-overview.md) ，以及如何使用開機診斷對 [azure 中的虛擬機器進行疑難排解](./troubleshooting/boot-diagnostics.md)。