---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993026"
---
## <a name="set-up-authentication"></a>設定驗證

若要存取服務，您必須提供帳戶金鑰、存取權杖或 Azure Active Directory 驗證權杖。 您也可以在[驗證概念頁面](../articles/spatial-anchors/concepts/authentication.md)中深入了解相關資訊。

### <a name="account-keys"></a>帳戶金鑰

帳戶金鑰是一種認證，可讓您的應用程式向 Azure Spatial Anchors 服務進行驗證。 帳戶金鑰的用途是協助您快速開始使用。 在應用程式與 Azure Spatial Anchors 整合的開發階段，其效用尤其顯著。 因此，您可以在開發期間，將帳戶金鑰內嵌在用戶端應用程式中。 在完成開發階段時，強烈建議您改用屬於生產層級、由存取權杖支援的驗證機制，或是 Azure Active Directory 使用者驗證。 若要取得用於開發的帳戶金鑰，請造訪您的 Azure Spatial Anchors 帳戶，並瀏覽至 [金鑰] 索引標籤。