---
title: Azure 實驗室服務中的容量限制
description: 瞭解 Azure 實驗室服務中的容量限制（虛擬機器限制）。
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 92bdc714d70b3d73ca2cbc76b1f5dc5366582cbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444092"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Azure 實驗室服務中的容量限制
Azure 實驗室服務具有 Azure 訂用帳戶的預設容量限制，以符合 Azure 計算配額限制，並減輕詐騙的風險。 所有 Azure 訂用帳戶將會有初始容量限制，這可能會根據訂用帳戶類型、標準計算核心數，以及 Azure 實驗室服務內可用的 GPU 核心而有所不同。 它會限制您可以在實驗室中建立多少個虛擬機器，然後才需要要求增加限制。  

如果您接近或已達到訂用帳戶的虛擬機器核心限制，當您嘗試執行可建立其他虛擬機器的動作時，您會看到來自 Azure 實驗室服務的訊息。 例如： 

- 建立實驗室
- 發佈實驗室
- 調整實驗室容量，以將更多虛擬機器新增至現有的實驗室

如果您已經達到核心限制，這些動作也可能會停用。 

![核心限制-警告訊息](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>預設限制為零個核心的訂用帳戶
一些較常用於詐騙的常見訂用帳戶類型，其預設限制為0個標準核心和0個 GPU 核心。 如果您使用上述其中一種訂用帳戶類型，則建立您實驗室帳戶的系統管理員必須先要求增加限制，才能使用 Azure 實驗室服務。 

系統管理員可以遵循下列步驟來要求增加限制：  

1.  在您的訂用帳戶中，[建立實驗室帳戶](tutorial-setup-lab-account.md)。
2.  在實驗室帳戶的 [**總覽**] 頁面上，按一下頂端的 [**要求限制增加**] 按鈕。 
3.  遵循表單中的步驟來提交支援要求，以增加限制。

## <a name="request-a-limit-increase"></a>要求增加限制
如果您達到核心限制，您可以要求增加限制，以繼續使用 Azure 實驗室服務。 要求程式是一個檢查點，可確保您的訂用帳戶不會牽涉到任何詐騙或無意間的大規模部署。

Azure 實驗室服務入口網站中虛擬機器核心限制的相關訊息包含要求增加限制的連結。 此連結會開啟新的瀏覽器索引標籤，您可以在其中建立新的支援要求。 [問題類型]、[訂用帳戶] 和 [配額類型] 資訊會自動填入，如下圖所示： 

![新增支援要求](./media/capacity-limits/new-support-request.png)


然後，系統會提示您提供有關增加限制的詳細資訊。 在 [**描述**] 欄位中，提供下列詳細資料：

- 您嘗試執行的動作（例如，建立實驗室來教授電腦科學類別、執行駭客松等等）。
- 您在此實驗室中使用的虛擬機器大小
- 您需要的虛擬機器數目

提交支援要求之後，我們將會檢查要求。 如有必要，我們會聯絡您以取得其他詳細資料。 

## <a name="next-steps"></a>後續步驟
請參閱下列文章：
- [常見問題集](classroom-labs-faq.md)。