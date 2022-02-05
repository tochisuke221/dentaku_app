<template>
  <div>
    <button :id="'button' + func" :class="disabledClearBtn()" @click="updateFuncMode()">{{func}}</button>
  </div>
</template>

<script>
export default{
  props: ["func", "inputNum"],
  methods:{
    updateFuncMode(){
      this.$emit("func-click", this.func)
    },
    //動的にクラスを付与（Cボタンの非活性）
    disabledClearBtn(){
      if(!this.inputNum && this.func === 'C'){ return "disabled" }
    }
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
