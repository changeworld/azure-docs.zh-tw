---
title: IoT 解決方案加速器的常見問題集 - Azure | Microsoft Docs
description: 本文會回答 IoT 解決方案加速器的常見問題。 它包含 GitHub 存放庫的連結。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75647708"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>IoT 解決方案加速器的常見問題集

另請參閱連線的處理站[特有常見問題](iot-accelerators-faq-cf.md)和[遠端監視特有的常見問題](iot-accelerators-faq-rm-v2.md)。

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>哪裡可以找到解決方案加速器的原始程式碼？

原始程式碼儲存在下列 GitHub 儲存機制中︰

* [遠端監視解決方案加速器 (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) \(英文\)
* [遠端監視解決方案加速器 (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) \(英文\)
* [預測性維護解決方案加速器](https://github.com/Azure/azure-iot-predictive-maintenance) \(英文\)
* [連線的處理站解決方案加速器](https://github.com/Azure/azure-iot-connected-factory) \(英文\)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>我可以使用哪些 SDK 來為解決方案加速器開發裝置用戶端？

您可以在 [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub 存放庫中找到不同語言 (C、.NET、Java、Node.js、Python) 的 IoT 裝置 SDK 連結。

如果您使用 DevKit 裝置，您可以在 [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub 存放庫中找到資源和範例。

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>新的微服務架構是否適用於這三個解決方案加速器？

目前，只有遠端監視解決方案是使用微服務架構，因為它涵蓋最廣泛的情節。

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>以微服務作為基礎的開放原始碼新基礎架構能在更新中提供哪些優點？

在過去兩年中，雲端架構已大幅發展。 微服務問世以來都是絕佳的模式，可達成縮放及彈性，而不必犧牲開發速度。 此架構模式運用於多項在內部使用的 Microsoft 服務，都獲得絕佳的可靠性與延展性結果。 Microsoft 會在解決方案加速器中將這些知識付諸實現，，讓客戶可以從中獲益。

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>我是服務管理員，我想要變更我的訂用帳戶與特定 Azure AD 租用戶之間的目錄對應。 如何完成這項工作？

請參閱[將現有的訂用帳戶新增至您的 Azure AD 目錄](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>我想要在使用組織帳戶登入時變更服務管理員或共同管理員

請參閱支援文章[在使用組織帳戶登入時變更服務管理員和共同管理員](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>為什麼出現以下錯誤？ 「您的帳戶沒有適當的權限可建立方案。 請洽詢您的帳戶管理員或嘗試使用不同的帳戶。」

看看下圖中的指導方針︰

![權限流程圖](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> 在驗證您為 Azure AD 租用戶的全域管理員和訂用帳戶的共同管理員後，如果還是持續看到錯誤，請帳戶管理員依以下順序移除使用者並重新指派必要的權限。 首先，將使用者新增為全域管理員，然後將使用者新增為 Azure 訂用帳戶的共同管理員。 如果問題持續發生，請連絡[說明與支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>當我有 Azure 訂用帳戶時為何會出現此錯誤？ 「需要有 Azure 訂用帳戶，才能建立預先設定的方案。 只需要幾分鐘的時間，您就可以建立免費試用帳戶。」

如果您確定有 Azure 訂用帳戶，請驗證您的訂用帳戶的租用戶對應，並確認已在下拉式清單中選取正確的租用戶。 如果您已驗證租用戶是否正確，請遵循先前圖表並驗證您的訂用帳戶與此 Azure AD 租用戶的對應。

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>哪裡可以找到舊版遠端監視解決方案的相關資訊？

舊版遠端監視解決方案加速器稱為 IoT 套件遠端監視預先設定解決方案。 您可以在找到封存的檔 [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/) 。

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>是否可在與現有解決方案相同的地理區域中使用新的解決方案加速器？

是，可在相同的地理區域中使用新的遠端監視。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>刪除 Azure 入口網站中的資源群組和在 azureiotsolutions.com 中的解決方案加速器上按一下 [刪除] 有何差異？

* 如果您在[azureiotsolutions.com](https://www.azureiotsolutions.com/)中刪除解決方案加速器，就會刪除您在建立解決方案加速器時所部署的所有資源。 將額外資源新增到資源群組時，也會一併刪除這些資源。
* 如果您在 [Azure 入口網站](https://portal.azure.com)中刪除資源群組，則只會刪除該資源群組中的資源。 您還必須刪除與解決方案加速器相關聯的 Azure Active Directory 應用程式。

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>可以繼續利用我在 Azure IoT 解決方案加速器中的現有投資嗎？

是。 目前存在的任何解決方案都會繼續在您的 Azure 訂用帳戶中運作，而且原始碼在 GitHub 中仍保持可用。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個 IoT 中樞執行個體？

根據預設，您的佈建方式可以是[每個訂用帳戶 10 個 IoT 中樞](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits)。 您可以建立 [Azure 支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)來提高此限制。 結果就是，由於每個解決方案加速器都會佈建一個新的 IoT 中樞，因此在一個指定的訂用帳戶中，您最多只能佈建 10 個解決方案加速器。

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個 Azure Cosmos DB 執行個體？

50 個。 您可以建立 [Azure 支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)來提高此限制，但每一個訂用帳戶預設只能佈建 50 個 Cosmos DB 執行個體。

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個免費 Bing 地圖服務 API？

2 個。 您只可以在 Azure 訂用帳戶中針對 Enterprise 方案建立兩個內部交易層級 1 Bing 地圖。 根據預設，遠端監視解決方案會隨著內部交易層級 1 方案一起佈建。 因此，您只可以在一個訂用帳戶中最多佈建 2 個遠端監視方案。

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>如果我有 Microsoft Azure for DreamSpark，是否可以建立解決方案加速器？

> [!NOTE]
> Microsoft Azure for DreamSpark 現在稱為 Microsoft Imagine 學生版。

您目前無法使用 [Microsoft Azure for DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) 帳戶來建立解決方案加速器。 不過，您只需花幾分鐘即可建立 [Azure 免費試用帳戶](https://azure.microsoft.com/free/)，此帳戶可讓您建立解決方案加速器。

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>如何刪除 Azure AD 租用戶？

請參閱 Eric Golpe 的部落格文章：[刪除 Azure AD 租用戶的逐步解說](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)。

### <a name="next-steps"></a>後續步驟

您也可以探索 IoT 解決方案加速器的一些其他特性與功能：

* [探索遠端監視解決方案加速器的功能](quickstart-remote-monitoring-deploy.md)
* [預測性維護解決方案加速器概觀](iot-accelerators-predictive-overview.md)
* [部署連線的處理站解決方案加速器](quickstart-connected-factory-deploy.md) \(英文\)
* [從頭建立 IoT 安全性](/azure/iot-fundamentals/iot-security-ground-up)
