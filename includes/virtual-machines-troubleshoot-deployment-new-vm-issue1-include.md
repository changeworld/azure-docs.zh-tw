---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67173977"
---
## <a name="issue-custom-image-provisioning-errors"></a>問題：自訂映像；佈建錯誤
如果您上傳或擷取一般化的 VM 映像做為特殊化 VM 的映像，會發生佈建錯誤，反之亦然。 前者會導致佈建逾時錯誤，後者將導致佈建失敗。 若要毫無錯誤地部署自訂映像，必須確定擷取過程中映像類型不會變更。

下表列出一般化和特殊化映像的可能組合、您會遇到的錯誤類型、以及要如何修正錯誤。

