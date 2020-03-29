---
title: 瞭解安全最佳實踐 - Azure 數位雙子星 |微軟文檔
description: 瞭解 Azure 數位孿生和物聯網的安全最佳實踐。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122554"
---
# <a name="azure-digital-twins-security-best-practices"></a>Azure Digital Twins 安全性最佳做法

Azure Digital Twins 安全性可讓您精確地存取 IoT 圖表中的特定資源和動作。 它通過稱為[基於角色的存取控制的](./security-role-based-access-control.md)細微性角色和版權管理來這樣做。

Azure Digital Twins 也會使用 Azure IoT 中的其他安全性功能，包括 Azure Active Directory (Azure AD)。 因此，要設定和保護在 Azure Digital Twins 上建置的應用程式，必須使用目前建議的許多相同 [Azure IoT 安全性做法](../iot-fundamentals/iot-security-best-practices.md)。

本文摘要說明要遵循的主要最佳做法。

> [!IMPORTANT]
> 若要確保 IoT 空間能獲得最大安全性，請檢閱其他安全性資源。 請務必包含您的裝置廠商。

> [!TIP]
> 使用[IoT 的 Azure 安全中心](https://docs.microsoft.com/azure/asc-for-iot/)説明檢測 IoT 安全威脅和漏洞。

## <a name="iot-security-best-practices"></a>IoT 安全性最佳做法

可以安全地保護 IoT 裝置的一些主要做法包括：

> [!div class="checklist"]
> * 以防竄改的方式保護連線到 IoT 空間的每個裝置。
> * 限制 IoT 空間內每個裝置、感應器及人員的角色。 如果遭到入侵，可將影響降至最低。
> * 請考量可能會使用裝置 IP 位址篩選和連接埠限制。
> * 限制 I/O 和裝置頻寬，以改善效能。 速率限制可以藉由防止拒絕服務的攻擊來改善安全性。
> * 使設備固件、作業系統和軟體保持最新。
> * 定期審核和審查設備、軟體、網路和閘道安全最佳實踐，因為它們在不斷改進和發展。
> * 使用可信、經過認證且合規的安全系統、軟體和設備。 例如，查看 Azure 雲[的合規性產品](https://azure.microsoft.com/overview/trusted-cloud/compliance/)。

可以安全地保護 IoT 空間的一些主要做法包括：

> [!div class="checklist"]
> * 加密已儲存或永續性的資料。
> * 需要定期變更或重新整理密碼或金鑰。
> * 謹慎地依角色來限制存取權和權限。 閱讀以下基於[角色的存取控制最佳實踐](#role-based-access-control-best-practices)部分。
> * 考慮劃分的網路拓撲，以便將每個網路上的設備與其他網路隔離。
> * 使用功能強大的加密。 需要長密碼、使用安全協定和[多重要素驗證](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。

[監視](./how-to-configure-monitoring.md) IoT 資源，以監看落在經常性作業範圍外的極端值、威脅或資源參數。 使用 Azure 分析進行監視管理。

> [!IMPORTANT]
> 閱讀 Azure [IoT 安全最佳實踐](../iot-fundamentals/iot-security-best-practices.md)，開始全面的 IoT 安全性原則。

> [!NOTE]
> 有關事件處理和監視的詳細資訊，請閱讀[Azure 數位孿生的路由事件和消息](./concepts-events-routing.md)。

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory 最佳做法

Azure 數位孿生使用[Azure 活動目錄](https://docs.microsoft.com/azure/active-directory/authentication/)對使用者進行身份驗證並保護應用程式。 Azure Active Directory 支援各種新式架構的驗證。 全部都以 OAuth 2.0 或 OpenID Connect 之類的業界標準通訊協定為基礎。 若要保護 Azure Active Directory 的 IoT 空間，其中的一些主要做法包括：

> [!div class="checklist"]
> * 將 Azure Active Directory 應用程式祕密和金鑰儲存在安全的位置，例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。
> * 使用受信任[憑證授權單位](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)所發出的憑證 (而非應用程式祕密) 進行驗證。
> * 限制權杖的 OAuth 2.0 存取範圍。
> * 確認權杖的有效時間長度以及權杖是否依然有效。
> * 設定適當的權杖有效時間長度。 更新過期權杖。
> * 根據[基於角色的存取控制最佳實踐](#role-based-access-control-best-practices)刪除未使用的**重定向 URI**和許可權。

## <a name="role-based-access-control-best-practices"></a>角色型存取控制最佳做法

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Azure IoT 最佳做法，請參閱 [IoT 安全性最佳做法](../iot-fundamentals/iot-security-best-practices.md)。

* 若要了解角色型存取控制，請參閱[角色型存取控制](./security-role-based-access-control.md)。

* 若要了解驗證，請參閱[使用 API 進行驗證](./security-authenticating-apis.md)。
