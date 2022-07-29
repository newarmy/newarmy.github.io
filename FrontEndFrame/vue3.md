# vue3
## vue 响应性 
### reactive()  
返回的是一个原始对象的 Proxy，它和原始对象是不相等的  
只有代理是响应式的，更改原始对象不会触发更新。因此，使用 Vue 的响应式系统的最佳实践是 仅使用你声明对象的代理版本。  
``` javascript  
const raw = {}
const proxy = reactive(raw)

// 代理和原始对象不是全等的
console.log(proxy === raw) // false
```  
reactive() 的局限性:  
1. 仅对对象类型有效（对象、数组和 Map、Set 这样的集合类型），而对 string、number 和 boolean 这样的 原始类型 无效。  
2. 因为 Vue 的响应式系统是通过 property 访问进行追踪的，因此我们必须始终保持对该响应式对象的相同引用。这意味着我们不可以随意地“替换”一个响应式对象，因为这将导致对初始引用的响应性连接丢失：  
``` javascript  
  let state = reactive({ count: 0 })

// 上面的引用 ({ count: 0 }) 将不再被追踪（响应性连接已丢失！）
state = reactive({ count: 1 })  
```  
### ref() 定义响应式变量  
为了解决 reactive() 带来的限制，Vue 也提供了一个 ref() 方法来允许我们创建可以使用任何值类型的响应式 ref：  
ref() 从参数中获取到值，将其包装为一个带 .value property 的 ref 对象  
ref() 使我们能创造一种任意值的 “引用” 并能够不丢失响应性地随意传递。这个功能非常重要，因为它经常用于将逻辑提取到 组合函数 中。  
1. ref 在模板中的解包  
   - 当 ref 在模板中作为顶层 property 被访问时，它们会被自动“解包” 
   ``` javascript  
   const count = ref(0)
    // template 中
    {{ count }} <!-- 无需 .value -->
   ```

   - 如果一个 ref 是文本插值(即一个 \{\{ \}\} 符号)计算的最终值  
   ``` javascript  
    const object = { foo: ref(1) }
    // 不能解包
    {{ object.foo + 1 }}
    // 计算的最终值 可以解包
    {{ object.foo }}
   ```  
2. ref 在响应式对象中的解包
    - 当一个 ref 作为一个响应式对象的 property 被访问或更改时，它会自动解包  
    - 如果将一个新的 ref 赋值给一个关联了已有 ref 的 property，那么它会替换掉旧的 ref  
3. 数组和集合类型的 ref 解包  
 不像响应式对象，当 ref 作为响应式数组或像 Map 这种原生集合类型的元素被访问时，不会进行解包。  

### 响应原理  
``` javascript  
let A0 = 1
let A1 = 2
let A2
function update() {
  A2 = A0 + A1
}
whenDepsChange(update)
```  
whenDepsChange(update) 函数有如下的任务:
1. 当一个变量被读取时进行追踪。例如我们执行了表达式 A0 + A1 的计算，则 A0 和 A1 都被读取到了。

2. 如果一个变量在当前运行的副作用中被读取了，就将该副作用设为此变量的一个订阅者。例如由于 A0 和 A1 在 update() 执行时被访问到了，则 update() 需要在第一次调用之后成为 A0 和 A1 的订阅者。

3. 探测一个变量的变化。例如当我们给 A0 赋了一个新的值后，应该通知其所有订阅了的副作用重新执行。

[参考1](https://staging-cn.vuejs.org/guide/extras/reactivity-in-depth.html#state-machines )   
[参考2](https://staging-cn.vuejs.org/guide/essentials/reactivity-fundamentals.html)  

## 组合式函数  
