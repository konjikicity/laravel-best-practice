---
theme: nord
background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
download: true
colorSchema: 'dark'
layout: center
highlighter: shiki
title: 'Laravelベストプラクティス勉強会'
lineNumbers: true
transition: fade-out
mdc: true
fonts:
  # basically the text
  sans: 'Noto Sans JP'
  # use with `font-serif` css class from windicss
  serif: 'Robot Slab'
  # for code blocks, inline code, etc.
  mono: 'Fira Code'
---

<h1 class="font-bold">Laravelベストプラクティス勉強会</h1>

<p claas="pt-12">
受託ナレッジシェア by Ond
</p>

---
transition: fade-out
---

<h1 class="font-bold">
    目次
</h1>

<div class="pt-6"></div>

<p class="font-bold">
1. PHPとは？
</p>

<div class="pt-6"></div>

<p class="font-bold">
2. Laravelとは？
</p>

<div class="pt-6"></div>

<p class="font-bold">
3. Laravelベストプラクティス
</p>

<div class="pt-6"></div>

<p class="font-bold">
4. 個人的に大事だと思うこと
</p>

<div class="pt-6"></div>

<p class="font-bold">
5. おわりに
</p>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">PHPとは</h1>

---
transition: fade-out
---

<logos-php class="mt-4 w-20 h-10" />
<h3 class="py-8 font-bold text-red">動的なウェブサイトを作るために開発された<br>サーバー側で動作するプログラミング言語</h3>

<h4>主な特徴</h4>
<p>・HTMLに直接埋め込める</p>
<p>・動的型付けのためシンプルに書ける</p>
<p>・シェアが大きいので使用者が単純に多い</p>

<p class="pt-6">FacebookやWordPressで使用されている</p>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">Laravelとは</h1>

---
transition: fade-out
---

<logos-laravel class="mt-4 w-20 h-20" />

<h3 class="py-8 font-bold text-red">
  PHPでWebサイトを作るための、便利機能が詰まった<br>
「開発キット(フレームワーク)」です</h3>

<h4>主な特徴</h4>
<p>・コードがわかりやすくシンプル</p>
<p>・MVCモデルを採用</p>
<p>・シェアが大きいので情報量も多い</p>

<p class="pt-12">ログイン機能・セキュリティ対策・メール送信etc...</p>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">Laravelベストプラクティス</h1>

---
transition: fade-out
---

<h1 class="font-bold">単一責任の原則</h1>

<p>クラスとメソッドは1つの責任だけ持つべき</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
// 色々な責任を持っている
// 認証ユーザーの判定, 長い名前の作成, 短い名前の作成
public function getFullNameAttribute(): string
{
    if (auth()->user() && auth()->user()->hasRole('client') && auth()->user()->isVerified()) {
        return 'Mr. ' . $this->first_name . ' ' . $this->middle_name . ' ' . $this->last_name;
    } else {
        return $this->first_name[0] . '. ' . $this->last_name;
    }
}

