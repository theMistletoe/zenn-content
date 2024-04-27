---
title: "曜日を見えるようにする"
---

# RED

ではもともと進めたかった、曜日を表示する実装に入っていきましょう。
skip句を外して、もともと実装したかったケースのテストコードが実行されるようにしましょう。


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

-  it.skip('日付を指定すると、曜日が見える', () => {
+  it('日付を指定すると、曜日が見える', () => {
    const date = new Date("2023-05-21");
    const { getByText } = render(<DateBox date={date} />);
    const dayOfWeek = getByText('日');
    expect(dayOfWeek).toBeInTheDocument();
  });
});
```

これできれいに曜日の表示をテストするコード飲みがREDになる状態になりました。

# GREEN

ではこの状態で、実装を進めてGREENにしていきましょう。

```diff ts:DateBox.tsx
export function DateBox({date}: {date: Date}) {
    return (
        <div>
            <p>{date.getDate()}</p>
+            <p>{['日', '月', '火', '水', '木', '金', '土'][date.getDay()]}</p>
        </div>
    );
};
```

これで曜日も表示されるようになりました！

# リファクタリング

ここでリファクタリングです。
曜日を表示する実装をしましたが、やや一目では分かりづらい実装に感じます。

曜日を表していることがわかりやすい関数に抽出して、関数名から推測しやすくなるようにリファクタしましょう。

```diff ts:DateBox.tsx
export function DateBox({date}: {date: Date}) {
  
+    function translateDayOfWeek(date: Date): string {
+        return ['日', '月', '火', '水', '木', '金', '土'][date.getDay()];
+    }

    return (
        <div>
            <p>{date.getDate()}</p>
-            <p>{['日', '月', '火', '水', '木', '金', '土'][date.getDay()]}</p>
+            <p>{translateDayOfWeek(date)}</p>
        </div>
    );
};
```

これで曜日を表していることをわかりよくしてみました。

