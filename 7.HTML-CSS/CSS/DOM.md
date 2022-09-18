# DOM节点的获取

# DOM树的遍历
```js
function visitNode(n) {
  if (n instanceof Comment) {     
    //  注释
    console.info('Comment node ---', n.textContent)
  }
  if (n instanceof Text) {   
    // 文本
    // console.info('Text node ---', n.textContent?.trim()) // ?.trim 用于去掉空格  注意：换行也算一个text node
    // 去掉换行节点
    const t = n.textContent?.trim();     // 这个可以去掉字符串的首尾空格
    if (t) {
      console.info('Text node ---', t);
    }
  }
  if (n instanceof HTMLElement) {
    // element
    console.info('Element node ---', `<${n.tagName.toLowerCase()}>`)
  }
}


function dfs(root) {
  visitNode(root);
  let childNodes = root.childNodes  // .childNodes 和 .children 不一样  .childNodes 获取包含所有的node类型。.children 是只获取元素不获取文本和注释
  childNodes = [...childNodes]   // 最好转化为数组好计算
  // if (childNodes.length) {
    childNodes.forEach(child => {     // forEach遍历可迭代的  
      dfs(child);   // 递归
    })
  // }
}



// 广度优先遍历(队列)

function bfs(root) {
  const queue = [];   
  queue.push(root);
  while (queue.length > 0) {
    const curNode = queue.shift();
    if (curNode == null) break;

    visitNode(curNode);
    
    // 子节点入队
    const childNodes = curNode.childNodes;   // .childNodes 这是一个关键点
    if (childNodes.length) {  // 用childNodes.length 有什么用呢？   
      childNodes.forEach(child => queue.push(child))   // 尽管childNodes本身是一个类数组，但是内置了forEach方法
    }
  }
}
```

# DOM高度等
对应文章：https://juejin.cn/post/7011062379546935303
```js
网页可见区域宽： document.body.clientWidth (Element.clientWidth 属性表示元素的内部宽度，以像素计。该属性包括内边距，但不包括垂直滚动条（如果有）、边框和外边距 Element.clientHeight 属性同上)
网页可见区域高： document.body.clientHeight
网页可见区域宽： document.body.offsetWidth (包括边线的宽) (分别表示元素的布局宽度和布局高度，是由 元素的边框、 元素的padding， 滚动条的宽度（如果存在的话）以及CSS设置的宽度)
网页可见区域高： document.body.offsetHeight (包括边线的高)
网页正文全文宽： document.body.scrollWidth (包括屏幕上不可见的内容)
网页正文全文高： document.body.scrollHeight (包括屏幕上不可见的内容)
网页被卷去的高： document.body.scrollTop (scrollTop和scrollLeft的值仅在存在滚动条的元素上有效，否则他们的值恒定为0)
网页被卷去的左： document.body.scrollLeft

offsetParent: 返回一个指向最近的（只包含层级的最近）包含该元素的定位元素或者最近的table, td, th, body元素

元素的大小以及相对视口的位置：Element.getBoundingClientRect()
元素的实际高度：document.getElementById("div").offsetHeight (元素的边框、 元素的padding， 滚动条的宽度（如果存在的话）以及CSS设置的宽度)
元素的实际宽度：document.getElementById("div").offsetWidth
元素的实际距离其offsetParent元素左边界的距离：document.getElementById("div").offsetLeft
元素的实际距离其offsetParent元素上边界的距离：document.getElementById("div").offsetTop
```

# 监听浏览器窗口变化
```js
window.onresize = () => {
      //打印浏览器宽高
    console.log(document.body.clientWidth， '==> BODY对象宽度'）
    console.log(document.body.clientHeight， '==> BODY对象高度'）
    console.log(document.documentElement.clientWidth， '==> 可见区域宽度'）
    console.log(document.documentElement.clientHeight， '==> 可见区域高度'）
}
```