---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67174616"
---
針對飛行中的資料：

- 標準 TLS 1.2 用於在裝置與 Azure 之間移動的資料。 未回溯到 TLS 1.1 和更早版本。 如果不支援 TLS 1.2，代理程式通訊將會遭到封鎖。 入口網站和 SDK 管理也需要 TLS 1.2。
- 當用戶端透過瀏覽器的本機 web UI 存取您的裝置時，會使用標準 TLS 1.2 做為預設的安全通訊協定。

    - 最佳做法是將瀏覽器設定為使用 TLS 1.2。
    - 如果瀏覽器不支援 TLS 1.2，您可以使用 TLS 1.1 或 TLS 1.0。
- 我們建議您在從資料伺服器複製資料時，使用 SMB 3.0 搭配加密來保護資料。
