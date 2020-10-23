---
title: Azure 數位 Twins 的客戶資料要求功能
titleSuffix: Azure Digital Twins
description: 本文說明在 Azure 數位 Twins 中匯出和刪除個人資料的流程。
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461754"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Azure 數位 Twins 客戶資料要求功能

Azure 數位 Twins 是開發人員平臺，用於建立商務環境的安全數位標記法。 表示是由使用者所選取之資料來源中的即時狀態資料所驅動。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Azure 數位 Twins 中稱為 *數位 twins* 的數位標記法代表真實世界環境中的實體，而且會與識別碼相關聯。 Microsoft 不會保留任何資訊，也無法存取可讓識別碼與使用者相互關聯的資料。 

Azure 數位 Twins 中的許多數位 twins 不會直接代表個人實體，代表的典型物件可能是辦公室會議室或工廠樓層。 不過，使用者可能會將某些實體視為個人標識，並自行決定是否可維護自己的資產或將數位 twins 與個人結合的清查追蹤方法。 Azure 數位 Twins 會管理和儲存與數位 Twins 相關聯的所有資料，如同個人資料一樣。

若要查看、匯出及刪除資料主體要求中可能參考的個人資料，Azure 數位 Twins 系統管理員可以使用使用者和角色的 [**Azure 入口網站**](https://portal.azure.com/) ，或適用于數位 Twins 的 [**AZURE 數位 Twins REST api**](/rest/api/azure-digitaltwins/) 。 Azure 入口網站和 REST Api 提供不同的方法，讓使用者可以服務這類資料主體要求。

## <a name="identifying-customer-data"></a>識別客戶資料

Azure 數位 Twins 會將 *個人資料* 視為與其系統管理員和使用者相關聯的資料。 

Azure 數位 Twins 會儲存具有環境存取權之使用者的 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *物件識別碼* 。 Azure 入口網站中的 Azure 數位 Twins 會顯示使用者電子郵件地址，但這些電子郵件地址不會儲存在 Azure 數位 Twins 內。 系統會使用 Azure Active Directory 物件識別碼，在 Azure Active Directory 中動態查閱它們。

## <a name="deleting-customer-data"></a>刪除客戶資料

Azure 數位 Twins 系統管理員可以使用 Azure 入口網站來刪除與使用者相關的資料。 您也可以使用 Azure 數位 Twins REST Api，在個別的數位 twins 上執行刪除作業。 如需可用 Api 的詳細資訊，請參閱 [Azure 數位 TWINS REST api 檔](/rest/api/azure-digitaltwins/)。

## <a name="exporting-customer-data"></a>匯出客戶資料

Azure 數位 Twins 會儲存與數位 Twins 相關的資料。 使用者可以透過 REST Api 取得和查看此資料，並使用複製和貼上來匯出此資料。 

您可以從 Azure 入口網站選取、複製和貼上客戶資料（包括使用者角色和角色指派）。 

## <a name="links-to-additional-documentation"></a>其他文件的連結

如需 Azure 數位 Twins 服務 Api 的完整清單，請參閱 [Azure 數位 TWINS REST api 檔](/rest/api/azure-digitaltwins/)。