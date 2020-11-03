---
title: 針對 Azure 保留建議進行疑難排解
description: 本文可協助您了解在 Azure 入口網站中顯示的 Azure 保留建立並進行疑難排解。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492227"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>針對 Azure 保留建議進行疑難排解

本文可協助您了解在 Azure 入口網站中顯示的 Azure 保留建立並進行疑難排解。 您可能不會看到建議，也可能看到不符合您預期的建議。 例如，您可能已有特定 VM 組態的保留執行個體。 您會希望看到類似 VM 組態的建議。

## <a name="symptoms"></a>徵兆

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至 [保留]。
2. 選取 [+新增]，然後選取某個產品。
3. 在 **建議** 索引標籤上，您可能不會看到任何建議，或看到不符合預期的建議。

## <a name="cause"></a>原因

建議的產品清單是由您的範圍 (共用或單一) 所擁有的使用量 (相較於產品的保留成本) 所產生。 如果建議引擎偵測到費用有所節省，則會建議購買產品。 不過，當引擎找不到會節省任何費用時，就不會建議使用產品。

執行具有指定組態的資源時，不保證會藉由購買該組態的保留來節省成本。 例如，您可能會有偶爾出現的使用量。 若是如此，保留的總成本可能不會在保留期間的存留期內節省您的成本。

也請務必了解範圍選取會如何影響您獲得的建議。 當範圍設定為 **共用** 時，清單中的建議會顯示保留執行個體，其中 Azure 會尋找與帳單訂用帳戶相關聯之整個註冊所節省的成本。 當範圍設定為 **單一** 時，清單中的建議僅適用於在訂用帳戶中執行的資源。 系統可能會建議在某個範圍使用某些 VM 大小，而不會建議另一個範圍使用。 例如，您可能彙總了在註冊範圍內的 **Standard_B1ls** 使用量，這足以證明在註冊範圍購買保留的成本。 不過，註冊中的單一訂用帳戶可能沒有足夠的使用量來證明在範圍中購買保留的成本。 變更 **共用** 與 **單一** 之間的範圍可能會產生不同的建議。

根據所識別的成本節約，Azure 可能會建議購買特定期限的保留，而不建議購買其他期限的保留。 具體而言，三年期的折扣比一年期更多。 相較於一年期，Azure 比較有可能發現三年期能節省更多成本。

如果要了解 Azure 建議特定資源大小和數量的原因，請選取 **&lt;數量&gt;查看詳細資料** ，這裡提供深入的視覺效果資料，會顯示一段時間內可能的省下的成本。

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="顯示保留建議的範例，參閱詳細資料連結" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>解決方法

您可以為所需的期限購買任何保留數量。 購買與建議不同的數量和期限的保留，可能會讓您無法省下最多成本，也無法有效利用保留。

## <a name="next-steps"></a>下一步

- 如需保留建議的詳細資訊，請參閱[保留購買建議](determine-reservation-purchase.md)。
