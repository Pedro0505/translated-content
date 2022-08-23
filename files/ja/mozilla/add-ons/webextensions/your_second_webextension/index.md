---
title: 2 つめの拡張機能
slug: Mozilla/Add-ons/WebExtensions/Your_second_WebExtension
tags:
  - WebExtensions
translation_of: Mozilla/Add-ons/WebExtensions/Your_second_WebExtension
original_slug: Mozilla/Add-ons/WebExtensions/Walkthrough
---
<div>{{AddonSidebar}}
<p><a href="/ja/Add-ons/WebExtensions/Your_first_WebExtension">初めての拡張機能</a>を一通り読んでいる場合、既に拡張機能の書き方を知っていることと思います。<span class="seoSummary">この記事では、API の使い方をより詳しく説明するため、前回よりも少し複雑なアドオンを書いてみます。</span></p>

<p>この拡張機能では Firefox のツールバーにボタンを新しく追加します。ユーザーがこのボタンをクリックすると、動物を選択できるポップアップが表示されます。ユーザーが動物を選択すると、ウェブページのコンテンツが選択した動物の画像に置き換わります。</p>

<p>これらの機能を以下のように実装していきます。</p>

<ul>
 <li><strong><a href="/ja/docs/Mozilla/Add-ons/WebExtensions/Browser_action">ブラウザーアクション </a></strong><strong>（Firefox のツールバーに表示させるボタン）を定義</strong><br>
  このボタン用に次のものを用意します。
  <ul>
   <li>"beasts-32.png" というアイコン</li>
   <li>ボタン押下時に開くポップアップ（HTML / CSS / JavaScript で記述）</li>
  </ul>
 </li>
 <li><strong>拡張機能のアイコンを定義</strong>（ファイル名: "beasts-48.png"）。これはアドオンマネージャーに表示されます。</li>
 <li><strong>ウェブページに読み込ませるコンテンツスクリプト "beastify.js" を作成</strong><br>
  このスクリプトによってウェブページが実際に変更されます。</li>
 <li><strong>ウェブページを置き換える動物の画像をパッケージ化</strong><br>
  画像を "web-accessible なリソース" とし、ウェブページから参照できるようにします。</li>
</ul>

<p>この拡張機能は次のような全体構成になっています。</p>

<p><img alt="" src="https://mdn.mozillademos.org/files/13671/Untitled-1.png" style="display: block; height: 1200px; margin-left: auto; margin-right: auto; width: 860px;"></p>

<p>この拡張機能はシンプルですが、WebExtensions API の基本的なコンセプトを多く含んでいます。</p>

<ul>
 <li>ツールバーにボタンを追加する</li>
 <li>HTML / CSS / JavaScript を用いてポップアップのパネルを定義する</li>
 <li>ウェブページにコンテンツスクリプトを差し込む</li>
 <li>拡張機能の中においてコンテンツスクリプトと他のスクリプトとを通信させる</li>
 <li>ウェブページで用いるリソースを拡張機能にパッケージ化する</li>
</ul>

<p><a href="https://github.com/mdn/webextensions-examples/tree/master/beastify">ソースコード一式は GitHub で参照できます </a>。</p>

<p>この拡張機能を作成するには、Firefox 45 以上が必要です。</p>

<h2 id="Writing_the_extension" name="Writing_the_extension">拡張機能を書く</h2>

<p>新しいディレクトリーを作成し、そのディレクトリーに移動します。</p>

<pre class="brush: bash">mkdir beastify
cd beastify</pre>

<h3 id="manifest.json" name="manifest.json">manifest.json</h3>

<p>それでは、"beastify" ディレクトリー配下に新しいファイル "manifest.json" を作成します。以下の内容を書き込んで保存してください。</p>

<pre class="brush: json line-numbers  language-json"><code class="language-json"><span class="punctuation token">{</span>

  <span class="key token">"manifest_version":</span> <span class="number token">2</span><span class="punctuation token">,</span>
  <span class="key token">"name":</span> <span class="string token">"Beastify"</span><span class="punctuation token">,</span>
  <span class="key token">"version":</span> <span class="string token">"1.0"</span><span class="punctuation token">,</span>

  <span class="key token">"description":</span> <span class="string token">"Adds a browser action icon to the toolbar. Click the button to choose a beast. The active tab's body content is then replaced with a picture of the chosen beast. See https://developer.mozilla.org/ja/Add-ons/WebExtensions/Examples#beastify"</span><span class="punctuation token">,</span>
  <span class="key token">"homepage_url":</span> <span class="string token">"https://github.com/mdn/webextensions-examples/tree/master/beastify"</span><span class="punctuation token">,</span>
  <span class="key token">"icons":</span> <span class="punctuation token">{</span>
    <span class="key token">"48":</span> <span class="string token">"icons/beasts-48.png"</span>
  <span class="punctuation token">}</span><span class="punctuation token">,</span>

  <span class="key token">"permissions":</span> <span class="punctuation token">[</span>
    <span class="string token">"activeTab"</span>
  <span class="punctuation token">]</span><span class="punctuation token">,</span>

  <span class="key token">"browser_action":</span> <span class="punctuation token">{</span>
    <span class="key token">"default_icon":</span> <span class="string token">"icons/beasts-32.png"</span><span class="punctuation token">,</span>
    <span class="key token">"default_title":</span> <span class="string token">"Beastify"</span><span class="punctuation token">,</span>
    <span class="key token">"default_popup":</span> <span class="string token">"popup/choose_beast.html"</span>
  <span class="punctuation token">}</span><span class="punctuation token">,</span>

  <span class="key token">"web_accessible_resources":</span> <span class="punctuation token">[</span>
    <span class="string token">"beasts/frog.jpg"</span><span class="punctuation token">,</span>
    <span class="string token">"beasts/turtle.jpg"</span><span class="punctuation token">,</span>
    <span class="string token">"beasts/snake.jpg"</span>
  <span class="punctuation token">]</span>

<span class="punctuation token">}</span></code></pre>

