---
title: ゲートウェイ集約パターン
titleSuffix: Cloud Design Patterns
description: ゲートウェイを使用して、複数の個々の要求を 1 つの要求に集約します。
keywords: 設計パターン
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: c28e93efeae7dffe2f17288f3310f299ec545e1a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242303"
---
# <a name="gateway-aggregation-pattern"></a>ゲートウェイ集約パターン

ゲートウェイを使用して、複数の個々の要求を 1 つの要求に集約します。 このパターンは、クライアントが操作を実行するために、さまざまなバックエンド システムに複数の呼び出しを行う必要がある場合に便利です。

## <a name="context-and-problem"></a>コンテキストと問題

1 つのタスクを実行するには、クライアントはさまざまなバックエンド サービスへの呼び出しを行う必要があります。 タスクを実行するのに多くのサービスに依存するアプリケーションでは、要求ごとにリソースを展開する必要があります。 アプリケーションに新しい機能やサービスが追加されると、追加の要求が必要になり、リソース要件およびネットワーク呼び出しが増加します。 このクライアントとバックエンド間の頻繁な通信が、アプリケーションのパフォーマンスとスケールに悪影響を及ぼす可能性があります。  サービス間で大量の呼び出しがある小さなサービスを中心にアプリケーションが構築されるマイクロサービス アーキテクチャでは、より頻繁にこの問題が発生します。

次の図では、クライアントは、各サービスに要求を送信します (1、2、3)。 サービスはそれぞれ要求メッセージを処理し、アプリケーションに応答を送り返します (4、5、6)。 一般的に待機時間の長い携帯ネットワークでは、この方法で個々の要求を使用することは効率が悪く、接続が切断されたり、要求が不完全になったりする可能性があります。 要求は並列で実行できますが、アプリケーションは各要求のデータをすべて個別の接続で送信、待機、および処理する必要があるため、エラーが発生する可能性が高くなります。

![ゲートウェイ集約パターンの問題の図](./_images/gateway-aggregation-problem.png)

## <a name="solution"></a>解決策

ゲートウェイを使用すると、クライアントとサービス間の頻繁な通信を削減できます。 ゲートウェイはクライアント要求を受信し、さまざまなバックエンド システムに要求をディスパッチして結果を集計し、それらを要求元のクライアントに送り返します。

このパターンでは、アプリケーションがバックエンド サービスに対して行う要求の数を削減して、待機時間の長いネットワーク経由でのアプリケーションのパフォーマンスを向上することができます。

次の図では、アプリケーションはゲートウェイに要求を送信します (1)。 要求には、追加の要求のパッケージが含まれています。 ゲートウェイがこれらを分解し、関連するサービスに各要求を送信して (2) 処理します。 各サービスが応答をゲートウェイに返します (3)。 ゲートウェイは、各サービスからの応答を結合し、アプリケーションに応答を送信します (4)。 アプリケーションは、1 つの要求を行い、ゲートウェイから 1 つの応答のみを受信します。

![ゲートウェイ集約パターンの解決策の図](./_images/gateway-aggregation.png)

## <a name="issues-and-considerations"></a>問題と注意事項

- ゲートウェイがバックエンド間のサービス結合を行わないようにする必要があります。
- できる限り待機時間を短縮するため、ゲートウェイはバックエンド サービスの近くに配置する必要があります。
- ゲートウェイ サービスによって、単一障害点が生じる可能性があります。 アプリケーションの可用性の要件が満たされるように、ゲートウェイが適切に設計されていることを確認してください。
- ゲートウェイによって、ボトルネックが生じる可能性があります。 負荷を処理するための適切なパフォーマンスがゲートウェイに備わっており、予測した成長に応じて拡張できることを確認してください。
- サービスの連鎖的なエラーが発生しないように、ゲートウェイに対してロード テストを実行します。
- [バルクヘッド][bulkhead]、[サーキット ブレーク][circuit-breaker]、[再試行][retry]、タイムアウトなどの手法を使用して、耐障害性のある設計を実装します。
- 1 つまたは複数のサービスの呼び出しに時間がかかりすぎる場合、タイムアウトが許容され、データの部分的なセットが返されることがあります。 アプリケーションがこのシナリオを処理する方法を検討してください。
- 非同期 I/O を使用して、バックエンドでの遅延によってアプリケーションでパフォーマンスの問題が発生しないようにしてください。
- 相関 ID を使用して分散トレースを実装し、個々の呼び出しを追跡します。
- 要求メトリックおよび応答のサイズを監視します。
- エラーを処理するため、キャッシュ データをフェールオーバー戦略として返すことを検討してください。
- ゲートウェイに集約を構築する代わりに、ゲートウェイの背後に集約サービスを配置することを検討してください。 要求の集約には、ゲートウェイの他のサービスとは異なるリソース要件がある可能性があり、ゲートウェイのルーティングおよびオフロード機能に影響を及ぼすことがあります。

## <a name="when-to-use-this-pattern"></a>このパターンを使用する状況

このパターンは次の状況で使用します。

- クライアントは、操作を実行するために、複数のバックエンド サービスと通信する必要があります。
- クライアントは、携帯ネットワークなどの待機時間が長いネットワークを使用することがあります。

このパターンは、次の状況では適切でない可能性があります。

- 複数の操作で、クライアントと 1 つのサービス間の呼び出しの回数を削減したい場合。 このシナリオでは、サービスにバッチ操作を追加すると効果的である可能性があります。
- クライアントまたはアプリケーションがバックエンド サービスの近くに配置されており、待機時間が重要な要因ではない場合。

## <a name="example"></a>例

次の例では、Lua を使用してシンプルなゲートウェイ集約 NGINX サービスを作成する方法を示します。

```lua
worker_processes  4;

events {
  worker_connections 1024;
}

http {
  server {
    listen 80;

    location = /batch {
      content_by_lua '
        ngx.req.read_body()

        -- read json body content
        local cjson = require "cjson"
        local batch = cjson.decode(ngx.req.get_body_data())["batch"]

        -- create capture_multi table
        local requests = {}
        for i, item in ipairs(batch) do
          table.insert(requests, {item.relative_url, { method = ngx.HTTP_GET}})
        end

        -- execute batch requests in parallel
        local results = {}
        local resps = { ngx.location.capture_multi(requests) }
        for i, res in ipairs(resps) do
          table.insert(results, {status = res.status, body = cjson.decode(res.body), header = res.header})
        end

        ngx.say(cjson.encode({results = results}))
      ';
    }

    location = /service1 {
      default_type application/json;
      echo '{"attr1":"val1"}';
    }

    location = /service2 {
      default_type application/json;
      echo '{"attr2":"val2"}';
    }
  }
}
```

## <a name="related-guidance"></a>関連するガイダンス

- [フロントエンド パターン用バックエンド](./backends-for-frontends.md)
- [Gateway Offloading pattern](./gateway-offloading.md) (ゲートウェイ オフロード パターン)
- [Gateway Routing pattern](./gateway-routing.md) (ゲートウェイ ルーティング パターン)

[bulkhead]: ./bulkhead.md
[circuit-breaker]: ./circuit-breaker.md
[retry]: ./retry.md