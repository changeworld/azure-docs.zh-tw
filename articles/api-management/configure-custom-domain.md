---
title: 為 Azure API 管理實例配置自訂功能變數名稱
titleSuffix: Azure API Management
description: 本主題描述如何設定 Azure API 管理執行個體的自訂網域名稱。
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335842"
---
# <a name="configure-a-custom-domain-name"></a>設定自訂網域名稱

創建 Azure API 管理服務實例時，Azure 會為其分配`azure-api.net`子域（例如 。 `apim-service-name.azure-api.net` 但是，您可以使用自己的自訂功能變數名稱（如**contoso.com）** 公開 API 管理終結點。 本教程演示如何將現有自訂 DNS 名稱映射到 API 管理實例公開的終結點。

> [!IMPORTANT]
> API 管理僅接受具有與預設功能變數名稱或任何配置的自訂功能變數名稱匹配[的主機標頭](https://tools.ietf.org/html/rfc2616#section-14.23)值的請求。

> [!WARNING]
> 希望使用證書固定以提高應用程式安全性的客戶必須使用他們管理的自訂功能變數名稱和證書，而不是預設證書。 相反固定預設證書的客戶將硬依賴于他們不控制的證書的屬性，這不是推薦的做法。

## <a name="prerequisites"></a>Prerequisites

若要執行本文所述的步驟，您必須具有：

-   有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理實例。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
-   您或您的組織擁有的自訂功能變數名稱。 本主題不提供有關如何獲取自訂功能變數名稱的說明。
-   託管在 DNS 伺服器上的 CNAME 記錄，該記錄將自訂功能變數名稱映射到 API 管理實例的預設功能變數名稱。 本主題不提供有關如何託管 CNAME 記錄的說明。
-   您必須具備一個含有公開和私密金鑰的有效憑證 (.PFX)。 主題或主題替代名稱 （SAN） 必須與功能變數名稱匹配（這使 API 管理實例能夠通過 TLS 安全地公開 URL）。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 入口網站來設定自訂網域名稱

1. 導航到[Azure 門戶](https://portal.azure.com/)中的 API 管理實例。
1. 選擇**自訂域**。

    有許多終結點可以分配給自訂功能變數名稱。 目前有下列端點可用：

    - **閘道**（預設值為： `<apim-service-name>.azure-api.net`
    - **入口網站** (預設值為：`<apim-service-name>.portal.azure-api.net`)、
    - **管理** (預設值為：`<apim-service-name>.management.azure-api.net`)、
    - **SCM（** 預設值為： `<apim-service-name>.scm.azure-api.net`
    - **新門戶**（預設值為： `<apim-service-name>.developer.azure-api.net`）。

    > [!NOTE]
    > 只有**閘道**終結點可用於"消耗"層中的配置。
    > 您可以更新所有端點或一部分端點。 通常，客戶更新**閘道**（此 URL 用於調用通過 API 管理公開的 API）和**門戶**（開發人員門戶 URL）。
    > **管理和** **SCM**終結點僅由 API 管理實例擁有者在內部使用，因此不太頻繁地分配自訂功能變數名稱。
    > **高級**層支援為**閘道**終結點設置多個主機名稱。

1. 選取您想要更新的端點。
1. 在右側視窗中，按一下 [自訂]****。

    - 在 [自訂網域名稱]**** 中，指定您想要使用的名稱。 例如： `api.contoso.com` 。
    - 在**證書**中，從金鑰保存庫中選擇證書。 您還可以上載有效的 。如果證書受密碼保護，則提供**其密碼**。

    > [!NOTE]
    > 萬用字元功能變數名稱，例如`*.contoso.com`，除"消費"層外，所有層都支援萬用字元功能變數名稱。

    > [!TIP]
    > 我們建議使用 Azure 金鑰保存庫來管理證書並將其設置為自動旋轉。
    > 如果使用 Azure 金鑰保存庫管理自訂域 TLS/SSL 憑證，請確保將證書[作為_證書_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)插入金鑰保存庫，而不是_機密_。
    >
    > 要獲取 TLS/SSL 憑證，API 管理必須具有清單並獲取包含該證書的 Azure 金鑰保存庫上的秘密許可權。 使用 Azure 門戶時，將自動完成所有必要的配置步驟。 使用命令列工具或管理 API 時，必須手動授予這些許可權。 此作業有兩個階段。 首先，在 API 管理實例上使用託管標識頁，以確保啟用託管標識，並記下該頁上顯示的主體 ID。 其次，向包含證書的 Azure 金鑰保存庫上的此主體 ID 授予許可權清單並獲取此主體 ID 的許可權。
    >
    > 如果證書設置為自動旋轉，API Management 將自動獲取最新版本，而不會給服務造成任何停機時間（如果您的 API 管理層具有 SLA - 即除開發人員層之外的所有層中）。

1. 按一下 [套用]。

    > [!NOTE]
    > 指派憑證的流程可能需要 15 分鐘以上，視部署大小而定。 開發人員 SKU 具有停機時間，基本及以上 SKU 沒有停機時間。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 組態

為自訂功能變數名稱配置 DNS 時，有兩個選項：

-   配置指向配置自訂功能變數名稱端點的 CNAME 記錄。
-   配置指向 API 管理閘道 IP 位址的 A 記錄。

> [!NOTE]
> 儘管 API 管理實例 IP 位址是靜態的，但它可能會在少數情況下更改。 因此，建議在配置自訂域時使用 CNAME。 在選擇 DNS 配置方法時，請考慮到這一點。 請閱讀 IP[文檔文章](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses)和 API[管理常見問題解答](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)。

## <a name="next-steps"></a>後續步驟

[升級和調整服務的規模](upgrade-and-scale.md)
