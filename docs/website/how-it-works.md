---
title: 为啥 TW 内核这么快
---

# TurboWarp 是怎么使 Scratch 作品以原本的 10 ~ 100 倍的速度运行的？

TurboWarp 在 Scratch 还在用**解释器**是时候抢先一步用上了**编译器**。这使得 TurboWarp 的运行速度能够根据作品情况在 10 到 100 倍之间波动，但这也使得[实时脚本编辑](#live-script-editing)失效。

|测试|Scratch|TurboWarp|
|:-:|:-:|:-:|
|[Quicksort](https://scratch.mit.edu/projects/320372816)<br>排序 200000 随机项<br>越短越好|8.871秒|0.0451秒|
|[Cycles Raytracer](https://scratch.mit.edu/projects/412737809/)<br>RES=1 samp=10 dof=.08<br>越短越好|814秒|15秒|
|[Faster SHA-256 Hash](https://scratch.mit.edu/projects/611788242/)<br>哈希频率<br>越高越好|146次/秒|3010次/秒|

(使用 Chromium 140，Arch Linux，i7 4790k CPU测试)

请看下列代码

![当开始被点击，重复执行，移动"我的变量"步](./assets/forever-move-my-variable-steps.png)

Scratch 的解释器在运行时会遍历一个[抽象语法树](https://en.wikipedia.org/wiki/Abstract_syntax_tree)，它的内部实现看起来是这样的：

```json
{
  "va[U{Cbi_NZpSOSx_kVA": {
    "opcode": "event_whenflagclicked",
    "inputs": {},
    "fields": {},
    "next": "tzXnZ{8G!xK|t^WAWF{m",
    "topLevel": true
  },
  "tzXnZ{8G!xK|t^WAWF{m": {
    "opcode": "control_forever",
    "inputs": {
      "SUBSTACK": {
        "name": "SUBSTACK",
        "block": "$xf$bq|xl(}RhT-K,taS"
      }
    },
    "fields": {},
    "next": null,
    "topLevel": false
  },
  "$xf$bq|xl(}RhT-K,taS": {
    "opcode": "motion_movesteps",
    "inputs": {
      "STEPS": {
        "name": "STEPS",
        "block": "cw__.I:g}Y~`:5KmO00q"
      }
    },
    "fields": {},
    "next": null,
    "topLevel": false
  },
  "cw__.I:g}Y~`:5KmO00q": {
    "opcode": "data_variable",
    "inputs": {},
    "fields": {
      "VARIABLE": {
        "name": "VARIABLE",
        "id": "`jEk@4|i[#Fk?(8x)AV.-my variable"
      }
    },
    "next": null,
    "topLevel": false
  }
}
```

每当 Scratch 执行任何一个代码块时，它都需要完成一大堆操作：

 - 它需要根据块的 ID 查找该块，并确定其操作码所对应的功能。
 - 如果该代码块有输入框，那么这些输入框本身也是块，并且必须按照与其他任何模块相同的步骤进行处理，任何嵌套的输入框也是这样。
 - 它会手动维护一个包含块、循环、条件、过程等元素的代码栈。
 - Scratch 的脚本是可以被打断的，因此所有操作都必须支持暂停，并且能够在稍后继续执行。
 - Scratch 的脚本在运行中可以修改，所以在运行前进行缓存是十分麻烦的，这几乎不可能。
 - Scratch 在执行任何一个简单的块时，其内部都会发生***数不清***的事情。

解释器的算力开销是在 JavaScript 本身的开销之上叠加的。由于这段代码涉及多种动态类型，因此对于 JavaScript 的即时编译器来说，对其进行优化可能会比较困难。

TurboWarp 的编译器会消除这些不必要的开销，因为它会将脚本直接转换为 JavaScript 函数。例如，上述脚本会变成：

```js
const myVariable = stage.variables["`jEk@4|i[#Fk?(8x)AV.-my variable"];
return function* () {
  while (true) {
    runtime.ext_scratch3_motion._moveSteps((+myVariable.value || 0), target);
    yield;
  }
};
```

值得注意的是：

 - 无需再查找积木 ID 或操作码了：它们都变成了 JavaScript！
 - 无需再手动查找输入内容了：它们就是 JavaScript 函数的参数！
 - 无需再进行手动状态维护：交给 JavaScript！
 - 由于它变成了一个单独的 JavaScript 函数，所以我们难以实现[即使脚本编辑](#live-script-editing)。
 - 如果 JavaScript 的即时编译器发现某个变量始终为数字类型，那么理论上它就可以对其进行相应的优化。
 - 与我们编写的 JavaScript 代码相比，这段代码显得非常奇怪，而且运行速度也会慢一丢丢，这是因为我们需要保持与 Scratch 的特性兼容。
 - 我们手动对 JavaScript 代码进行了格式化，并对一些变量进行了重命名，以使变成给人看的：而实际的代码使用的是像 `b0` 这样的变量名，而且几乎没有换行，乱的一批。

当然，这是一个非常简单的脚本，由于解释器产生的性能开销几乎可以忽略不计，在大多数作品中都是这样。只有当每帧执行数千个代码块时，解释器的性能开销才会变得明显。

这里有一个更复杂的例子：一种排序算法——冒泡排序。

```js
const length = stage.variables["O;aH~(njYNn}Bl@}!%pS-length-"];
const list = stage.variables["O;aH~(njYNn}Bl@}!%pS-list-list"];
const newLength = stage.variables["O;aH~(njYNn}Bl@}!%pS-new-"];
const i = stage.variables["O;aH~(njYNn}Bl@}!%pS-i-"];
const temp = stage.variables["O;aH~(njYNn}Bl@}!%pS-tmp-"];
return function fun1_sort () {
  length.value = list.value.length;
  // 重复执行直到 length = 0
  while (!compareEqual(length.value, 0)) {
    newLength.value = 0;
    i.value = 1;
    // 重复 length - 1 次
    for (var counter = ((+length.value || 0) - 1) || 0; counter >= 0.5; counter--) {
      // 将 i 增加 1
      i.value = ((+i.value || 0) + 1);
      // 如果列表第 i - 1 项大于第 i 项
      if (
        compareGreaterThan(
          list.value[((((i.value || 0) - 1) || 0) | 0) - 1] ?? "",
          list.value[((i.value || 0) | 0) - 1] ?? ""
        )
      ) {
        // 交换列表中第 i 项和第 i - 1 项
        temp.value = listGet(list.value, i.value);
        listReplace(
          list,
          i.value,
          list.value[((((+i.value || 0) - 1) || 0) | 0) - 1] ?? ""
        );
        listReplace(
          list,
          (+i.value || 0) - 1,
          temp.value
        );
        newLength.value = i.value;
      }
    }
    length.value = newLength.value;
  }
};
```

诸如 `listGet`、`listReplace` 和 `compareEqual` 等函数属于 TurboWarp 运行时的一部分，并且其实现方式是为了与 Scratch 的特性相匹配。下面展示了冒泡排序所使用的函数供您参考，对于这些函数而言，准确性与性能比可读性更为重要，因为它们通常需要高效率。

```js
const isNotActuallyZero = val => {
  if (typeof val !== 'string') return false;
  for (let i = 0; i < val.length; i++) {
    const code = val.charCodeAt(i);
    if (code === 48 || code === 9) {
      return false;
    }
  }
  return true;
};

const compareEqualSlow = (v1, v2) => {
  const n1 = +v1;
  if (isNaN(n1) || (n1 === 0 && isNotActuallyZero(v1))) return ('' + v1).toLowerCase() === ('' + v2).toLowerCase();
  const n2 = +v2;
  if (isNaN(n2) || (n2 === 0 && isNotActuallyZero(v2))) return ('' + v1).toLowerCase() === ('' + v2).toLowerCase();
  return n1 === n2;
};

const compareEqual = (v1, v2) => (typeof v1 === 'number' && typeof v2 === 'number' && !isNaN(v1) && !isNaN(v2) || v1 === v2) ? v1 === v2 : compareEqualSlow(v1, v2);

const compareGreaterThanSlow = (v1, v2) => {
  let n1 = +v1;
  let n2 = +v2;
  if (n1 === 0 && isNotActuallyZero(v1)) {
    n1 = NaN;
  } else if (n2 === 0 && isNotActuallyZero(v2)) {
    n2 = NaN;
  }
  if (isNaN(n1) || isNaN(n2)) {
    const s1 = ('' + v1).toLowerCase();
    const s2 = ('' + v2).toLowerCase();
    return s1 > s2;
  }
  return n1 > n2;
};

const compareGreaterThan = (v1, v2) => typeof v1 === 'number' && typeof v2 === 'number' && !isNaN(v1) ? v1 > v2 : compareGreaterThanSlow(v1, v2);

const listIndexSlow = (index, length) => {
  if (index === 'last') {
    return length - 1;
  } else if (index === 'random' || index === 'any') {
    if (length > 0) {
      return (Math.random() * length) | 0;
    }
    return -1;
  }
  index = (+index || 0) | 0;
  if (index < 1 || index > length) {
    return -1;
  }
  return index - 1;
};

const listIndex = (index, length) => {
  if (typeof index !== 'number') {
    return listIndexSlow(index, length);
  }
  index = index | 0;
  return index < 1 || index > length ? -1 : index - 1;
};

const listGet = (list, idx) => {
  const index = listIndex(idx, list.length);
  if (index === -1) {
    return '';
  }
  return list[index];
};

const listReplace = (list, idx, value) => {
  const index = listIndex(idx, list.value.length);
  if (index === -1) {
    return;
  }
  list.value[index] = value;
  list._monitorUpToDate = false;
};
```

### 实时脚本编辑 {#live-script-editing}

如果使用编译器来启动脚本，那么在运行时移动、删除或添加代码块，都无法像 Scratch 那样实时看到更改的效果，只有重新启动脚本才能使更改生效。我们认为或许可以通过一些方法来实现这一功能，但这些方法可能会降低性能或者增加极大的复杂性。这是我们终极目标，但目前来看还实现不了。