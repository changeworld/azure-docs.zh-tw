---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "68361553"
---
### <a name="identity-tier"></a>身分識別層 

Microsoft Oracle 合作關係可讓您在 Azure、OCI 和 Oracle 應用程式之間設定統一的身分識別。 針對 JD Edwards EnterpriseOne 或 PeopleSoft 應用程式套件，需要 Oracle HTTP 伺服器（OHS）的實例，才能設定 Azure AD 與 Oracle IDCS 之間的單一登入。

OHS 會作為應用層的反向 proxy，這表示對終端應用程式的所有要求都會通過它。 Oracle Access Manager WebGate 是 OHS 的 web 伺服器外掛程式，可攔截傳送至結束應用程式的每個要求。 如果要存取的資源受到保護（需要已驗證的會話），WebGate 就會透過使用者的瀏覽器，以身分識別雲端服務起始 OIDC 驗證流程。 如需 OpenID Connect WebGate 所支援流程的詳細資訊，請參閱[Oracle Access Manager 檔](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)。

在此設定中，已登入 Azure AD 的使用者可以流覽至 JD Edwards EnterpriseOne 或 PeopleSoft 應用程式，而不需要再次登入（透過 Oracle 身分識別雲端服務）。 部署此解決方案的客戶享有單一登入的優點，包括一組認證、改良的登入體驗、改良的安全性，以及降低服務台成本。

若要深入瞭解如何使用 Azure AD 設定 JD Edwards EnterpriseOne 或 PeopleSoft 的單一登入，請參閱相關聯的[Oracle 白皮書](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)。