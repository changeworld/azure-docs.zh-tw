---
title: 提高在您開發的 daemon 應用程式中驗證和授權的復原能力
titleSuffix: Microsoft identity platform
description: 使用 Microsoft 身分識別平臺提高 daemon 應用程式中的驗證和授權復原的指引
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724836"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>提高在您開發的 daemon 應用程式中驗證和授權的復原能力

本文提供開發人員如何使用 Microsoft 身分識別平臺和 Azure Active Directory 來提高 daemon 應用程式復原能力的指引。 這包括背景進程、服務、伺服器到伺服器應用程式，以及不含使用者的應用程式。

![呼叫 Microsoft 身分識別的 daemon 應用程式](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>使用適用于 Azure 資源的受控識別

在 Microsoft Azure 上建立 daemon 應用程式的開發人員可以使用 [適用于 Azure 資源的受控](../managed-identities-azure-resources/overview.md)識別。 受控識別不需要開發人員管理秘密和認證。 這項功能可避免憑證到期、輪替錯誤或信任方面的錯誤，進而改善復原能力。 它也有幾個專門用來提高復原能力的內建功能。

受控識別會使用來自 Microsoft 身分識別的長期存取權杖和資訊，在現有權杖到期前的大型時段內主動取得新權杖。 嘗試取得新的權杖時，您的應用程式可以繼續執行。

受控識別也會使用區域端點來改善跨區域失敗的效能和恢復能力。 使用區域端點有助於將所有流量保留在地理區域內。 例如，如果您的 Azure 資源是在 WestUS2 中，所有流量（包括 Microsoft 身分識別產生的流量）都應該保持 WestUS2。 藉由合併服務的相依性來消除可能的失敗點。

## <a name="use-the-microsoft-authentication-library"></a>使用 Microsoft 驗證程式庫

不使用受控識別的背景程式應用程式開發人員可以使用 [Microsoft 驗證程式庫 (MSAL) ](../develop/msal-overview.md)，讓您能夠輕鬆地執行驗證和授權，並自動使用最佳作法來進行復原。 MSAL 可讓您更輕鬆地提供所需的用戶端認證。 例如，使用以憑證為基礎的認證時，您的應用程式不需要執行建立和簽署 JSON Web 權杖判斷提示。

### <a name="use-microsoftidentityweb-for-net-developers"></a>使用適用于 .NET 開發人員的 Web.config

在 ASP.NET Core 上建立 daemon 應用程式的開發人員可以使用 [web.config](../develop/microsoft-identity-web.md) 程式庫。 此程式庫建置於 MSAL 之上，讓 ASP.NET Core 應用程式更容易執行授權。 它包含數個分散式應用程式的 [分散式權杖](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) 快取策略，可在多個區域中執行。

## <a name="cache-and-store-tokens"></a>快取和儲存權杖

如果您不是使用 MSAL 來執行驗證和授權，您可以執行一些快取和儲存權杖的最佳作法。 MSAL 會自動實行並遵循這些最佳作法。

應用程式會從身分識別提供者取得權杖，以授權應用程式呼叫受保護的 Api。 當您的應用程式收到權杖時，包含權杖的回應也會包含 "expires \_ in" 屬性，告訴應用程式快取和重複使用權杖的時間長度。 應用程式必須使用 [到期時間 \_ ] 屬性來判斷權杖的存留期。 應用程式絕對不能嘗試解碼 API 存取權杖。 使用快取的權杖可防止您的應用程式與 Microsoft 身分識別之間不必要的流量。 您的使用者可以在權杖存留期的長度內保持登入應用程式。

## <a name="properly-handle-service-responses"></a>適當地處理服務回應

最後，雖然應用程式應該會處理所有錯誤回應，但是有一些回應可能會影響復原。 如果您的應用程式收到 HTTP 429 回應碼、太多要求，Microsoft 身分識別就會對您的要求進行節流。 如果您的應用程式繼續進行太多要求，它會繼續進行節流處理，以防止您的應用程式接收權杖。 您的應用程式應該不會嘗試再次取得權杖，直到經過時間（以秒為單位），然後才會在 [重試後] 回應欄位中完成。 接收429回應通常表示應用程式不會正確地快取和重複使用權杖。 開發人員應該複習權杖如何快取，並在應用程式中重複使用。

當應用程式收到 HTTP 5xx 回應碼時，應用程式不能進入快速重試迴圈。 如果有的話，應用程式應該採用與429回應相同的「重試」處理。 如果回應中未提供任何「重試後」標頭，我們建議您在回應後的第一次重試時，執行指數反向重試。

當要求超時的應用程式不應該立即重試。 在回應之後的第一次重試時，執行指數退重試。

## <a name="next-steps"></a>後續步驟

- [為登入使用者的應用程式建立恢復能力](resilience-client-app.md)
- [在您的身分識別與存取管理基礎結構中建立恢復功能](resilience-in-infrastructure.md)
- [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)