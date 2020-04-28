---
title: 瞭解安全性最佳做法-Azure 數位 Twins |Microsoft Docs
description: 瞭解 Azure 數位 Twins 和物聯網的安全性最佳作法。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122554"
---
# <a name="azure-digital-twins-security-best-practices"></a>Azure Digital Twins 安全性最佳做法

Azure Digital Twins 安全性可讓您精確地存取 IoT 圖表中的特定資源和動作。 它會透過名[為角色型存取控制](./security-role-based-access-control.md)的細微角色和版權管理來執行此動作。

Azure Digital Twins 也會使用 Azure IoT 中的其他安全性功能，包括 Azure Active Directory (Azure AD)。 因此，要設定和保護在 Azure Digital Twins 上建置的應用程式，必須使用目前建議的許多相同 [Azure IoT 安全性做法](../iot-fundamentals/iot-security-best-practices.md)。

本文摘要說明要遵循的主要最佳做法。

> [!IMPORTANT]
> 若要確保 IoT 空間能獲得最大安全性，請檢閱其他安全性資源。 請務必包含您的裝置廠商。

> [!TIP]
> 使用[適用于 iot 的 Azure 資訊安全中心](https://docs.microsoft.com/azure/asc-for-iot/)來協助偵測 iot 安全性威脅和弱點。

## <a name="iot-security-best-practices"></a>IoT 安全性最佳做法

可以安全地保護 IoT 裝置的一些主要做法包括：

> [!div class="checklist"]
> * 以防竄改的方式保護連線到 IoT 空間的每個裝置。
> * 限制 IoT 空間內每個裝置、感應器及人員的角色。 如果遭到入侵，可將影響降至最低。
> * 請考量可能會使用裝置 IP 位址篩選和連接埠限制。
> * 限制 I/O 和裝置頻寬，以改善效能。 速率限制可以藉由防止拒絕服務的攻擊來改善安全性。
> * 讓裝置的固件、作業系統和軟體保持在最新狀態。
> * 在裝置、軟體、網路和閘道安全性最佳作法持續改善和演進時，定期進行審核及審查。
> * 使用受信任、經認證且符合規範的安全性系統、軟體和裝置。 例如，請參閱 Azure 雲端[的合規性供應](https://azure.microsoft.com/overview/trusted-cloud/compliance/)專案。

可以安全地保護 IoT 空間的一些主要做法包括：

> [!div class="checklist"]
> * 加密已儲存或永續性的資料。
> * 需要定期變更或重新整理密碼或金鑰。
> * 謹慎地依角色來限制存取權和權限。 請閱讀下面的[角色型存取控制最佳做法](#role-based-access-control-best-practices)一節。
> * 請考慮使用分割的網路拓撲，讓每個網路上的裝置與其他網路隔離。
> * 使用功能強大的加密。 需要長密碼、使用安全通訊協定和[多重要素驗證](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。

[監視](./how-to-configure-monitoring.md) IoT 資源，以監看落在經常性作業範圍外的極端值、威脅或資源參數。 使用 Azure 分析進行監視管理。

> [!IMPORTANT]
> 閱讀 Azure [iot 安全性最佳做法](../iot-fundamentals/iot-security-best-practices.md)，以開始全方位的 IoT 安全性策略。

> [!NOTE]
> 如需事件處理和監視的詳細資訊，請參閱[使用 Azure 數位 Twins 路由事件和訊息](./concepts-events-routing.md)。

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory 最佳做法

Azure 數位 Twins 會使用[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/)來驗證使用者及保護應用程式。 Azure Active Directory 支援各種新式架構的驗證。 全部都以 OAuth 2.0 或 OpenID Connect 之類的業界標準通訊協定為基礎。 若要保護 Azure Active Directory 的 IoT 空間，其中的一些主要做法包括：

> [!div class="checklist"]
> * 將 Azure Active Directory 應用程式祕密和金鑰儲存在安全的位置，例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。
> * 使用受信任[憑證授權單位](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)所發出的憑證 (而非應用程式祕密) 進行驗證。
> * 限制權杖的 OAuth 2.0 存取範圍。
> * 確認權杖的有效時間長度以及權杖是否依然有效。
> * 設定適當的權杖有效時間長度。 更新過期權杖。
> * 依據[角色型存取控制的最佳做法](#role-based-access-control-best-practices)，移除未使用的重新**導向 uri**和許可權。

## <a name="role-based-access-control-best-practices"></a>角色型存取控制最佳做法

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Azure IoT 最佳做法，請參閱 [IoT 安全性最佳做法](../iot-fundamentals/iot-security-best-practices.md)。

* 若要了解角色型存取控制，請參閱[角色型存取控制](./security-role-based-access-control.md)。

* 若要了解驗證，請參閱[使用 API 進行驗證](./security-authenticating-apis.md)。
