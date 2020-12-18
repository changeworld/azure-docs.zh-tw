---
title: 在 Azure Data Share 中新增收件者
description: 瞭解如何在 Azure Data Share 中，將收件者新增至現有的資料共用。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680636"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>如何將收件者新增至您的共用

當您建立新的共用或現有的共用時，可以加入收件者。 從 Azure Data Share UI，您可以使用使用者的 Azure 登入電子郵件來新增收件者。  您可以從 API 使用使用者/服務主體和租使用者識別碼的組合。 當指定租使用者識別碼時，邀請只能接受至此租使用者。 此外，您也可以從 API 建立邀請，而不需要傳送電子郵件給收件者。 

## <a name="add-recipient-to-an-existing-share"></a>將收件者新增至現有的共用

在 Azure Data Share 中，流覽至您已傳送的共用，然後選取 [ **邀請** ] 索引標籤。以下列出此資料共用的所有邀請收件者。 若要加入新的，請按一下 [ **加入收件** 者]。

![螢幕擷取畫面顯示已選取 [新增收件者]。](./media/how-to/how-to-add-recipients/add-recipient.png)

頁面右側會彈出一個面板。 按一下 [ **加入收件** 者]，然後在空白行填寫新收件者的電子郵件。 請務必使用收件者的 Azure 登入電子郵件 (使用其電子郵件別名將無法) 。 

![螢幕擷取畫面顯示 [加入收件者] 窗格，您可以在其中新增和傳送邀請。](./media/how-to/how-to-add-recipients/add-recipient-side.png)

按一下 [ **新增並傳送邀請**]。 新收件者 () 將會傳送邀請電子郵件給這個共用。

## <a name="next-steps"></a>後續步驟
深入瞭解如何 [刪除共用的邀請](how-to-delete-invitation.md)。