<ul>
 <li>最初の 3 つのキー <code><a href="/ja/Add-ons/WebExtensions/manifest.json/manifest_version">manifest_version</a></code>、<code><a href="/ja/Add-ons/WebExtensions/manifest.json/name">name</a></code>、<code><a href="/ja/Add-ons/WebExtensions/manifest.json/version">version</a></code> は必須であり、拡張機能の基本的なメタデータを指定します</li>
 <li><code><a href="/ja/Add-ons/WebExtensions/manifest.json/description">description</a></code> と <code><a href="/ja/docs/Mozilla/Add-ons/WebExtensions/manifest.json/homepage_url">homepage_url</a></code> は省略可能ですが、設定しておくことをお勧めします。これは拡張機能についての役立つ情報となります</li>
 <li><code><a href="/ja/Add-ons/WebExtensions/manifest.json/icons">icons</a></code> は省略可能ですが、設定しておくことをお勧めします。この値は拡張機能のアイコンを指定するものであり、アイコンはアドオンマネージャーに表示されます</li>
 <li><code><a href="/ja/Add-ons/WebExtensions/manifest.json/permissions">permissions</a></code> は拡張機能に必要なパーミッションのリストです。ここでは <a href="/ja/Add-ons/WebExtensions/manifest.json/permissions#activeTab_permission"><code>activeTab</code> パーミッション </a> を要請しています</li>
 <li><code><a href="/ja/Add-ons/WebExtensions/manifest.json/browser_action">browser_action</a></code> はツールバーのボタンを指定するものです。ここでは 3 つの情報を指定しています
  <ul>
   <li><code>default_icon</code> ではボタンのアイコンを指定します（必須項目）</li>
   <li><code>default_title</code> ではツールチップの文字列を指定します（省略可）</li>
   <li><code>default_popup</code> ではユーザーがボタンをクリックした際に表示されるポップアップを指定します。今回はポップアップを表示させたいので、拡張機能に含める HTML ファイルを <code>defualt_popup</code> キーで指定しています</li>
  </ul>
 </li>
 <li><code><a href="/ja/Add-ons/WebExtensions/manifest.json/web_accessible_resources">web_accessible_resources</a></code> はウェブページからアクセスしたいファイルのリストです。この拡張機能では、拡張機能に同梱した画像でウェブページを置き換えるため、画像をウェブページからアクセス可能にする必要があります</li>
</ul>

<p>パスはすべて manifest.json 自身からの相対パスで指定することに注意します。</p>

<h3 id="The_icon" name="The_icon">アイコン</h3>

<p>拡張機能にはアイコンを用意すると良いでしょう。このアイコンは、アドオンマネージャーで拡張機能のリスト横に表示されます（アドオンマネージャーは "about:addons" の URL から確認できます）。今回の manifest.json では "icons/beasts-48.png" を用意していると宣言しています。</p>

<p>"icons" ディレクトリーを作成して、そこにアイコンを "beasts-48.png" という名前で 保存します。必要であれば <a class="external external-icon" href="https://github.com/mdn/webextensions-examples/blob/master/beastify/icons/beasts-48.png">サンプルで使用しているアイコン </a> を利用しても構いません（このアイコンは <a href="https://www.iconfinder.com/iconsets/free-retina-icon-set">Aha-Soft’s Free Retina iconset</a> から引用したものであり、該当する <a href="http://www.aha-soft.com/free-icons/free-retina-icon-set/">ライセンス </a> の下で使用しています）。</p>

<p>アイコンを自分で用意する場合 48x48 ピクセルのサイズにする必要があります。高解像度のディスプレイに 96x96 ピクセルのアイコンを表示させたい場合は、manifest.json の <code>icons</code> オブジェクトに <code>96</code> というプロパティで設定してください。</p>

<pre class="brush: json line-numbers  language-json"><code class="language-json"><span class="key token">"icons":</span> <span class="punctuation token">{</span>
  <span class="key token">"48":</span> <span class="string token">"icons/beasts-48.png"</span><span class="punctuation token">,</span>
  <span class="key token">"96":</span> <span class="string token">"icons/beasts-96.png"</span>
<span class="punctuation token">}</span></code></pre>

<h3 id="The_toolbar_button" name="The_toolbar_button">ツールバーのボタン</h3>

<p>ツールバーのボタンにもアイコンが必要です。今回の manifest.json では "icons/beasts-32.png" を用意していると宣言しています。</p>

