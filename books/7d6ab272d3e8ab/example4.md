---
title: "曜日を見えるようにする？"
---

# RED

では次に曜日も見えるようにして見ましょう。
まずはテストを書いていきます。

```diff ts:DateBox.spec.tsx
import React from 'react';
import { render } from '@testing-library/react';
import { DateBox } from '@/components/calendar/DateBox';

describe('DateBox', () => {
  it('数字の1を渡すと、1が見える', () => {
    const { getByText } = render(<DateBox dateNumber={1} />);
    const dateText = getByText('1');
    expect(dateText).toBeInTheDocument();
  });

  it('数字の2を渡すと、2が見える', () => {
    const { getByText } = render(<DateBox dateNumber={2} /);
    const dateText = getByText('2');
    expect(dateText).toBeInTheDocument();
  });

+  it('曜日が見える', () => {
+    // テスト書くぞ〜
+  });
});
```

ここではたと、「あれ、そもそもこの設計、ダメだな…」と気づきました。
日付と曜日を表示するのはこのDateBoxの責務なので、インターフェースとしてはDate型の値を渡すほうが良さそうです。

ではテストコードを書いていきましょう。


```diff ts:DateBox.spec.tsx
import React from 'react';
import { render } from '@testing-library/react';
import { DateBox } from '@/components/calendar/DateBox';

describe('DateBox', () => {
  it('数字の1を渡すと、1が見える', () => {
    const { getByText } = render(<DateBox dateNumber={1} />);
    const dateText = getByText('1');
    expect(dateText).toBeInTheDocument();
  });

  it('数字の2を渡すと、2が見える', () => {
    const { getByText } = render(<DateBox dateNumber={2} /);
    const dateText = getByText('2');
    expect(dateText).toBeInTheDocument();
  });

  it('日付を指定すると、曜日が見える', () => {
+    const date = new Date("2023-05-21");
+    const { getByText } = render(<DateBox date={date} />);
+    const dayOfWeek = getByText('日');
+    expect(dayOfWeek).toBeInTheDocument();
  });
});
```


ここで、**コンポーネントのインターフェース**を変えようとしています。
このまま日付のテストケースを実装しようとすると、既存の数字を表示するテストコードが失敗するようになります。

REDとGREENの状態が混在してはいけないので、ここで一旦追加したテストケースについて進めることをやめ、**既存のテストケースをDate型を使うように修正**していきます。
追加した曜日のテストケースは後で使用するので、今はskip句を使うことで、実行対象のテストケースから除外しておきましょう。


```diff ts:DateBox.spec.tsx
import React from 'react';
import { render } from '@testing-library/react';
import { DateBox } from '@/components/calendar/DateBox';

describe('DateBox', () => {
  it('数字の1を渡すと、1が見える', () => {
    const { getByText } = render(<DateBox dateNumber={1} />);
    const dateText = getByText('1');
    expect(dateText).toBeInTheDocument();
  });

  it('数字の2を渡すと、2が見える', () => {
    const { getByText } = render(<DateBox dateNumber={2} /);
    const dateText = getByText('2');
    expect(dateText).toBeInTheDocument();
  });

-  it('日付を指定すると、曜日が見える', () => {
+  it.skip('日付を指定すると、曜日が見える', () => {
    const date = new Date("2023-05-21");
    const { getByText } = render(<DateBox date={date} />);
    const dayOfWeek = getByText('日');
    expect(dayOfWeek).toBeInTheDocument();
  });
});
```

これでskipを除き、すべてのテストがGREENになりました。

この状態で、既存のテスト・実装をDate型を利用するように修正していきましょう。
まずは既存コードのインターフェースを修正するため、既存テストコードをREDの状態にしましょう。

```diff ts:DateBox.spec.tsx
import React from 'react';
import { render } from '@testing-library/react';
import { DateBox } from '@/components/calendar/DateBox';

describe('DateBox', () => {

-  it('数字の1を渡すと、1が見える', () => {
-    const { getByText } = render(<DateBox dateNumber={1} />);
-    const dateText = getByText('1');
+  it('2023/5/21を渡すと、21が見える', () => {
+    const date = new Date("2023-05-21");
+    const { getByText } = render(<DateBox date={date} />);
+    const dateText = getByText('21');
    expect(dateText).toBeInTheDocument();
  });

-  it('数字の2を渡すと、2が見える', () => {
-    const { getByText } = render(<DateBox dateNumber={2} /);
-    const dateText = getByText('2');
+  it('2024/6/1を渡すと、1が見える', () => {
+    const date = new Date("2024-06-01");
+    const { getByText } = render(<DateBox date={date} />);
+    const dateText = getByText('1');
    expect(dateText).toBeInTheDocument();
  });

  it.skip('日付を指定すると、曜日が見える', () => {
    const date = new Date("2023-05-21");
    const { getByText } = render(<DateBox date={date} />);
    const dayOfWeek = getByText('日');
    expect(dayOfWeek).toBeInTheDocument();
  });
});
```

これでインターフェースをDate型に変える準備が整いました。

# GREEN


それでは実装に入っていきます。GREENにしていきましょう。


```diff ts:DateBox.tsx
-export function DateBox({dateNumber}: {dateNumber: number}) {
+export function DateBox({date}: {date: Date}) {
    return (
        <div>
-            <p>{dateNumber}</p>
+            <p>{date.getDate()}</p>
        </div>
    );
};
```

これでDateBoxのコンポーネントで日付・曜日の概念を扱いやすくなりました。

# リファクタリング

ここでもまだいいかな、という印象ですね。
ただ大事なのは、この**リファクタリングするフェーズを忘れない**ということです。

テストファーストで実装を進めるめていると、GREENなった瞬間に「できた！」とうれしくなって、ついつい次に行きたくなってしまいます。
TDDを実践する場合は、より良い設計や書き方、インターフェースがないかを常に考えるようにしましょう。

