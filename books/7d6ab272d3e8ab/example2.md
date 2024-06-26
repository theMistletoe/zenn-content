---
title: "はじめのTDDサイクル"
---

<!-- 最初にTODOリストを整理したい -->


おおよそのコンポーネント設計をしたところではじめに戻って、どこからコードを書いていきましょうか?
もちろんテストコードから書いていきます!

最初に書き始めるのは、サイズの小さい部品から進めてみることにしましょう。
この場合、最も小さなコンポーネントであるDateBoxになりそうです。
こちらのテストコードから書いていきましょう。


# RED


`__tests__/components/calendar/DateBox.spec.tsx`を作成し、テストコードを書いていきます。

```ts:DateBox.spec.tsx
import React from 'react';
import { render } from '@testing-library/react';
import { DateBox } from '@/components/calendar/DateBox';

describe('DateBox', () => {
  it('数字の1を渡すと、1が見える', () => {
    const { getByText } = render(<DateBox dateNumber={1} />);
    const dateText = getByText('1');
    expect(dateText).toBeInTheDocument();
  });
});
```

DateBoxコンポーネントに日付を示す数字1を渡すことで、日付の「1」が表示されていることをテストするコードです。

このまま実行すると、そもそもコンポーネントがないのでコンポーネントが見つからないエラーとなります。
テストコードを実行したので、まずはREDにしていきます。


`components/calendar/DateBox.tsx`を新規作成し、REDとなるような実装を書いていきましょう。

```ts:DateBox.tsx
export function DateBox({dateNumber}: {dateNumber: number}) {
    return (
        <div>
            <p></p>
        </div>
    );
};
```


これでコンポーネントをインポート可能になり、想定通りテストがREDで失敗する状態になりました。

# GREEN

では、次にGREENにしていきます。

一旦教科書どおりに進めるために、仮実装を使ってとにかくテストが通る状態を作りましょう。


```ts:DateBox.tsx
export function DateBox({dateNumber}: {dateNumber: number}) {
    return (
        <div>
            <p>1</p>
        </div>
    );
};
```

誰が見ても「ん？」となる実装ですが、これで良いのです。
これで「確実に」数字の1が描画されていることがわかります。
試しに実装側の数字を「2」に変えてみましょう。

```ts:DateBox.tsx
export function DateBox({dateNumber}: {dateNumber: number}) {
    return (
        <div>
            <p>2</p> {/* 1→2に変えてみる */}
        </div>
    );
};
```

すると、テストがまた想定通り失敗するはずです。
フロントエンドのテストに慣れていない場合、「このテストコードは正しくテストできているのか？」という場面がよくあるかと思います。
そのような場合は、このように実装側を修正することで、テストコードが思ったとおりにバグを検出できるのかどうか、を見てみるよにしましょう。

再びテストに合格するように、数字を戻しておきます。


```ts:DateBox.tsx
export function DateBox({dateNumber}: {dateNumber: number}) {
    return (
        <div>
            <p>1</p> {/* 1に戻す */}
        </div>
    );
};
```





# リファクタリング

さて、RED→GREENときたら、次はリファクタリングです。

ただここではリファクタリングはちょっと思いつかなかったです。
いかんせんシンプル過ぎますし、1人でやっているので…

TDDをする際には、ペアプロやモブプロを一緒に実践することをお勧めします。
もう1人の目が入ることで、より良い設計を見つけたり、議論することが出来ます。


