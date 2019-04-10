---
title: サブスクリプション ガバナンスのシナリオと例
description: 一般的なシナリオでの Azure サブスクリプション ガバナンスの実装方法の例を紹介します。
author: rdendtler
ms.date: 01/03/2017
ms.author: rodend
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 5ecdf1782fc274205e2750b9dc36fd2de3010e1f
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244183"
---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Azure エンタープライズ スキャフォールディングの実装例

この記事では、企業が [Azure エンタープライズ スキャフォールディング](azure-scaffold.md)の推奨事項を実装する方法の例を紹介します。 Contoso という架空の会社を使って一般的なシナリオのベスト プラクティスを示します。

## <a name="background"></a>バックグラウンド

Contoso は、顧客にサプライ チェーン ソリューションを提供する世界企業です。 同社は、サービスとしてのソフトウェア モデルから、オンプレミスに配置されるパッケージ化されたモデルに至るまで、あらゆるものを提供しています。 インド、米国、カナダに重要な開発センターがあり、世界中でソフトウェアを開発しています。

同社の ISV の部分は、重要なビジネスで製品を管理する複数の独立した部署に分けられています。 部署ごとに開発者、プロダクト マネージャー、アーキテクトがいます。

エンタープライズ テクノロジ サービス (ETS) 部では、一元化された IT 機能を提供し、各部署がそれぞれのアプリケーションをホストする複数のデータセンターを管理しています。 ETS 組織では、データセンターの管理に加え、一元化されたコラボレーション サービス (電子メールや Web サイトなど) とネットワーク/テレフォニー サービスを提供し、管理しています。 また、運用スタッフのいない小規模の部署の顧客向けワークロードも管理しています。

この記事では、次のペルソナを使用します。

* Dave は ETS の Azure 管理者です。
* Alice はサプライ チェーン部の Contoso 開発責任者です。

Contoso 社では、基幹業務アプリケーションと顧客向けアプリケーションを構築する必要があります。 これらのアプリケーションは Azure で実行することが決定されました。 Dave は[規範的なサブスクリプション ガバナンス](azure-scaffold.md)に関する記事を読み、推奨事項を実装する準備を整えました。

## <a name="scenario-1-line-of-business-application"></a>シナリオ 1: 基幹業務アプリケーション

Contoso 社では、世界中の開発者が使用するソース コード管理システム (BitBucket) の構築を進めています。 このアプリケーションは、ホスティングにサービスとしてのインフラストラクチャ (IaaS) を使用し、Web サーバーとデータベース サーバーで構成されます。 開発者は開発環境のサーバーにはアクセスしますが、Azure のサーバーにアクセスする必要はありません。 Contoso ETS では、アプリケーションの所有者とチームがアプリケーションを管理できるようにしたいと考えています。 アプリケーションは、Contoso 社の企業ネットワーク上でのみ使用できます。 Dave は、このアプリケーションのサブスクリプションを設定する必要があります。 このサブスクリプションは、将来、開発者関連の他のソフトウェアもホストする予定です。

### <a name="naming-standards-and-resource-groups"></a>命名規則とリソース グループ

Dave は、すべての部署に共通する開発者ツールをサポートするサブスクリプションを作成することにしました。 Dave は、サブスクリプションと (アプリケーションおよびネットワークの) リソース グループにわかりやすい名前を付ける必要があります。 Dave は次のサブスクリプションとリソース グループを作成しました。

| Item | Name | 説明 |
| --- | --- | --- |
| サブスクリプション |Contoso ETS DeveloperTools Production |共通の開発ツールをサポートします。 |
| リソース グループ |bitbucket-prod-rg |アプリケーションの Web サーバーとデータベース サーバーが含まれます。 |
| リソース グループ |corenetworks-prod-rg |仮想ネットワークとサイト間ゲートウェイ接続が含まれます。 |

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

サブスクリプションの作成後、Dave は適切なチームとアプリケーションの所有者がリソースにアクセスできるようにしたいと思いました。 Dave は、チームによって要件が異なることを認識しています。 そこで、Contoso 社のオンプレミス Active Directory (AD) から Azure Active Directory に同期されたグループを利用し、適切なレベルのアクセス権をチームに付与することにしました。

