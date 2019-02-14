---
title: 使用vue-property-decorator
date: 2019-02-01 10:41:10
categories: 
  - Vue
tags: 
  - 插件
  - TypeScript
---
## @Emit
@Emit(event?: string) decorator
```typescript
import { Vue, Component, Emit } from 'vue-property-decorator'

@Component
export default class YourComponent extends Vue {
  count = 0

  @Emit()
  addToCount(n: number) {
    this.count += n
  }

  @Emit('reset')
  resetCount() {
    this.count = 0
  }

  @Emit()
  returnValue() {
    return 10
  }

  @Emit()
  promise() {
    return new Promise(resolve => {
      setTimeout(() => {
        resolve(20)
      }, 0)
    })
  }
}
```
等价于
```javascript
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    addToCount(n) {
      this.count += n
      this.$emit('add-to-count', n)
    },
    resetCount() {
      this.count = 0
      this.$emit('reset')
    },
    returnValue() {
      this.$emit('return-value', 10)
    },
    promise() {
      const promise = new Promise(resolve => {
        setTimeout(() => {
          resolve(20)
        }, 0)
      })

      promise.then(value => {
        this.$emit('promise', value)
      })
    }
  }
}
```
## @Inject

## @Model
@Model(event?: string, options: (PropOptions | Constructor[] | Constructor) = {}) decorator
```typescript
import { Vue, Component, Model } from 'vue-property-decorator'

@Component
export default class YourComponent extends Vue {
  @Model('change', { type: Boolean }) checked!: boolean
}
```
等价于
```javascript
export default {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: {
      type: Boolean
    },
  },
}
```
## @Prop
@Prop(options: (PropOptions | Constructor[] | Constructor) = {}) decorator
```typescript
import { Vue, Component, Prop } from 'vue-property-decorator'

@Component
export default class YourComponent extends Vue {
  @Prop(Number) propA!: number
  @Prop({ default: 'default value' }) propB!: string
  @Prop([String, Boolean]) propC!: string | boolean
}
```
等价于
```javascript
export default {
  props: {
    propA: {
      type: Number
    },
    propB: {
      default: 'default value'
    },
    propC: {
      type: [String, Boolean]
    },
  }
}
```
## @Provide
@Provide(key?: string | symbol) / @Inject(options?: { from?: InjectKey, default?: any } | InjectKey) decorator
```typescript
import { Component, Inject, Provide, Vue } from 'vue-property-decorator'

const symbol = Symbol('baz')

@Component
export class MyComponent extends Vue {
  @Inject() foo!: string
  @Inject('bar') bar!: string
  @Inject({ from: 'optional', default: 'default' }) optional!: string
  @Inject(symbol) baz!: string


  @Provide() foo = 'foo'
  @Provide('bar') baz = 'bar'
}
```
等价于
```typescript
const symbol = Symbol('baz')

export const MyComponent = Vue.extend({

  inject: {
    foo: 'foo',
    bar: 'bar',
    'optional': { from: 'optional', default: 'default' },
    [symbol]: symbol
  },
  data () {
    return {
      foo: 'foo',
      baz: 'bar'
    }
  },
  provide () {
    return {
      foo: this.foo,
      bar: this.baz
    }
  }
})
```
## @Watch
@Watch(path: string, options: WatchOptions = {}) decorator
```typescript
import { Vue, Component, Watch } from 'vue-property-decorator'

@Component
export default class YourComponent extends Vue {
  @Watch('child')
  onChildChanged(val: string, oldVal: string) { }

  @Watch('person', { immediate: true, deep: true })
  onPersonChanged1(val: Person, oldVal: Person) { }

  @Watch('person')
  onChildChanged2(val: Person, oldVal: Person) { }
}
```
等价于
```javascript
export default {
  watch: {
    'child': [
      {
        handler: 'onChildChanged',
        immediate: false,
        deep: false
      }
    ],
    'person': [
      {
        handler: 'onPersonChanged1',
        immediate: true,
        deep: true
      },
      {
        handler: 'onPersonChanged2',
        immediate: false,
        deep: false
      }
    ]
  },
  methods: {
    onChildChanged(val, oldVal) { },
    onPersonChanged1(val, oldVal) { }
    onPersonChanged2(val, oldVal) { }
  }
}
```
### 注意事项
- 父组件的函数的参数问题

## @Component (provided by vue-class-component)
@Component({
  components: {
    HelloWorld,
  },
})
## Mixins (the helper function named mixins provided by vue-class-component)

## 参考
https://github.com/kaorun343/vue-property-decorator
