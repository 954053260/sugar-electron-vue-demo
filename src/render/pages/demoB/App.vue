<template>
  <div>
    <div>
      <div>store state name: <span style="color: red">{{storeName}}</span></div>
    </div>
  </div>
</template>
<script>
import { ipc, store } from 'sugar-electron/render';
const GET_NAME = '/name';
export default {
  data() {
    return {
      storeName: ""
    }
  },
  created() {
    ipc.response(GET_NAME, (json, cb) => {
      cb(`demoB name随机数${Math.random() * 10}`);
    });

    this.unsubscribe = store.subscribe((data) => {
      this.storeName = data.name;
    });

    this.timer = setInterval(() => {
      ipc.publisher('demoB', `demoB广播随机数${Math.random() * 10}`)
    }, 2000)
  },
  destroyed() {
    ipc.unresponse(GET_NAME);
    this.unsubscribe();
    clearInterval(timer);
  },
  methods: {
   
  }
};
</script>