```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
// それぞれの責任を別のメソッドに切り分ける
public function getFullNameAttribute(): string
{
    return $this->isVerifiedClient() ? $this->getFullNameLong() : $this->getFullNameShort();
}

public function isVerifiedClient(): bool
{
    return auth()->user() && auth()->user()->hasRole('client') && auth()->user()->isVerified();
}

public function getFullNameLong(): string
{
    return 'Mr. ' . $this->first_name . ' ' . $this->middle_name . ' ' . $this->last_name;
}

public function getFullNameShort(): string
{
    return $this->first_name[0] . '. ' . $this->last_name;
}
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">ファットモデル、スキニーコントローラ</h1>

<p>DBに関連するロジックはEloquentモデルかリポジトリクラスに入れる</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
public function index()
{
    $clients = Client::verified()
        ->with(['orders' => function ($q) {
            $q->where('created_at', '>', Carbon::today()->subWeek());
        }])
        ->get();

    return view('index', ['clients' => $clients]);
}
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
public function index()
{
    return view('index', ['clients' => $this->client->getWithNewOrders()]);
}

class Client extends Model
{
    public function getWithNewOrders()
    {
        return $this->verified()
            ->with(['orders' => function ($q) {
                $q->where('created_at', '>', Carbon::today()->subWeek());
            }])
            ->get();
    }
}
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">バリデーション</h1>

<p>バリデーションはコントローラからリクエストクラスに移動させる</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
public function store(Request $request)
{
    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

    ...
}
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
public function store(PostRequest $request)
{
    ...
}

class PostRequest extends Request
{
    public function rules()
    {
        return [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
            'publish_at' => 'nullable|date',
        ];
    }
}
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">ビジネスロジックはサービスクラスに</h1>

<p>コントローラは1つの責任だけを持つべき。ビジネスロジックはサービスクラスへ移動</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }

    ...
}
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
public function store(Request $request)
{
    $this->articleService->handleUploadedImage($request->file('image'));

    ...
}

class ArticleService
{
    public function handleUploadedImage($image)
    {
        if (!is_null($image)) {
            $image->move(public_path('images') . 'temp');
        }
    }
}
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">繰り返し書かない (DRY)</h1>

<p>コードを再利用する。Eloquentスコープなどを活用</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
public function getActive()
{
    return $this->where('verified', 1)
        ->whereNotNull('deleted_at')->get();
}

public function getArticles()
{
    return $this->whereHas('user', function ($q) {
        $q->where('verified', 1)
            ->whereNotNull('deleted_at');
    })->get();
}
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
public function scopeActive($q)
{
    return $q->where('verified', 1)
        ->whereNotNull('deleted_at');
}

public function getActive()
{
    return $this->active()->get();
}

public function getArticles()
{
    return $this->whereHas('user', function ($q) {
        $q->active();
    })->get();
}
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">Eloquentを優先して使う</h1>

<p>クエリビルダや生SQLよりもEloquentを優先。読みやすくメンテナンスしやすい</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad（生SQL）</p>

```sql
SELECT *
FROM `articles`
WHERE EXISTS (SELECT *
    FROM `users`
    WHERE `articles`.`user_id` = `users`.`id`
    AND EXISTS (SELECT *
        FROM `profiles`
        WHERE `profiles`.`user_id` = `users`.`id`)
    AND `users`.`deleted_at` IS NULL)
AND `verified` = '1'
AND `active` = '1'
ORDER BY `created_at` DESC
```
</div>

<div class="w-1/2 p-2">

<p>Good（Eloquent）</p>

```php
Article::has('user.profile')
    ->verified()
    ->latest()
    ->get();
```

<p class="pt-4 text-sm">Eloquentには論理削除、イベント、スコープなどの優れた組み込みツールがある</p>
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">マスアサインメント</h1>

<p>個別にプロパティを設定せず、マスアサインメントを活用</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
$article->category_id = $category->id;
$article->save();
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
$category->article()->create($request->validated());
```

<p class="pt-4 text-sm">リレーションメソッドと$request->validated()を組み合わせることで、安全かつ簡潔に記述できる</p>
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">Eager Loading（N+1問題）</h1>

<p>Bladeテンプレート内でクエリを実行しない。Eager Loadingを使う</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad（100ユーザに対して101回のDBクエリ）</p>

```blade
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```
</div>

<div class="w-1/2 p-2">

<p>Good（100ユーザに対して2回のDBクエリ）</p>

```php
$users = User::with('profile')->get();
```

```blade
@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">説明的なメソッド名と変数名</h1>

<p>コメントよりも説明的なメソッド名と変数名を付けるほうが良い</p>

<div class="flex flex-col gap-4">

<div>
<p>Bad</p>

```php
if (count((array) $builder->getQuery()->joins) > 0)
```
</div>

<div>
<p>Better</p>

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```
</div>

<div>
<p>Good</p>

```php
if ($this->hasJoins())
```
</div>

</div>

---
transition: fade-out
---

