---
title: "空の日付セルを用意する"
---


ここで一度コンポーネントを表示してみて、どのような仕上がりになっているのか確認してみましょう。
2024年の5月のカレンダーを一旦表示して見るようにしましょう。

![何かがおかしい…](/images/7d6ab272d3e8ab/wrong_datelist.png)

ここで表示してみたことであることに気づきます。


そうです。曜日の表示位置が普段見ているカレンダーの位置と異なっています。
普通は日曜日、または月曜日に1週間が始まりますよね。
**2024/7/1は水曜日**なのですが、左上詰めで表示されているが故に、**カレンダーが水曜日にから始まるような形になってしまっています…**

今回は日曜日が左に来るようなカレンダーを想定しましょう。

そのためには**当月の初日の曜日に合うように、空欄のセルで埋める**ような仕様にしようと思います。
空欄の日付のセルを表示できるようにして、当月の初日の曜日が合うようにDateBoxListの前を**空欄セルで埋める**ような実装としてみましょう。

# RED

ではまず、**DateBoxを空欄のセルとして表示できるような実装**を進めてみましょう。
まず、テストコードを書きます。


```diff ts:__tests__/components/calendar/DateBox.spec.tsx
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
    const date = new Date("2023-05-21");
    const { getByText } = render(<DateBox date={date} />);
    const dayOfWeek = getByText('日');
    expect(dayOfWeek).toBeInTheDocument();
  });
+
+  it('blankオプションを渡すと、日付が見えない空欄セルが見える', async () => {
+    const { queryByText } = render(<DateBox blank />);
+    const cell = await queryByText("paragraph");
+    expect(cell).not.toBeInTheDocument();
+  });
});
```

# GREEN

では実装を進めてみます。


```diff ts:components/calendar/DateBox.tsx
-export function DateBox({date}: {date: Date}) {
+export function DateBox({
+    date,
+    blank,
+}:
+{
+    date?: Date,
+    blank?: boolean
+}) {
  
    function translateDayOfWeek(date: Date): string {
        return ['日', '月', '火', '水', '木', '金', '土'][date.getDay()];
    }

    return (
        <div data-testid="date-box" className="bg-white border border-gray-300 h-32">
-            <p>{date.getDate()}</p>
-            <p>{translateDayOfWeek(date)}</p>
+            {!blank && date && (
+                <>
+                    <p>{date.getDate()}</p>
+                    <p>{translateDayOfWeek(date)}</p>
+                </>
+            )}
        </div>
    );
};
```

これでDateBoxコンポーネントにblank属性を指定することで、何もないセルを表示することができるようになりました。


# リファクタリング

この時点ではリファクタリングは大丈夫そうだと判断しました。



