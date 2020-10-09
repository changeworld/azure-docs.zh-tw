---
title: Azure Data Share 詞彙
description: '瞭解用來描述 Azure Data Share (資料提供者、資料取用者、資料共用、共用訂用帳戶、快照集、邀請、收件者中所使用資源的常用詞彙。 ) '
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "73468549"
---
# <a name="azure-data-share-concepts"></a>Azure Data Share 概念 

Azure Data Share 引進一些與資料共用相關的新術語。 本文說明您可能會在整個服務中看到使用的一些常用詞彙。 

## <a name="data-provider"></a>資料提供者

資料提供者是與取用者共用資料的組織。 資料提供者通常可以是資料的擁有者或編者。 資料提供者想要共用各種類型的資料。 資料提供者可能想要共用的一些資料範例包括原始資料，例如銷售點或時間序列資料。 資料提供者也可能想要共用已預先處理的策劃資料，其已包含分析和見解。 

## <a name="data-consumer"></a>資料取用者 

資料取用者是從資料提供者接收資料的組織。 資料取用者可能想要使用自己的資料來聯結共用資料，以衍生見解。 在某些情況下，資料取用者可能會接收已經處理過的資料。 

## <a name="data-share"></a>Data Share

資料共用是一組作為單一實體共用的資料集。 資料集可以來自 Azure Data Share 支援的許多 Azure 資料來源。 目前，Azure Data Share 支援 Azure Blob 儲存體和 Azure Data Lake 存放區。 

## <a name="share-subscription"></a>共用訂用帳戶 

當資料取用者接受來自資料提供者的資料共用邀請時，就會建立共用訂用帳戶。 資料提供者可以流覽至其 Azure Data Share 帳戶中的 **已傳送共用** ，然後選取 [ **共用**訂用帳戶]，以查看作用中的共用訂閱。

資料取用者可以流覽至 [ **已接收的共用** ] 並查看其所接收共用的狀態，以檢查它們是否有作用中的共用訂用帳戶。 

## <a name="snapshot"></a>快照式

當資料取用者接受資料共用邀請時，就可以建立快照集。 當他們接受邀請時，他們就可以觸發與其共用之資料的完整快照集。 快照集是資料取用者產生快照集時的資料複本。 

快照集有兩種類型：完整和增量。 完整快照集包含資料共用中的所有資料。 增量快照集包含自從上次觸發快照集以來已更新/新增的所有資料。 

## <a name="snapshot-settings-in-azure-data-share"></a>Azure Data Share 中的快照集設定
 
資料提供者可以啟用資料共用的快照集設定。 這種設定可讓資料取用者在發生累加更新時收到累加式更新。 如果資料提供者想要讓資料取用者接收已共用的資料更新，則應該啟用此設定。 

如果資料提供者啟用此設定，則可以選取週期間隔。 週期間隔可以是每小時或每日。 

資料取用者可加入宣告此快照集排程來接收累加式更新，這些更新包含自從第一次產生新快照集以來已變更的任何資料。 

## <a name="invitation"></a>邀請

資料提供者可以邀請多個收件者加入其資料共用。 他們可以藉由將收件者新增至資料共用來這麼做。 建立資料共用之後，也可以新增邀請。 

如果未接受邀請，則資料提供者可以在傳送邀請之後予以刪除。 如果資料提供者刪除了邀請，但尚未接受，則資料取用者將無法接受它。 

一天最多可將邀請重新傳送五次。 

## <a name="recipient"></a>收件者

收件者是收到資料共用邀請的人。 通常，資料提供者會將收件者新增至他們所建立的資料共用。 邀請的收件者接受邀請後，就會成為資料取用者。  

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。
