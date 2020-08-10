<template>
  <div>
    <div>
      <button @click="onCreateDemoB">创建demoB</button>
      <button @click="onSetSizeDemoB">设置demoB setSize(600, 600)</button>
    </div>
    <hr />
    <div>
      <button @click="onRequestDemoBName">请求demoB name</button>
      <div>
        demoB name:
        <span style="color: red">{{this.name}}</span>
      </div>
    </div>
    <div>
      <div>
        订阅demoB消息:
        <span style="color: red">{{this.data}}</span>
      </div>
    </div>
    <hr />
    <div>
      <button @click="onChangeStore">通过输入框，设置store state name</button>
      <input ref="ref" />
      <div>
        store state name:
        <span style="color: red">{{this.storeName}}</span>
      </div>
    </div>
  </div>
</template>
<script>
import { ipc, windowCenter, store } from "sugar-electron/render";
const { demoB } = windowCenter;
const GET_NAME = "/name";
export default {
  data() {
    return {
      name: "",
      storeName: "",
      describeData: ""
    }
  },
  created() {
    ipc.response(GET_NAME, (json, cb) => {
      cb('demoA');
    });

    this.unsubscribe1 = demoB.subscribe('demoB', (data) => {
      this.describeData = data;
    });

    this.unsubscribe2 = store.subscribe((data) => {
      this.storeName = data.name;
    });
  },
  destroyed() {
    ipc.unresponse(GET_NAME);
    this.unsubscribe1();
    this.unsubscribe2();
  },
  methods: {
    onCreateDemoB() {
      demoB.open();
    },
    onSetSizeDemoB() {
      demoB.setSize(600, 600);
    },
    onRequestDemoBName() {
      demoB.request(GET_NAME).then(data => {
        this.name = data;
      });
    },
    onChangeStore() {
      store.setState({
        name: this.$refs.ref.value
      });
    }
  }
};
</script>