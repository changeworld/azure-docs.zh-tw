---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 994a7726adec07f2f6533d460d05469a0f3c7bf3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102534"
---
| 資源 | 限制 |
| --- | --- |
| 每一雲端服務的虛擬機器 <sup>1</sup> |50 |
| 每一雲端服務的輸入端點 <sup>2</sup> |150 |

<sup>1</sup> 使用傳統部署模型 (而非 Azure Resource Manager) 所建立的虛擬機器都會自動儲存在雲端服務中。 您可以將更多虛擬機器加入至該雲端服務以獲得負載平衡和可用性。 

<sup>2</sup> 輸入端點即可從虛擬機器的雲端服務外部與某個虛擬機器進行通訊。 在相同雲端服務或虛擬網路中的虛擬機器可自動彼此通訊。  
