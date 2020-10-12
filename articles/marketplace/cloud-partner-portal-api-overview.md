---
title: Cloud Partner 入口網站 API 參考-Microsoft 商業 marketplace
description: Marketplace API 作業的描述、使用先決條件及清單。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: bfb6e9cb510f5fe887f108dfdea5932406aafe0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292927"
---
# <a name="cloud-partner-portal-api-reference"></a>Cloud Partner 入口網站 API 參考

> [!NOTE]
> Cloud Partner 入口網站 Api 已與整合，並且將繼續在合作夥伴中心中運作。 轉換導入了少量的變更。 請參閱本檔中所列的 [CPP Api 變更](#changes-to-cpp-apis-after-the-migration-to-partner-center) ，以確保您的程式碼在轉換至合作夥伴中心之後仍繼續運作。 CPP Api 應該僅用於已整合的現有產品，然後轉換為合作夥伴中心;新產品應使用合作夥伴中心提交 Api。

Cloud Partner 入口網站 REST API 允許對工作負載、供應項目及發行設定檔進行以程式設計方式達成的擷取和操作。 這些 API 會使用角色型存取控制 (RBAC) 來在處理期間強制執行正確的權限。

本參考能提供 Cloud Partner 入口網站 REST API 的技術詳細資料。 本文件中的承載範例僅供參考，並會隨新功能的加入而變更。

## <a name="prerequisites-and-considerations"></a>先決條件與考量

在使用 API 之前，您應該檢閱：

- [先決條件](./cloud-partner-portal-api-prerequisites.md)文章以了解如何將服務主體新增至您的帳戶，並取得 Azure Active Directory (Azure AD) 存取權杖以用於驗證。
- 可用來呼叫這些 Api 的兩個 [並行控制](./cloud-partner-portal-api-concurrency-control.md) 策略。
- 其他 API [考量](./cloud-partner-portal-api-considerations.md)，例如版本控制和錯誤處理。

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>在遷移至合作夥伴中心之後變更 CPP Api

| **API** | **變更描述** | **影響** |
| ------- | ---------------------- | ---------- |
| POST Publish、GoLive、Cancel | 針對已移轉的供應項目，回應標頭將會有不同的格式，但會繼續以相同的方式運作，表示用來擷取作業狀態的相對路徑。 | 針對供應項目傳送任何對應的 POST 要求時，視該供應項目的移轉狀態而定，位置標頭將會具有兩種格式的其中一種：<ul><li>未移轉的供應項目<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>已移轉的供應項目<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 作業 | 針對先前在回應中支援「通知-電子郵件」欄位的供應專案類型，此欄位將會被取代，而且不會再傳回給已遷移的供應專案。 | 針對已移轉的供應項目，我們將不會繼續針對要求中所指定的電子郵件清單傳送通知。 相反地，API 服務將會對應到合作夥伴中心的通知電子郵件程序以傳送電子郵件。 具體而言，系統會將通知傳送到您在合作夥伴中心 [帳戶設定] 的 [賣方連絡資訊] 區段中所設定的電子郵件地址，以向您通知作業進度。<br><br>請檢查合作夥伴中心中 [帳戶設定](https://partner.microsoft.com/dashboard/account/management) 的 [賣方連絡人資訊] 區段中設定的電子郵件地址，以確保提供正確的電子郵件通知。  |

## <a name="common-tasks"></a>常見工作

本參考會詳細說明用來處理下列常見工作的 API。

### <a name="offers"></a>供應項目

- [擷取所有供應項目](./cloud-partner-portal-api-retrieve-offers.md)
- [擷取特定供應項目](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [擷取供應項目狀態](./cloud-partner-portal-api-retrieve-offer-status.md)
- [建立供應項目](./cloud-partner-portal-api-creating-offer.md)
- [發佈供應項目](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>作業

- [擷取作業](./cloud-partner-portal-api-retrieve-operations.md)
- [取消作業](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>發佈應用程式

- [上線](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>其他工作

- [設定虛擬機器供應項目定價](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>疑難排解

- [針對驗證錯誤進行疑難排解](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
