---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704192"
---
### <a name="run-multiple-containers-on-the-same-host"></a>在相同主機上執行多個容器

如果您打算使用公開的連接埠執行多個容器，請務必使用不同的公開連接埠來執行每個容器。 例如，在連接埠 5000 上執行第一個容器，以及在連接埠 5001 上執行第二個容器。

您可以讓此容器和不同的 Azure 認知服務容器在主機上一起執行。 您也可以針對相同的認知服務容器執行多個容器。
