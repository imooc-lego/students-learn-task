Clipboard.d.ts文件定义

从这次作业中，关于ts的类型定义学到了很多东西，通过查看github中定义文件
[1.ts](https://github.com/zenorocha/clipboard.js/blob/734d36b6ffe3346304904a44f12e4f3f69330101/src/clipboard.d.ts#L17[)
[2.ts](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/clipboard/index.d.ts)

```ts
type Action = 'cut' | 'copy'

type triggerType = string | HTMLElement | HTMLCollection | NodeList

declare class MyClipboard {
  constructor (
    selector: triggerType, 
    options: MyClipboard.Options)
    destroy(): void
    resolveOptions(options: MyClipboard.Options | {}): void
    listenClick(trigger: triggerType): void
    on(type: Response, handler: (e: MyClipboard.Event) => void): this;
    on(type: string, handler: (...args: any[]) => void): this;
}

declare namespace MyClipboard {
  interface Options {
    action?(elem: Element): Action
    target?(elem: Element): Element
    text?(elem: Element): string
    container?: Element
  }
  interface Event {
    action: string;
    text: string;
    trigger: Element;
    clearSelection(): void;
  }
}
```

第一次知道了在namespace中也可以定义interface，学到了在接口中判断是否属于函数的方法。

