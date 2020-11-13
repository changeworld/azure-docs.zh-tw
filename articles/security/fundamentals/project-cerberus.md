---
title: 固件完整性-Azure 安全性
description: 深入瞭解密碼編譯度量，以確保固件完整性。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557566"
---
# <a name="project-cerberus"></a>專案 Cerberus

Cerberus 是符合 NIST 800-193 規範的硬體，其具有無法複製的身分識別。 Cerberus 的設計目的是為了進一步提高 Azure 基礎結構的安全性狀態，方法是為固件完整性提供強式的信賴錨點。

## <a name="enabling-an-anchor-of-trust"></a>啟用信任錨點
每個 Cerberus 晶片都有唯一的密碼編譯身分識別，此身分識別會使用根為 Microsoft 憑證授權單位單位)  (CA 的已簽署憑證鏈來建立。 從 Cerberus 取得的測量可以用來驗證元件的完整性，例如：

- 主機
- 基礎板管理控制器 (BMC)
- 所有週邊設備，包括網路介面卡和 [系統晶片](https://en.wikipedia.org/wiki/System_on_a_chip) (SoC) 

此信任錨點有助於保護平臺固件：

- 在平臺上執行的遭盜用的固件二進位檔
- 惡意探索作業系統、應用程式或虛擬程式中錯誤的惡意程式碼與駭客
- 特定類型的供應鏈攻擊 (製造、元件、傳輸) 
- 具有系統管理許可權或硬體存取權的惡意內部人員

## <a name="cerberus-attestation"></a>Cerberus 證明
Cerberus 會使用平臺固件資訊清單 (PFM) 來驗證服務器元件的固件完整性。 PFM 會定義授權的固件版本清單，並提供 Azure [主機證明服務](measured-boot-host-attestation.md)的平臺度量。 主機證明服務會驗證量測，並決定只允許信任的主機加入 Azure 車隊和裝載客戶工作負載。

Cerberus 的功能結合了主機證明服務，增強了高度安全的 Azure 生產基礎結構。

> [!NOTE]
> 若要深入瞭解，請參閱 GitHub 上的 [專案 Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) 資訊。

## <a name="next-steps"></a>後續步驟
若要深入瞭解我們要如何驅動平臺完整性和安全性，請參閱：

- [固件安全性](firmware.md)
- [安全開機](secure-boot.md)
- [測量開機和主機證明](measured-boot-host-attestation.md)
- [待用加密](encryption-atrest.md)
- [虛擬程式安全性](hypervisor.md)