<h1 class="font-bold">JSとCSSをBladeから分離</h1>

<p>JSとCSSをBladeテンプレートの中に入れない、PHPクラスの中にHTMLを入れない</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```javascript
let article = `{{ json_encode($article) }}`;
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
<input id="article" type="hidden" value='@json($article)'>

// または
<button class="js-fav-article" data-article='@json($article)'>
    {{ $article->name }}
</button>
```

```javascript
// JSファイルで
let article = $('#article').val();
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">定数・configファイルを使う</h1>

<p>コード内の文字列の代わりにconfigファイル、languageファイル、定数を使う</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">標準Laravelツールを使う</h1>

<p>サードパーティ製パッケージより、Laravel標準機能を優先</p>

<div class="text-sm">

| タスク | 標準ツール | サードパーティ製 |
|--------|------------|------------------|
| 認可 | Policies | Entrust, Sentinel |
| アセットコンパイル | Laravel Mix, Vite | Grunt, Gulp |
| 開発環境 | Laravel Sail, Homestead | Docker |
| 単体テスト | PHPUnit, Mockery | Phpspec |
| DB | Eloquent | Doctrine |
| テンプレート | Blade | Twig |
| フォームバリデーション | Request classes | コントローラ内 |
| API認証 | Passport, Sanctum | JWT パッケージ |

</div>

---
transition: fade-out
---

<h1 class="font-bold">Laravelの命名規則に従う</h1>

<div class="text-sm">

| 対象 | 規則 | Good | Bad |
|------|------|------|-----|
| コントローラ | 単数形 | ArticleController | ArticlesController |
| ルート | 複数形 | articles/1 | article/1 |
| モデル | 単数形 | User | Users |
| テーブル | 複数形 | article_comments | articleComments |
| テーブルカラム | スネークケース | meta_title | MetaTitle |
| メソッド | キャメルケース | getAll | get_all |
| 変数 | キャメルケース | $articlesWithAuthor | $articles_with_author |
| ビュー | ケバブケース | show-filtered.blade.php | showFiltered.blade.php |

</div>

---
transition: fade-out
---

<h1 class="font-bold">短く読みやすい構文で書く</h1>

<div class="text-sm">

| 一般的な構文 | 短く読みやすい構文 |
|--------------|-------------------|
| `Session::get('cart')` | `session('cart')` |
| `$request->session()->get('cart')` | `session('cart')` |
| `$request->input('name')` | `$request->name` |
| `return Redirect::back()` | `return back()` |
| `is_null($object->relation) ? null : $object->relation->id` | `optional($object->relation)->id` |
| `Carbon::now()` | `now()` |
| `->where('column', '=', 1)` | `->where('column', 1)` |
| `->orderBy('created_at', 'desc')` | `->latest()` |
| `->orderBy('created_at', 'asc')` | `->oldest()` |

</div>

---
transition: fade-out
---

<h1 class="font-bold">IoCコンテナを使う</h1>

<p>newの代わりにIoCコンテナもしくはファサードを使う</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
$user = new User;
$user->create($request->validated());
```

<p class="pt-4 text-sm">newはクラス間の密結合を生み出し、テストを難しくする</p>
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
public function __construct(User $user)
{
    $this->user = $user;
}

...

$this->user->create($request->validated());
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">.envファイルを直接参照しない</h1>

<p>configファイル経由でデータを参照する</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
$apiKey = env('API_KEY');
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
// config/api.php
'key' => env('API_KEY'),

// データを使用する
$apiKey = config('api.key');
```
</div>
</div>

<p class="pt-4">config:cacheを使用した場合、env()は動作しなくなるため</p>

---
transition: fade-out
---

<h1 class="font-bold">日付フォーマットの管理</h1>

<p>日付を標準フォーマットで保存し、アクセサとミューテータで表示を変更</p>

<div class="flex">
<div class="w-1/2 p-2">

<p>Bad</p>

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```
</div>

<div class="w-1/2 p-2">

<p>Good</p>

