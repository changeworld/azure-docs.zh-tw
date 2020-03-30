---
title: 為自託管 Azure API 管理閘道配置自訂功能變數名稱 |微軟文檔
description: 本主題介紹為自託管 Azure API 管理閘道配置自訂功能變數名稱的步驟。
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335939"
---
# <a name="configure-a-custom-domain-name"></a>設定自訂網域名稱

預配[自託管的 Azure API 管理閘道](self-hosted-gateway-overview.md)時，不會為其分配主機名稱，並且必須由其 IP 位址引用。 本文演示如何映射現有的自訂 DNS 名稱（也稱為主機名稱）自託管閘道。

> [!NOTE]
> 自託管閘道功能處於預覽狀態。 在預覽期間，自託管閘道僅在開發人員和高級層中可用，無需支付額外費用。 開發人員層僅限於單個自託管閘道部署。

## <a name="prerequisites"></a>Prerequisites

若要執行本文所述的步驟，您必須具有：

-   有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理實例。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
- 自託管閘道。 有關詳細資訊，請參閱[如何預配自託管閘道](api-management-howto-provision-self-hosted-gateway.md)
-   您或您的組織擁有的自訂功能變數名稱。 本主題不提供有關如何獲取自訂功能變數名稱的說明。
-   託管在 DNS 伺服器上的 DNS 記錄，該記錄將自訂功能變數名稱映射到自託管閘道的 IP 位址。 本主題不提供有關如何託管 DNS 記錄的說明。
-   您必須具備一個含有公開和私密金鑰的有效憑證 (.PFX)。 主題或主題替代名稱 （SAN） 必須與功能變數名稱匹配（這使 API 管理實例能夠通過 TLS 安全地公開 URL）。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>將自訂域證書添加到 API 管理服務

1. 從 **"安全"** 下選擇**證書**。
2. 選擇 **= 添加**。
3. 在**Id**欄位中輸入證書的資源名稱。
4. 選擇包含證書的檔 （。通過選擇 **"證書"** 欄位或它旁邊的資料夾圖示，PFX）。
5. 在 **"密碼"** 欄位中輸入證書的密碼。
6. 選擇 **"創建**"以將證書添加到 API 管理服務。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>使用 Azure 門戶為自託管閘道設置自訂功能變數名稱

1. 從 **"設置"** 下選擇**閘道**。
2. 選擇要為其配置功能變數名稱的自託管閘道。
3. 在 **"設置"** 下選擇**主機名稱**。
4. 選取 [+ 新增]****
5. 在 **"名稱"** 欄位中輸入主機名稱的資源名稱。
6. 在 **"主機名稱"** 欄位中輸入功能變數名稱。
7. 從證書下拉清單中選擇**證書**。
8. 如果通過此閘道公開的任何 API 使用用戶端憑證身份驗證，請選擇**協商用戶端憑證**核取方塊。
    > [!WARNING]
    > 此設置由為閘道配置的所有功能變數名稱共用。
9. 選擇 **"添加"** 可將自訂功能變數名稱分配給選定的自託管閘道。

## <a name="next-steps"></a>後續步驟

[升級和調整服務的規模](upgrade-and-scale.md)
