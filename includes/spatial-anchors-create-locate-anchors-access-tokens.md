---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993037"
---
### <a name="access-tokens"></a>存取權杖

向 Azure Spatial Anchors 進行驗證時，使用存取權杖是更健全的方法。 特別是當您準備應用程式以進行生產環境部署時。 此方法的運作方式大致上是設定可供您的用戶端應用程式用以安全驗證的後端服務。 您的後端服務在執行階段會與 AAD 互動，並且透過 Azure Spatial Anchors 安全權杖服務來要求存取權杖。 然後，此權杖會傳遞至用戶端應用程式，並且在 SDK 中用來向 Azure Spatial Anchors 進行驗證。
