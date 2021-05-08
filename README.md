# 使用
```
// 全局
import SearchForm from 'element-search-form'
Vue.use(SearchForm);

// 单个组件使用
import SearchForm from 'element-search-form'
export default {
  components: {
    SearchForm
  },
  ...
}
```

组建中使用：
```
<search-form v-model="queryParams" :config="searchConfig" @search="getData('search')">
  <div slot="custom-slot" slot-scope="{slotData}">
    <!-- 组件自身的queryParams和slot暴露出来的slotData是全等的 -->
    支持自定义slot：{{queryParams}} {{slotData}}
  </div>
</search-form>

<script>
export default {
  queryParams: {
    aaa: '默认值111',
    bbb: '',
    xxx: '',
    otherPropety1: '...其他和搜索无关的属性',
    otherPropety1: '...其他和搜索无关的属性',
  },
  searchConfig: [
    {slot: 'custom-slot'}, // slot类型的对象不需要传其他参数
    {type: 'input', label: '用户名称', model: 'aaa-绑定v-model中的aaa'},
    {type: 'select', label: '用户属性', model: 'bbb-绑定v-model中的bbb', options: [
      {label: '选项1', value: '111'},
      {label: '选项2', value: '222'},
    ]},
    {type: 'date-picker', label: '时间', model: 'xxx'},
  ],
}
</script>
```

# 参数
| 属性 | 类型 | 描述 |
| - | - | - |
| value(使用v-model即可) | Object(必需) | 绑定的值 |
| config | Array | 生成Element-From组件 |
| labelWidth | String(必非需) | 同Element-Form组件的label-width属性 |
| search | Funtion(必须) | 点击搜索触发 |

# 需要注意的点
- search方法：`@search="getData('search')"`一般会手动传一个参数，在getData函数中方便判断，因为搜索的时候我们一般会把当前页的页码重置为1
