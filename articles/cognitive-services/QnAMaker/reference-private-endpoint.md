---
title: 如何設定私人端點-QnA Maker
description: 瞭解 QnA Maker 受控提供的私人端點建立。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 5a7ddcf25993ab4ce87bdc47c5e0ae32fa24b07f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956492"
---
# <a name="private-endpoints"></a>私人端點

Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 現在，QnA Maker 可讓您為 Azure 搜尋服務建立私人端點的支援。 這項功能可在 QnA Maker 管理中使用。 

私人端點是由 [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)以個別服務的形式提供。 如需成本的詳細資訊，請參閱 [定價頁面。](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>必要條件
> [!div class="checklist"]
> * 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。
> * 在 Azure 入口網站中建立 QnA Maker [受控資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 。 請記住您在建立資源時選取的 Azure Active Directory 識別碼、訂用帳戶、QnA 資源名稱。

## <a name="steps-to-enable-private-endpoint"></a>啟用私人端點的步驟
1. 將 *參與者* 角色指派給 Azure 搜尋服務實例中 QnA Maker 受控服務。 這種作業需要訂用帳戶的 *擁有* 者存取權。 移至服務資源中的 [身分識別] 索引標籤，以取得身分識別。
![受控服務識別](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. 前往 [Azure 搜尋服務] IAM 索引標籤，將上述身分識別新增為 *參與者* 。 ![受控服務 IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. 按一下 [ *新增角色指派*]，新增身分識別，然後按一下 [ *儲存*]。
![受控角色指派](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. 現在，移至 Azure 搜尋服務實例中的 [ *網路* 功能] 索引標籤，並將端點連線資料從 *公用* 切換為 *私* 用。 這項作業是長時間執行的進程，最多可能需要30分鐘的時間才能完成。 
![受控 Azure 搜尋服務網路](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. 移至 QnA Maker 受控服務的 [ *網路* ] 索引標籤，並在 [ *允許存取來源*] 底下，選取 [ *選取的網路和私人端點* ] 選項，然後按一下 [ *儲存*] 
![受控 QnA maker newtorking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

這會建立 QnA maker 服務和 Azure 認知搜尋服務實例之間的私人端點連線。 您可以在 Azure 搜尋服務實例的 [ *網路* ] 索引標籤上確認私人端點連線。 完成整個作業之後，您就可以使用 QnA Maker 服務。 
![受控網路服務](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>支援詳細資料
 * 當您啟用 QnAMaker 服務的私用存取權之後，我們就不支援變更 Azure 搜尋服務。 如果您在啟用私用存取之後，透過 [設定] 索引標籤變更 Azure 搜尋服務，QnAMaker 服務將會變成無法使用。
 * 建立私人端點連線之後，如果您將 Azure 搜尋服務的網路功能切換為「公用」，您將無法使用 QnAMaker 服務。 Azure 搜尋服務的網路功能必須是「私人」，私人端點連線才能運作