<p>アイコンを "beasts-32.png" という名前で "icons" ディレクトリー内に保存します。必要であれば <a class="external external-icon" href="https://github.com/mdn/webextensions-examples/blob/master/beastify/icons/beasts-32.png">サンプルで使用しているアイコン </a> を利用しても構いません（このアイコンは <a href="http://www.iconbeast.com/free">IconBeast Lite のアイコン集</a> から引用したものであり、該当する <a href="http://www.iconbeast.com/faq/">ライセンス </a> の下で使用しています）。</p>

<p>ポップアップを使わない場合、ユーザーがボタンをクリックした際にはクリックイベントが拡張機能に向けて送出されます。ポップアップを使う場合にはクリックイベントは送出されず、代わりにポップアップが開きます。今回はポップアップが必要なので、次の項で作成しましょう。</p>

<h3 id="The_popup" name="The_popup">ポップアップ</h3>

<p>今回のポップアップでは、ユーザーが 3 つの動物から 1 つ選択できる機能を持つこととします。</p>

<p>拡張機能のルートディレクトリー配下に "popup" というディレクトリーを新しく作成します。ここにはポップアップ用のコードを保管します。ポップアップは次の 3 ファイルから構成されます。</p>

<ul>
 <li><strong><code>choose_beast.html</code></strong> パネルのコンテンツを定義する</li>
 <li><strong><code>choose_beast.css</code></strong> コンテンツのスタイルを指定する</li>
 <li><strong><code>choose_beast.js</code></strong> アクティブなタブでコンテンツスクリプトを実行し、ユーザーの選択を処理する</li>
</ul>

<pre class="brush: bash line-numbers  language-bash"><code class="language-bash">mkdir popup
cd popup
touch choose_beast.html choose_beast.css choose_beast.js</code></pre>

<h4 id="choose_beast.html" name="choose_beast.html">choose_beast.html</h4>

<p>HTML ファイルは次のようになります。</p>

<pre class="brush: html line-numbers  language-html"><code class="language-html"><span class="doctype token">&lt;!DOCTYPE html&gt;</span>

<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>html</span><span class="punctuation token">&gt;</span></span>
  <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>head</span><span class="punctuation token">&gt;</span></span>
    <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>meta</span> <span class="attr-name token">charset</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>utf-8<span class="punctuation token">"</span></span><span class="punctuation token">&gt;</span></span>
    <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>link</span> <span class="attr-name token">rel</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>stylesheet<span class="punctuation token">"</span></span> <span class="attr-name token">href</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>choose_beast.css<span class="punctuation token">"</span></span><span class="punctuation token">/&gt;</span></span>
  <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>head</span><span class="punctuation token">&gt;</span></span>

<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>body</span><span class="punctuation token">&gt;</span></span>
  <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>div</span> <span class="attr-name token">id</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>popup-content<span class="punctuation token">"</span></span><span class="punctuation token">&gt;</span></span>
    <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>div</span> <span class="attr-name token">class</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>button beast<span class="punctuation token">"</span></span><span class="punctuation token">&gt;</span></span>Frog<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>div</span><span class="punctuation token">&gt;</span></span>
    <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>div</span> <span class="attr-name token">class</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>button beast<span class="punctuation token">"</span></span><span class="punctuation token">&gt;</span></span>Turtle<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>div</span><span class="punctuation token">&gt;</span></span>
    <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>div</span> <span class="attr-name token">class</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>button beast<span class="punctuation token">"</span></span><span class="punctuation token">&gt;</span></span>Snake<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>div</span><span class="punctuation token">&gt;</span></span>
    <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>div</span> <span class="attr-name token">class</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>button reset<span class="punctuation token">"</span></span><span class="punctuation token">&gt;</span></span>Reset<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>div</span><span class="punctuation token">&gt;</span></span>
  <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>div</span><span class="punctuation token">&gt;</span></span>
  <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>div</span> <span class="attr-name token">id</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>error-content<span class="punctuation token">"</span></span> <span class="attr-name token">class</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>hidden<span class="punctuation token">"</span></span><span class="punctuation token">&gt;</span></span>
    <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>p</span><span class="punctuation token">&gt;</span></span>Can't beastify this web page.<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>p</span><span class="punctuation token">&gt;</span></span><span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>p</span><span class="punctuation token">&gt;</span></span>Try a different page.<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>p</span><span class="punctuation token">&gt;</span></span>
  <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>div</span><span class="punctuation token">&gt;</span></span>
  <span class="tag token"><span class="tag token"><span class="punctuation token">&lt;</span>script</span> <span class="attr-name token">src</span><span class="attr-value token"><span class="punctuation token">=</span><span class="punctuation token">"</span>choose_beast.js<span class="punctuation token">"</span></span><span class="punctuation token">&gt;</span></span><span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>script</span><span class="punctuation token">&gt;</span></span>
<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>body</span><span class="punctuation token">&gt;</span></span>

<span class="tag token"><span class="tag token"><span class="punctuation token">&lt;/</span>html</span><span class="punctuation token">&gt;</span></span></code></pre>

<p><code>"popup-content"</code> という ID の <code><a href="/ja/docs/Web/HTML/Element/div">&lt;div&gt;</a></code> 要素があって、動物の選択をする要素が入っています。<code>"error-content"</code> という ID の <code>&lt;div&gt;</code> 要素と <code>"hidden"</code> クラスもあります。それはポップアップの初期化に問題がある場合に使います。</p>

