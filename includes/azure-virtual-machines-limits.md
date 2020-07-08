---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334602"
---
| 資源 | 限制 |
| --- | --- |
| 每一個雲端服務的[虛擬機器](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)數量 <sup>1</sup> |50 |
| 每一雲端服務的輸入端點 <sup>2</sup> |150 |

<sup>1</sup>使用傳統部署模型建立的虛擬機器（而不是 Azure Resource Manager）會自動儲存在雲端服務中。 您可以將更多虛擬機器加入至該雲端服務以獲得負載平衡和可用性。 

<sup>2</sup> 輸入端點即可從虛擬機器的雲端服務外部與某個虛擬機器進行通訊。 在相同雲端服務或虛擬網路中的虛擬機器可自動彼此通訊。 如需詳細資訊，請參閱[如何設定虛擬機器的端點](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 