```php
// Model
protected $casts = [
    'ordered_at' => 'datetime',
];

public function getSomeDateAttribute($date)
{
    return $date->format('m-d');
}

// View
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->some_date }}
```
</div>
</div>

---
transition: fade-out
---

<h1 class="font-bold">その他グッドプラクティス</h1>

<div class="pt-6">

- ルートファイルにはロジックを入れない

- Bladeテンプレートの中でVanilla PHP（標準のPHPコード）の使用は最小限に

- 可能な限りテストを書く

- ドキュメントを読む習慣をつける

</div>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">
    個人的に大事だと思うこと
</h1>

---
transition: fade-out
---

<h1 class="font-bold">1. 公式ドキュメントを読む</h1>

<div class="pt-6">

- Laravelの公式ドキュメントは非常に充実している

- 新機能やベストプラクティスが常に更新されている

- 困ったらまずドキュメントを確認する習慣をつける

- 英語が苦手でも、コード例を見るだけでも参考になる

</div>

<p class="pt-8 text-blue">https://laravel.com/docs</p>

---
transition: fade-out
---

<h1 class="font-bold">2. 小さく始めて、徐々に改善</h1>

<div class="pt-6">

- 最初から完璧なコードを書こうとしない

- まず動くものを作り、リファクタリングで改善

- 「動く → 正しい → 速い」の順番で進める

- 過度な抽象化は後で苦しむことが多い

</div>

---
transition: fade-out
---

<h1 class="font-bold">3. テストを書く</h1>

<div class="pt-6">

- テストがあると安心してリファクタリングできる

- バグの再発防止になる

- 仕様書代わりにもなる

- 最初は Feature Test から始めると良い

</div>

```php
public function test_user_can_view_articles()
{
    $user = User::factory()->create();
    $article = Article::factory()->create();

    $response = $this->actingAs($user)->get('/articles');

    $response->assertStatus(200);
    $response->assertSee($article->title);
}
```

---
transition: fade-out
---

<h1 class="font-bold">4. チームで規約を統一する</h1>

<div class="pt-6">

- コーディング規約を決めて共有する

- Laravel Pintなどのツールでフォーマットを自動化

- レビューで「書き方」ではなく「ロジック」に集中できる

- 新メンバーのオンボーディングも楽になる

</div>

```bash
# Laravel Pint でコードを自動整形
./vendor/bin/pint
```

---
transition: fade-out
---

<h1 class="font-bold">5. エラーログを活用する</h1>

<div class="pt-6">

- `storage/logs/laravel.log` を定期的に確認

- 本番環境では Sentry や Bugsnag などの監視ツールを導入

- エラーが起きたら原因を追求し、根本解決する

- 「とりあえず動いた」で終わらせない

</div>

---
transition: fade-out
---

<h1 class="font-bold">6. パフォーマンスを意識する</h1>

<div class="pt-6">

- N+1問題は最も多いパフォーマンス低下の原因

- Laravel Debugbar で クエリ数を確認する習慣

- キャッシュを適切に使う

- 必要なカラムだけ取得する（`select()`）

</div>

```php
// Laravel Debugbar をインストール
composer require barryvdh/laravel-debugbar --dev
```

---
transition: fade-out
layout: center
---

<h1 class="font-bold">
    おわりに
</h1>

---
transition: fade-out
---

<h1 class="font-bold">おわりに</h1>
<div>

<p class="pt-6">結構なボリュームになってしまいましたが、</p>
<p>今回の発表で再度、自分も復習できました。</p>
<p>Laravelは奥が深く、今回紹介したのも一部ですが、</p>
<p>よく使うベストプラクティスをチョイスしたので、ためになれば幸いです。</p>

<h3 class="text-red pt-12">あとslidevで作るのはすごく楽しかったです。</h3>
<p class="text-blue">https://ja.sli.dev</p>
</div>

---
transition: fade-out
layout: center
---

<h1 class="font-bold">ご静聴ありがとうございました。</h1>
