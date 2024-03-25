---
title: "日付を表示する"
---

<!-- 最初にTODOリストを整理したい -->


さて、このままでは数字の1が出てくるだけのコンポーネントになってしまうので、引数で渡された数字に応じて動的に表示する数字を変えたいです。

テストケースを増やしていきましょう。


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
+
+  it('数字の2を渡すと、2が見える', () => {
+    const { getByText } = render(<DateBox dateNumber={2} />);
+    const dateText = getByText('2');
+    expect(dateText).toBeInTheDocument();
+  });
});
```

これでテストが失敗し、REDな状態になりました。
では実装を修正していきます。



```diff ts:DateBox.tsx
export function DateBox({dateNumber}: {dateNumber: number}) {
    return (
        <div>
-            <p>1</p>
+            <p>{dateNumber}</p>
        </div>
    );
};
```

これで両方のテストケースがGREENになりました。
リファクタリングも、まだコードも小さく、このまますすめてみましょう。


