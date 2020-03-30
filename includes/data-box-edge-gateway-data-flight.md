---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174616"
---
對於飛行中的資料：

- 標準 TLS 1.2 用於在設備和 Azure 之間傳輸的資料。 沒有回退到 TLS 1.1 和更早版本。 如果 TLS 1.2 不受支援，代理通信將被阻止。 TLS 1.2 還需要用於門戶和 SDK 管理。
- 當用戶端通過瀏覽器的本地 Web UI 訪問您的設備時，標準 TLS 1.2 將用作預設安全協定。

    - 最佳做法是將瀏覽器配置為使用 TLS 1.2。
    - 如果瀏覽器不支援 TLS 1.2，則可以使用 TLS 1.1 或 TLS 1.0。
- 我們建議您將 SMB 3.0 與加密一起加密，以保護資料從資料伺服器複製時。
