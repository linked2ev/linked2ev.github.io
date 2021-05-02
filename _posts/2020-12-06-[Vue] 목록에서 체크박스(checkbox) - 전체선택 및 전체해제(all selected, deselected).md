---
layout: post
title:  "[Vue] 목록에서 체크박스(checkbox) - 전체선택 및 전체해제(all selected, deselected)"
subtitle:   "[Vue]"
categories: vue
tags: vue-devhistory
comments: true
---

Vue에서 체크박스 전체 선택 및 전체 해제에 대한 코드 포스팅

<br><br> 


---

github 코드 : [https://github.com/linked2ev/springboot-thymeleaf-vue](https://github.com/linked2ev/springboot-thymeleaf-vue/blob/master/src/main/resources/templates/views/example/list-checkbox.html)

---

```html
 <table class="table">
    <thead>
    <tr>
        <th scope="col">
            <input type="checkbox"
                    v-model="allChecked"
                    @click="checkedAll($event.target.checked)"
            >
        </th>
    </tr>
    </thead>
    <tbody>
    <tr v-for="(item, index) in boardList" :key="item.boardId">
        <td>
            <input type="checkbox"
                    :id="'check_' + item.boardId"
                    :value="item.boardId"
                    v-model="item.selected"
                    @change="selected($event)"
            >
        </td>
    </tr>
    </tbody>
</table>
```

```js
let app = new Vue({
    el: '#app',
    data() {
        return {
            boardList: [],
            allChecked: false
        }
    },
    methods: {
        getList() {
            axios.get('/example/api/boards', {
            })
            .then((response) => {
                console.log(response.data);
                this.boardList = response.data;
            })
            .catch((error) => {
                console.log(error);
            })
            .finally( () => {
            });
        },
        checkedAll(checked) {
            this.allChecked = checked
            for (let i in this.boardList) {
                this.boardList[i].selected = this.allChecked;
            }
        },
        selected (e) {
            for (let i in this.boardList) {
                if(! this.boardList[i].selected) {
                    this.allChecked = false;
                    return;
                } else {
                    this.allChecked = true;
                }
            }
        },
        getSelected(){
            let boardIds = [];
            for (let i in this.boardList) {
                if(this.boardList[i].selected) {
                    boardIds.push(this.boardList[i].boardId);
                }
            }
        },
    },
    mounted() {
        this.getList();
    }
})
```
---