Dave はサブスクリプションの次のロールを割り当てました。

| Role | 割り当て先 | 説明 |
| --- | --- | --- |
| [所有者](/azure/role-based-access-control/built-in-roles#owner) |Contoso 社の AD のマネージド ID |この ID は、Contoso 社の ID 管理ツールを使用して Just In Time (JIT) アクセスで管理されます。この ID により、サブスクリプションの所有者のアクセスを完全に監査できます |
| [Security Reader](/azure/role-based-access-control/built-in-roles#security-reader) |セキュリティおよびリスク管理部門 |このロールでは、ユーザーは Azure Security Center とリソースの状態を確認できます |
| [Network Contributor](/azure/role-based-access-control/built-in-roles#network-contributor) |ネットワーク チーム |このロールでは、Contoso 社のネットワーク チームがサイト間 VPN と仮想ネットワークを管理できます |
| *カスタム ロール* |アプリケーションの所有者 |Dave は、リソース グループ内のリソースを変更できるロールを作成しました。 詳細については、「[Azure RBAC のカスタム ロール](/azure/role-based-access-control/custom-roles)」をご覧ください。 |

### <a name="policies"></a>ポリシー

Dave には、サブスクリプション内のリソースの管理に関する次の要件があります。

* 開発ツールは世界中の開発者をサポートするため、どのリージョンでもユーザーによるリソースの作成を妨げることは望ましくありません。 ただし、リソースが作成された場所は把握する必要があります。
* Dave はコストを懸念しています。 そのため、アプリケーションの所有者がコストのかかる仮想マシンを不必要に作成するのを防ぐ必要があります。
* このアプリケーションは多数の部署の開発者にサービスを提供するので、Dave は各リソースに部署とアプリケーションの所有者でタグ付けしたいと考えています。 これらのタグを使用することで、ETS は適切なチームに請求できます。

Dave は次の [Azure ポリシー](/azure/azure-policy/azure-policy-introduction)を作成します。

| フィールド | 効果 | 説明 |
| --- | --- | --- |
| location |audit |すべてのリージョンでのリソースの作成を監査します。 |
| type |deny |G シリーズ仮想マシンの作成を拒否します。 |
| tags |deny |アプリケーションの所有者タグが必要です。 |
| tags |deny |コスト センター タグが必要です。 |
| tags |append |タグ名 **BusinessUnit** とタグ値 **ETS** をすべてのリソースに追加します。 |

### <a name="resource-tags"></a>リソース タグ

Dave は、BitBucket 実装のコスト センターを特定するために、請求に関する具体的な情報が必要であることを理解しています。 また、ETS が所有するすべてのリソースを把握する必要があります。

Dave は、リソース グループとリソースに次の[タグ](/azure/azure-resource-manager/resource-group-using-tags)を追加しました。

| タグ名 | タグ値 |
| --- | --- |
| ApplicationOwner |このアプリケーションを管理するユーザーの名前 |
| CostCenter |Azure の利用料金を支払うグループのコスト センター |
| BusinessUnit |**ETS** (サブスクリプションに関連付けられている部署) |

### <a name="core-network"></a>コア ネットワーク

Contoso ETS 情報セキュリティおよびリスク管理チームは、Dave が提案した Azure へのアプリケーションの移行計画を確認しました。 チームは、アプリケーションがインターネットに公開されないようにすることを求めています。  Dave には、今後 Azure に移行する予定の開発者アプリもあります。 これらのアプリにはパブリック インターフェイスが必要です。  これらの要件を満たすために、Dave は内部および外部仮想ネットワークと、アクセスを制限するネットワーク セキュリティ グループを提供することにしました。

Dave は次のリソースを作成しました。

| リソースの種類 | Name | 説明 |
| --- | --- | --- |
| Virtual Network |internal-vnet |BitBucket アプリケーションで使用されます。ExpressRoute 経由で Contoso 社の企業ネットワークに接続されています。  サブネット (`bitbucket`) は、特定の IP アドレス空間を使用してアプリケーションを提供します |
| Virtual Network |external-vnet |公開エンドポイントを必要とする将来のアプリケーションで使用できます |
| ネットワーク セキュリティ グループ |bitbucket-nsg |アプリケーションが存在するサブネット (`bitbucket`) に対して、ポート 443 での接続だけを許可することで、このワークロードの攻撃対象領域を最小限に抑えることができます |

### <a name="resource-locks"></a>リソース ロック

Dave は、Contoso 社の企業ネットワークから内部仮想ネットワークへの接続を、予想外のスクリプトや誤削除から保護する必要があることに気付きました。

そこで、次の[リソース ロック](/azure/azure-resource-manager/resource-group-lock-resources)を作成しました。

| ロックの種類 | リソース | 説明 |
| --- | --- | --- |
| **CanNotDelete** |internal-vnet |ユーザーが仮想ネットワークまたはサブネットを削除できないようにします。ただし、新しいサブネットの追加を妨げることはありません |

### <a name="azure-automation"></a>Azure Automation

このアプリケーションには自動化するものはありません。 Dave は Azure Automation アカウントを作成しましたが、最初のうちはこのアカウントを使用しません。

### <a name="azure-security-center"></a>Azure Security Center

Contoso IT サービス管理部門では、脅威をすばやく特定し、処理する必要があります。 また、どのような問題が存在する可能性があるかを理解したいと思っています。

これらの要件を満たすために、Dave は [Azure Security Center](/azure/security-center/security-center-intro) を有効にし、セキュリティ閲覧者ロールがアクセスできるようにしました。

## <a name="scenario-2-customer-facing-app"></a>シナリオ 2: 顧客向けアプリケーション

サプライ チェーン部のビジネス リーダーは、ロイヤルティ カードを使用して Contoso 社の顧客との関わりを強化するさまざまな機会を特定しました。 Alice のチームはこのアプリケーションを作成する必要があり、Azure がビジネス ニーズへの対応力を高めると判断しました。 Alice は ETS の Dave と協力して、このアプリケーションの開発と運用に使用する 2 つのサブスクリプションを構成することにしました。

### <a name="azure-subscriptions"></a>Azure サブスクリプション

Dave は Azure Enterprise Portal にログインし、サプライ チェーン部門が既に存在することを確認しました。  ただし、このプロジェクトは Azure におけるサプライ チェーン チームの最初の開発プロジェクトであるため、Dave は Alice の開発チーム用の新しいアカウントが必要であることに気付きました。  Dave はチーム用の "R & D" アカウントを作成し、Alice にアクセスを割り当てました。 Alice は Azure Portal からログインし、2 つのサブスクリプションを作成しました。1 つは開発サーバーを保持するためのサブスクリプション、もう 1 つは運用サーバーを保持するためのサブスクリプションです。  Alice は次のサブスクリプションを作成するときに、以前に定めた命名規則に従いました。

| サブスクリプションの用途 | Name |
| --- | --- |
| 開発 |Contoso SupplyChain ResearchDevelopment LoyaltyCard Development |
| Production |Contoso SupplyChain Operations LoyaltyCard Production |

### <a name="policies"></a>ポリシー

Dave と Alice はアプリケーションについて話し合い、このアプリケーションが北米リージョンの顧客にのみサービスを提供することを明確にしました。  Alice とチームは、Azure のアプリケーション サービス環境と Azure SQL を使用してアプリケーションを作成することを計画しました。 開発中に仮想マシンを作成することが必要な場合があります。  Alice は、ETS が関与しなくても、開発者が問題を調査するために必要なリソースを使用できるようにしたいと考えています。

**開発サブスクリプション**では、次のポリシーを作成しました。

| フィールド | 効果 | 説明 |
| --- | --- | --- |
| location |audit |すべてのリージョンでのリソースの作成を監査します。 |

開発環境でユーザーが作成できる SKU の種類を制限していないので、リソース グループまたはリソースにタグは不要です。

**運用サブスクリプション**では、次のポリシーを作成しました。

| フィールド | 効果 | 説明 |
| --- | --- | --- |
| location |deny |米国のデータセンター以外でのリソースの作成を拒否します |
| tags |deny |アプリケーションの所有者タグが必要です。 |
| tags |deny |部門タグが必要です |
| tags |append |各リソース グループに運用環境を示すタグを追加します |

運用環境でユーザーが作成できる SKU の種類に制限はありません。

### <a name="resource-tags"></a>リソース タグ

Dave は、課金と所有権について適切なビジネス グループを特定するために、具体的な情報が必要であることを理解しています。 Dave は、リソース グループとリソースのリソース タグを定義しました。

| タグ名 | タグ値 |
| --- | --- |
| ApplicationOwner |このアプリケーションを管理するユーザーの名前 |
| 部署 |Azure の利用料金を支払うグループのコスト センター |
| EnvironmentType |**Production** (サブスクリプションの名前に **Production** が含まれていても、このタグを含めることで、ポータルや請求書でリソースを確認するときに簡単に識別できるようになります) |

### <a name="core-networks"></a>コア ネットワーク

Contoso ETS 情報セキュリティおよびリスク管理チームは、Dave が提案した Azure へのアプリケーションの移行計画を確認しました。 チームは、ロイヤルティ カード アプリケーションを適切に分離し、DMZ ネットワーク内で保護できるようにすることを求めています。  この要件を満たすために、Dave と Alice は外部仮想ネットワークと、ロイヤルティ カード アプリケーションを Contoso 社の企業ネットワークから分離するネットワーク セキュリティ グループを作成することにしました。

**開発サブスクリプション**では、次のリソースを作成しました。

| リソースの種類 | Name | 説明 |
| --- | --- | --- |
| Virtual Network |internal-vnet |Contoso ロイヤルティ カード開発環境を提供します。ExpressRoute 経由で Contoso 社の企業ネットワークに接続されています |

**運用サブスクリプション**では、次のリソースを作成しました。

| リソースの種類 | Name | 説明 |
| --- | --- | --- |
| Virtual Network |external-vnet |ロイヤルティ カード アプリケーションをホストします。Contoso 社の ExpressRoute には直接接続されていません。 コードは、ソース コード システムによって PaaS サービスに直接プッシュされます |
| ネットワーク セキュリティ グループ |loyaltycard-nsg |TCP 443 での受信通信だけを許可することで、このワークロードの攻撃対象領域を最小限に抑えることができます。  Contoso 社では、保護を強化するために、Web アプリケーション ファイアウォールを使用した調査も行っています |

### <a name="resource-locks"></a>リソース ロック

Dave と Alice は話し合い、誤ったコード プッシュ時の誤削除を防ぐために、環境内の一部の重要なリソースにリソース ロックを追加することにしました。

次のロックを作成しました。

| ロックの種類 | リソース | 説明 |
| --- | --- | --- |
| **CanNotDelete** |external-vnet |ユーザーが仮想ネットワークまたはサブネットを削除できないようにします。 このロックによって、新しいサブネットの追加が妨げられることはありません |

### <a name="azure-automation"></a>Azure Automation

Alice と開発チームは、このアプリケーションの環境を管理するさまざまな Runbook を用意しています。 Runbook により、アプリケーションのノードの追加/削除や他の DevOps タスクを実行できます。

これらの Runbook を使用するために、[Automation](/azure/automation/automation-intro) を有効にしました。

### <a name="azure-security-center"></a>Azure Security Center

Contoso IT サービス管理部門では、脅威をすばやく特定し、処理する必要があります。 また、どのような問題が存在する可能性があるかを理解したいと思っています。

これらの要件を満たすために、Dave は Azure Security Center を有効にしました。 Dave は Azure Security Center がリソースを監視していることを確認し、DevOps チームとセキュリティ チームがアクセスできるようにしました。

## <a name="next-steps"></a>次の手順

* Resource Manager テンプレートの作成方法については、「[Azure Resource Manager テンプレートを作成するためのベスト プラクティス](/azure/azure-resource-manager/resource-manager-template-best-practices)」をご覧ください。
