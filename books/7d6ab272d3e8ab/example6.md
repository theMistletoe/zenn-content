---
title: "月の日付たちを表示する"
---


これまで、1つの日付のセルのコンポーネントについて、進めてきました。
それではこのコンポーネントをつなげて、1月の日付の塊を作っていきましょう！
DateBoxを集めたDateBoxListというコンポーネントとして作っていこうと思います。

# RED

まずはテストコードを書いてみましょう。


```ts:DateBoxList.spec.tsx
import React from 'react';
import { render } from '@testing-library/react';
import { DateBoxList } from '@/components/calendar/DateBoxList';

describe('DateBoxList', () => {
  it('should render the correct number of DateBoxes', () => {
    const date = new Date("2024-02-01");
    const { getAllByTestId, getByText } = render(<DateBoxList date={date} />);

    const dateBoxes = getAllByTestId('date-box');
    expect(dateBoxes).toHaveLength(29);

    const firstDate = getByText('1');
    const lastDate = getByText('29');
    expect(firstDate).toBeInTheDocument();
    expect(lastDate).toBeInTheDocument();
  });
});
```

DateBoxListにDateを渡すことで、
`date-box`というdata-testidを持った要素が、その月の日数分表示されるような想定にしましょう。

# GREEN

それでは実装に入ってみましょう。
まずは準備的に、DateBoxをテストで取得できるようdata-testidを付与しましょう

```diff ts:DateBox.tsx
export function DateBox({date}: {date: Date}) {
  
    function translateDayOfWeek(date: Date): string {
        return ['日', '月', '火', '水', '木', '金', '土'][date.getDay()];
    }

    return (
-        <div>
+        <div data-testid="date-box">
            <p>{date.getDate()}</p>
            <p>{translateDayOfWeek(date)}</p>
        </div>
    );
};
```

ではその上でDateBoxListコンポーネントを実装していきましょう！

```ts:components/calendar/DateBoxList.tsx
import { DateBox } from "./DateBox";

export function DateBoxList({date}: {date: Date}) {
    const firstDate = new Date(date.getFullYear(), date.getMonth(), 1);
    const lastDate = new Date(date.getFullYear(), date.getMonth() + 1, 0);
    const dateList = [];
    for (let i = firstDate.getDate(); i <= lastDate.getDate(); i++) {
        dateList.push(new Date(date.getFullYear(), date.getMonth(), i));
    }
    return (
        <div>
            {dateList.map((date, index) => (
                <DateBox key={index} date={date} />
            ))}
        </div>
    );
}
```

これでひとまず、当月の日付一覧を表示することができるようになりました。

# リファクタリング

では少しリファクタリングしていきます。
使用しているタグ属性を`<div>`から変えておきましょう。
UIやコンポーネントの振る舞いには影響が出ないはずです。


```diff ts:components/calendar/DateBoxList.tsx
import { DateBox } from "./DateBox";

export function DateBoxList({date}: {date: Date}) {
    const firstDate = new Date(date.getFullYear(), date.getMonth(), 1);
    const lastDate = new Date(date.getFullYear(), date.getMonth() + 1, 0);
    const dateList = [];
    for (let i = firstDate.getDate(); i <= lastDate.getDate(); i++) {
        dateList.push(new Date(date.getFullYear(), date.getMonth(), i));
    }
    return (
-        <div>
+        <ul>
            {dateList.map((date, index) => (
+               <li key={index}>
                    <DateBox key={index} date={date} />
+               </l>
            ))}
-        </div>
+        </ul>
    );
}
```


続いて、stylingについても修正していきましょう。
こちらに関しては、ふるまいを変えるわけではないので、テストを壊すことなく修正していきます。

```diff ts:components/calendar/DateBoxList.tsx
import { DateBox } from "./DateBox";

export function DateBoxList({date}: {date: Date}) {
    const firstDate = new Date(date.getFullYear(), date.getMonth(), 1);
    const lastDate = new Date(date.getFullYear(), date.getMonth() + 1, 0);
    const dateList = [];
    for (let i = firstDate.getDate(); i <= lastDate.getDate(); i++) {
        dateList.push(new Date(date.getFullYear(), date.getMonth(), i));
    }
    return (
-        <ul>
+        <ul className="grid grid-cols-7 w-full">
            {dateList.map((date, index) => (
                <DateBox key={index} date={date} />
            ))}
        </ul>
    );
}
```

```diff ts:components/calendar/DateBox.tsx
export function DateBox({date}: {date: Date}) {
  
    function translateDayOfWeek(date: Date): string {
        return ['日', '月', '火', '水', '木', '金', '土'][date.getDay()];
    }

    return (
-        <div data-testid="date-box">
+        <div data-testid="date-box" className="bg-white border border-gray-300 h-32">
            <p>{date.getDate()}</p>
            <p>{translateDayOfWeek(date)}</p>
        </div>
    );
};
```
