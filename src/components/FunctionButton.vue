<template>
  <div>
    <button :id="'button' + func" :class="disabledBtn" @click="updateFuncMode()">{{func}}</button>
  </div>
</template>

<script>
export default{
  props: ["func", "inputNum"],
  computed:{
    //動的にクラスを付与（Cボタンの非活性）
    disabledBtn(){
      if(!this.inputNum && this.func === 'C'){ return "disabled" }
    }
  },
  methods:{
    updateFuncMode(){
      this.$emit("func-click", this.func)
    },
  }
}
</script>

<style scoped>
  button{
    color: white;
    background: orange;
    border-radius: 50%;
    border:none;
    width: 40px;
    height: 40px;
    margin-left:5px
  }
  button:active{
    opacity: 0.7;
  }
  button:hover{
    cursor: pointer;
  }
  button.disabled{
    background: gray;
  }
</style>
