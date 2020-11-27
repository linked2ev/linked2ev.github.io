
homebrew install 모음

<br><br> 




```html
<input type="checkbox" class="inp_comm" 
id="checkAll"
v-model="checked"
@change="checkedAll($event)" >


                   
                   
 <tr v-for="(item, index) in seasonList" :key="item.seasonId">
      <input type="checkbox" class="inp_comm"
                                           :id="'check_' + item.seasonId"
                                           :value="item.seasonId"
                                           v-model="item.selected"
                                           @change="selected($event)"
                                    >
</tr>
```

```js
   data: {
        checked: false,

    },
```

```js
 methods: {
        checkedAll(e) {
            for (let i in this.seasonList) {
                this.seasonList[i].selected = this.checked;
            }
        },
        selected (e) {
            for (let i in this.seasonList) {
                if(! this.seasonList[i].selected) {
                    this.checked = false;
                    return;
                } else {
                    this.checked = true;
                }
            }
        },
        getSelected(){
            var seasonIds = [];// 삭제 할 시즌 아이디
            for (let i in this.seasonList) {
                if(this.seasonList[i].selected) {
                    seasonIds.push(this.seasonList[i].seasonId);
                }
            }
            console.log('delete seasonIds', seasonIds);
        },
 }
```
---

