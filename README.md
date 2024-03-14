## Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

## 青山メモ

`Next.js` で特別な意味を持っていそうなファイル名/ディレクトリ名
- `page.tsx`: ページそのものを表すコンポーネント
- `layout.tsx`: 子コンポーネントをラップしていくことでアプリケーション全体（一部）に影響するレイアウトを定義する？部分レンダリングによって効率的にレンダリングができる。
- `[id]` (本アプリケーションだと `invoices/[id]/edit` とかで使われている。): 動的ルーティング？に便利なんだっけか。
- `loading.tsx`: 遅い処理によってロードしているときに表示するコンポーネントを記述する？ `<Suspense />` コンポーネントみたいな感じかな。
- `(overview)`: `loading.tsx` の適用ページを一部に制限したいときに、このディレクトリ配下に `page.tsx` と一緒にいれる。

### 第4章 Creating Lyouts and Pages
Next.jsはファイルシステムルーティングを使用して、ネストされたルートを作成する。各フォルダはURL segmentにマッピングされるRoute segmentを表す。

例えば、このアプリケーションだと `/app/dashboard/invoices/page.tsx` というパスで Invoice ページのコンポーネントが実装されている場合，そこにブラウザ上でアクセスするには `/dashboard/invoices/` というURLを使う。先頭の `/` が `app` ，すなわちアプリケーションのルートを表し，残りはディレクトリ構造そのままで `page.tsx` にアクセスできる。

Next.jsでは， `page.tsx` が特別なファイルとして認識され，`/app/dashboard/page.tsx` だと URL `/dashboard` とすることで，`page.tsx` で実装されるコンポーネントが見れる...のかな（もちろんlayoutとかに埋め込まれる画）？基本的にReactなので，コンポーネントは JSX を返す関数コンポーネントの形で書くようにする。

Next.jsでレイアウトを使用する利点: 部分レンダリングができる。

### 第5章 Navigating Betwee Pages

Next.jsでは `<Link />` コンポーネント（ `next/link` ）を使ってアプリケーション内のページ間をリンクする。 `<Link />` は `<a>` タグっぽく使える。

`usePathname` を使うことで、今ユーザがどこにいるかを示すアクティブリンクが参照できる。 `'use client';` ディレクティブと一緒に使うこと！

### 第7章 Fetching Data

Next.js はデフォルトでは React Server Componentsを使用してデータを取得する。

リクエストウォーターフォール: 前のリクエスト完了に依存する一連のネットワークリクエスト → 場合によって並列データフェッチも考える( `Promise.all()` が使える)

### 第9章 Streaming

ストリーミングとは、routeをより小さな「chunk」に分割し、準備が整っとらサーバーからクライアントに段階的にストリーミングするデータ転送技術である。

よって, ストリーミングを活用することでめっちゃ遅い処理がボトルネックになってページが全然表示されない！！！から軽いのから表示されていくからユーザーはそれらに対してアクションできる！！へとUXを向上させられる（?）

### 第11章 Adding Search and Pagination

- `useSearchParams`: 現在のURLパラメータにアクセスできる。例えば， `/dashboard/invoices?page=1&query=pending` のURLの検索パラメータは `{page: '1', query: 'pedding'}` のようになる。
- `useRouter`: クライアントコンポーネント内のルート間のナビゲーションをプログラムで有効にする。得られる `replace` でユーザーの検索データを更新できる（ `replace(${pathname}?{params.toString()})` 的な感じ）

検索を使うページコンポーネントは `searchParams` を受け入れる `props` が必要。これで得られる値から動的にコンポーネントの状態を変化させることができる。

キーストロークごとにデーベースにクエリを実行するのやばいよね -> __デバウンス__ を使う。

`npm i use-debounce` でライブラリをインストールする。

```
const handleSearch = useDebouncedCallback((term) => {
  console.log(`Searching... ${term}`);
 
  const params = new URLSearchParams(searchParams);
  if (term) {
    params.set('query', term);
  } else {
    params.delete('query');
  }
  replace(`${pathname}?${params.toString()}`);
}, 300);
```

こんな感じでユーザが入力をやめてから第2引数時間（300ms）経過した後にのみ 第一引数のコールバックを呼び出す。

### 感想

React進化させた感じだとスーパー初心者ながらに思った。Reactがよく理解できている人がやればかなりいい感じにNext.jsの真髄を理解できるのかもしれないが、「はにゃ？」な状態でtryすると結局なんか微妙な理解で終わっちゃいそう。実際にしばらくアプリ開発で利用し、その中で習得していくしかなさそう。

流れとしてド頭にCSSとか フォント/画像の最適化とかがあったが、これはNext.jsの思想に基づくものなんだろうか...？

感覚的には5〜12章あたりがNextアプリを作る上で大事そう。13章〜は保守性やユーザビリティ・安全性とか、そういう部分に焦点を当てている。
