---
title: 無法使用金鑰保存庫證書添加自訂域
titleSuffix: Azure API Management
description: 瞭解如何解決無法使用金鑰保存庫證書在 Azure API 管理中添加自訂域的問題。
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430583"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>無法更新 API 管理服務主機名

本文介紹了為 Azure API 管理服務添加自訂域時可能會遇到的"更新 API 管理服務主機名失敗"錯誤。 本文提供疑難排解步驟，以説明您解決問題。

## <a name="symptoms"></a>徵狀

當您嘗試使用 Azure 金鑰保存庫的證書為 API 管理服務添加自訂域時，會收到以下錯誤訊息：

- 無法更新 API 管理服務主機名。 請求資源'https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0失敗與狀態碼：禁止請求Id： 。 異常消息：操作返回不正確狀態碼"禁止"。

## <a name="cause"></a>原因

API 管理服務無權訪問您嘗試用於自訂域的金鑰保存庫。

## <a name="solution"></a>解決方法

若要解決此問題，請依照下列步驟執行︰

1. 轉到[Azure 門戶](Https://portal.azure.com)，選擇 API 管理實例，然後選擇**託管標識**。 確保 **"使用 Azure 活動目錄註冊"** 選項設置為 **"是**"。 
    ![向 Azure 活動控制器註冊](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. 在 Azure 門戶中，打開**金鑰保存庫**服務，然後選擇要用於自訂域的金鑰保存庫。
1. 選擇**訪問策略**，並檢查是否有與 API 管理服務實例名稱匹配的服務主體。 如果有，請選擇服務主體，並確保它具有"**獲取**許可權"下列出的 **"獲取許可權**"。  
    ![添加服務主體的訪問策略](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. 如果 API 管理服務不在清單中，請選擇 **"添加訪問策略**"，然後創建以下訪問策略：
    - **從範本配置**： 無
    - **選擇主體**：搜索 API 管理服務的名稱，然後從清單中選擇它
    - **金鑰許可權**：無
    - **秘密許可權**： 獲取
    - **證書許可權**：無
1. 選擇 **"確定"** 以創建訪問策略。
1. 按一下 [儲存]**** 以儲存變更。

檢查問題是否已解決。 為此，請嘗試使用金鑰保存庫證書在 API 管理服務中創建自訂域。

## <a name="next-steps"></a>後續步驟
瞭解有關 API 管理服務的更多資訊：

- 查看更多有關 API 管理的 [視訊](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 。
* 如需其他保護後端服務的方式，請參閱[相互憑證驗證](api-management-howto-mutual-certificates.md)。

* [創建 API 管理服務實例](get-started-create-service-instance.md)。
* [管理第一個 API](import-and-publish.md)。