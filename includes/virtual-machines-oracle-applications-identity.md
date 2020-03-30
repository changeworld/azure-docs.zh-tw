---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361553"
---
### <a name="identity-tier"></a>標識層 

通過 Microsoft-Oracle 合作關係，您可以在 Azure、OCI 和 Oracle 應用程式之間設置統一標識。 對於 JD Edwards 企業一 號或 PeopleSoft 應用程式套件，需要在 Azure AD 和 Oracle IDCS 之間設置單個登錄的 Oracle HTTP 伺服器 （OHS） 實例。

OHS 充當應用程式層的反向代理，這意味著所有對端應用程式的請求都通過它。 Oracle 訪問管理器 WebGate 是一個 OHS Web 服務器外掛程式，它攔截到最終應用程式的每個請求。 如果正在訪問的資源受到保護（需要經過身份驗證的會話），WebGate 將通過使用者的瀏覽器啟動具有標識雲服務的 OIDC 身份驗證流。 有關 OpenID 連接 WebGate 支援的流的詳細資訊，請參閱[Oracle 訪問管理器文檔](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)。

通過此設置，已登錄到 Azure AD 的使用者可以通過 Oracle 標識雲服務導航到 JD Edwards 企業一 號或 PeopleSoft 應用程式，而無需再次登錄。 部署此解決方案的客戶可享受單一登入的優勢，包括一組憑據、改進的登錄體驗、改進的安全性和降低説明台成本。

要瞭解有關為 JD Edwards 企業一 號或使用 Azure AD 為 PeopleSoft 設置單一登入設置單一登入名的更多資訊，請參閱關聯的[Oracle 白皮書](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)。