---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "85114509"
---

## <a name="using-this-example-knowledge-base"></a>使用此範例知識庫

本快速入門中的知識庫起始於 2 個對話式 QnA 配對 (這是為了簡化範例，且在更新方法中有高度可預測的識別碼可供使用)，且會將問題的後續提示與新的配對產生關聯。 這是本快速入門中的規劃作業，會依特定順序實作。

如果您日後要依據相依於現有 QnA 配對的後續提示來開發知識庫，您可以選擇：
* 針對較大的知識庫，在支援自動化的文字編輯器或 TSV 工具中管理知識庫，然後立即將知識庫完全取代為更新。
* 對於較小的知識庫，則完全在 QnA Maker 入口網站中管理後續提示。

本快速入門中使用的 QnA 配對詳細資料：
* QnA 配對的類型 - 在更新之後，此知識庫中有 2 種類型的 QnA 配對：Chitchat 和網域專屬資訊。 這在繫結至交談應用程式 (例如聊天機器人) 的知識庫上很常見。
* 雖然知識庫答案可以透過中繼資料進行篩選，或使用後續追蹤提示，但不在本快速入門的說明範圍中。 您可以在[這裡](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)找到不限定語言的 generateAnswer 範例。
* 解答文字是 Markdown 格式，而且可以包含[各式各樣的 Markdown](../reference-markdown-format.md)，例如影像 (公開可用的網際網路影像)、連結 (公開可用的 URL) 和項目符號 (本快速入門不會用到此類型)。
