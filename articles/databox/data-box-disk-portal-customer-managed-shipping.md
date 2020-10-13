---
title: Microsoft Azure 資料箱磁碟自我管理寄送 | 資料中的 Microsoft Docs
description: 說明 Azure 資料箱磁碟裝置的自我管理寄送工作流程
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: alkohli
ms.openlocfilehash: c4b3479e5728c32e66bc40f950bc948bf61dce42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575158"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>在 Azure 入口網站中使用 Azure 資料箱磁碟的自我管理寄送

本文說明 Azure 資料箱磁碟訂購、取貨和送貨的自我管理寄送工作。 您可以使用 Azure 入口網站來管理資料箱磁碟。

## <a name="prerequisites"></a>Prerequisites

當您[訂購 Azure 資料箱磁碟](data-box-disk-deploy-ordered.md)時，系統會提供自我管理寄送選項。 自我管理寄送僅適用於以下地區：

* 美國政府
* 西歐
* 日本
* 新加坡
* 南韓
* 南非
* 印度 (預覽) 

## <a name="use-self-managed-shipping"></a>使用自我管理寄送

當您下單訂購資料箱磁碟時，可以選擇自我管理寄送選項。

1. 在您的 Azure 資料箱磁碟訂單中，於 [連絡人詳細資料] 下，選取 [+ 新增交貨地址]。

   ![訂單向導的螢幕擷取畫面，其中顯示已叫用 [新增送貨位址] 選項的 [連絡人詳細資料] 步驟。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 選擇出貨類型時，選取 [自我管理寄送] 選項。 只有您的所在區域支援時 (如必要條件中所述)，此選項才可供使用。

3. 在您提供交貨地址之後，必須驗證該地址並完成您的訂單。

   ![[新增交貨位址] 對話方塊的螢幕擷取畫面，其中已呼叫 [交貨方式] 選項和 [新增交貨位址] 選項。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 一旦裝置已備妥且您收到電子郵件通知，您就可以安排取貨。 在您的 Azure 資料箱磁碟訂單中，移至 [概觀]，然後選取 [排程取貨]。

   ![訂購要領取的資料箱裝置](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. 依照 [安排 Azure 的取貨] 中的指示進行。 您必須先傳送電子郵件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)，以安排到您所在區域的資料中心取貨時間，才能得到授權碼。

   ![排程取貨](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. 排程裝置取貨時間後，您便能夠在 [安排 Azure 的取貨] 中檢視您的授權碼。

   ![[排程收取 Azure] 對話方塊的螢幕擷取畫面，其中包含已叫用之取貨文字方塊的授權碼。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   請記下此 [授權碼]。

   根據安全性需求，在排程挑選時，必須出示將抵達取得的人員名稱，以供您參考。

   您也必須提供將前往資料中心取貨人員的詳細資料。 您或連絡人必須攜帶政府核准使用附相片的身分證，以供資料中心驗證。

   此外，取貨人員也必須有 [授權碼]。 授權碼對於取貨或卸載是唯一的，並會在資料中心進行驗證。

7. 從資料中心領取裝置後，您的訂單就會自動變成 [已取貨] 狀態。

   ![已取貨](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. 領取裝置後，您可以將資料複製到您所在地點的資料箱磁碟。 資料複製完成之後，您就可以準備寄送資料箱磁碟。

   當您完成資料複製之後，必須聯絡作業來安排送貨預約。 您將需要分享即將前往資料中心的磁碟送貨人員詳細資料。 在送貨時，資料中心也必須驗證授權碼。 [安排送貨] 下的 Azure 入口網站中，將會提供送貨的授權碼。

   > [!NOTE]
   > 請勿透過電子郵件分享授權碼。 在送貨期間，此授權碼只會在資料中心進行驗證。

9. 如果您已收到送貨預約，則現在訂單在 Azure 入口網站中應該是 [已就緒，可在 Azure 資料中心收貨] 狀態。

   ![[新增交貨位址] 對話方塊的螢幕擷取畫面，其中包含 [使用選項出貨] 和 [新增寄送位址] 選項稱為 out。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. 在驗證您的識別碼和授權碼，並將裝置送貨至資料中心後，訂單狀態應為 [已收到]。

    ![完成接收](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. 在收到裝置後，將會繼續進行資料複製。 當複製完成時，訂單即完成。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站部署 Azure 資料箱磁碟](data-box-disk-quickstart-portal.md)
