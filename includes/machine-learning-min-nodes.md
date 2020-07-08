---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 05/08/2020
ms.author: larryfr
ms.openlocfilehash: c43b8b211b827a38db75bed17c23e684e54922a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996326"
---
> [!IMPORTANT]
> 若要避免在沒有任何作業正在執行時產生費用，請**將最小節點設定為 0**。 此設定可讓 Azure Machine Learning 在節點不在使用中時，將它們解除配置。 任何大於0的值都會讓節點數目保持執行，即使它們不在使用中也一樣。