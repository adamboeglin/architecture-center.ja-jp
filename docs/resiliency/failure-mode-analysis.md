---
title: 障害モード分析
description: Azure に基づくクラウド ソリューションの障害モード分析を実行するためのガイドラインです。
author: MikeWasson
ms.date: 05/07/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency
ms.openlocfilehash: 6d0f58161c5b9d5922c21f24b1b1a50bab836bb1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248069"
---
# <a name="failure-mode-analysis"></a><span data-ttu-id="69edd-103">障害モード分析</span><span class="sxs-lookup"><span data-stu-id="69edd-103">Failure mode analysis</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="69edd-104">障害モード分析 (FMA) は、システムで考えられる障害点を特定することによって、システムに回復力を持たせるためのプロセスです。</span><span class="sxs-lookup"><span data-stu-id="69edd-104">Failure mode analysis (FMA) is a process for building resiliency into a system, by identifying possible failure points in the system.</span></span> <span data-ttu-id="69edd-105">FMA をアーキテクチャおよび設計フェーズで行って、最初からシステムに障害復旧を組み込むことができるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-105">The FMA should be part of the architecture and design phases, so that you can build failure recovery into the system from the beginning.</span></span>

<span data-ttu-id="69edd-106">FMA の一般的な実施プロセスを次に示します。</span><span class="sxs-lookup"><span data-stu-id="69edd-106">Here is the general process to conduct an FMA:</span></span>

1. <span data-ttu-id="69edd-107">システム内のすべてのコンポーネントを明らかにします。</span><span class="sxs-lookup"><span data-stu-id="69edd-107">Identify all of the components in the system.</span></span> <span data-ttu-id="69edd-108">ID プロバイダーやサード パーティのサービスなど、外部の依存関係を含みます。</span><span class="sxs-lookup"><span data-stu-id="69edd-108">Include external dependencies, such as as identity providers, third-party services, and so on.</span></span>
2. <span data-ttu-id="69edd-109">コンポーネントごとに発生する可能性のある障害を特定します。</span><span class="sxs-lookup"><span data-stu-id="69edd-109">For each component, identify potential failures that could occur.</span></span> <span data-ttu-id="69edd-110">1 つのコンポーネントに複数の障害モードが存在する場合があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-110">A single component may have more than one failure mode.</span></span> <span data-ttu-id="69edd-111">たとえば、影響と可能な軽減策が異なるため、読み取り障害と書き込み障害は別のものとして検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-111">For example, you should consider read failures and write failures separately, because the impact and possible mitigations will be different.</span></span>
3. <span data-ttu-id="69edd-112">総合的なリスクに従って、各障害モードを評価します。</span><span class="sxs-lookup"><span data-stu-id="69edd-112">Rate each failure mode according to its overall risk.</span></span> <span data-ttu-id="69edd-113">次の点を考慮します。</span><span class="sxs-lookup"><span data-stu-id="69edd-113">Consider these factors:</span></span>

   - <span data-ttu-id="69edd-114">障害の可能性はどれくらいか。</span><span class="sxs-lookup"><span data-stu-id="69edd-114">What is the likelihood of the failure.</span></span> <span data-ttu-id="69edd-115">比較的よくあることか。</span><span class="sxs-lookup"><span data-stu-id="69edd-115">Is it relatively common?</span></span> <span data-ttu-id="69edd-116">非常にまれなことか。</span><span class="sxs-lookup"><span data-stu-id="69edd-116">Extrememly rare?</span></span> <span data-ttu-id="69edd-117">正確な数値は必要はありません。目的は、優先順位をランク付けすることです。</span><span class="sxs-lookup"><span data-stu-id="69edd-117">You don't need exact numbers; the purpose is to help rank the priority.</span></span>
   - <span data-ttu-id="69edd-118">可用性、データの損失、金銭的コスト、および業務の中断の観点から、アプリケーションに対してどの程度の影響がありますか。</span><span class="sxs-lookup"><span data-stu-id="69edd-118">What is the impact on the application, in terms of availability, data loss, monetary cost, and business disruption?</span></span>

4. <span data-ttu-id="69edd-119">各障害モードについて、アプリケーションがどのように対応および復旧するかを決定します。</span><span class="sxs-lookup"><span data-stu-id="69edd-119">For each failure mode, determine how the application will respond and recover.</span></span> <span data-ttu-id="69edd-120">コストとアプリケーションの複雑さのトレードオフを検討します。</span><span class="sxs-lookup"><span data-stu-id="69edd-120">Consider tradeoffs in cost and application complexity.</span></span>

<span data-ttu-id="69edd-121">この記事では、FMA プロセスの出発点として、起こりうる障害モードの一覧とその軽減方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="69edd-121">As a starting point for your FMA process, this article contains a catalog of potential failure modes and their mitigations.</span></span> <span data-ttu-id="69edd-122">カタログは、テクノロジまたは Azure サービス、およびアプリケーション レベルの設計の一般的なカテゴリで分けられています。</span><span class="sxs-lookup"><span data-stu-id="69edd-122">The catalog is organized by technology or Azure service, plus a general category for application-level design.</span></span> <span data-ttu-id="69edd-123">カタログは完全ではありませんが、主要な Azure サービスの多くをカバーしています。</span><span class="sxs-lookup"><span data-stu-id="69edd-123">The catalog is not exhaustive, but covers many of the core Azure services.</span></span>

## <a name="app-service"></a><span data-ttu-id="69edd-124">App Service</span><span class="sxs-lookup"><span data-stu-id="69edd-124">App Service</span></span>

<!-- markdownlint-disable MD026 -->

### <a name="app-service-app-shuts-down"></a><span data-ttu-id="69edd-125">App Service アプリがシャットダウンする。</span><span class="sxs-lookup"><span data-stu-id="69edd-125">App Service app shuts down.</span></span>

<span data-ttu-id="69edd-126">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-126">**Detection**.</span></span> <span data-ttu-id="69edd-127">考えられる原因:</span><span class="sxs-lookup"><span data-stu-id="69edd-127">Possible causes:</span></span>

- <span data-ttu-id="69edd-128">予期されたシャットダウン</span><span class="sxs-lookup"><span data-stu-id="69edd-128">Expected shutdown</span></span>

  - <span data-ttu-id="69edd-129">オペレーターが、たとえば Azure Portal を使用してシャットダウンしました。</span><span class="sxs-lookup"><span data-stu-id="69edd-129">An operator shuts down the application; for example, using the Azure portal.</span></span>
  - <span data-ttu-id="69edd-130">アプリはアイドル状態だったために、アンロードされました </span><span class="sxs-lookup"><span data-stu-id="69edd-130">The app was unloaded because it was idle.</span></span> <span data-ttu-id="69edd-131">(`Always On` の設定が無効の場合のみ)。</span><span class="sxs-lookup"><span data-stu-id="69edd-131">(Only if the `Always On` setting is disabled.)</span></span>

- <span data-ttu-id="69edd-132">予期されないシャットダウン</span><span class="sxs-lookup"><span data-stu-id="69edd-132">Unexpected shutdown</span></span>

  - <span data-ttu-id="69edd-133">アプリがクラッシュしました。</span><span class="sxs-lookup"><span data-stu-id="69edd-133">The app crashes.</span></span>
  - <span data-ttu-id="69edd-134">App Service の VM インスタンスが使用できなくなりました。</span><span class="sxs-lookup"><span data-stu-id="69edd-134">An App Service VM instance becomes unavailable.</span></span>

<span data-ttu-id="69edd-135">Application_End ログは、アプリ ドメインのシャットダウン (ソフト プロセス クラッシュ) をキャッチし、アプリケーション ドメインのシャットダウンをキャッチする唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="69edd-135">Application_End logging will catch the app domain shutdown (soft process crash) and is the only way to catch the application domain shutdowns.</span></span>

<span data-ttu-id="69edd-136">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-136">**Recovery:**</span></span>

- <span data-ttu-id="69edd-137">シャットダウンが予期されていた場合は、アプリケーションのシャットダウン イベントを使用して正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="69edd-137">If the shutdown was expected, use the application's shutdown event to shut down gracefully.</span></span> <span data-ttu-id="69edd-138">たとえば、ASP.NET では、`Application_End` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-138">For example, in ASP.NET, use the `Application_End` method.</span></span>
- <span data-ttu-id="69edd-139">アイドルの間にアプリケーションがアンロードされた場合は、次の要求で自動的に再起動されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-139">If the application was unloaded while idle, it is automatically restarted on the next request.</span></span> <span data-ttu-id="69edd-140">ただし、"コールド スタート" コストが発生します。</span><span class="sxs-lookup"><span data-stu-id="69edd-140">However, you will incur the "cold start" cost.</span></span>
- <span data-ttu-id="69edd-141">アイドル状態の間にアプリケーションがアンロードされないようにするには、Web アプリで `Always On` の設定を有効にします。</span><span class="sxs-lookup"><span data-stu-id="69edd-141">To prevent the application from being unloaded while idle, enable the `Always On` setting in the web app.</span></span> <span data-ttu-id="69edd-142">「[Azure App Service での Web アプリの構成][app-service-configure]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="69edd-142">See [Configure web apps in Azure App Service][app-service-configure].</span></span>
- <span data-ttu-id="69edd-143">オペレーターによるアプリのシャットダウンを防ぐには、`ReadOnly` レベルでリソース ロックを設定します。</span><span class="sxs-lookup"><span data-stu-id="69edd-143">To prevent an operator from shutting down the app, set a resource lock with `ReadOnly` level.</span></span> <span data-ttu-id="69edd-144">詳細については、[Azure Resource Manager でのリソースのロック][rm-locks]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-144">See [Lock resources with Azure Resource Manager][rm-locks].</span></span>
- <span data-ttu-id="69edd-145">アプリがクラッシュした場合、または App Service の VM が使用できなくなった場合は、App Service がアプリを自動的に再起動します。</span><span class="sxs-lookup"><span data-stu-id="69edd-145">If the app crashes or an App Service VM becomes unavailable, App Service automatically restarts the app.</span></span>

<span data-ttu-id="69edd-146">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-146">**Diagnostics**.</span></span> <span data-ttu-id="69edd-147">アプリケーション ログと Web サーバー ログ。</span><span class="sxs-lookup"><span data-stu-id="69edd-147">Application logs and web server logs.</span></span> <span data-ttu-id="69edd-148">「[Azure App Service の Web アプリの診断ログの有効化][app-service-logging]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-148">See [Enable diagnostics logging for web apps in Azure App Service][app-service-logging].</span></span>

### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a><span data-ttu-id="69edd-149">特定のユーザーが繰り返し不適切な要求を行ったり、システムを過負荷状態にする。</span><span class="sxs-lookup"><span data-stu-id="69edd-149">A particular user repeatedly makes bad requests or overloads the system.</span></span>

<span data-ttu-id="69edd-150">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-150">**Detection**.</span></span> <span data-ttu-id="69edd-151">ユーザーを認証し、アプリケーション ログにユーザー ID を含めます。</span><span class="sxs-lookup"><span data-stu-id="69edd-151">Authenticate users and include user ID in application logs.</span></span>

<span data-ttu-id="69edd-152">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-152">**Recovery:**</span></span>

- <span data-ttu-id="69edd-153">[Azure API Management][api-management] を使用して、ユーザーからの要求を調整します。</span><span class="sxs-lookup"><span data-stu-id="69edd-153">Use [Azure API Management][api-management] to throttle requests from the user.</span></span> <span data-ttu-id="69edd-154">「[Azure API Management を使用した高度な要求スロットル][api-management-throttling]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-154">See [Advanced request throttling with Azure API Management][api-management-throttling]</span></span>
- <span data-ttu-id="69edd-155">ユーザーをブロックします。</span><span class="sxs-lookup"><span data-stu-id="69edd-155">Block the user.</span></span>

