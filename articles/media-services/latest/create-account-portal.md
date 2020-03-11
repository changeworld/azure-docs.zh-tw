---
title: 使用 Azure 入口網站建立 Azure 媒體服務帳戶
description: 本教學課程逐步引導您完成使用 Azure 入口網站建立 Azure 媒體服務帳戶的步驟。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: c90278eccf889595378d3b6b07de2468910c660c
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080871"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>使用 Azure 入口網站建立媒體服務帳戶

Azure 入口網站提供快速建立 Azure 媒體服務帳戶的方式。 您可以使用自己的帳戶，來存取讓您在 Azure 中儲存、加密、編碼、管理和串流播放媒體內容的媒體服務。

目前，您可以使用[Azure 入口網站](https://portal.azure.com/)來執行下列動作：

* 管理媒體服務 v3[實況活動](live-events-outputs-concept.md)， 
* view （不管理） v3[資產](assets-concept.md)， 
* [取得存取 api 的相關資訊](access-api-portal.md)。 

針對所有其他管理工作（例如，[轉換和作業](transforms-jobs-concept.md)和[內容保護](content-protection-overview.md)），請使用[REST API](https://aka.ms/ams-v3-rest-ref)、 [CLI](https://aka.ms/ams-v3-cli-ref)或其中一個支援的[sdk](media-services-apis-overview.md#sdks)。

本文說明如何使用 Azure 入口網站建立媒體服務帳戶。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要 Azure 帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。 

## <a name="create-a-media-services-account"></a>建立媒體服務帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 按一下 [ **+ 建立資源**] > **媒體** > **媒體服務**。
1. 在 [**建立媒體服務帳戶**] 區段中，輸入必要的值。
    
    | 名稱 | 描述 |
    | ---|---|
    |**帳戶名稱**|輸入新媒體服務帳戶的名稱。 媒體服務帳戶名稱為全部小寫且不含空格的字母或數字，且長度是 3 到 24 個字元。|
    |**訂用帳戶**|如果您有多個訂用帳戶，請從您有權存取的 Azure 訂用帳戶清單中選取一個。|
    |**資源群組**|選取新的或現有的資源。 資源群組是共用生命週期、權限及原則的資源集合。 [在此](../../azure-resource-manager/management/overview.md#resource-groups)深入了解。|
    |**位置**|選取將用來儲存媒體服務帳戶之媒體和元資料記錄的地理區域。 此區域將用於處理和串流媒體。 只有可用的媒體服務區域才會出現在下拉式清單方塊中。 |
    |**儲存體帳戶**|選取儲存體帳戶，以從您的媒體服務帳戶提供媒體內容的 blob 儲存體。 您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。 新的儲存體帳戶會建立於相同的區域中。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。<br/><br/>您只能有一個**主要**儲存體帳戶，而與您的媒體服務帳戶相關聯的**次要**儲存體帳戶可以有任意數量。 您可以使用 Azure 入口網站來新增次要儲存體帳戶。 如需詳細資訊，請參閱[Azure 媒體服務帳戶的 Azure 儲存體帳戶](storage-account-concept.md)。<br/><br/>媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於相同位置的儲存體帳戶，以避免產生額外的延遲和資料輸出費用。|
    
1. 選取 **[釘選到儀表板]** 以查看帳戶部署的進度。
1. 按一下表單底部的 [建立] 。
   
    成功建立帳戶後，隨即載入概觀頁面。 在串流端點資料表中，此帳戶將具有 [**已停止**] 狀態的預設串流端點。 

    建立媒體服務帳戶時，**預設**串流端點會新增至 [已停止] 狀態的帳戶。 若要開始串流處理您的內容，並利用[動態封裝](dynamic-packaging-overview.md)和[動態加密](content-protection-overview.md)功能，您想要串流內容的串流端點必須**處於 [執行**中] 狀態。 

## <a name="next-steps"></a>後續步驟

如果您打算以程式設計方式存取媒體服務 API，請參閱[使用 Azure AD 驗證來存取 AZURE 媒體服務 api](access-api-portal.md)。

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

