---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893980"
---
創建 Azure 機器學習工作區或工作區使用的資源時，可能會收到類似于以下消息的錯誤：

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

大多數資來源提供者都是自動註冊的，但不是全部。 如果您收到此消息，則需要註冊提到的提供程式。

有關註冊資來源提供者的資訊，請參閱[解決資來源提供者註冊的錯誤](../articles/azure-resource-manager/templates/error-register-resource-provider.md)。