<span data-ttu-id="69edd-156">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-156">**Diagnostics**.</span></span> <span data-ttu-id="69edd-157">すべての認証要求をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="69edd-157">Log all authentication requests.</span></span>

### <a name="a-bad-update-was-deployed"></a><span data-ttu-id="69edd-158">不正な更新プログラムがデプロイされた。</span><span class="sxs-lookup"><span data-stu-id="69edd-158">A bad update was deployed.</span></span>

<span data-ttu-id="69edd-159">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-159">**Detection**.</span></span> <span data-ttu-id="69edd-160">Azure Portal を使用して (「[Azure Web アプリのパフォーマンスの監視][app-insights-web-apps]」を参照してください)、または[正常性エンドポイントの監視パターン][health-endpoint-monitoring-pattern]を実装して、アプリケーションの正常性を監視します。</span><span class="sxs-lookup"><span data-stu-id="69edd-160">Monitor the application health through the Azure Portal (see [Monitor Azure web app performance][app-insights-web-apps]) or implement the [health endpoint monitoring pattern][health-endpoint-monitoring-pattern].</span></span>

<span data-ttu-id="69edd-161">**復旧:**。</span><span class="sxs-lookup"><span data-stu-id="69edd-161">**Recovery:**.</span></span> <span data-ttu-id="69edd-162">複数の[デプロイ スロット][app-service-slots]を使用して、最後の正常な展開にロールバックします。</span><span class="sxs-lookup"><span data-stu-id="69edd-162">Use multiple [deployment slots][app-service-slots] and roll back to the last-known-good deployment.</span></span> <span data-ttu-id="69edd-163">詳細については、[基本的な Web アプリケーション][ra-web-apps-basic]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-163">For more information, see [Basic web application][ra-web-apps-basic].</span></span>

## <a name="azure-active-directory"></a><span data-ttu-id="69edd-164">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="69edd-164">Azure Active Directory</span></span>

### <a name="openid-connect-oidc-authentication-fails"></a><span data-ttu-id="69edd-165">OpenID Connect (OIDC) 認証が失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-165">OpenID Connect (OIDC) authentication fails.</span></span>

<span data-ttu-id="69edd-166">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-166">**Detection**.</span></span> <span data-ttu-id="69edd-167">次のような障害モードの可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-167">Possible failure modes include:</span></span>

1. <span data-ttu-id="69edd-168">Azure AD が使用できないか、またはネットワークの問題のために到達できません。</span><span class="sxs-lookup"><span data-stu-id="69edd-168">Azure AD is not available, or cannot be reached due to a network problem.</span></span> <span data-ttu-id="69edd-169">認証エンドポイントへのリダイレクトが失敗し、OIDC ミドルウェアが例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="69edd-169">Redirection to the authentication endpoint fails, and the OIDC middleware throws an exception.</span></span>
2. <span data-ttu-id="69edd-170">Azure AD テナントが存在しません。</span><span class="sxs-lookup"><span data-stu-id="69edd-170">Azure AD tenant does not exist.</span></span> <span data-ttu-id="69edd-171">認証エンドポイントへのリダイレクトから HTTP エラー コードが返り、OIDC ミドルウェアが例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="69edd-171">Redirection to the authentication endpoint returns an HTTP error code, and the OIDC middleware throws an exception.</span></span>
3. <span data-ttu-id="69edd-172">ユーザーを認証できません。</span><span class="sxs-lookup"><span data-stu-id="69edd-172">User cannot authenticate.</span></span> <span data-ttu-id="69edd-173">検出戦略は不要です。Azure AD がログインの失敗を処理します。</span><span class="sxs-lookup"><span data-stu-id="69edd-173">No detection strategy is necessary; Azure AD handles login failures.</span></span>

<span data-ttu-id="69edd-174">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-174">**Recovery:**</span></span>

1. <span data-ttu-id="69edd-175">ミドルウェアからのハンドルされない例外をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-175">Catch unhandled exceptions from the middleware.</span></span>
2. <span data-ttu-id="69edd-176">`AuthenticationFailed` イベントを処理します。</span><span class="sxs-lookup"><span data-stu-id="69edd-176">Handle `AuthenticationFailed` events.</span></span>
3. <span data-ttu-id="69edd-177">エラー ページにユーザーをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="69edd-177">Redirect the user to an error page.</span></span>
4. <span data-ttu-id="69edd-178">ユーザーが再試行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-178">User retries.</span></span>

## <a name="azure-search"></a><span data-ttu-id="69edd-179">Azure Search</span><span class="sxs-lookup"><span data-stu-id="69edd-179">Azure Search</span></span>

### <a name="writing-data-to-azure-search-fails"></a><span data-ttu-id="69edd-180">Azure Search へのデータの書き込みが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-180">Writing data to Azure Search fails.</span></span>

<span data-ttu-id="69edd-181">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-181">**Detection**.</span></span> <span data-ttu-id="69edd-182">`Microsoft.Rest.Azure.CloudException` エラーをキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-182">Catch `Microsoft.Rest.Azure.CloudException` errors.</span></span>

<span data-ttu-id="69edd-183">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-183">**Recovery:**</span></span>

<span data-ttu-id="69edd-184">[Search .NET SDK][search-sdk] は、一時的な障害の後で自動的に再試行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-184">The [Search .NET SDK][search-sdk] automatically retries after transient failures.</span></span> <span data-ttu-id="69edd-185">クライアント SDK によってスローされた例外は、一時的ではないエラーとして処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-185">Any exceptions thrown by the client SDK should be treated as non-transient errors.</span></span>

<span data-ttu-id="69edd-186">既定の再試行ポリシーは、指数バックオフを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-186">The default retry policy uses exponential back-off.</span></span> <span data-ttu-id="69edd-187">異なる再試行ポリシーを使用するには、`SearchIndexClient` クラスまたは `SearchServiceClient` クラスで `SetRetryPolicy` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="69edd-187">To use a different retry policy, call `SetRetryPolicy` on the `SearchIndexClient` or `SearchServiceClient` class.</span></span> <span data-ttu-id="69edd-188">詳細については、[自動再試行][auto-rest-client-retry]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-188">For more information, see [Automatic Retries][auto-rest-client-retry].</span></span>

<span data-ttu-id="69edd-189">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-189">**Diagnostics**.</span></span> <span data-ttu-id="69edd-190">[検索トラフィックの分析][search-analytics]を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-190">Use [Search Traffic Analytics][search-analytics].</span></span>

### <a name="reading-data-from-azure-search-fails"></a><span data-ttu-id="69edd-191">Azure Search からのデータの読み取りが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-191">Reading data from Azure Search fails.</span></span>

<span data-ttu-id="69edd-192">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-192">**Detection**.</span></span> <span data-ttu-id="69edd-193">`Microsoft.Rest.Azure.CloudException` エラーをキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-193">Catch `Microsoft.Rest.Azure.CloudException` errors.</span></span>

<span data-ttu-id="69edd-194">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-194">**Recovery:**</span></span>

<span data-ttu-id="69edd-195">[Search .NET SDK][search-sdk] は、一時的な障害の後で自動的に再試行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-195">The [Search .NET SDK][search-sdk]  automatically retries after transient failures.</span></span> <span data-ttu-id="69edd-196">クライアント SDK によってスローされた例外は、一時的ではないエラーとして処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-196">Any exceptions thrown by the client SDK should be treated as non-transient errors.</span></span>

<span data-ttu-id="69edd-197">既定の再試行ポリシーは、指数バックオフを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-197">The default retry policy uses exponential back-off.</span></span> <span data-ttu-id="69edd-198">異なる再試行ポリシーを使用するには、`SearchIndexClient` クラスまたは `SearchServiceClient` クラスで `SetRetryPolicy` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="69edd-198">To use a different retry policy, call `SetRetryPolicy` on the `SearchIndexClient` or `SearchServiceClient` class.</span></span> <span data-ttu-id="69edd-199">詳細については、[自動再試行][auto-rest-client-retry]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-199">For more information, see [Automatic Retries][auto-rest-client-retry].</span></span>

<span data-ttu-id="69edd-200">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-200">**Diagnostics**.</span></span> <span data-ttu-id="69edd-201">[検索トラフィックの分析][search-analytics]を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-201">Use [Search Traffic Analytics][search-analytics].</span></span>

## <a name="cassandra"></a><span data-ttu-id="69edd-202">Cassandra</span><span class="sxs-lookup"><span data-stu-id="69edd-202">Cassandra</span></span>

### <a name="reading-or-writing-to-a-node-fails"></a><span data-ttu-id="69edd-203">ノードに対する読み取りまたは書き込みが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-203">Reading or writing to a node fails.</span></span>

