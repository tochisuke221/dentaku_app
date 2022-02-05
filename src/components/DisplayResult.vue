<template>
  <div class="display-result">
    <h3>{{displayNumber}}</h3>
  </div>
</template>

<script>
export default{
  props:["result", "inputNum", "priorityCalcResult"],
  computed: {
    displayResult(){
      if(isFinite(this.result)){
        return Number(this.result).toLocaleString()
      }else{
        return "Error..(;_;)"
      }
    },
    displayNumber(){
      if(this.inputNum){
        return Number(this.inputNum).toLocaleString()
      }

      if(this.priorityCalcResult){
        return Number(this.priorityCalcResult).toLocaleString()
      }

      return this.displayResult
    }
  }
}
</script>

<style scoped>
  .display-result{
    font-size: 23px;
    line-height: 60px;
    width: 190px;
    padding: 0 10px;
    height: 60px;
    background: black;
    opacity: 0.9;
    color: white;
    text-align: right
  }
</style>