<p>通常のウェブページと同じように CSS と JS ファイルを読み込んでいることに注意してください。</p>

<h4 id="choose_beast.css" name="choose_beast.css">choose_beast.css</h4>

<p>この CSS ではポップアップのサイズを固定し、3 つの選択肢がポップアップを埋めるようにし、基本的なスタイリングを施しています。また要素を <code>class="hidden"</code> で隠してもいます: つまり <code>"error-content"</code> <code>&lt;div&gt;</code> は既定では隠れています。</p>

<pre class="brush: css line-numbers  language-css"><code class="language-css"><span class="selector token">html, body </span><span class="punctuation token">{</span>
  <span class="property token">width</span><span class="punctuation token">:</span> <span class="number token">100</span>px<span class="punctuation token">;</span>
<span class="punctuation token">}</span>

<span class="selector token"><span class="class token">.hidden</span> </span><span class="punctuation token">{</span>
  <span class="property token">display</span><span class="punctuation token">:</span> none<span class="punctuation token">;</span>
<span class="punctuation token">}</span>

<span class="selector token"><span class="class token">.button</span> </span><span class="punctuation token">{</span>
  <span class="property token">margin</span><span class="punctuation token">:</span> <span class="number token">3%</span> auto<span class="punctuation token">;</span>
  <span class="property token">padding</span><span class="punctuation token">:</span> <span class="number token">4</span>px<span class="punctuation token">;</span>
  <span class="property token">text-align</span><span class="punctuation token">:</span> center<span class="punctuation token">;</span>
  <span class="property token">font-size</span><span class="punctuation token">:</span> <span class="number token">1.5</span>em<span class="punctuation token">;</span>
  <span class="property token">cursor</span><span class="punctuation token">:</span> pointer<span class="punctuation token">;</span>
<span class="punctuation token">}</span>

<span class="selector token"><span class="class token">.beast</span><span class="pseudo-class token">:hover</span> </span><span class="punctuation token">{</span>
  <span class="property token">background-color</span><span class="punctuation token">:</span> <span class="hexcode token">#CFF2F2</span><span class="punctuation token">;</span>
<span class="punctuation token">}</span>

<span class="selector token"><span class="class token">.beast</span> </span><span class="punctuation token">{</span>
  <span class="property token">background-color</span><span class="punctuation token">:</span> <span class="hexcode token">#E5F2F2</span><span class="punctuation token">;</span>
<span class="punctuation token">}</span>

<span class="selector token"><span class="class token">.reset</span> </span><span class="punctuation token">{</span>
  <span class="property token">background-color</span><span class="punctuation token">:</span> <span class="hexcode token">#FBFBC9</span><span class="punctuation token">;</span>
<span class="punctuation token">}</span>

<span class="selector token"><span class="class token">.reset</span><span class="pseudo-class token">:hover</span> </span><span class="punctuation token">{</span>
  <span class="property token">background-color</span><span class="punctuation token">:</span> <span class="hexcode token">#EAEA9D</span><span class="punctuation token">;</span>
<span class="punctuation token">}</span></code></pre>

<h4 id="choose_beast.js" name="choose_beast.js">choose_beast.js</h4>

<p>これがポップアップ用の JavaScript です:</p>

<pre class="brush: js line-numbers  language-js"><code>
<span class="comment token">/**
 * ページのすべてを隠す CSS、ただし
 * "beastify-image" クラスを持つ要素は除く
 */</span>
<span class="keyword token">const</span> hidePage <span class="operator token">=</span> <span class="template-string token"><span class="string token">`body &gt; :not(.beastify-image) {
                    display: none;
                  }`</span></span><span class="punctuation token">;</span>

<span class="comment token">/**
 * ボタンクリックをリッスンし、ページ内のコンテンツスクリプトに
 * 適切なメッセージを送る
 */</span>
