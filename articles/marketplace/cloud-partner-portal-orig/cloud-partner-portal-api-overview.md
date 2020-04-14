---
title: 雲合作夥伴門戶 API 參考 |Azure 應用商店
description: Marketplace API 作業的描述、使用先決條件及清單。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256411"
---
# <a name="cloud-partner-portal-api-reference"></a>Cloud Partner 入口網站 API 參考

> [!NOTE]
> 雲合作夥伴門戶 API 與合作夥伴中心集成,在您的產品/服務遷移到合作夥伴中心後將繼續工作。 集成引入了小更改。 查看本文件中列出的[對 CPP API 的更改](#changes-to-cpp-apis-after-the-migration-to-partner-center),以確保代碼在遷移到合作夥伴中心後繼續工作。

Cloud Partner 入口網站 REST API 允許對工作負載、供應項目及發行設定檔進行以程式設計方式達成的擷取和操作。 這些 API 會使用角色型存取控制 (RBAC) 來在處理期間強制執行正確的權限。

本參考能提供 Cloud Partner 入口網站 REST API 的技術詳細資料。 本文件中的承載範例僅供參考，並會隨新功能的加入而變更。

## <a name="prerequisites-and-considerations"></a>先決條件與考量

在使用 API 之前，您應該檢閱：

- [先決條件](./cloud-partner-portal-api-prerequisites.md)文章以了解如何將服務主體新增至您的帳戶，並取得 Azure Active Directory (Azure AD) 存取權杖以用於驗證。
- 可用於調用這些 API 的兩[個併發控制](./cloud-partner-portal-api-concurrency-control.md)策略。
- 其他 API [考量](./cloud-partner-portal-api-considerations.md)，例如版本控制和錯誤處理。

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>遷移到合作夥伴中心後對 CPP API 的變更

| **API** | **變更描述** | **影響** |
| ------- | ---------------------- | ---------- |
| POST 發佈、GoLive、取消 | 對於遷移的要約,回應標頭將具有不同的格式,但將繼續以相同的方式工作,表示檢索操作狀態的相對路徑。 | 送出任何相應的 POST 請求為產品/服務時,位置標頭將具有兩種格式之一,具體取決於產品/服務的狀態:<ul><li>非遷移優惠<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>遷移優惠<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| 取得動作 | 對於回應中以前支援"通知電子郵件"欄位的產品/服務類型,此欄位將被棄用,並且不再為遷移的優惠返回。 | 對於遷移的優惠,我們將不再向請求中指定的電子郵件清單發送通知。 相反,API 服務將與合作夥伴中心中的通知電子郵件進程一致發送電子郵件。 具體來說,通知將發送到合作夥伴中心中帳戶設置的賣家聯繫資訊部分中設置的電子郵寄地址,以通知您操作進度。<br><br>請查看合作夥伴中心[中「帳戶設置](https://partner.microsoft.com/dashboard/account/management)的賣家聯繫資訊」部分中設置的電子郵件位址,以確保為通知提供正確的電子郵件。  |

## <a name="common-tasks"></a>常見工作

本參考會詳細說明用來處理下列常見工作的 API。

### <a name="offers"></a>供應項目

- [擷取所有供應項目](./cloud-partner-portal-api-retrieve-offers.md)
- [擷取特定供應項目](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [擷取供應項目狀態](./cloud-partner-portal-api-retrieve-offer-status.md)
- [建立產品/服務](./cloud-partner-portal-api-creating-offer.md)
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
