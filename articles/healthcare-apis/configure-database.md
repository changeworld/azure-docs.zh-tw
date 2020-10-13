---
title: 在 Azure API for FHIR 中設定資料庫設定
description: 本文說明如何在 Azure API for FHIR 中設定資料庫設定
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: be3cf7d946e7502147942fa8954ade70dd47bedf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839923"
---
# <a name="configure-database-settings"></a>設定資料庫設定 

Azure API for FHIR 會使用資料庫來儲存其資料。 基礎資料庫的效能取決於在服務布建期間選取的要求單位數目 (RU) ，或在服務布建之後，于資料庫設定中選取的數目。

Azure API for FHIR 會從 Cosmos DB 中借用 ru 的概念 (在設定基礎資料庫的效能時，請參閱 [Azure Cosmos DB) 中的要求單位](https://docs.microsoft.com/azure/cosmos-db/request-units) 。 

您必須布建輸送量，以確保您的資料庫隨時都能使用足夠的系統資源。 您的應用程式需要多少 ru 取決於您所執行的作業。 作業可以從簡單的讀取和寫入範圍，到更複雜的查詢。 

> [!NOTE]
> 當不同的作業耗用不同的 RU 數目時，我們會傳迴響應標頭中每個 API 呼叫所耗用的實際 RU 數目。 如此一來，您就可以分析應用程式所取用的 ru 數目。

## <a name="update-throughput"></a>更新輸送量

若要變更 Azure 入口網站中的這項設定，請流覽至您的 Azure API for FHIR，然後開啟資料庫分頁。 接下來，根據您的效能需求，將布建的輸送量變更為所需的值。 您最多可以將值變更為 10000 RU/秒。 如果您需要較高的值，請聯絡 Azure 支援。

如果資料庫輸送量大於 10000 RU/秒，或如果儲存在資料庫中的資料超過 50 GB，您的用戶端應用程式必須能夠處理接續權杖。 在資料庫中，會為每個輸送量增加 10000 RU/秒，或如果儲存的資料量超過 50 GB，就會在資料庫中建立新的資料分割。 多個分割區會使用接續 token 來建立多重頁面回應。

> [!NOTE] 
> 較高的值表示較高的 Azure API for FHIR 輸送量和更高的服務成本。

![Config Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何更新 Azure API for FHIR 的 ru。 若要瞭解如何將客戶管理的金鑰設定為資料庫設定：

>[!div class="nextstepaction"]
>[設定客戶管理的金鑰](bring-your-own-key.md)

或者，您可以部署完全受控的 Azure API for FHIR：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
