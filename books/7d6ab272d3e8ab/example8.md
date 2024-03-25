---
title: "初日の表示位置を正しい位置にする"
---




それではこのblankのDateBoxを使って、曜日の表示位置を調整しましょう。
テストコードでこの仕様を表現していきます。
ある月の初日がListの適切な番地に存在するか、を検証しましょう。

例えば2024年の2月1日は木曜日なので、リストの5番目に2月1日が来ているかどうかを検証する形にしましょう。


```diff ts:__tests__/components/calendar/DateBoxList.spec.tsx
import React from 'react';
import { render } from '@testing-library/react';
import { DateBoxList } from '@/components/calendar/DateBoxList';

describe('DateBoxList', () => {
  it('should render the correct number of DateBoxes', () => {
    const date = new Date("2024-02-01");
-    const { getAllByTestId, getByText } = render(<DateBoxList date={date} />);
-
-    const dateBoxes = getAllByTestId('date-box');
-    expect(dateBoxes).toHaveLength(29);
+    const { getByText } = render(<DateBoxList date={date} />);

    const firstDate = getByText('1');
    const lastDate = getByText('29');
    expect(firstDate).toBeInTheDocument();
    expect(lastDate).toBeInTheDocument();
  });
+
+  it('should render blank DateBoxes before the first day of the month', () => {
+    const date = new Date("2024-02-01");
+    const { getAllByTestId } = render(<DateBoxList date={date} />);
+
+    const dateBoxes = getAllByTestId('date-box');
+    expect(dateBoxes).toHaveLength(33);
+    expect(dateBoxes[0]).toHaveTextContent('');
+    expect(dateBoxes[1]).toHaveTextContent('');
+    expect(dateBoxes[2]).toHaveTextContent('');
+    expect(dateBoxes[3]).toHaveTextContent('');
+    expect(dateBoxes[4]).toHaveTextContent(/^1木$/);
+  });
});
```

それでは実装を修正していきます。


```diff ts:components/calendar/DateBoxList.tsx
import { DateBox } from "./DateBox";

export function DateBoxList({date}: {date: Date}) {
    const firstDate = new Date(date.getFullYear(), date.getMonth(), 1);
    const lastDate = new Date(date.getFullYear(), date.getMonth() + 1, 0);
    const dateList = [];

+    for (let i = 1; i <= firstDate.getDay(); i++) {
+        dateList.push(undefined);
+    }

    for (let i = firstDate.getDate(); i <= lastDate.getDate(); i++) {
        dateList.push(new Date(date.getFullYear(), date.getMonth(), i));
    }
    return (
        <ul className="grid grid-cols-7 w-full">
            {dateList.map((date, index) => (
-                <DateBox key={index} date={date} />
+                {date ? <DateBox key={index} date={date} /> : <DateBox key={index} blank />}
            ))}
        </ul>
    );
}
```


これで、実装を見てみると、1日の表示位置が想定通りの位置になるように配置されるようになりました。

