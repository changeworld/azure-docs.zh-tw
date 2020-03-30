---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334602"
---
| 資源 | 限制 |
| --- | --- |
| 每一個雲端服務的[虛擬機器](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)數量 <sup>1</sup> |50 |
| 每一雲端服務的輸入端點 <sup>2</sup> |150 |

<sup>1</sup>使用經典部署模型而不是 Azure 資源管理器創建的虛擬機器將自動存儲在雲服務中。 您可以將更多虛擬機器加入至該雲端服務以獲得負載平衡和可用性。 

<sup>2</sup> 輸入端點即可從虛擬機器的雲端服務外部與某個虛擬機器進行通訊。 在相同雲端服務或虛擬網路中的虛擬機器可自動彼此通訊。 有關詳細資訊，請參閱[如何將終結點設置為虛擬機器](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 
