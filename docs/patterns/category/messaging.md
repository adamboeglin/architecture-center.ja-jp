---
title: メッセージングのパターン
titleSuffix: Cloud Design Patterns
description: クラウド アプリケーションの分散特性には、スケーラビリティを最大化するために、コンポーネントとサービスが (できれば疎結合的に) 接続されているメッセージング インフラストラクチャが必要です。 非同期メッセージングは広く使用されており、多くの利点がもたらされますが、メッセージの順序、有害メッセージの管理、べき等など、課題も多数あります。
keywords: 設計パターン
author: dragon119
ms.date: 03/01/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: e2079d8ed33dd205c155348d455f6380fff660a3
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249337"
---
# <a name="messaging-patterns"></a>メッセージングのパターン

クラウド アプリケーションの分散特性には、スケーラビリティを最大化するために、コンポーネントとサービスが (できれば疎結合的に) 接続されているメッセージング インフラストラクチャが必要です。 非同期メッセージングは広く使用されており、多くの利点がもたらされますが、メッセージの順序、有害メッセージの管理、べき等など、課題も多数あります。

| Pattern | まとめ |
| ------- | ------- |
| [要求チェック](../claim-check.md) | 大きいメッセージを要求チェックとペイロードに分割して、メッセージ バスに過度な負荷がかかることを防ぎます。 |
| [競合コンシューマー](../competing-consumers.md) | 複数の同時実行コンシューマーが、同じメッセージング チャネルで受信したメッセージを処理できるようにします。 |
| [パイプとフィルター](../pipes-and-filters.md) | 複雑な処理を実行するタスクを、再利用できる一連の独立した要素に分解します。 |
| [優先順位キュー](../priority-queue.md) | サービスに送信される要求に優先順位を設定し、優先順位の高い要求から順番に受信および処理されるようにします。 |
| [パブリッシャーとサブスクライバー](../publisher-subscriber.md) | 送信側と受信側を結合せずに、アプリケーションから複数の対象コンシューマーに対して非同期的にイベントを通知できるようにします。 |
| [キュー ベースの負荷平準化](../queue-based-load-leveling.md) | タスクとそのタスクが呼び出すサービスとの間でバッファーとして機能するキューを使用して、断続的な大きい負荷を平準化します。 |
| [Scheduler エージェント スーパーバイザー](../scheduler-agent-supervisor.md) | 分散された一連のサービスやその他のリモート リソースにわたる一連のアクションを調整します。 |