<span data-ttu-id="69edd-204">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-204">**Detection**.</span></span> <span data-ttu-id="69edd-205">例外をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-205">Catch the exception.</span></span> <span data-ttu-id="69edd-206">.NET クライアントの場合は、通常、これは `System.Web.HttpException` です。</span><span class="sxs-lookup"><span data-stu-id="69edd-206">For .NET clients, this will typically be `System.Web.HttpException`.</span></span> <span data-ttu-id="69edd-207">他のクライアントでは、例外の種類が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-207">Other client may have other exception types.</span></span>  <span data-ttu-id="69edd-208">詳細については、「[Cassandra error handling done right](https://www.datastax.com/dev/blog/cassandra-error-handling-done-right)」(Cassandra のエラー処理) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-208">For more information, see [Cassandra error handling done right](https://www.datastax.com/dev/blog/cassandra-error-handling-done-right).</span></span>

<span data-ttu-id="69edd-209">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-209">**Recovery:**</span></span>

- <span data-ttu-id="69edd-210">各 [Cassandra クライアント](https://wiki.apache.org/cassandra/ClientOptions)には、専用の再試行ポリシーと機能があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-210">Each [Cassandra client](https://wiki.apache.org/cassandra/ClientOptions) has its own retry policies and capabilities.</span></span> <span data-ttu-id="69edd-211">詳細については、「[Cassandra error handling done right][cassandra-error-handling]」(Cassandra のエラー処理) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-211">For more information, see [Cassandra error handling done right][cassandra-error-handling].</span></span>
- <span data-ttu-id="69edd-212">データ ノードがフォールト ドメイン間に分散される、ラック対応のデプロイを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-212">Use a rack-aware deployment, with data nodes distributed across the fault domains.</span></span>
- <span data-ttu-id="69edd-213">ローカル クォーラム整合性で複数のリージョンにデプロイします。</span><span class="sxs-lookup"><span data-stu-id="69edd-213">Deploy to multiple regions with local quorum consistency.</span></span> <span data-ttu-id="69edd-214">一時的ではない障害が発生した場合は、別のリージョンにフェールオーバーします。</span><span class="sxs-lookup"><span data-stu-id="69edd-214">If a non-transient failure occurs, fail over to another region.</span></span>

<span data-ttu-id="69edd-215">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-215">**Diagnostics**.</span></span> <span data-ttu-id="69edd-216">アプリケーション ログ</span><span class="sxs-lookup"><span data-stu-id="69edd-216">Application logs</span></span>

## <a name="cloud-service"></a><span data-ttu-id="69edd-217">クラウド サービス</span><span class="sxs-lookup"><span data-stu-id="69edd-217">Cloud Service</span></span>

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a><span data-ttu-id="69edd-218">Web ロールまたは worker ロールが予期せずシャットダウンされる。</span><span class="sxs-lookup"><span data-stu-id="69edd-218">Web or worker roles are unexpectedly being shut down.</span></span>

<span data-ttu-id="69edd-219">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-219">**Detection**.</span></span> <span data-ttu-id="69edd-220">[RoleEnvironment.Stopping][RoleEnvironment.Stopping] イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="69edd-220">The [RoleEnvironment.Stopping][RoleEnvironment.Stopping] event is fired.</span></span>

<span data-ttu-id="69edd-221">**復旧**。</span><span class="sxs-lookup"><span data-stu-id="69edd-221">**Recovery**.</span></span> <span data-ttu-id="69edd-222">[RoleEntryPoint.OnStop][RoleEntryPoint.OnStop] メソッドをオーバーライドして、正常にクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="69edd-222">Override the [RoleEntryPoint.OnStop][RoleEntryPoint.OnStop] method to gracefully clean up.</span></span> <span data-ttu-id="69edd-223">詳細については、「[The Right Way to Handle Azure OnStop Events][onstop-events]」(Azure OnStop イベントを処理する正しい方法) (ブログ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-223">For more information, see [The Right Way to Handle Azure OnStop Events][onstop-events] (blog).</span></span>

## <a name="cosmos-db"></a><span data-ttu-id="69edd-224">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="69edd-224">Cosmos DB</span></span>

### <a name="reading-data-fails"></a><span data-ttu-id="69edd-225">データの読み取りが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-225">Reading data fails.</span></span>

<span data-ttu-id="69edd-226">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-226">**Detection**.</span></span> <span data-ttu-id="69edd-227">`System.Net.Http.HttpRequestException` または `Microsoft.Azure.Documents.DocumentClientException` をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-227">Catch `System.Net.Http.HttpRequestException` or `Microsoft.Azure.Documents.DocumentClientException`.</span></span>

<span data-ttu-id="69edd-228">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-228">**Recovery:**</span></span>

- <span data-ttu-id="69edd-229">SDK によって、失敗した試行が自動的にもう一度実行されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-229">The SDK automatically retries failed attempts.</span></span> <span data-ttu-id="69edd-230">再試行回数と最大待機時間を設定するには、`ConnectionPolicy.RetryOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="69edd-230">To set the number of retries and the maximum wait time, configure `ConnectionPolicy.RetryOptions`.</span></span> <span data-ttu-id="69edd-231">クライアントがスローする例外は、再試行ポリシーを超えているか、一時的なエラーでないかのいずれかです。</span><span class="sxs-lookup"><span data-stu-id="69edd-231">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>
- <span data-ttu-id="69edd-232">クライアントが Cosmos DB によってスロットルされると、HTTP 429 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-232">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="69edd-233">`DocumentClientException` で状態コードを確認します。</span><span class="sxs-lookup"><span data-stu-id="69edd-233">Check the status code in the `DocumentClientException`.</span></span> <span data-ttu-id="69edd-234">エラー 429 が繰り返し発生する場合は、コレクションのスループットの値を増やすことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-234">If you are getting error 429 consistently, consider increasing the throughput value of the collection.</span></span>
  - <span data-ttu-id="69edd-235">MongoDB API を使用している場合は、調整のときに、サービスはエラー コード 16500 を返します。</span><span class="sxs-lookup"><span data-stu-id="69edd-235">If you are using the MongoDB API, the service returns error code 16500 when throttling.</span></span>
- <span data-ttu-id="69edd-236">複数のリージョンの間で Cosmos DB データベースをレプリケートします。</span><span class="sxs-lookup"><span data-stu-id="69edd-236">Replicate the Cosmos DB database across two or more regions.</span></span> <span data-ttu-id="69edd-237">すべてのレプリカは読み取り可能です。</span><span class="sxs-lookup"><span data-stu-id="69edd-237">All replicas are readable.</span></span> <span data-ttu-id="69edd-238">クライアント SDK を使用して、`PreferredLocations` パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="69edd-238">Using the client SDKs, specify the `PreferredLocations` parameter.</span></span> <span data-ttu-id="69edd-239">これは、Azure リージョンの順序付きリストです。</span><span class="sxs-lookup"><span data-stu-id="69edd-239">This is an ordered list of Azure regions.</span></span> <span data-ttu-id="69edd-240">すべての読み取りは、リストで最初に利用可能なリージョンに送信されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-240">All reads will be sent to the first available region in the list.</span></span> <span data-ttu-id="69edd-241">要求が失敗すると、クライアントはリストのリージョンを順番に試します。</span><span class="sxs-lookup"><span data-stu-id="69edd-241">If the request fails, the client will try the other regions in the list, in order.</span></span> <span data-ttu-id="69edd-242">詳細については、「[SQL API を使用して Azure Cosmos DB グローバル分散をセットアップする方法][cosmosdb-multi-region]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-242">For more information, see [How to setup Azure Cosmos DB global distribution using the SQL API][cosmosdb-multi-region].</span></span>

<span data-ttu-id="69edd-243">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-243">**Diagnostics**.</span></span> <span data-ttu-id="69edd-244">クライアント側ですべてのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="69edd-244">Log all errors on the client side.</span></span>

### <a name="writing-data-fails"></a><span data-ttu-id="69edd-245">データの書き込みが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-245">Writing data fails.</span></span>

<span data-ttu-id="69edd-246">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-246">**Detection**.</span></span> <span data-ttu-id="69edd-247">`System.Net.Http.HttpRequestException` または `Microsoft.Azure.Documents.DocumentClientException` をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-247">Catch `System.Net.Http.HttpRequestException` or `Microsoft.Azure.Documents.DocumentClientException`.</span></span>

<span data-ttu-id="69edd-248">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-248">**Recovery:**</span></span>

- <span data-ttu-id="69edd-249">SDK によって、失敗した試行が自動的にもう一度実行されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-249">The SDK automatically retries failed attempts.</span></span> <span data-ttu-id="69edd-250">再試行回数と最大待機時間を設定するには、`ConnectionPolicy.RetryOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="69edd-250">To set the number of retries and the maximum wait time, configure `ConnectionPolicy.RetryOptions`.</span></span> <span data-ttu-id="69edd-251">クライアントがスローする例外は、再試行ポリシーを超えているか、一時的なエラーでないかのいずれかです。</span><span class="sxs-lookup"><span data-stu-id="69edd-251">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>
- <span data-ttu-id="69edd-252">クライアントが Cosmos DB によってスロットルされると、HTTP 429 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-252">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="69edd-253">`DocumentClientException` で状態コードを確認します。</span><span class="sxs-lookup"><span data-stu-id="69edd-253">Check the status code in the `DocumentClientException`.</span></span> <span data-ttu-id="69edd-254">エラー 429 が繰り返し発生する場合は、コレクションのスループットの値を増やすことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-254">If you are getting error 429 consistently, consider increasing the throughput value of the collection.</span></span>
- <span data-ttu-id="69edd-255">複数のリージョンの間で Cosmos DB データベースをレプリケートします。</span><span class="sxs-lookup"><span data-stu-id="69edd-255">Replicate the Cosmos DB database across two or more regions.</span></span> <span data-ttu-id="69edd-256">プライマリ リージョンが失敗した場合、別のリージョンが書き込みにレベル上げされます。</span><span class="sxs-lookup"><span data-stu-id="69edd-256">If the primary region fails, another region will be promoted to write.</span></span> <span data-ttu-id="69edd-257">手動でフェールオーバーをトリガーすることもできます。</span><span class="sxs-lookup"><span data-stu-id="69edd-257">You can also trigger a failover manually.</span></span> <span data-ttu-id="69edd-258">SDK が自動的に検出とルーティングを行うので、アプリケーション コードはフェールオーバー後も引き続き動作します。</span><span class="sxs-lookup"><span data-stu-id="69edd-258">The SDK does automatic discovery and routing, so application code continues to work after a failover.</span></span> <span data-ttu-id="69edd-259">フェールオーバー期間中は (通常は分単位)、SDK が新しい書き込みリージョンを探しているので、書き込み操作の待機時間が長くなります。</span><span class="sxs-lookup"><span data-stu-id="69edd-259">During the failover period (typically minutes), write operations will have higher latency, as the SDK finds the new write region.</span></span> <span data-ttu-id="69edd-260">詳細については、「[SQL API を使用して Azure Cosmos DB グローバル分散をセットアップする方法][cosmosdb-multi-region]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-260">For more information, see [How to setup Azure Cosmos DB global distribution using the SQL API][cosmosdb-multi-region].</span></span>
- <span data-ttu-id="69edd-261">フォールバックとしては、ドキュメントをバックアップ キューに保存し、後でキューを処理します。</span><span class="sxs-lookup"><span data-stu-id="69edd-261">As a fallback, persist the document to a backup queue, and process the queue later.</span></span>

<span data-ttu-id="69edd-262">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-262">**Diagnostics**.</span></span> <span data-ttu-id="69edd-263">クライアント側ですべてのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="69edd-263">Log all errors on the client side.</span></span>

## <a name="elasticsearch"></a><span data-ttu-id="69edd-264">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="69edd-264">Elasticsearch</span></span>

### <a name="reading-data-from-elasticsearch-fails"></a><span data-ttu-id="69edd-265">Elasticsearch からのデータの読み取りが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-265">Reading data from Elasticsearch fails.</span></span>

<span data-ttu-id="69edd-266">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-266">**Detection**.</span></span> <span data-ttu-id="69edd-267">使用されている特定の [Elasticsearch クライアント][elasticsearch-client]に対する適切な例外をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-267">Catch the appropriate exception for the particular [Elasticsearch client][elasticsearch-client] being used.</span></span>

<span data-ttu-id="69edd-268">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-268">**Recovery:**</span></span>

- <span data-ttu-id="69edd-269">再試行メカニズムを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-269">Use a retry mechanism.</span></span> <span data-ttu-id="69edd-270">各クライアントには、独自の再試行ポリシーがあります。</span><span class="sxs-lookup"><span data-stu-id="69edd-270">Each client has its own retry policies.</span></span>
- <span data-ttu-id="69edd-271">高可用性のためには、複数の Elasticsearch ノードをデプロイして、レプリケーションを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-271">Deploy multiple Elasticsearch nodes and use replication for high availability.</span></span>

<span data-ttu-id="69edd-272">詳細については、「[Running Elasticsearch on Azure][elasticsearch-azure]」(Azure で Elasticsearch を実行する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-272">For more information, see [Running Elasticsearch on Azure][elasticsearch-azure].</span></span>

<span data-ttu-id="69edd-273">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-273">**Diagnostics**.</span></span> <span data-ttu-id="69edd-274">Elasticsearch 用の監視ツールを使用するか、クライアント側でペイロードを含むすべてのエラーをログに記録することができます。</span><span class="sxs-lookup"><span data-stu-id="69edd-274">You can use monitoring tools for Elasticsearch, or log all errors on the client side with the payload.</span></span> <span data-ttu-id="69edd-275">「[Running Elasticsearch on Azure][elasticsearch-azure]」(Azure で Elasticsearch を実行する) の「Monitoring」(監視) セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-275">See the 'Monitoring' section in [Running Elasticsearch on Azure][elasticsearch-azure].</span></span>

### <a name="writing-data-to-elasticsearch-fails"></a><span data-ttu-id="69edd-276">Elasticsearch へのデータの書き込みが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-276">Writing data to Elasticsearch fails.</span></span>

<span data-ttu-id="69edd-277">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-277">**Detection**.</span></span> <span data-ttu-id="69edd-278">使用されている特定の [Elasticsearch クライアント][elasticsearch-client]に対する適切な例外をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-278">Catch the appropriate exception for the particular [Elasticsearch client][elasticsearch-client] being used.</span></span>

<span data-ttu-id="69edd-279">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-279">**Recovery:**</span></span>

- <span data-ttu-id="69edd-280">再試行メカニズムを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-280">Use a retry mechanism.</span></span> <span data-ttu-id="69edd-281">各クライアントには、独自の再試行ポリシーがあります。</span><span class="sxs-lookup"><span data-stu-id="69edd-281">Each client has its own retry policies.</span></span>
- <span data-ttu-id="69edd-282">アプリケーションが低下した整合性レベルを許容できる場合は、`write_consistency` の設定を `quorum` にした書き込みを検討します。</span><span class="sxs-lookup"><span data-stu-id="69edd-282">If the application can tolerate a reduced consistency level, consider writing with `write_consistency` setting of `quorum`.</span></span>

<span data-ttu-id="69edd-283">詳細については、「[Running Elasticsearch on Azure][elasticsearch-azure]」(Azure で Elasticsearch を実行する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-283">For more information, see [Running Elasticsearch on Azure][elasticsearch-azure].</span></span>

<span data-ttu-id="69edd-284">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-284">**Diagnostics**.</span></span> <span data-ttu-id="69edd-285">Elasticsearch 用の監視ツールを使用するか、クライアント側でペイロードを含むすべてのエラーをログに記録することができます。</span><span class="sxs-lookup"><span data-stu-id="69edd-285">You can use monitoring tools for Elasticsearch, or log all errors on the client side with the payload.</span></span> <span data-ttu-id="69edd-286">「[Running Elasticsearch on Azure][elasticsearch-azure]」(Azure で Elasticsearch を実行する) の「Monitoring」(監視) セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-286">See the 'Monitoring' section in [Running Elasticsearch on Azure][elasticsearch-azure].</span></span>

## <a name="queue-storage"></a><span data-ttu-id="69edd-287">ストレージ</span><span class="sxs-lookup"><span data-stu-id="69edd-287">Queue storage</span></span>

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a><span data-ttu-id="69edd-288">Azure Queue Storage へのメッセージの書き込みが常に失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-288">Writing a message to Azure Queue storage fails consistently.</span></span>

<span data-ttu-id="69edd-289">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-289">**Detection**.</span></span> <span data-ttu-id="69edd-290">*N* 回再試行を試みた後も、書き込み操作がまだ失敗します。</span><span class="sxs-lookup"><span data-stu-id="69edd-290">After *N* retry attempts, the write operation still fails.</span></span>

<span data-ttu-id="69edd-291">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-291">**Recovery:**</span></span>

- <span data-ttu-id="69edd-292">ローカル キャッシュにデータを格納しておき、後でサービスが利用可能になったら、ストレージに書き込みを転送します。</span><span class="sxs-lookup"><span data-stu-id="69edd-292">Store the data in a local cache, and forward the writes to storage later, when the service becomes available.</span></span>
- <span data-ttu-id="69edd-293">セカンダリ キューを作成し、プライマリ キューが使用できない場合は、そのキューに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="69edd-293">Create a secondary queue, and write to that queue if the primary queue is unavailable.</span></span>

<span data-ttu-id="69edd-294">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-294">**Diagnostics**.</span></span> <span data-ttu-id="69edd-295">[ストレージ メトリック][storage-metrics]を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-295">Use [storage metrics][storage-metrics].</span></span>

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a><span data-ttu-id="69edd-296">特定のキューからのメッセージをアプリケーションが処理できない。</span><span class="sxs-lookup"><span data-stu-id="69edd-296">The application cannot process a particular message from the queue.</span></span>

<span data-ttu-id="69edd-297">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-297">**Detection**.</span></span> <span data-ttu-id="69edd-298">アプリケーション固有です。</span><span class="sxs-lookup"><span data-stu-id="69edd-298">Application specific.</span></span> <span data-ttu-id="69edd-299">たとえば、メッセージに無効なデータが含まれる場合や、ビジネス ロジックが何らかの理由で失敗する場合があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-299">For example, the message contains invalid data, or the business logic fails for some reason.</span></span>

<span data-ttu-id="69edd-300">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-300">**Recovery:**</span></span>

<span data-ttu-id="69edd-301">別のキューにメッセージを移動します。</span><span class="sxs-lookup"><span data-stu-id="69edd-301">Move the message to a separate queue.</span></span> <span data-ttu-id="69edd-302">そのキューで別のプロセスを実行してメッセージを調べます。</span><span class="sxs-lookup"><span data-stu-id="69edd-302">Run a separate process to examine the messages in that queue.</span></span>

<span data-ttu-id="69edd-303">Azure Service Bus メッセージング キューの使用を検討します。このキューは、この目的に対して[配信不能キュー][sb-dead-letter-queue]の機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="69edd-303">Consider using Azure Service Bus Messaging queues, which provides a [dead-letter queue][sb-dead-letter-queue] functionality for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="69edd-304">WebJobs でストレージ キューを使用している場合、WebJobs SDK は組み込みの有害メッセージ処理を提供します。</span><span class="sxs-lookup"><span data-stu-id="69edd-304">If you are using Storage queues with WebJobs, the WebJobs SDK provides built-in poison message handling.</span></span> <span data-ttu-id="69edd-305">「[Web ジョブ SDK を使用して Azure Queue Storage を操作する方法][sb-poison-message]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-305">See [How to use Azure queue storage with the WebJobs SDK][sb-poison-message].</span></span>

<span data-ttu-id="69edd-306">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-306">**Diagnostics**.</span></span> <span data-ttu-id="69edd-307">アプリケーション ログを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-307">Use application logging.</span></span>

## <a name="redis-cache"></a><span data-ttu-id="69edd-308">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="69edd-308">Redis Cache</span></span>

### <a name="reading-from-the-cache-fails"></a><span data-ttu-id="69edd-309">キャッシュからの読み取りが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-309">Reading from the cache fails.</span></span>

<span data-ttu-id="69edd-310">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-310">**Detection**.</span></span> <span data-ttu-id="69edd-311">`StackExchange.Redis.RedisConnectionException` をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-311">Catch `StackExchange.Redis.RedisConnectionException`.</span></span>

<span data-ttu-id="69edd-312">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-312">**Recovery:**</span></span>

1. <span data-ttu-id="69edd-313">一時的な障害の場合は再試行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-313">Retry on transient failures.</span></span> <span data-ttu-id="69edd-314">Azure Redis Cache は、「[Azure Redis Cache の再試行ガイドライン][redis-retry]」に従って組み込みの再試行をサポートします。</span><span class="sxs-lookup"><span data-stu-id="69edd-314">Azure Redis cache supports built-in retry through See [Redis Cache retry guidelines][redis-retry].</span></span>
2. <span data-ttu-id="69edd-315">一時的でない障害はキャッシュ ミスとして処理し、元のデータ ソースにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="69edd-315">Treat non-transient failures as a cache miss, and fall back to the original data source.</span></span>

<span data-ttu-id="69edd-316">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-316">**Diagnostics**.</span></span> <span data-ttu-id="69edd-317">[Redis Cache の診断][redis-monitor]を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-317">Use [Redis Cache diagnostics][redis-monitor].</span></span>

### <a name="writing-to-the-cache-fails"></a><span data-ttu-id="69edd-318">キャッシュへの書き込みが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-318">Writing to the cache fails.</span></span>

<span data-ttu-id="69edd-319">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-319">**Detection**.</span></span> <span data-ttu-id="69edd-320">`StackExchange.Redis.RedisConnectionException` をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-320">Catch `StackExchange.Redis.RedisConnectionException`.</span></span>

<span data-ttu-id="69edd-321">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-321">**Recovery:**</span></span>

1. <span data-ttu-id="69edd-322">一時的な障害の場合は再試行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-322">Retry on transient failures.</span></span> <span data-ttu-id="69edd-323">Azure Redis Cache は、「[Azure Redis Cache の再試行ガイドライン][redis-retry]」に従って組み込みの再試行をサポートします。</span><span class="sxs-lookup"><span data-stu-id="69edd-323">Azure Redis cache supports built-in retry through See [Redis Cache retry guidelines][redis-retry].</span></span>
2. <span data-ttu-id="69edd-324">エラーが一時的でない場合は、それを無視し、他のトランザクションが後でキャッシュに書き込めるようにします。</span><span class="sxs-lookup"><span data-stu-id="69edd-324">If the error is non-transient, ignore it and let other transactions write to the cache later.</span></span>

<span data-ttu-id="69edd-325">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-325">**Diagnostics**.</span></span> <span data-ttu-id="69edd-326">[Redis Cache の診断][redis-monitor]を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-326">Use [Redis Cache diagnostics][redis-monitor].</span></span>

## <a name="sql-database"></a><span data-ttu-id="69edd-327">SQL Database</span><span class="sxs-lookup"><span data-stu-id="69edd-327">SQL Database</span></span>

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a><span data-ttu-id="69edd-328">プライマリ リージョンのデータベースに接続できない。</span><span class="sxs-lookup"><span data-stu-id="69edd-328">Cannot connect to the database in the primary region.</span></span>

<span data-ttu-id="69edd-329">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-329">**Detection**.</span></span> <span data-ttu-id="69edd-330">接続が失敗します。</span><span class="sxs-lookup"><span data-stu-id="69edd-330">Connection fails.</span></span>

<span data-ttu-id="69edd-331">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-331">**Recovery:**</span></span>

<span data-ttu-id="69edd-332">前提条件:データベースがアクティブ geo レプリケーション用に構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-332">Prerequisite: The database must be configured for active geo-replication.</span></span> <span data-ttu-id="69edd-333">「[Azure SQL Database のアクティブ geo レプリケーション][sql-db-replication]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-333">See [SQL Database Active Geo-Replication][sql-db-replication].</span></span>

- <span data-ttu-id="69edd-334">クエリの場合は、セカンダリ レプリカから読み取ります。</span><span class="sxs-lookup"><span data-stu-id="69edd-334">For queries, read from a secondary replica.</span></span>
- <span data-ttu-id="69edd-335">挿入と更新の場合は、手動でセカンダリ レプリカにフェールオーバーします。</span><span class="sxs-lookup"><span data-stu-id="69edd-335">For inserts and updates, manually fail over to a secondary replica.</span></span> <span data-ttu-id="69edd-336">[Azure SQL Database の計画的または非計画的なフェールオーバーの開始][sql-db-failover]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-336">See [Initiate a planned or unplanned failover for Azure SQL Database][sql-db-failover].</span></span>

<span data-ttu-id="69edd-337">レプリカは異なる接続文字列を使うので、アプリケーションで接続文字列を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-337">The replica uses a different connection string, so you will need to update the connection string in your application.</span></span>

### <a name="client-runs-out-of-connections-in-the-connection-pool"></a><span data-ttu-id="69edd-338">クライアントが接続プール内の接続を使い切る。</span><span class="sxs-lookup"><span data-stu-id="69edd-338">Client runs out of connections in the connection pool.</span></span>

<span data-ttu-id="69edd-339">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-339">**Detection**.</span></span> <span data-ttu-id="69edd-340">`System.InvalidOperationException` エラーをキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-340">Catch `System.InvalidOperationException` errors.</span></span>

<span data-ttu-id="69edd-341">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-341">**Recovery:**</span></span>

- <span data-ttu-id="69edd-342">操作をやり直してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-342">Retry the operation.</span></span>
- <span data-ttu-id="69edd-343">軽減計画としては、ユース ケースごとに接続プールを分離して、1 つのユース ケースがすべての接続を独占できないようにします。</span><span class="sxs-lookup"><span data-stu-id="69edd-343">As a mitigation plan, isolate the connection pools for each use case, so that one use case can't dominate all the connections.</span></span>
- <span data-ttu-id="69edd-344">最大接続プール数を増やします。</span><span class="sxs-lookup"><span data-stu-id="69edd-344">Increase the maximum connection pools.</span></span>

<span data-ttu-id="69edd-345">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-345">**Diagnostics**.</span></span> <span data-ttu-id="69edd-346">アプリケーション ログ。</span><span class="sxs-lookup"><span data-stu-id="69edd-346">Application logs.</span></span>

### <a name="database-connection-limit-is-reached"></a><span data-ttu-id="69edd-347">データベース接続の制限に達する。</span><span class="sxs-lookup"><span data-stu-id="69edd-347">Database connection limit is reached.</span></span>

<span data-ttu-id="69edd-348">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-348">**Detection**.</span></span> <span data-ttu-id="69edd-349">Azure SQL Database では、同時実行 worker、ログイン、およびセッションの数が制限されています。</span><span class="sxs-lookup"><span data-stu-id="69edd-349">Azure SQL Database limits the number of concurrent workers, logins, and sessions.</span></span> <span data-ttu-id="69edd-350">制限は、サービス レベルに依存します。</span><span class="sxs-lookup"><span data-stu-id="69edd-350">The limits depend on the service tier.</span></span> <span data-ttu-id="69edd-351">詳細については、「[Azure SQL Database のリソース制限][sql-db-limits]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-351">For more information, see [Azure SQL Database resource limits][sql-db-limits].</span></span>

<span data-ttu-id="69edd-352">これらのエラーを検出するには、`System.Data.SqlClient.SqlException` をキャッチし、`SqlException.Number` の値で SQL エラー コードを調べます。</span><span class="sxs-lookup"><span data-stu-id="69edd-352">To detect these errors, catch `System.Data.SqlClient.SqlException` and check the value of `SqlException.Number` for the SQL error code.</span></span> <span data-ttu-id="69edd-353">関連するエラー コードの一覧については、[SQL Database クライアント アプリケーションの SQL エラー コードのデータベース接続エラーとその他の問題][sql-db-errors]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-353">For a list of relevant error codes, see [SQL error codes for SQL Database client applications: Database connection error and other issues][sql-db-errors].</span></span>

<span data-ttu-id="69edd-354">**復旧**。</span><span class="sxs-lookup"><span data-stu-id="69edd-354">**Recovery**.</span></span> <span data-ttu-id="69edd-355">これらのエラーは一時的なものと見なされるので、再試行すると問題が解決する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-355">These errors are considered transient, so retrying may resolve the issue.</span></span> <span data-ttu-id="69edd-356">常にこれらのエラーが発生する場合は、データベースの拡張を検討してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-356">If you consistently hit these errors, consider scaling the database.</span></span>

<span data-ttu-id="69edd-357">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-357">**Diagnostics**.</span></span> <span data-ttu-id="69edd-358">- [sys.event_log][sys.event_log] をクエリすると、正常なデータベース接続、接続エラー、デッドロックが返ります。</span><span class="sxs-lookup"><span data-stu-id="69edd-358">- The [sys.event_log][sys.event_log] query returns successful database connections, connection failures, and deadlocks.</span></span>

- <span data-ttu-id="69edd-359">失敗した接続に対する[警告ルール][azure-alerts]を作成します。</span><span class="sxs-lookup"><span data-stu-id="69edd-359">Create an [alert rule][azure-alerts] for failed connections.</span></span>
- <span data-ttu-id="69edd-360">[SQL Database 監査][sql-db-audit]を有効にして、失敗したログインを調べます。</span><span class="sxs-lookup"><span data-stu-id="69edd-360">Enable [SQL Database auditing][sql-db-audit] and check for failed logins.</span></span>

## <a name="service-bus-messaging"></a><span data-ttu-id="69edd-361">Service Bus メッセージング</span><span class="sxs-lookup"><span data-stu-id="69edd-361">Service Bus Messaging</span></span>

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a><span data-ttu-id="69edd-362">Service Bus キューからのメッセージの読み取りが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-362">Reading a message from a Service Bus queue fails.</span></span>

<span data-ttu-id="69edd-363">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-363">**Detection**.</span></span> <span data-ttu-id="69edd-364">クライアント SDK からの例外をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-364">Catch exceptions from the client SDK.</span></span> <span data-ttu-id="69edd-365">Service Bus 例外の基底クラスは [MessagingException][sb-messagingexception-class] です。</span><span class="sxs-lookup"><span data-stu-id="69edd-365">The base class for Service Bus exceptions is [MessagingException][sb-messagingexception-class].</span></span> <span data-ttu-id="69edd-366">エラーが一時的なものである場合は、`IsTransient` プロパティが true です。</span><span class="sxs-lookup"><span data-stu-id="69edd-366">If the error is transient, the `IsTransient` property is true.</span></span>

<span data-ttu-id="69edd-367">詳細については、「[Service Bus メッセージングの例外][sb-messaging-exceptions]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-367">For more information, see [Service Bus messaging exceptions][sb-messaging-exceptions].</span></span>

<span data-ttu-id="69edd-368">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-368">**Recovery:**</span></span>

1. <span data-ttu-id="69edd-369">一時的な障害の場合は再試行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-369">Retry on transient failures.</span></span> <span data-ttu-id="69edd-370">「[Service Bus の再試行ガイドライン][sb-retry]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-370">See [Service Bus retry guidelines][sb-retry].</span></span>
2. <span data-ttu-id="69edd-371">どの受信者にも配信できないメッセージは、"*配信不能キュー*" に入れられます。</span><span class="sxs-lookup"><span data-stu-id="69edd-371">Messages that cannot be delivered to any receiver are placed in a *dead-letter queue*.</span></span> <span data-ttu-id="69edd-372">このキューを使用して、受信できなかったメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="69edd-372">Use this queue to see which messages could not be received.</span></span> <span data-ttu-id="69edd-373">配信不能キューは自動的にクリーンアップされません。</span><span class="sxs-lookup"><span data-stu-id="69edd-373">There is no automatic cleanup of the dead-letter queue.</span></span> <span data-ttu-id="69edd-374">明示的に取得するまで、メッセージは残っています。</span><span class="sxs-lookup"><span data-stu-id="69edd-374">Messages remain there until you explicitly retrieve them.</span></span> <span data-ttu-id="69edd-375">「[Service Bus の配信不能キューの概要][sb-dead-letter-queue]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-375">See [Overview of Service Bus dead-letter queues][sb-dead-letter-queue].</span></span>

### <a name="writing-a-message-to-a-service-bus-queue-fails"></a><span data-ttu-id="69edd-376">Service Bus キューへのメッセージの書き込みが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-376">Writing a message to a Service Bus queue fails.</span></span>

<span data-ttu-id="69edd-377">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-377">**Detection**.</span></span> <span data-ttu-id="69edd-378">クライアント SDK からの例外をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="69edd-378">Catch exceptions from the client SDK.</span></span> <span data-ttu-id="69edd-379">Service Bus 例外の基底クラスは [MessagingException][sb-messagingexception-class] です。</span><span class="sxs-lookup"><span data-stu-id="69edd-379">The base class for Service Bus exceptions is [MessagingException][sb-messagingexception-class].</span></span> <span data-ttu-id="69edd-380">エラーが一時的なものである場合は、`IsTransient` プロパティが true です。</span><span class="sxs-lookup"><span data-stu-id="69edd-380">If the error is transient, the `IsTransient` property is true.</span></span>

<span data-ttu-id="69edd-381">詳細については、「[Service Bus メッセージングの例外][sb-messaging-exceptions]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-381">For more information, see [Service Bus messaging exceptions][sb-messaging-exceptions].</span></span>

<span data-ttu-id="69edd-382">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-382">**Recovery:**</span></span>

1. <span data-ttu-id="69edd-383">Service Bus クライアントは、一時的なエラーの後で自動的に再試行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-383">The Service Bus client automatically retries after transient errors.</span></span> <span data-ttu-id="69edd-384">既定では、指数バックオフを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-384">By default, it uses exponential back-off.</span></span> <span data-ttu-id="69edd-385">最大再試行回数または最大タイムアウト期間に達した後、クライアントは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="69edd-385">After the maximum retry count or maximum timeout period, the client throws an exception.</span></span> <span data-ttu-id="69edd-386">詳細については、「[Service Bus の再試行ガイドライン][sb-retry]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-386">For more information, see [Service Bus retry guidelines][sb-retry].</span></span>
2. <span data-ttu-id="69edd-387">キューのクォータを超えた場合、クライアントは [QuotaExceededException][QuotaExceededException] をスローします。</span><span class="sxs-lookup"><span data-stu-id="69edd-387">If the queue quota is exceeded, the client throws [QuotaExceededException][QuotaExceededException].</span></span> <span data-ttu-id="69edd-388">例外メッセージを見ると、さらに詳細がわかります。</span><span class="sxs-lookup"><span data-stu-id="69edd-388">The exception message gives more details.</span></span> <span data-ttu-id="69edd-389">再試行の前にキューからメッセージをいくつかドレインし、サーキット ブレーカー パターンを使用してクォータを超えている間は再試行を続けないようにすることを検討します。</span><span class="sxs-lookup"><span data-stu-id="69edd-389">Drain some messages from the queue before retrying, and consider using the Circuit Breaker pattern to avoid continued retries while the quota is exceeded.</span></span> <span data-ttu-id="69edd-390">また、[BrokeredMessage.TimeToLive] プロパティの設定が高すぎないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="69edd-390">Also, make sure the [BrokeredMessage.TimeToLive] property is not set too high.</span></span>
3. <span data-ttu-id="69edd-391">1 つのリージョン内では、[パーティション分割されたキューまたはトピック][sb-partition]を使用して回復性を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="69edd-391">Within a region, resiliency can be improved by using [partitioned queues or topics][sb-partition].</span></span> <span data-ttu-id="69edd-392">パーティション分割されていないキューまたはトピックは 1 つのメッセージング ストアに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="69edd-392">A non-partitioned queue or topic is assigned to one messaging store.</span></span> <span data-ttu-id="69edd-393">このメッセージング ストアを使用できない場合、そのキューまたはトピックに対するすべての操作が失敗します。</span><span class="sxs-lookup"><span data-stu-id="69edd-393">If this messaging store is unavailable, all operations on that queue or topic will fail.</span></span> <span data-ttu-id="69edd-394">パーティション分割されたキューまたはトピックは、複数のメッセージング ストア間で分割されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-394">A partitioned queue or topic is partitioned across multiple messaging stores.</span></span>
4. <span data-ttu-id="69edd-395">回復性を高めるには、異なるリージョンに 2 つの Service Bus 名前空間を作成し、メッセージを複製します。</span><span class="sxs-lookup"><span data-stu-id="69edd-395">For additional resiliency, create two Service Bus namespaces in different regions, and replicate the messages.</span></span> <span data-ttu-id="69edd-396">アクティブ レプリケーションまたはパッシブ レプリケーションを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="69edd-396">You can use either active replication or passive replication.</span></span>

    - <span data-ttu-id="69edd-397">アクティブ レプリケーション:クライアントによって、両方のキューにすべてのメッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-397">Active replication: The client sends every message to both queues.</span></span> <span data-ttu-id="69edd-398">受信側は、両方のキューでリッスンします。</span><span class="sxs-lookup"><span data-stu-id="69edd-398">The receiver listens on both queues.</span></span> <span data-ttu-id="69edd-399">メッセージに一意識別子でタグを付け、クライアントが重複したメッセージを破棄できるようにします。</span><span class="sxs-lookup"><span data-stu-id="69edd-399">Tag messages with a unique identifier, so the client can discard duplicate messages.</span></span>
    - <span data-ttu-id="69edd-400">パッシブ レプリケーション:クライアントによって、メッセージが 1 つのキューに送信されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-400">Passive replication: The client sends the message to one queue.</span></span> <span data-ttu-id="69edd-401">エラーが発生した場合、クライアントは他のキューにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="69edd-401">If there is an error, the client falls back to the other queue.</span></span> <span data-ttu-id="69edd-402">受信側は、両方のキューでリッスンします。</span><span class="sxs-lookup"><span data-stu-id="69edd-402">The receiver listens on both queues.</span></span> <span data-ttu-id="69edd-403">この方法では、送信される重複したメッセージの数が減ります。</span><span class="sxs-lookup"><span data-stu-id="69edd-403">This approach reduces the number of duplicate messages that are sent.</span></span> <span data-ttu-id="69edd-404">ただし、それでも受信側は重複したメッセージを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-404">However, the receiver must still handle duplicate messages.</span></span>

    <span data-ttu-id="69edd-405">詳細については、[GeoReplication サンプル][sb-georeplication-sample]に関するページ、および「[Service Bus の障害および災害に対するアプリケーションの保護のベスト プラクティス](/azure/service-bus-messaging/service-bus-outages-disasters/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-405">For more information, see [GeoReplication sample][sb-georeplication-sample] and [Best practices for insulating applications against Service Bus outages and disasters](/azure/service-bus-messaging/service-bus-outages-disasters/).</span></span>

### <a name="duplicate-message"></a><span data-ttu-id="69edd-406">重複メッセージ。</span><span class="sxs-lookup"><span data-stu-id="69edd-406">Duplicate message.</span></span>

<span data-ttu-id="69edd-407">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-407">**Detection**.</span></span> <span data-ttu-id="69edd-408">メッセージの `MessageId` プロパティと `DeliveryCount` プロパティを調べます。</span><span class="sxs-lookup"><span data-stu-id="69edd-408">Examine the `MessageId` and `DeliveryCount` properties of the message.</span></span>

<span data-ttu-id="69edd-409">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-409">**Recovery:**</span></span>

- <span data-ttu-id="69edd-410">可能であれば、メッセージ処理操作をべき等になるように設計します。</span><span class="sxs-lookup"><span data-stu-id="69edd-410">If possible, design your message processing operations to be idempotent.</span></span> <span data-ttu-id="69edd-411">できない場合は、既に処理されたメッセージのメッセージ ID を保存しておき、メッセージを処理する前に ID を確認します。</span><span class="sxs-lookup"><span data-stu-id="69edd-411">Otherwise, store message IDs of messages that are already processed, and check the ID before processing a message.</span></span>
- <span data-ttu-id="69edd-412">`RequiresDuplicateDetection` を true に設定してキューを作成することで、重複の検出を有効にします。</span><span class="sxs-lookup"><span data-stu-id="69edd-412">Enable duplicate detection, by creating the queue with `RequiresDuplicateDetection` set to true.</span></span> <span data-ttu-id="69edd-413">このように設定すると、Service Bus は、前のメッセージと同じ `MessageId` で送信されたメッセージを自動的に削除します。</span><span class="sxs-lookup"><span data-stu-id="69edd-413">With this setting, Service Bus automatically deletes any message that is sent with the same `MessageId` as a previous message.</span></span>  <span data-ttu-id="69edd-414">以下の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-414">Note the following:</span></span>

  - <span data-ttu-id="69edd-415">この設定は、重複するメッセージがキューに格納されるのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="69edd-415">This setting prevents duplicate messages from being put into the queue.</span></span> <span data-ttu-id="69edd-416">受信側が同じメッセージを複数回処理することを防ぐものではありません。</span><span class="sxs-lookup"><span data-stu-id="69edd-416">It doesn't prevent a receiver from processing the same message more than once.</span></span>
  - <span data-ttu-id="69edd-417">重複データの検出には時間枠があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-417">Duplicate detection has a time window.</span></span> <span data-ttu-id="69edd-418">この期間を超えて重複が送信された場合は検出されません。</span><span class="sxs-lookup"><span data-stu-id="69edd-418">If a duplicate is sent beyond this window, it won't be detected.</span></span>

<span data-ttu-id="69edd-419">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-419">**Diagnostics**.</span></span> <span data-ttu-id="69edd-420">重複するメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="69edd-420">Log duplicated messages.</span></span>

### <a name="the-application-cant-process-a-particular-message-from-the-queue"></a><span data-ttu-id="69edd-421">アプリケーションでキューからの特定のメッセージを処理できない。</span><span class="sxs-lookup"><span data-stu-id="69edd-421">The application can't process a particular message from the queue.</span></span>

<span data-ttu-id="69edd-422">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-422">**Detection**.</span></span> <span data-ttu-id="69edd-423">アプリケーション固有です。</span><span class="sxs-lookup"><span data-stu-id="69edd-423">Application specific.</span></span> <span data-ttu-id="69edd-424">たとえば、メッセージに無効なデータが含まれる場合や、ビジネス ロジックが何らかの理由で失敗する場合があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-424">For example, the message contains invalid data, or the business logic fails for some reason.</span></span>

<span data-ttu-id="69edd-425">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-425">**Recovery:**</span></span>

<span data-ttu-id="69edd-426">2 つの障害モードを考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-426">There are two failure modes to consider.</span></span>

- <span data-ttu-id="69edd-427">受信側が障害を検出します。</span><span class="sxs-lookup"><span data-stu-id="69edd-427">The receiver detects the failure.</span></span> <span data-ttu-id="69edd-428">この場合、配信不能キューにメッセージを移動します。</span><span class="sxs-lookup"><span data-stu-id="69edd-428">In this case, move the message to the dead-letter queue.</span></span> <span data-ttu-id="69edd-429">後で、別のプロセスを実行して配信不能キューのメッセージを調べます。</span><span class="sxs-lookup"><span data-stu-id="69edd-429">Later, run a separate process to examine the messages in the dead-letter queue.</span></span>
- <span data-ttu-id="69edd-430">受信側が、メッセージの処理の途中で失敗します (ハンドルされない例外など)。</span><span class="sxs-lookup"><span data-stu-id="69edd-430">The receiver fails in the middle of processing the message &mdash; for example, due to an unhandled exception.</span></span> <span data-ttu-id="69edd-431">このケースを処理するには、`PeekLock` モードを使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-431">To handle this case, use `PeekLock` mode.</span></span> <span data-ttu-id="69edd-432">このモードでは、ロックの期限が切れると、他の受信者がメッセージを処理できるようになります。</span><span class="sxs-lookup"><span data-stu-id="69edd-432">In this mode, if the lock expires, the message becomes available to other receivers.</span></span> <span data-ttu-id="69edd-433">メッセージが最大配信数または Time-To-Live を超えた場合、メッセージは配信不能キューに自動的に移動されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-433">If the message exceeds the maximum delivery count or the time-to-live, the message is automatically moved to the dead-letter queue.</span></span>

<span data-ttu-id="69edd-434">詳細については、「[Service Bus の配信不能キューの概要][sb-dead-letter-queue]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-434">For more information, see [Overview of Service Bus dead-letter queues][sb-dead-letter-queue].</span></span>

<span data-ttu-id="69edd-435">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-435">**Diagnostics**.</span></span> <span data-ttu-id="69edd-436">アプリケーションは、配信不能キューにメッセージを移動したときは常に、アプリケーション ログにイベントを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="69edd-436">Whenever the application moves a message to the dead-letter queue, write an event to the application logs.</span></span>

## <a name="service-fabric"></a><span data-ttu-id="69edd-437">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="69edd-437">Service Fabric</span></span>

### <a name="a-request-to-a-service-fails"></a><span data-ttu-id="69edd-438">サービスに対する要求が失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-438">A request to a service fails.</span></span>

<span data-ttu-id="69edd-439">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-439">**Detection**.</span></span> <span data-ttu-id="69edd-440">サービスがエラーを返します。</span><span class="sxs-lookup"><span data-stu-id="69edd-440">The service returns an error.</span></span>

<span data-ttu-id="69edd-441">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-441">**Recovery:**</span></span>

- <span data-ttu-id="69edd-442">プロキシをもう一度検索し (`ServiceProxy` または `ActorProxy`)、サービス/アクター メソッドを再度呼び出します。</span><span class="sxs-lookup"><span data-stu-id="69edd-442">Locate a proxy again (`ServiceProxy` or `ActorProxy`) and call the service/actor method again.</span></span>
- <span data-ttu-id="69edd-443">**ステートフル サービス**。</span><span class="sxs-lookup"><span data-stu-id="69edd-443">**Stateful service**.</span></span> <span data-ttu-id="69edd-444">トランザクションの信頼できるコレクションに操作をラップします。</span><span class="sxs-lookup"><span data-stu-id="69edd-444">Wrap operations on reliable collections in a transaction.</span></span> <span data-ttu-id="69edd-445">エラーがある場合、トランザクションはロールバックされます。</span><span class="sxs-lookup"><span data-stu-id="69edd-445">If there is an error, the transaction will be rolled back.</span></span> <span data-ttu-id="69edd-446">要求は、キューからプルされた場合は再度処理されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-446">The request, if pulled from a queue, will be processed again.</span></span>
- <span data-ttu-id="69edd-447">**ステートレス サービス**。</span><span class="sxs-lookup"><span data-stu-id="69edd-447">**Stateless service**.</span></span> <span data-ttu-id="69edd-448">サービスが外部ストアにデータを保存する場合は、すべての操作をべき等にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-448">If the service persists data to an external store, all operations need to be idempotent.</span></span>

<span data-ttu-id="69edd-449">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-449">**Diagnostics**.</span></span> <span data-ttu-id="69edd-450">アプリケーション ログ</span><span class="sxs-lookup"><span data-stu-id="69edd-450">Application log</span></span>

### <a name="service-fabric-node-is-shut-down"></a><span data-ttu-id="69edd-451">Service Fabric ノードがシャットダウンされる。</span><span class="sxs-lookup"><span data-stu-id="69edd-451">Service Fabric node is shut down.</span></span>

<span data-ttu-id="69edd-452">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-452">**Detection**.</span></span> <span data-ttu-id="69edd-453">キャンセル トークンが、サービスの `RunAsync` メソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="69edd-453">A cancellation token is passed to the service's `RunAsync` method.</span></span> <span data-ttu-id="69edd-454">Service Fabric は、ノードをシャットダウンする前に、タスクをキャンセルします。</span><span class="sxs-lookup"><span data-stu-id="69edd-454">Service Fabric cancels the task before shutting down the node.</span></span>

<span data-ttu-id="69edd-455">**復旧**。</span><span class="sxs-lookup"><span data-stu-id="69edd-455">**Recovery**.</span></span> <span data-ttu-id="69edd-456">キャンセル トークンを使用して、シャットダウンを検出します。</span><span class="sxs-lookup"><span data-stu-id="69edd-456">Use the cancellation token to detect shutdown.</span></span> <span data-ttu-id="69edd-457">Service Fabric がキャンセルを要求しているときは、すべての作業を終了して、可能な限り早く  `RunAsync` を終了します。</span><span class="sxs-lookup"><span data-stu-id="69edd-457">When Service Fabric requests cancellation, finish any work and exit `RunAsync` as quickly as possible.</span></span>

<span data-ttu-id="69edd-458">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-458">**Diagnostics**.</span></span> <span data-ttu-id="69edd-459">アプリケーション ログ</span><span class="sxs-lookup"><span data-stu-id="69edd-459">Application logs</span></span>

## <a name="storage"></a><span data-ttu-id="69edd-460">Storage</span><span class="sxs-lookup"><span data-stu-id="69edd-460">Storage</span></span>

### <a name="writing-data-to-azure-storage-fails"></a><span data-ttu-id="69edd-461">Azure Storage へのデータの書き込みが失敗する</span><span class="sxs-lookup"><span data-stu-id="69edd-461">Writing data to Azure Storage fails</span></span>

<span data-ttu-id="69edd-462">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-462">**Detection**.</span></span> <span data-ttu-id="69edd-463">クライアントは、書き込み時にエラーを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="69edd-463">The client receives errors when writing.</span></span>

<span data-ttu-id="69edd-464">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-464">**Recovery:**</span></span>

1. <span data-ttu-id="69edd-465">操作を再試行し、一時的な障害から復旧します。</span><span class="sxs-lookup"><span data-stu-id="69edd-465">Retry the operation, to recover from transient failures.</span></span> <span data-ttu-id="69edd-466">クライアント SDK の[再試行ポリシー][Storage.RetryPolicies]がこれを自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="69edd-466">The [retry policy][Storage.RetryPolicies] in the client SDK handles this automatically.</span></span>
2. <span data-ttu-id="69edd-467">ストレージに対する過剰な負荷を回避するため、サーキット ブレーカー パターンを実装します。</span><span class="sxs-lookup"><span data-stu-id="69edd-467">Implement the Circuit Breaker pattern to avoid overwhelming storage.</span></span>
3. <span data-ttu-id="69edd-468">N 回再試行が失敗した場合は、正常なフォールバックを実行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-468">If N retry attempts fail, perform a graceful fallback.</span></span> <span data-ttu-id="69edd-469">例: </span><span class="sxs-lookup"><span data-stu-id="69edd-469">For example:</span></span>

   - <span data-ttu-id="69edd-470">ローカル キャッシュにデータを格納しておき、後でサービスが利用可能になったら、ストレージに書き込みを転送します。</span><span class="sxs-lookup"><span data-stu-id="69edd-470">Store the data in a local cache, and forward the writes to storage later, when the service becomes available.</span></span>
   - <span data-ttu-id="69edd-471">書き込みアクションがトランザクション スコープであった場合は、トランザクションを補正します。</span><span class="sxs-lookup"><span data-stu-id="69edd-471">If the write action was in a transactional scope, compensate the transaction.</span></span>

<span data-ttu-id="69edd-472">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-472">**Diagnostics**.</span></span> <span data-ttu-id="69edd-473">[ストレージ メトリック][storage-metrics]を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-473">Use [storage metrics][storage-metrics].</span></span>

### <a name="reading-data-from-azure-storage-fails"></a><span data-ttu-id="69edd-474">Azure Storage からのデータの読み取りが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-474">Reading data from Azure Storage fails.</span></span>

<span data-ttu-id="69edd-475">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-475">**Detection**.</span></span> <span data-ttu-id="69edd-476">クライアントは、読み取り時にエラーを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="69edd-476">The client receives errors when reading.</span></span>

<span data-ttu-id="69edd-477">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-477">**Recovery:**</span></span>

1. <span data-ttu-id="69edd-478">操作を再試行し、一時的な障害から復旧します。</span><span class="sxs-lookup"><span data-stu-id="69edd-478">Retry the operation, to recover from transient failures.</span></span> <span data-ttu-id="69edd-479">クライアント SDK の[再試行ポリシー][Storage.RetryPolicies]がこれを自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="69edd-479">The [retry policy][Storage.RetryPolicies] in the client SDK handles this automatically.</span></span>
2. <span data-ttu-id="69edd-480">RA-GRS ストレージでは、プライマリ エンドポイントからの読み取りが失敗した場合、セカンダリ エンドポイントから読み取りを再試行してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-480">For RA-GRS storage, if reading from the primary endpoint fails, try reading from the secondary endpoint.</span></span> <span data-ttu-id="69edd-481">クライアント SDK はこれを自動的に処理できます。</span><span class="sxs-lookup"><span data-stu-id="69edd-481">The client SDK can handle this automatically.</span></span> <span data-ttu-id="69edd-482">「[Azure Storage のレプリケーション][storage-replication]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-482">See [Azure Storage replication][storage-replication].</span></span>
3. <span data-ttu-id="69edd-483">再試行が *N* 回失敗する場合は、フォールバック アクションを実行して正常にデグレードします。</span><span class="sxs-lookup"><span data-stu-id="69edd-483">If *N* retry attempts fail, take a fallback action to degrade gracefully.</span></span> <span data-ttu-id="69edd-484">たとえば、製品の画像をストレージから取得できない場合は、汎用的なプレースホルダー画像を表示します。</span><span class="sxs-lookup"><span data-stu-id="69edd-484">For example, if a product image can't be retrieved from storage, show a generic placeholder image.</span></span>

<span data-ttu-id="69edd-485">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-485">**Diagnostics**.</span></span> <span data-ttu-id="69edd-486">[ストレージ メトリック][storage-metrics]を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-486">Use [storage metrics][storage-metrics].</span></span>

## <a name="virtual-machine"></a><span data-ttu-id="69edd-487">仮想マシン</span><span class="sxs-lookup"><span data-stu-id="69edd-487">Virtual machine</span></span>

### <a name="connection-to-a-backend-vm-fails"></a><span data-ttu-id="69edd-488">バックエンド VM への接続が失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-488">Connection to a backend VM fails.</span></span>

<span data-ttu-id="69edd-489">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-489">**Detection**.</span></span> <span data-ttu-id="69edd-490">ネットワーク接続エラー。</span><span class="sxs-lookup"><span data-stu-id="69edd-490">Network connection errors.</span></span>

<span data-ttu-id="69edd-491">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-491">**Recovery:**</span></span>

- <span data-ttu-id="69edd-492">ロード バランサーの背後の可用性セットに、少なくとも 2 つのバックエンド VM をデプロイします。</span><span class="sxs-lookup"><span data-stu-id="69edd-492">Deploy at least two backend VMs in an availability set, behind a load balancer.</span></span>
- <span data-ttu-id="69edd-493">接続エラーが一時的な場合は、TCP が正常にメッセージの送信を再試行することがあります。</span><span class="sxs-lookup"><span data-stu-id="69edd-493">If the connection error is transient, sometimes TCP will successfully retry sending the message.</span></span>
- <span data-ttu-id="69edd-494">アプリケーションに再試行ポリシーを実装します。</span><span class="sxs-lookup"><span data-stu-id="69edd-494">Implement a retry policy in the application.</span></span>
- <span data-ttu-id="69edd-495">永続的なエラーまたは一時的でないエラーの場合は、[サーキット ブレーカー][circuit-breaker] パターンを実装します。</span><span class="sxs-lookup"><span data-stu-id="69edd-495">For persistent or non-transient errors, implement the [Circuit Breaker][circuit-breaker] pattern.</span></span>
- <span data-ttu-id="69edd-496">呼び出し元の VM がネットワーク送信制限を超えた場合は、送信キューがいっぱいになります。</span><span class="sxs-lookup"><span data-stu-id="69edd-496">If the calling VM exceeds its network egress limit, the outbound queue will fill up.</span></span> <span data-ttu-id="69edd-497">送信キューが常にいっぱいになっている場合は、スケールアウトを検討してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-497">If the outbound queue is consistently full, consider scaling out.</span></span>

<span data-ttu-id="69edd-498">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-498">**Diagnostics**.</span></span> <span data-ttu-id="69edd-499">サービスの境界でイベントのログを記録します。</span><span class="sxs-lookup"><span data-stu-id="69edd-499">Log events at service boundaries.</span></span>

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a><span data-ttu-id="69edd-500">VM インスタンスが使用不可または異常になる。</span><span class="sxs-lookup"><span data-stu-id="69edd-500">VM instance becomes unavailable or unhealthy.</span></span>

<span data-ttu-id="69edd-501">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-501">**Detection**.</span></span> <span data-ttu-id="69edd-502">VM インスタンスが正常かどうかを通知する Load Balancer [正常性プローブ][lb-probe]を構成します。</span><span class="sxs-lookup"><span data-stu-id="69edd-502">Configure a Load Balancer [health probe][lb-probe] that signals whether the VM instance is healthy.</span></span> <span data-ttu-id="69edd-503">プローブは、重要な機能が正しく応答しているかどうかを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-503">The probe should check whether critical functions are responding correctly.</span></span>

<span data-ttu-id="69edd-504">**復旧**。</span><span class="sxs-lookup"><span data-stu-id="69edd-504">**Recovery**.</span></span> <span data-ttu-id="69edd-505">アプリケーション層ごとに、複数の VM インスタンスを同じ可用性セットに格納し、VM の前にロード バランサーを配置します。</span><span class="sxs-lookup"><span data-stu-id="69edd-505">For each application tier, put multiple VM instances into the same availability set, and place a load balancer in front of the VMs.</span></span> <span data-ttu-id="69edd-506">正常性プローブが失敗した場合、Load Balancer は異常なインスタンスへの新しい接続の送信を停止します。</span><span class="sxs-lookup"><span data-stu-id="69edd-506">If the health probe fails, the Load Balancer stops sending new connections to the unhealthy instance.</span></span>

<span data-ttu-id="69edd-507">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-507">**Diagnostics**.</span></span> <span data-ttu-id="69edd-508">- Load Balancer の [Log Analytics][lb-monitor] を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-508">- Use Load Balancer [log analytics][lb-monitor].</span></span>

- <span data-ttu-id="69edd-509">すべての正常性監視エンドポイントを監視するように、監視システムを構成します。</span><span class="sxs-lookup"><span data-stu-id="69edd-509">Configure your monitoring system to monitor all of the health monitoring endpoints.</span></span>

### <a name="operator-accidentally-shuts-down-a-vm"></a><span data-ttu-id="69edd-510">オペレーターが誤って VM をシャットダウンした。</span><span class="sxs-lookup"><span data-stu-id="69edd-510">Operator accidentally shuts down a VM.</span></span>

<span data-ttu-id="69edd-511">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-511">**Detection**.</span></span> <span data-ttu-id="69edd-512">該当なし</span><span class="sxs-lookup"><span data-stu-id="69edd-512">N/A</span></span>

<span data-ttu-id="69edd-513">**復旧**。</span><span class="sxs-lookup"><span data-stu-id="69edd-513">**Recovery**.</span></span> <span data-ttu-id="69edd-514">リソース ロックを `ReadOnly` レベルに設定します。</span><span class="sxs-lookup"><span data-stu-id="69edd-514">Set a resource lock with `ReadOnly` level.</span></span> <span data-ttu-id="69edd-515">詳細については、[Azure Resource Manager でのリソースのロック][rm-locks]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-515">See [Lock resources with Azure Resource Manager][rm-locks].</span></span>

<span data-ttu-id="69edd-516">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-516">**Diagnostics**.</span></span> <span data-ttu-id="69edd-517">[Azure Activity Logs][azure-activity-logs] を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-517">Use [Azure Activity Logs][azure-activity-logs].</span></span>

## <a name="webjobs"></a><span data-ttu-id="69edd-518">WebJobs</span><span class="sxs-lookup"><span data-stu-id="69edd-518">WebJobs</span></span>

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a><span data-ttu-id="69edd-519">SCM ホストがアイドル状態のとき、継続的なジョブが実行を停止する。</span><span class="sxs-lookup"><span data-stu-id="69edd-519">Continuous job stops running when the SCM host is idle.</span></span>

<span data-ttu-id="69edd-520">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-520">**Detection**.</span></span> <span data-ttu-id="69edd-521">WebJob 関数にキャンセル トークンを渡します。</span><span class="sxs-lookup"><span data-stu-id="69edd-521">Pass a cancellation token to the WebJob function.</span></span> <span data-ttu-id="69edd-522">詳細については、[グレースフル シャットダウン][web-jobs-shutdown]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-522">For more information, see [Graceful shutdown][web-jobs-shutdown].</span></span>

<span data-ttu-id="69edd-523">**復旧**。</span><span class="sxs-lookup"><span data-stu-id="69edd-523">**Recovery**.</span></span> <span data-ttu-id="69edd-524">Web アプリで `Always On` の設定を有効にします。</span><span class="sxs-lookup"><span data-stu-id="69edd-524">Enable the `Always On` setting in the web app.</span></span> <span data-ttu-id="69edd-525">詳細については、[WebJobs でのバックグラウンド タスクの実行][web-jobs]に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-525">For more information, see [Run Background tasks with WebJobs][web-jobs].</span></span>

## <a name="application-design"></a><span data-ttu-id="69edd-526">アプリケーションの設計</span><span class="sxs-lookup"><span data-stu-id="69edd-526">Application design</span></span>

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a><span data-ttu-id="69edd-527">アプリケーションが受信要求のスパイクを処理できない。</span><span class="sxs-lookup"><span data-stu-id="69edd-527">Application can't handle a spike in incoming requests.</span></span>

<span data-ttu-id="69edd-528">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-528">**Detection**.</span></span> <span data-ttu-id="69edd-529">アプリケーションによって異なります。</span><span class="sxs-lookup"><span data-stu-id="69edd-529">Depends on the application.</span></span> <span data-ttu-id="69edd-530">一般的な現象:</span><span class="sxs-lookup"><span data-stu-id="69edd-530">Typical symptoms:</span></span>

- <span data-ttu-id="69edd-531">Web サイトが HTTP 5xx エラー コードを返し始めます。</span><span class="sxs-lookup"><span data-stu-id="69edd-531">The website starts returning HTTP 5xx error codes.</span></span>
- <span data-ttu-id="69edd-532">データベースやストレージなどの依存サービスが、要求の調整を始めます。</span><span class="sxs-lookup"><span data-stu-id="69edd-532">Dependent services, such as database or storage, start to throttle requests.</span></span> <span data-ttu-id="69edd-533">サービスに応じて、HTTP 429 (要求数が多すぎる) などの HTTP エラーを探します。</span><span class="sxs-lookup"><span data-stu-id="69edd-533">Look for HTTP errors such as HTTP 429 (Too Many Requests), depending on the service.</span></span>
- <span data-ttu-id="69edd-534">HTTP キューが長くなりすぎます。</span><span class="sxs-lookup"><span data-stu-id="69edd-534">HTTP queue length grows.</span></span>

<span data-ttu-id="69edd-535">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-535">**Recovery:**</span></span>

- <span data-ttu-id="69edd-536">増加した負荷を処理するためにスケールアウトします。</span><span class="sxs-lookup"><span data-stu-id="69edd-536">Scale out to handle increased load.</span></span>
- <span data-ttu-id="69edd-537">エラーを軽減し、障害が連鎖してアプリケーション全体が停止するのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="69edd-537">Mitigate failures to avoid having cascading failures disrupt the entire application.</span></span> <span data-ttu-id="69edd-538">次のような軽減戦略があります。</span><span class="sxs-lookup"><span data-stu-id="69edd-538">Mitigation strategies include:</span></span>

  - <span data-ttu-id="69edd-539">[調整パターン][throttling-pattern]を実装して、バックエンド システムの過負荷を回避します。</span><span class="sxs-lookup"><span data-stu-id="69edd-539">Implement the [Throttling pattern][throttling-pattern] to avoid overwhelming backend systems.</span></span>
  - <span data-ttu-id="69edd-540">[キュー ベースの負荷の平準化][queue-based-load-leveling]を使用して、要求をバッファリングし、適切な速度で処理します。</span><span class="sxs-lookup"><span data-stu-id="69edd-540">Use [queue-based load leveling][queue-based-load-leveling] to buffer requests and process them at an appropriate pace.</span></span>
  - <span data-ttu-id="69edd-541">特定のクライアントを優先します。</span><span class="sxs-lookup"><span data-stu-id="69edd-541">Prioritize certain clients.</span></span> <span data-ttu-id="69edd-542">たとえば、アプリケーションに無料レベルと有料レベルがある場合、無料レベルの顧客を調整し、有料レベルの顧客は調整しないようにします。</span><span class="sxs-lookup"><span data-stu-id="69edd-542">For example, if the application has free and paid tiers, throttle customers on the free tier, but not paid customers.</span></span> <span data-ttu-id="69edd-543">「[Priority Queue パターン][priority-queue-pattern]」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69edd-543">See [Priority queue pattern][priority-queue-pattern].</span></span>

<span data-ttu-id="69edd-544">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-544">**Diagnostics**.</span></span> <span data-ttu-id="69edd-545">[App Service 診断ログ][app-service-logging]を使用します。</span><span class="sxs-lookup"><span data-stu-id="69edd-545">Use [App Service diagnostic logging][app-service-logging].</span></span> <span data-ttu-id="69edd-546">[Azure Log Analytics][azure-log-analytics]、[Application Insights][app-insights]、[New Relic][new-relic] などのサービスを使用して、診断ログを理解します。</span><span class="sxs-lookup"><span data-stu-id="69edd-546">Use a service such as [Azure Log Analytics][azure-log-analytics], [Application Insights][app-insights], or [New Relic][new-relic] to help understand the diagnostic logs.</span></span>

### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a><span data-ttu-id="69edd-547">ワークフローまたは分散トランザクションの操作の 1 つが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-547">One of the operations in a workflow or distributed transaction fails.</span></span>

<span data-ttu-id="69edd-548">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-548">**Detection**.</span></span> <span data-ttu-id="69edd-549">*N* 回再試行した後で、まだ失敗します。</span><span class="sxs-lookup"><span data-stu-id="69edd-549">After *N* retry attempts, it still fails.</span></span>

<span data-ttu-id="69edd-550">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-550">**Recovery:**</span></span>

- <span data-ttu-id="69edd-551">軽減計画としては、[スケジューラー エージェント スーパーバイザー][scheduler-agent-supervisor] パターンを実装して、ワークフロー全体を管理します。</span><span class="sxs-lookup"><span data-stu-id="69edd-551">As a mitigation plan, implement the [Scheduler Agent Supervisor][scheduler-agent-supervisor] pattern to manage the entire workflow.</span></span>
- <span data-ttu-id="69edd-552">タイムアウト時に再試行しないでください。</span><span class="sxs-lookup"><span data-stu-id="69edd-552">Don't retry on timeouts.</span></span> <span data-ttu-id="69edd-553">このエラーの場合、成功する確率は高くありません。</span><span class="sxs-lookup"><span data-stu-id="69edd-553">There is a low success rate for this error.</span></span>
- <span data-ttu-id="69edd-554">後で再試行するため、キューに作業を格納します。</span><span class="sxs-lookup"><span data-stu-id="69edd-554">Queue work, in order to retry later.</span></span>

<span data-ttu-id="69edd-555">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-555">**Diagnostics**.</span></span> <span data-ttu-id="69edd-556">補正アクションなど、(成功と失敗を含む) すべての操作をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="69edd-556">Log all operations (successful and failed), including compensating actions.</span></span> <span data-ttu-id="69edd-557">相関 ID を使用して、同じトランザクション内のすべての操作を追跡できるようにします。</span><span class="sxs-lookup"><span data-stu-id="69edd-557">Use correlation IDs, so that you can track all operations within the same transaction.</span></span>

### <a name="a-call-to-a-remote-service-fails"></a><span data-ttu-id="69edd-558">リモート サービスの呼び出しが失敗する。</span><span class="sxs-lookup"><span data-stu-id="69edd-558">A call to a remote service fails.</span></span>

<span data-ttu-id="69edd-559">**検出**。</span><span class="sxs-lookup"><span data-stu-id="69edd-559">**Detection**.</span></span> <span data-ttu-id="69edd-560">HTTP エラー コード。</span><span class="sxs-lookup"><span data-stu-id="69edd-560">HTTP error code.</span></span>

<span data-ttu-id="69edd-561">**復旧:**</span><span class="sxs-lookup"><span data-stu-id="69edd-561">**Recovery:**</span></span>

1. <span data-ttu-id="69edd-562">一時的な障害の場合は再試行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-562">Retry on transient failures.</span></span>
2. <span data-ttu-id="69edd-563">*N* 回試みても呼び出しが失敗する場合は、フォールバック アクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="69edd-563">If the call fails after *N* attempts, take a fallback action.</span></span> <span data-ttu-id="69edd-564">(アプリケーション固有。)</span><span class="sxs-lookup"><span data-stu-id="69edd-564">(Application specific.)</span></span>
3. <span data-ttu-id="69edd-565">[サーキット ブレーカー パターン][circuit-breaker]を実装し、障害の連鎖を回避します。</span><span class="sxs-lookup"><span data-stu-id="69edd-565">Implement the [Circuit Breaker pattern][circuit-breaker] to avoid cascading failures.</span></span>

<span data-ttu-id="69edd-566">**診断**</span><span class="sxs-lookup"><span data-stu-id="69edd-566">**Diagnostics**.</span></span> <span data-ttu-id="69edd-567">リモート呼び出しのすべてのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="69edd-567">Log all remote call failures.</span></span>

## <a name="next-steps"></a><span data-ttu-id="69edd-568">次の手順</span><span class="sxs-lookup"><span data-stu-id="69edd-568">Next steps</span></span>

<span data-ttu-id="69edd-569">FMA プロセスについては、「[Resilience by design for cloud services][resilience-by-design-pdf]」(クラウド サービス向けの設計による回復力) を参照してください (PDF のダウンロード)。</span><span class="sxs-lookup"><span data-stu-id="69edd-569">For more information about the FMA process, see [Resilience by design for cloud services][resilience-by-design-pdf] (PDF download).</span></span>

<!-- markdownlint-enable MD026 -->

<!-- links -->

[api-management]: /azure/api-management/
[api-management-throttling]: /azure/api-management/api-management-sample-flexible-throttling/
[app-insights]: /azure/application-insights/app-insights-overview/
[app-insights-web-apps]: /azure/application-insights/app-insights-azure-web-apps/
[app-service-configure]: /azure/app-service-web/web-sites-configure/
[app-service-logging]: /azure/app-service-web/web-sites-enable-diagnostic-log/
[app-service-slots]: /azure/app-service-web/web-sites-staged-publishing/
[auto-rest-client-retry]: https://github.com/Azure/autorest/tree/master/docs
[azure-activity-logs]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs/
[azure-alerts]: /azure/monitoring-and-diagnostics/insights-alerts-portal/
[azure-log-analytics]: /azure/log-analytics/log-analytics-overview/
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: https://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cosmosdb-multi-region]: /azure/cosmos-db/tutorial-global-distribution-sql-api
[elasticsearch-azure]: ../elasticsearch/index.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: /azure/load-balancer/load-balancer-monitor-log/
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview/#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: ../reference-architectures/app-service-web-app/basic-web-app.md
[redis-monitor]: /azure/redis-cache/cache-how-to-monitor/
[redis-retry]: ../best-practices/retry-service-specific.md#azure-redis-cache
[resilience-by-design-pdf]: https://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: /azure/azure-resource-manager/resource-group-lock-resources/
[sb-dead-letter-queue]: /azure/service-bus-messaging/service-bus-dead-letter-queues/
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: /azure/service-bus-messaging/service-bus-messaging-exceptions/
[sb-outages]: /azure/service-bus-messaging/service-bus-outages-disasters/#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: /azure/service-bus-messaging/service-bus-partitioning/
[sb-poison-message]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#poison
[sb-retry]: ../best-practices/retry-service-specific.md#service-bus
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: /azure/search/search-traffic-analytics/
[sql-db-audit]: /azure/sql-database/sql-database-auditing-get-started/
[sql-db-errors]: /azure/sql-database/sql-database-develop-error-messages/#resource-governance-errors
[sql-db-failover]: /azure/sql-database/sql-database-geo-replication-failover-portal/
[sql-db-limits]: /azure/sql-database/sql-database-resource-limits/
[sql-db-replication]: /azure/sql-database/sql-database-geo-replication-overview/
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: /azure/storage/storage-redundancy/
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: /azure/app-service-web/web-sites-create-web-jobs/
[web-jobs-shutdown]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful
