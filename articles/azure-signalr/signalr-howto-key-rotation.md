---
title: 如何輪替 Azure SignalR Service 的存取金鑰
description: 有關於客戶為何需要定期輪替存取金鑰，以及如何透過 Azure 入口網站 GUI 和 Azure CLI 執行此作業的概觀。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 133edc64ac2f858a397a4a184c24497dae8af333
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67565734"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>如何輪替 Azure SignalR Service 的存取金鑰

每個 Azure SignalR Service 執行個體都有一對存取金鑰，我們稱之為主要和次要金鑰。 對服務提出要求時，就會使用這些金鑰來驗證 SignalR 用戶端。 金鑰會與執行個體端點 url 相關聯。 保護您的金鑰安全，並定期進行輪替。 您會取得兩個存取金鑰，因此可藉由使用一個金鑰並重新產生另一個金鑰來維持連線。

## <a name="why-rotate-access-keys"></a>輪替存取金鑰？

基於安全考量和合規性需求，請定期輪替您的存取金鑰。

## <a name="regenerate-access-keys"></a>重新產生存取金鑰

1. 移至 [Azure 入口網站](https://portal.azure.com/)，並以您的認證登入。

1. 在您要重新產生的金鑰所屬的 Azure SignalR Service 執行個體中，尋找 [金鑰]**** 區段。

1. 選取導覽功能表上的 [金鑰]****。

1. 選取 [重新產生主要金鑰]**** 或 [重新產生次要金鑰]****。

   新的金鑰和對應的連接字串隨即建立並顯示。

   ![重新產生金鑰](media/signalr-howto-key-rotation/regenerate-keys.png)

您也可以使用 [Azure CLI](/cli/azure/signalr/key?view=azure-cli-latest#az-signalr-key-renew) 來重新產生金鑰。

## <a name="update-configurations-with-new-connection-strings"></a>使用新的連接字串更新組態

1. 複製新產生的連接字串。

1. 更新所有組態以使用新的連接字串。

1. 視需要重新啟動應用程式。

## <a name="forced-access-key-regeneration"></a>已強制重新產生存取金鑰

在某些情況下，Azure SignalR Service 可能會強制重新產生必要的存取金鑰。 此服務會透過電子郵件和入口網站通知來通知客戶。 如果您收到此通訊，或因存取金鑰而發生服務失敗，請依照本指南中的指示進行金鑰輪替。

## <a name="next-steps"></a>後續步驟

基於安全考量，請定期輪替您的存取金鑰。

在本指南中，您已了解如何重新產生存取金鑰。 繼續進行後續有關透過 OAuth 或 Azure Functions 驗證的教學課程。

> [!div class="nextstepaction"]
> [與 ASP.NET Core 身分識別整合](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [建置含驗證功能的無伺服器即時應用程式](./signalr-tutorial-authenticate-azure-functions.md)