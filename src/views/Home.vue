<template>
<!--  <div class="home">-->
<!--    <img alt="Vue logo" src="../assets/logo.png">-->
<!--    <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>-->
<!--  </div>-->
  <FlowChart2></FlowChart2>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
// import HelloWorld from '@/components/HelloWorld.vue'; // @ is an alias to /src
import FlowChart2 from '@/components/FlowChart2.vue'; // @ is an alias to /src

@Component({
  components: {
    FlowChart2,
  },
})
export default class Home extends Vue {}
</script>
