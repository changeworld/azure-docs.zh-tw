---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466375"
---
針對航班中的資料：

- 標準傳輸層安全性 (TLS) 1.2 用於裝置與 Azure 之間傳輸的資料。 沒有 TLS 1.1 及更早的版本。 如果不支援 TLS 1.2，代理程式通訊將會遭到封鎖。 入口網站和 SDK 管理也需要 TLS 1.2。
- 當用戶端透過瀏覽器的本機 web UI 存取您的裝置時，系統會使用標準 TLS 1.2 作為預設的安全通訊協定。

  - 最佳做法是將您的瀏覽器設定為使用 TLS 1.2。
  - 您的裝置僅支援 TLS 1.2，不支援舊版 TLS 1.1 或 TLS 1.0。
- 建議您在從資料伺服器複製資料時，使用具有加密的 SMB 3.0 來保護資料。
