---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673759"
---
### <a name="identity-tier"></a>身分識別層 

Microsoft 與 Oracle 的合作關係可讓您跨 Azure、OCI、您的 Oracle 應用程設定統一的身分識別。 若使用 JD Edwards EnterpriseOne 或 PeopleSoft 應用程式套件，則需要 Oracle HTTP 伺服器 (OHS) 的執行個體，才能設定 Azure AD 與 Oracle IDCS 之間的單一登入。

OHS 會做為應用層的反向 Proxy，這表示對一般應用程式提出的所有要求都會通過。 Oracle Access Manager WebGate 是 OHS 的 Web 伺服器外掛程式，可攔截傳送至一般應用程式的每個要求。 如果要存取的資源受到保護 (需要已驗證的工作階段)，WebGate 就會透過使用者的瀏覽器，以身分識別雲端服務起始 OIDC 驗證流程。 如需 OpenID Connect WebGate 所支援流程的詳細資訊，請參閱 [Oracle Access Manager 文件](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327)。

在此設定中，已 (透過 Oracle 身分識別雲端服務) 登入 Azure AD 的使用者可以瀏覽至 JD Edwards EnterpriseOne 或 PeopleSoft 應用程式，而不需要再次登入。 部署此解決方案的客戶享有單一登入的優點，包括同一組認證、改良的登入體驗、改良的安全性、降低的技術支援中心成本。

若要深入瞭解如何使用 Azure AD 為 JD Edwards EnterpriseOne 或 PeopleSoft 設定單一登入，請參閱相關的 [Oracle 白皮書](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)。