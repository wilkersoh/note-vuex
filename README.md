* [What inside Vuex store](#what-inside-vuex-store)
* [modules](#modules)
* [namespaced: true](#namespaced--true)
* [Retrieve store value from DOM](#retrieve-store-value-from-dom)
* [Create vuex](#create-vuex)


## What inside Vuex store

1. State
2. Mutations (cannot doing async in here) [`commit` mutations] 
- 它是負責update state的, 我們只能update state 從 mutations

 3.  Actions [`dispatch` actions] 

- 它是負責 commit mutations 然後 pass 拿到的value給它

 4.  Getters

- 可以 根據 state 改變我們要的data 給DOM (eg: fillter)

```jsx
import { createStrore } from 'vuex';

export default createStrore({
  state: {
		counter: 0,
		colorCode: '',
  },
	mutations: {
		decreaseCounter(state, valueFromAction) {
      state.count -= valueFromAction
    },
		setColorCode(state, valueFromComputed) {
			state.colorCode = valueFromComputed
		}
	},
	actions: {
    // call api
		async decreaseCounter({ commit }) {
			const res = await API;
			commit("decreaseCounter", res.data);
		},
		setColorCode({ commit }, valueFromComputed) {
			commit('setColorCode', valueFromComputed)
    }
  },
	getters: { // 我們可以 change things 然後 顯示在 dom
		counterSquared(state) {
	    return state.counter * state.counter
    }, 
	}
})
```

```jsx
import { createStrore } from 'vuex';

export default createStrore({
  state: {
		counter: 0,
		colorCode: '',
  },
	mutations: {
		decreaseCounter(state, valueFromAction) {
      state.count -= valueFromAction
    },
		setColorCode(state, valueFromComputed) {
			state.colorCode = valueFromComputed
		}
	},
	actions: {
    // call api
		async decreaseCounter({ commit }) {
			const res = await API;
			commit("decreaseCounter", res.data);
		},
		setColorCode({ commit }, valueFromComputed) {
			commit('setColorCode', valueFromComputed)
    }
  },
	getters: {  
		counterSquared(state) {
	    return state.counter * state.counter
    }, 
		
	}
})
```

```jsx
<template>
	<div>
		<p>can direct retrieve $store value</p>
		{{ $store.state.counter }}
		<p>This is getter value</p>
		{{ $store.getters.counterSquared }}

		<div @click="$store.dispatch('decreaseCounter')">decrease number</div>
		我們能用 mapActions， 那樣就不需要寫 this.$store or $store料
		<div @click="decreaseCounter">decrease number</div>
		<div>
			<input v-model="colorCode" type="text" />
		</div>
	</div>
</template>

import { mapActions， mapGetters, mapState } from "vuex";
export default {
	name: 'Home',
	methods: {
		...mapActions(['decreaseCounter']);
		decreaseCounter() {
			this.decreaseCounter();
    }
	},
	computed: {
		...mapGetters(['counterSquared'])
		colorCode: { 
			get() {
				return this.$store.state.colorCode
			},
			set(newValue) { // 我們需要從 這裡更改 而不是 直接去改 store的 value
				this.$store.dispatch('setColorCode', newValue)
			}
		}
  }
}
```

Note: 如果沒有很複雜還是 async await 的 狀態 可以使用`this.$store.commit("setColorCode", newValue)`  但是 最好 還是 必須通過 `actions` 去 處理 mutations

`mapActions`, `mapGetters` 他們接受 Array or Object. 

```jsx
methods: {
	...mapActions({ decreaseCounter: "decreaseCounter" }) 
	// objectKey 是 我的 vuejs methods裡的名字， objectValue 那個是 我 store裡的名字 然後
	// spread它 進去 methods裡 那樣 我就不需要寫 decreateCounter() { this.decreateCount() 了 }
}
```

## modules

1. Way 1

```jsx
// store/modules/counter.js
export const state = {
    count: 0,
  };

  export const mutations = {
    increment(state, value) {
      state.count += value;
    },
    decrement(state) {
      state.count--;
    },
  };

  export const actions = {
    increaseCounter({ commit }, value) {
      commit("increment", value);
    },
    decreaseCounter({ commit }) {
      commit("decrement");
    },
  };
// store/index.js
import { createStore } from "vuex";
import * as counter from "./modules/counter";

export default createStore({
	modules: {
    counter,
  },	
})
```

2. Way 2

```jsx
// store/modules/counter.js
export default {
  state: {
    count: 0,
  },

  mutations: {
    increment(state, value) {
      state.count += value;
    },
    decrement(state) {
      state.count--;
    },
  },
  actions: {
    increaseCounter({ commit }, value) {
      commit("increment", value);
    },
    decreaseCounter({ commit }) {
      commit("decrement");
    },
  },
};
// store/index.js
import { createStore } from "vuex";
import counter from "./modules/counter";

export default createStore({
  modules: {
    counter,
  },
  // state,
  // mutations,
  // actions,
});
```

## namespaced: true

```jsx
// store/modules/counter.js
export default {
	namespaced: true,
	state: {...},
}

// component 
export default {
	const increment = () => {
    const number = Math.floor(Math.random() * 10);
    store.dispatch('counter/increaseCounter', number); // + counter/
  }
}

```

## Retrieve store value from DOM

```jsx
/*
	counter.count, counter 是namespaced: true　
	能直接 拿到 $store instance 在 dom
*/
<template>
	<h1>{{ $store.state.counter.count }}</h1> 
</template>
```

## Create vuex

```bash
yarn add vuex
```