<span class="keyword token">function</span> <span class="function token">listenForClicks</span><span class="punctuation token">(</span><span class="punctuation token">)</span> <span class="punctuation token">{</span>
  document<span class="punctuation token">.</span><span class="function token">addEventListener</span><span class="punctuation token">(</span><span class="string token">"click"</span><span class="punctuation token">,</span> <span class="punctuation token">(</span>e<span class="punctuation token">)</span> <span class="operator token">=</span><span class="operator token">&gt;</span> <span class="punctuation token">{</span>

    <span class="comment token">/**
     * 動物の名前を受け取って、対応する画像の URL を取得する
     */</span>
    <span class="keyword token">function</span> <span class="function token">beastNameToURL</span><span class="punctuation token">(</span>beastName<span class="punctuation token">)</span> <span class="punctuation token">{</span>
      <span class="keyword token">switch</span> <span class="punctuation token">(</span>beastName<span class="punctuation token">)</span> <span class="punctuation token">{</span>
        <span class="keyword token">case</span> <span class="string token">"Frog"</span><span class="punctuation token">:</span>
          <span class="keyword token">return</span> browser<span class="punctuation token">.</span>extension<span class="punctuation token">.</span><span class="function token">getURL</span><span class="punctuation token">(</span><span class="string token">"beasts/frog.jpg"</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
        <span class="keyword token">case</span> <span class="string token">"Snake"</span><span class="punctuation token">:</span>
          <span class="keyword token">return</span> browser<span class="punctuation token">.</span>extension<span class="punctuation token">.</span><span class="function token">getURL</span><span class="punctuation token">(</span><span class="string token">"beasts/snake.jpg"</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
        <span class="keyword token">case</span> <span class="string token">"Turtle"</span><span class="punctuation token">:</span>
          <span class="keyword token">return</span> browser<span class="punctuation token">.</span>extension<span class="punctuation token">.</span><span class="function token">getURL</span><span class="punctuation token">(</span><span class="string token">"beasts/turtle.jpg"</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
      <span class="punctuation token">}</span>
    <span class="punctuation token">}</span>

    <span class="comment token">/**
     * アクティブなタブにページを隠す CSS を挿入して
     * 動物の URL を取得し、
     * アクティブなタブのコンテンツスクリプトに "beastify" メッセージを送る
     */</span>
    <span class="keyword token">function</span> <span class="function token">beastify</span><span class="punctuation token">(</span>tabs<span class="punctuation token">)</span> <span class="punctuation token">{</span>
      browser<span class="punctuation token">.</span>tabs<span class="punctuation token">.</span><span class="function token">insertCSS</span><span class="punctuation token">(</span><span class="punctuation token">{</span>code<span class="punctuation token">:</span> hidePage<span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">.</span><span class="function token">then</span><span class="punctuation token">(</span><span class="punctuation token">(</span><span class="punctuation token">)</span> <span class="operator token">=</span><span class="operator token">&gt;</span> <span class="punctuation token">{</span>
        <span class="keyword token">let</span> url <span class="operator token">=</span> <span class="function token">beastNameToURL</span><span class="punctuation token">(</span>e<span class="punctuation token">.</span>target<span class="punctuation token">.</span>textContent<span class="punctuation token">)</span><span class="punctuation token">;</span>
        browser<span class="punctuation token">.</span>tabs<span class="punctuation token">.</span><span class="function token">sendMessage</span><span class="punctuation token">(</span>tabs<span class="punctuation token">[</span><span class="number token">0</span><span class="punctuation token">]</span><span class="punctuation token">.</span>id<span class="punctuation token">,</span> <span class="punctuation token">{</span>
          command<span class="punctuation token">:</span> <span class="string token">"beastify"</span><span class="punctuation token">,</span>
          beastURL<span class="punctuation token">:</span> url
        <span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
      <span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="punctuation token">}</span>

    <span class="comment token">/**
     * アクティブなタブからページを隠す CSS を削除し、
     * アクティブなタブのコンテンツスクリプトに "reset" メッセージを送る
     */</span>
    <span class="keyword token">function</span> <span class="function token">reset</span><span class="punctuation token">(</span>tabs<span class="punctuation token">)</span> <span class="punctuation token">{</span>
      browser<span class="punctuation token">.</span>tabs<span class="punctuation token">.</span><span class="function token">removeCSS</span><span class="punctuation token">(</span><span class="punctuation token">{</span>code<span class="punctuation token">:</span> hidePage<span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">.</span><span class="function token">then</span><span class="punctuation token">(</span><span class="punctuation token">(</span><span class="punctuation token">)</span> <span class="operator token">=</span><span class="operator token">&gt;</span> <span class="punctuation token">{</span>
        browser<span class="punctuation token">.</span>tabs<span class="punctuation token">.</span><span class="function token">sendMessage</span><span class="punctuation token">(</span>tabs<span class="punctuation token">[</span><span class="number token">0</span><span class="punctuation token">]</span><span class="punctuation token">.</span>id<span class="punctuation token">,</span> <span class="punctuation token">{</span>
          command<span class="punctuation token">:</span> <span class="string token">"reset"</span><span class="punctuation token">,</span>
        <span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
      <span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="punctuation token">}</span>

    <span class="comment token">/**
     * ただコンソールにエラーをログ出力する
     */</span>
    <span class="keyword token">function</span> <span class="function token">reportError</span><span class="punctuation token">(</span>error<span class="punctuation token">)</span> <span class="punctuation token">{</span>
      console<span class="punctuation token">.</span><span class="function token">error</span><span class="punctuation token">(</span><span class="template-string token"><span class="string token">`Could not beastify: </span><span class="interpolation token"><span class="interpolation-punctuation punctuation token">${</span>error<span class="interpolation-punctuation punctuation token">}</span></span><span class="string token">`</span></span><span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="punctuation token">}</span>

    <span class="comment token">/**
     * アクティブなタブを取得し、
     * "beastify()" か "reset()" を適切に呼び出す
     */</span>
    <span class="keyword token">if</span> <span class="punctuation token">(</span>e<span class="punctuation token">.</span>target<span class="punctuation token">.</span>classList<span class="punctuation token">.</span><span class="function token">contains</span><span class="punctuation token">(</span><span class="string token">"beast"</span><span class="punctuation token">)</span><span class="punctuation token">)</span> <span class="punctuation token">{</span>
      browser<span class="punctuation token">.</span>tabs<span class="punctuation token">.</span><span class="function token">query</span><span class="punctuation token">(</span><span class="punctuation token">{</span>active<span class="punctuation token">:</span> <span class="keyword token">true</span><span class="punctuation token">,</span> currentWindow<span class="punctuation token">:</span> <span class="keyword token">true</span><span class="punctuation token">}</span><span class="punctuation token">)</span>
        <span class="punctuation token">.</span><span class="function token">then</span><span class="punctuation token">(</span>beastify<span class="punctuation token">)</span>
        <span class="punctuation token">.</span><span class="keyword token">catch</span><span class="punctuation token">(</span>reportError<span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="punctuation token">}</span>
    <span class="keyword token">else</span> <span class="keyword token">if</span> <span class="punctuation token">(</span>e<span class="punctuation token">.</span>target<span class="punctuation token">.</span>classList<span class="punctuation token">.</span><span class="function token">contains</span><span class="punctuation token">(</span><span class="string token">"reset"</span><span class="punctuation token">)</span><span class="punctuation token">)</span> <span class="punctuation token">{</span>
      browser<span class="punctuation token">.</span>tabs<span class="punctuation token">.</span><span class="function token">query</span><span class="punctuation token">(</span><span class="punctuation token">{</span>active<span class="punctuation token">:</span> <span class="keyword token">true</span><span class="punctuation token">,</span> currentWindow<span class="punctuation token">:</span> <span class="keyword token">true</span><span class="punctuation token">}</span><span class="punctuation token">)</span>
        <span class="punctuation token">.</span><span class="function token">then</span><span class="punctuation token">(</span>reset<span class="punctuation token">)</span>
        <span class="punctuation token">.</span><span class="keyword token">catch</span><span class="punctuation token">(</span>reportError<span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="punctuation token">}</span>
  <span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
<span class="punctuation token">}</span>

<span class="comment token">/**
 * スクリプトにエラーがあった。
 * ポップアップのエラーメッセージを表示し、通常の UI を隠す。
 */</span>
<span class="keyword token">function</span> <span class="function token">reportExecuteScriptError</span><span class="punctuation token">(</span>error<span class="punctuation token">)</span> <span class="punctuation token">{</span>
  document<span class="punctuation token">.</span><span class="function token">querySelector</span><span class="punctuation token">(</span><span class="string token">"#popup-content"</span><span class="punctuation token">)</span><span class="punctuation token">.</span>classList<span class="punctuation token">.</span><span class="function token">add</span><span class="punctuation token">(</span><span class="string token">"hidden"</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
  document<span class="punctuation token">.</span><span class="function token">querySelector</span><span class="punctuation token">(</span><span class="string token">"#error-content"</span><span class="punctuation token">)</span><span class="punctuation token">.</span>classList<span class="punctuation token">.</span><span class="function token">remove</span><span class="punctuation token">(</span><span class="string token">"hidden"</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
  console<span class="punctuation token">.</span><span class="function token">error</span><span class="punctuation token">(</span><span class="template-string token"><span class="string token">`Failed to execute beastify content script: </span><span class="interpolation token"><span class="interpolation-punctuation punctuation token">${</span>error<span class="punctuation token">.</span>message<span class="interpolation-punctuation punctuation token">}</span></span><span class="string token">`</span></span><span class="punctuation token">)</span><span class="punctuation token">;</span>
<span class="punctuation token">}</span>

<span class="comment token">/**
 * ポップアップを読み込んだ時、コンテンツスクリプトをアクティブなタブに挿入し、
 * クリックハンドラーを追加する。
 * スクリプトの挿入ができない場合、エラー処理をする。
 */</span>
browser<span class="punctuation token">.</span>tabs<span class="punctuation token">.</span><span class="function token">executeScript</span><span class="punctuation token">(</span><span class="punctuation token">{</span>file<span class="punctuation token">:</span> <span class="string token">"/content_scripts/beastify.js"</span><span class="punctuation token">}</span><span class="punctuation token">)</span>
<span class="punctuation token">.</span><span class="function token">then</span><span class="punctuation token">(</span>listenForClicks<span class="punctuation token">)</span>
<span class="punctuation token">.</span><span class="keyword token">catch</span><span class="punctuation token">(</span>reportExecuteScriptError<span class="punctuation token">)</span><span class="punctuation token">;</span></code></pre>

<p>開始するのは 96行です。ポップアップスクリプトはポップアップが読み込まれ次第、アクティブなタブのコンテンツスクリプトを実行し、その手段は <code><a href="/ja/docs/Mozilla/Add-ons/WebExtensions/API/tabs/executeScript">browser.tabs.executeScript()</a></code> API です。コンテンツスクリプトの実行が成功したら、タブが閉じられるかユーザーが別のページに移動するまで、コンテンツスクリプトがページにロードされたままになります。</p>

<p><code>browser.tabs.executeScript()</code> の呼び出しがよく失敗する理由は、コンテンツスクリプトをすべてのページでは実行できないことです。例えば、about:debugging のような権限のあるブラウザーページでは実行できませんし、<a href="https://addons.mozilla.org/">addons.mozilla.org</a> ドメイン内のページでも実行できません。失敗した場合、<code>reportExecuteScriptError()</code> は <code>"popup-content"</code> <code>&lt;div&gt;</code> を隠して、<code>"error-content"</code> <code>&lt;div&gt;</code> を表示し、エラーを <a href="/ja/Add-ons/WebExtensions/Debugging">console</a> にログ出力します。</p>

<p>コンテンツスクリプトの実行が成功したら、<code>listenForClicks()</code> を呼び出します。これはポップアップのクリックをリッスンします。</p>

<ul>
 <li><code>class="beast"</code> の付いたボタンがクリックされたら、<code>beastify()</code> を呼び出す</li>
 <li><code>class="reset"</code> の付いたボタンがクリックされたら、<code>reset()</code> を呼び出す</li>
</ul>

<p><code>beastify()</code> 関数は次の 3 つを行います:</p>

<ul>
 <li>特定の動物画像を指す URL をボタンクリックに割り当てる</li>
 <li>CSS を挿入してページの全コンテンツを隠し、その手段は <code><a href="/ja/Add-ons/WebExtensions/API/tabs/insertCSS">browser.tabs.insertCSS()</a></code> API</li>
 <li>コンテンツスクリプトに "beastify" 送り、その手段は <code><a href="/ja/docs/Mozilla/Add-ons/WebExtensions/API/tabs/sendMessage">browser.tabs.sendMessage()</a></code> API。ページを動物化するか尋ねて、動物画像への URL を渡す</li>
</ul>

<p><code>reset()</code> 関数は動物化を取り消す:</p>

<ul>
 <li>追加した CSS を削除し、その手段は <code><a href="/ja/Add-ons/WebExtensions/API/tabs/removeCSS">browser.tabs.removeCSS()</a></code> API</li>
 <li>コンテンツスクリプトにページをリセットする "reset" メッセージを送る</li>
</ul>

<h3 id="The_content_script" name="The_content_script">コンテンツスクリプト</h3>

<p>拡張機能のルートディレクトリー配下に "content_scripts" というディレクトリーを新しく作成します。続いてそこに "beastify.js" という名前のファイルを作成し、下記のスクリプトを記述します。</p>

<pre class="brush: js line-numbers  language-js"><code class="language-js"><span class="punctuation token">(</span><span class="keyword token">function</span><span class="punctuation token">(</span><span class="punctuation token">)</span> <span class="punctuation token">{</span>
  <span class="comment token">/**
   * グローバルなガード変数をチェック、設定する。
   * コンテンツスクリプトが再び同じページに挿入された場合、
   * 次は何もしない。
   */</span>
  <span class="keyword token">if</span> <span class="punctuation token">(</span>window<span class="punctuation token">.</span>hasRun<span class="punctuation token">)</span> <span class="punctuation token">{</span>
    <span class="keyword token">return</span><span class="punctuation token">;</span>
  <span class="punctuation token">}</span>
  window<span class="punctuation token">.</span>hasRun <span class="operator token">=</span> <span class="keyword token">true</span><span class="punctuation token">;</span>

  <span class="comment token">/**
   * 動物の画像の URL を受け取り、既存の動物をすべて削除し、次に
   * 画像を指す IMG 要素の作成・スタイル適用を行い、
   * 作成したノードをドキュメント内に挿入する
   */</span>
  <span class="keyword token">function</span> <span class="function token">insertBeast</span><span class="punctuation token">(</span>beastURL<span class="punctuation token">)</span> <span class="punctuation token">{</span>
    <span class="function token">removeExistingBeasts</span><span class="punctuation token">(</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="keyword token">let</span> beastImage <span class="operator token">=</span> document<span class="punctuation token">.</span><span class="function token">createElement</span><span class="punctuation token">(</span><span class="string token">"img"</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
    beastImage<span class="punctuation token">.</span><span class="function token">setAttribute</span><span class="punctuation token">(</span><span class="string token">"src"</span><span class="punctuation token">,</span> beastURL<span class="punctuation token">)</span><span class="punctuation token">;</span>
    beastImage<span class="punctuation token">.</span>style<span class="punctuation token">.</span>height <span class="operator token">=</span> <span class="string token">"100vh"</span><span class="punctuation token">;</span>
    beastImage<span class="punctuation token">.</span>className <span class="operator token">=</span> <span class="string token">"beastify-image"</span><span class="punctuation token">;</span>
    document<span class="punctuation token">.</span>body<span class="punctuation token">.</span><span class="function token">appendChild</span><span class="punctuation token">(</span>beastImage<span class="punctuation token">)</span><span class="punctuation token">;</span>
  <span class="punctuation token">}</span>

  <span class="comment token">/**
   * ページからすべての動物を削除する
   */</span>
  <span class="keyword token">function</span> <span class="function token">removeExistingBeasts</span><span class="punctuation token">(</span><span class="punctuation token">)</span> <span class="punctuation token">{</span>
    <span class="keyword token">let</span> existingBeasts <span class="operator token">=</span> document<span class="punctuation token">.</span><span class="function token">querySelectorAll</span><span class="punctuation token">(</span><span class="string token">".beastify-image"</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="keyword token">for</span> <span class="punctuation token">(</span><span class="keyword token">let</span> beast <span class="keyword token">of</span> existingBeasts<span class="punctuation token">)</span> <span class="punctuation token">{</span>
      beast<span class="punctuation token">.</span><span class="function token">remove</span><span class="punctuation token">(</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
  <span class="punctuation token">}</span>
  <span class="punctuation token">}</span>

  <span class="comment token">/**
   * バックグラウンドスクリプトからのメッセージをリッスンし、
   * "beastify()" か "reset()" を呼び出す。
   */</span>
  browser<span class="punctuation token">.</span>runtime<span class="punctuation token">.</span>onMessage<span class="punctuation token">.</span><span class="function token">addListener</span><span class="punctuation token">(</span><span class="punctuation token">(</span>message<span class="punctuation token">)</span> <span class="operator token">=</span><span class="operator token">&gt;</span> <span class="punctuation token">{</span>
    <span class="keyword token">if</span> <span class="punctuation token">(</span>message<span class="punctuation token">.</span>command <span class="operator token">===</span> <span class="string token">"beastify"</span><span class="punctuation token">)</span> <span class="punctuation token">{</span>
      <span class="function token">insertBeast</span><span class="punctuation token">(</span>message<span class="punctuation token">.</span>beastURL<span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="punctuation token">}</span> <span class="keyword token">else</span> <span class="keyword token">if</span> <span class="punctuation token">(</span>message<span class="punctuation token">.</span>command <span class="operator token">===</span> <span class="string token">"reset"</span><span class="punctuation token">)</span> <span class="punctuation token">{</span>
      <span class="function token">removeExistingBeasts</span><span class="punctuation token">(</span><span class="punctuation token">)</span><span class="punctuation token">;</span>
    <span class="punctuation token">}</span>
  <span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">;</span>

<span class="punctuation token">}</span><span class="punctuation token">)</span><span class="punctuation token">(</span><span class="punctuation token">)</span><span class="punctuation token">;</span></code></pre>

<p>このコンテンツスクリプトが最初にすることは、グローバル変数の <code>window.hasRun</code> のチェックです: セットされていればスクリプトは早くリターンし、そうでなければ <code>window.hasRun</code> をセットして処理し続けます。こうする理由は、ユーザーがポップアップを開くたびに、ポップアップはアクティブなタブのコンテンツスクリプトを実行して、そのために 1 つのタブに複数の実行スクリプトのインスタンスができてしまいます。これが起きると、最初のインスタンスだけが処理するのを確かめる必要があります。</p>

<p>その次に、始まる場所は 40行で、ここでコンテンツスクリプトはポップアップからのメッセージをリッスンし、その手段は <code><a href="/ja/Add-ons/WebExtensions/API/runtime/onMessage">browser.runtime.onMessage</a></code> API です。上で見たように、ポップアップスクリプトは 2種類の異なるメッセージを送ります: "beastify" と "reset"</p>

<ul>
 <li>メッセージが "beastify" なら、動物画像を指す URL が含まれるはずです。以前の "beastify" 呼び出しで追加された動物をすべて削除して、<code><a href="/ja/docs/Web/HTML/Element/img">&lt;img&gt;</a></code> 要素を作成、追加して、その <code>src</code> 属性に動物の URL をセットします。</li>
 <li>メッセージが "reset" なら、ただ追加された動物をすべて削除します。</li>
</ul>

<h3 id="The_beasts" name="The_beasts">動物</h3>

<p>最後に、動物の画像を用意しておく必要があります。</p>

<p>拡張機能のルートディレクトリー配下に   "beasts" という名前のディレクトリーを新しく作成し、その中に 3 つの画像を適切な名前で保存します。画像は <a href="https://github.com/mdn/webextensions-examples/tree/master/beastify/beasts">GitHub リポジトリ </a> から、またはここからでも取得できます。</p>

<p><img alt="" src="https://mdn.mozillademos.org/files/11459/frog.jpg" style="display: inline-block; height: 200px; margin: 20px; width: 200px;"><img alt="" src="https://mdn.mozillademos.org/files/11461/snake.jpg" style="display: inline-block; height: 200px; margin: 20px; width: 200px;"><img alt="" src="https://mdn.mozillademos.org/files/11463/turtle.jpg" style="display: inline-block; height: 200px; margin: 20px; width: 200px;"></p>

<h2 id="Testing_it_out" name="Testing_it_out">動かしてみよう</h2>

<p>正しいファイルが正しい場所にあるかどうか、もう一度確認してください。</p>

<pre class="line-numbers  language-html"><code class="language-html">beastify/

    beasts/
        frog.jpg
        snake.jpg
        turtle.jpg

    content_scripts/
        beastify.js

    icons/
        beasts-32.png
        beasts-48.png

    popup/
        choose_beast.css
        choose_beast.html
        choose_beast.js

    manifest.json</code></pre>

<p>Firefox 45 以降では、拡張機能をディスクから一時的にインストールできるようになりました。</p>

<p>Firefox で "about:debugging" を開き、"一時的なアドオンを読み込む" をクリックし、自分で作成した manifest.json ファイルを選択してください。拡張機能のアイコンが Firefox のツールバーに表示されているはずです。</p>

<p>{{EmbedYouTube("sAM78GU4P34")}}</p>

<p>適当なウェブページを開き、拡張機能のアイコンをクリックし、動物を選択してください。するとウェブページが動物の画像に置き換わるはずです。</p>

<p>{{EmbedYouTube("YMQXyAQSiE8")}}</p>

<h2 id="Developing_from_the_command_line" name="Developing_from_the_command_line">コマンドラインから開発する</h2>

<p>仮インストールを <a href="/ja/Add-ons/WebExtensions/Getting_started_with_web-ext">web-ext</a> ツールを用いて自動化できます。次を試してください:</p>

<pre class="brush: bash line-numbers  language-bash"><code class="language-bash">cd beastify
web-ext run</code></pre>
</div>