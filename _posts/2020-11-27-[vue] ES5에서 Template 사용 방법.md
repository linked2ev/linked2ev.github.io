---
layout: post
title:  "[Vue] ES5에서 vue Template 사용 방법"
subtitle:   "[Vue]"
categories: vue
tags: vue-devhistory
comments: true
---

webpack 등 빌드 툴과, vue-cli 를 이용한 SPA 화면 개발이 아닌 Vue CDN을 추가하여 vue.js를 ES5 방식으로 개발 시에 컴포넌트 사용 예제이다.

<br><br> 



---

github 코드 : https://github.com/linked2ev/springboot-thymeleaf-vue  
참고 : https://blog.martinwork.co.kr/vuejs/2017/10/26/vue-component-template.html  
참고 : https://vuejsdevelopers.com/2017/03/24/vue-js-component-templates/?jsdojo_id=medium_cot

---

<br><br>

# Vue.js Template 을 컴포넌트화하는 방법

Vuejs 내에서도 각 template을 컴포넌트 방법 중에 전역/지역 컴포넌트 방식으로 아래와 같은 방식이다.

- Template literal
- X-Templates
- Inline

<br><br>


# Vue CDN 추가

```html
<script th:src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

```html
<div id="app">
    <div>
        <h2>전역 컴포넌트</h2>

        <h4>Template literal 컴포넌트</h4>
        <ex1-string-component></ex1-string-component>

        <h4>X-Templates 컴포넌트</h4>
        <ex2-template-component></ex2-template-component>

        <h4>inline 컴포넌트</h4>
        <ex3-inline-example inline-template>
            <div class="module_tab" id="tab" data-module-tab="wrap">
                <ul>
                    <li v-for="(item, index) in items">
                        <button @click="btnClick( index )" type="button"><span>{{ item.button }}</span></button>
                    </li>
                </ul>
                <div class="module_tab_contents">
                    {{ message }}
                </div>
            </div>
        </ex3-inline-example>
    </div>

    <hr>

    <div>
        <h2>전역 컴포넌트</h2>

        <h4>Template literal 컴포넌트</h4>
        <ex4-component></ex4-component>

        <h4>X-Templates 컴포넌트</h4>
        <ex5-component></ex5-component>
    </div>
</div><!-- //vue app id -->


<!-- ex2) ex2 컴포넌트 -->
<script type="text/x-template" id="ex2-template">
    <div class="module_tab" data-module-tab="wrap">
        <ul>
            <li v-for="(item, index) in items">
                <button @click="btnClick( index )" type="button"><span>{{ item.button }}</span></button>
            </li>
        </ul>
        <div class="module_tab_contents">
            {{ message }}
        </div>
    </div>
</script>
```



```js
/**
 * ES5 - Componet 사용예제
 */

/* 전역 컴포넌트 */
//Template literal 형태
Vue.component('ex1-string-component', {
    template: `
        <div class="module_tab" data-module-tab="wrap">
            <ul>
                <li v-for="(item, index) in items">
                    <button @click="btnClick( index )" type="button"><span>{{ item.button }}</span></button>
                </li>
            </ul>
            <div class="module_tab_contents">
                {{ message }}
            </div>
        </div>
    `,
    data () {
        return {
            message: '',
            items: [{
                'button': '버튼1',
                'contents': '버튼1 클릭'
            },{
                'button': '버튼2',
                'contents': '버튼2 클릭'
            },{
                'button': '버튼3',
                'contents': '버튼3 클릭'
            }]
        }
    },
    methods: {
        btnClick (targetIndex) {
            this.message = this.items[targetIndex].contents;
        }
    }
})

//X-templates 형태 - Vue Root App Id <div> 밖에 선언
Vue.component('ex2-template-component', {
    template: '#ex2-template',//<x-template> Id
    data () {
        return {
            message: '',
            items: [{
                'button': '버튼1',
                'contents': '버튼1 클릭'
            },{
                'button': '버튼2',
                'contents': '버튼2 클릭'
            },{
                'button': '버튼3',
                'contents': '버튼3 클릭'
            }]
        }
    },
    methods: {
        btnClick (targetIndex) {
            this.message = this.items[targetIndex].contents;
        }
    }
});

//Inline templates 형태 - Vue Root App Id <Div> 안에 선언
Vue.component('ex3-inline-example', {
    data () {
        return {
            message: '',
            items: [{
                'button': '버튼1',
                'contents': '버튼1 클릭'
            },{
                'button': '버튼2',
                'contents': '버튼2 클릭'
            },{
                'button': '버튼3',
                'contents': '버튼3 클릭'
            }]
        }
    },
    methods: {
        btnClick (targetIndex) {
            this.message = this.items[targetIndex].contents;
        }
    }
});

/* 지역 컴포넌트 */
//Template literal 형태
let ex4StringComponent = {
    template: `
        <div class="module_tab" data-module-tab="wrap">
            <ul>
                <li v-for="(item, index) in items">
                    <button @click="btnClick( index )" type="button"><span>{{ item.button }}</span></button>
                </li>
            </ul>
            <div class="module_tab_contents">
                {{ message }}
            </div>
        </div>
    `,
    data () {
        return {
            message: '',
            items: [{
                'button': '버튼1',
                'contents': '버튼1 클릭'
            },{
                'button': '버튼2',
                'contents': '버튼2 클릭'
            },{
                'button': '버튼3',
                'contents': '버튼3 클릭'
            }]
        }
    },
    methods: {
        btnClick (targetIndex) {
            this.message = this.items[targetIndex].contents;
        }
    }
}

//X-templates 형태 - Vue Root App Id <Div>밖에 선언
let ex5TemplateComponent = {
    template: '#ex2-template',//<x-template> Id
    data () {
        return {
            message: '',
            items: [{
                'button': '버튼1',
                'contents': '버튼1 클릭'
            },{
                'button': '버튼2',
                'contents': '버튼2 클릭'
            },{
                'button': '버튼3',
                'contents': '버튼3 클릭'
            }]
        }
    },
    methods: {
        btnClick (targetIndex) {
            this.message = this.items[targetIndex].contents;
        }
    }
}


/**
 * 컴포넌트는 vue app id 보다 위에 선언되어야 한다.
 * app
 */
let app = new Vue({
    el: '#app',
    components: {
        'ex4-component': ex4StringComponent,
        'ex5-component': ex5TemplateComponent
    }
})
```