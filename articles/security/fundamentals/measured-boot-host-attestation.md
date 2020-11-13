---
title: 以固件測量的開機和主機證明-Azure 安全性
description: Azure 固件測量開機和主機證明的技術總覽。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 73ae811c17a578cafc557b0cda9e98b101dd5c03
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557559"
---
# <a name="measured-boot-and-host-attestation"></a>測量開機和主機證明
本文說明 Microsoft 如何透過測量的開機和主機證明，來確保主機的完整性和安全性。

## <a name="measured-boot"></a>測量的開機

[信賴平臺模組](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) 是由受信任的協力廠商所提供的一種防密碼編譯安全審核元件。 開機設定記錄檔中所記錄的雜湊連結量值， (PCR 在主機最後進行啟動載入序列時) 。 下圖顯示此記錄程式。 以累加方式將先前的雜湊量值新增至下一個測量的雜湊，並在 union 完成雜湊鏈上執行雜湊演算法。

![顯示主機證明服務雜湊鏈的圖表。](./media/measured-boot-host-attestation/hash-chaining.png)

當主機使用開機設定記錄檔 (TCGLog) 來提供其設定狀態證明時，即會完成證明。 偽造開機記錄檔並不容易，因為 TPM 不會公開其在讀取和延伸作業以外的 PCR 值。 此外，主機證明服務所提供的認證會密封為特定的 PCR 值。 使用雜湊連結，可讓您無法在頻外偽造或解除密封認證。

## <a name="host-attestation-service"></a>主機證明服務

主機證明服務是一種預防措施，可檢查主機電腦是否值得信任，然後才允許它們與客戶資料或工作負載互動。 主機證明服務會藉由驗證合規性聲明， (可驗證的主機合規性驗證，) 每個主機針對 (安全狀態) 定義的證明原則來傳送。 此系統的完整性是由 TPM 提供的 [根信任](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) 所保證。

主機證明服務存在於特製化鎖定環境內的每個 Azure 叢集中。 鎖定的環境包含參與主機電腦啟動載入通訊協定的其他閘道管理員服務。  (PKI) 的公開金鑰基礎結構，可做為驗證證明要求來源的媒介，以及驗證成功的主機證明) 時 (的身分識別簽發者。 發行給證明主機的證明認證會密封為其身分識別。 只有要求的主機可以解除密封認證，並利用它們來取得增量許可權。 這可防止攔截式攻擊和偽造攻擊。

如果 Azure 主機從具有安全性設定錯誤的處理站抵達，或在資料中心內遭到篡改，其 TCGLog 會在下一次證明時包含主機證明服務所旗標的入侵指標，這會導致證明失敗。 證明失敗會導致 Azure 車隊無法信任違規的主機。 這種預防措施可有效地封鎖與主機之間的所有通訊，並觸發事件工作流程。 進行調查和詳細的事後剖析後分析，以判斷根本原因和任何可能的洩漏跡象。 只有在分析完成後，才會補救主機，並有機會加入 Azure 車隊並取得客戶工作負載。

以下是主機證明服務的高層級架構：

![顯示主機證明服務架構的圖表。](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>證明度量

以下是現今所捕獲的許多度量範例。

### <a name="secure-boot-and-secure-boot-keys"></a>安全開機和安全開機金鑰
藉由驗證簽章資料庫和撤銷的簽章資料庫摘要正確，主機證明服務可確保用戶端代理程式會將正確的軟體視為受信任。 藉由驗證公開金鑰註冊金鑰資料庫與公用平臺金鑰的簽章，主機證明服務會確認只有受信任的當事人有權修改哪些軟體被視為受信任的定義。 最後，藉由確保安全開機處於作用中狀態，主機證明服務會驗證是否已強制執行這些定義。

### <a name="debug-controls"></a>Debug 控制項
偵錯工具是適用于開發人員的強大工具。 不過，如果提供給非信任的合作物件，則自由對記憶體和其他 debug 命令的存取權可能會降低資料保護和系統完整性。 主機證明服務可確保在生產電腦上開機時，會停用任何一種偵測。

### <a name="code-integrity"></a>程式碼完整性
UEFI [安全開機](secure-boot.md) 可確保只有受信任的低層級軟體可以在開機順序期間執行。 不過，相同的檢查也必須套用到開機後環境，再到具有核心模式存取的驅動程式和其他可執行檔。 為了達成此目的，您可以使用程式碼完整性 (CI) 原則，藉由指定有效和不正確簽章，來定義哪些驅動程式、二進位檔和其他可執行檔被視為受信任。 這些原則會強制執行。 違規原則會產生警示給安全性事件回應小組進行調查。

## <a name="next-steps"></a>後續步驟
若要深入瞭解我們要如何驅動平臺完整性和安全性，請參閱：

- [固件安全性](firmware.md)
- [安全開機](secure-boot.md)
- [專案 Cerberus](project-cerberus.md)
- [待用加密](encryption-atrest.md)
- [虛擬程式安全性](hypervisor.md)