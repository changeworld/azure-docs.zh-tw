---
title: 設定 Azure API 管理實例的自訂功能變數名稱
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
ms.openlocfilehash: a7032c64efa486c65830e013373239647a368540
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311145"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>設定 Azure API 管理實例的自訂功能變數名稱

當您建立 Azure API 管理服務實例時，Azure 會為它指派一個 `azure-api.net` (的子域，例如 `apim-service-name.azure-api.net`) 。 不過，您可以使用自己的自訂功能變數名稱（例如 **contoso.com**）來公開 API 管理端點。 本教學課程說明如何將現有的自訂 DNS 名稱對應至 API 管理實例所公開的端點。

> [!IMPORTANT]
> API 管理只接受 [主機標頭](https://tools.ietf.org/html/rfc2616#section-14.23) 值符合預設功能變數名稱或任何已設定自訂功能變數名稱的要求。

> [!WARNING]
> 如果客戶想要使用憑證釘選來改善其應用程式的安全性，則必須使用其所管理的自訂功能變數名稱和憑證，而非預設憑證。 改為釘選預設憑證的客戶，將會對其無法控制的憑證屬性進行硬性相依性，這不是建議的作法。

## <a name="prerequisites"></a>Prerequisites

若要執行本文所述的步驟，您必須具有：

-   有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理實例。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
-   您或您的組織所擁有的自訂功能變數名稱。 本主題不提供如何採購自訂功能變數名稱的指示。
-   在 DNS 伺服器上裝載的 CNAME 記錄，可將自訂功能變數名稱對應至您的 API 管理實例的預設功能變數名稱。 本主題不提供如何裝載 CNAME 記錄的指示。
-   您必須具備一個含有公開和私密金鑰的有效憑證 (.PFX)。 主體或主體別名 (SAN) 必須符合功能變數名稱 (如此可讓 API 管理實例透過 TLS) 安全地公開 Url。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 入口網站來設定自訂網域名稱

1. 在 [Azure 入口網站](https://portal.azure.com/)中，流覽至您的 API 管理實例。
1. 選取 [ **自訂網域**]。

    您可以為多個端點指派自訂功能變數名稱。 目前有下列端點可用：

    - **閘道** (預設值為： `<apim-service-name>.azure-api.net`) 、
    - **開發人員入口網站 (舊版) ** (預設值為： `<apim-service-name>.portal.azure-api.net`) 、
    - **開發人員入口網站** (預設為： `<apim-service-name>.developer.azure-api.net`) 。
    - **管理** (預設值為：`<apim-service-name>.management.azure-api.net`)、
    - **SCM** (預設值為： `<apim-service-name>.scm.azure-api.net`) 、

    > [!NOTE]
    > 只有 **閘道** 端點可在使用層中進行設定。
    > 您可以更新所有端點或一部分端點。 客戶通常會更新 **閘道** (此 URL 是用來呼叫透過 api 管理) 的 api，以及 (開發人員入口網站 URL) 所公開的 **入口網站** 。
    > **管理** 和 **SCM** 端點僅供「API 管理」實例擁有者在內部使用，因此較不常指派自訂功能變數名稱。
    > 進階層支援設定**閘道****端點的多**個主機名稱。

1. 選取您想要更新的端點。
1. 在右側視窗中，按一下 [自訂]****。

    - 在 [自訂網域名稱]**** 中，指定您想要使用的名稱。 例如： `api.contoso.com` 。
    - 在 **憑證**中，選取 Key Vault 中的憑證。 您也可以上傳有效的。PFX 檔案，並提供其 **密碼**（如果以密碼保護憑證）。

    > [!NOTE]
    > 萬用字元功能變數名稱（例如， `*.contoso.com` 在使用層以外的所有層級都支援）。

    > [!TIP]
    > 建議您使用 [Azure Key Vault 來管理憑證](../key-vault/certificates/about-certificates.md) ，並將其設定為 autorenew。
    > 如果您使用 Azure Key Vault 管理自訂網域 TLS/SSL 憑證，請確定憑證已插入 Key Vault [作為 _憑證_](/rest/api/keyvault/createcertificate/createcertificate)，而不是 _秘密_。
    >
    > 若要提取 TLS/SSL 憑證，API 管理必須具有包含憑證之 Azure Key Vault 的清單和取得秘密許可權。 使用 Azure 入口網站將會自動完成所有必要的設定步驟。 使用命令列工具或管理 API 時，必須以手動方式授與這些許可權。 此作業有兩個階段。 首先，請在您的 API 管理實例上使用受控識別頁面，以確定已啟用受控識別，並記下該頁面上顯示的主體識別碼。 其次，在包含憑證的 Azure Key Vault 上授與許可權清單，並取得此主體識別碼的秘密許可權。
    >
    > 如果將憑證設定為 autorenew，則如果您的 API 管理層級（開發人員) 層除外），則 API 管理會自動挑選最新版本，而不會在服務 (任何停機時間。

1. 按一下 [套用]。

    > [!NOTE]
    > 指派憑證的流程可能需要 15 分鐘以上，視部署大小而定。 開發人員 SKU 有停機時間，基本版和更高版本的 Sku 沒有停機時間。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 組態

設定自訂功能變數名稱的 DNS 時，您有兩個選項：

-   設定指向已設定自訂功能變數名稱之端點的 CNAME 記錄。
-   設定指向您的 API 管理閘道 IP 位址的 A 記錄。

> [!NOTE]
> 雖然 API 管理實例 IP 位址是靜態的，但在某些情況下可能會變更。 基於這個原因，建議您在設定自訂網域時使用 CNAME。 選擇 DNS 設定方法時請考慮這一點。 如需詳細資訊，請參閱 [IP 檔文章](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) 和 [API 管理常見問題](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)。

## <a name="next-steps"></a>後續步驟

[升級和調整服務的規模](upgrade-and-scale.md)
