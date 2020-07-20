---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 520fd50b2b0864f43c08687f05de377679b36d84
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886886"
---
## <a name="preventative"></a>預防

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 瞭解[如何在 Azure 中將 Linux 虛擬機器加密](/azure/virtual-machines/linux/encrypt-disks), 以及如何[加密 Windows VM 上的虛擬磁片](/azure/virtual-machines/windows/encrypt-disks)。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | Azure 虛擬機器支援[ExpressRoute](/azure/expressroute)和 VNet 加密。 請參閱[Vm 中的傳輸中加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶管理的金鑰是支援的 Azure 加密案例;請參閱[Azure 加密總覽](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。|
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過 HTTPS 和 SSL。 |

## <a name="network-segmentation"></a>網路分割

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | . |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 是 | 請參閱[使用 Azure Resource Manager 部署模型設定強制通道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

## <a name="detection"></a>偵測

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[在 azure 中監視和更新 Linux 虛擬機器](/azure/virtual-machines/linux/tutorial-monitoring)和[在 azure 中監視和更新 Windows 虛擬機器](/azure/virtual-machines/windows/tutorial-monitoring)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 |  |
| Authorization| 是 |  |


## <a name="audit-trail"></a>稽核線索

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核 | 否 |  |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 |  | 
