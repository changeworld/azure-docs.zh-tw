---
title: 如果 Azure 服務中斷影響到 Azure Key Vault 時該怎麼辦 - Azure Key Vault | Microsoft Docs
description: 了解如果 Azure 服務中斷影響到 Azure Key Vault 時該怎麼辦。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 35814f34550ac7bf4ad85a96d0838df62fe63be6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073177"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure 金鑰保存庫的可用性與備援

Azure 金鑰保存庫具備多層備援功能，以確保您的金鑰和密碼會保持可供應用程式使用，甚至在服務的個別元件失敗時也是如此。

金鑰保存庫的內容會在區域內複寫，以及複寫到至少距離 150 英哩但位於相同地理位置內的次要區域，以便讓金鑰和祕密保有高持久性。 請參閱 [Azure 配對的區域](../../best-practices-availability-paired-regions.md)文件，以取得特定區域配對的詳細資料。

如果金鑰保存庫服務內的個別元件失敗，則區域內的替代元件會接替來處理您的要求，以確保不會導致功能的效能降低。 您不需要採取任何動作來啟動此程序，其會以您無法察覺的方式自動發生。

在整個 Azure 區域都無法供使用的罕見情況下，您在該區域中所發出的「Azure 金鑰保存庫」要求會自動路由傳送 (容錯移轉  ) 到次要地區。 當主要區域再次可用時，要求就會路由傳送回 (容錯回復  ) 主要區域。 同樣地，您不需要採取任何動作，因為這會自動發生。

透過此高可用性設計，Azure Key Vault 不需要停機即可進行維護活動。

有一些要注意的警告事項：

* 發生區域容錯移轉時，可能需要幾分鐘讓服務進行容錯移轉。 在容錯移轉之前於這段時間內所提出的要求可能會失敗。
* 在容錯移轉期間，您的金鑰保存庫會處於唯讀模式。 在此模式中支援的要求是：
  * 列出憑證
  * 取得憑證
  * 列出密碼
  * 取得密碼
  * 列出金鑰
  * 取得金鑰 (的屬性)
  * Encrypt
  * 解密
  * 包裝
  * 解除包裝
  * Verify
  * 簽署
  * Backup
* 在容錯移轉期間，您將無法對金鑰保存庫屬性進行變更。 您將無法變更存取原則或防火牆組態和設定。
* 在容錯移轉進行容錯回復之後，所有要求類型 (包括讀取「和」  寫入要求) 都會可供使用。

