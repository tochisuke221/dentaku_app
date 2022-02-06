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
        if(this.result.match(/\d/g).length > 9){ return parseFloat(this.result).toExponential(0) }
        if(this.result.includes('e')){ return parseFloat(this.result).toExponential(0)}
        if(parseFloat(this.result) < 1 && parseFloat(this.result) > -1){ return this.result }
        // -があるかないかで数値の方をどうするか決める
        if(this.result[0] === '-'){ return parseFloat(this.result.slice(0, 10)).toLocaleString()}
        return parseFloat(this.result.slice(0, 9)).toLocaleString()
      }else{
        return "Error..(;_;)"
      }
    },
    displayNumber(){
      if(this.inputNum){
        if(parseFloat(this.inputNum) < 1 && parseFloat(this.inputNum) > -1){ return this.inputNum }
        if(this.inputNum[0] === '-'){ return parseFloat(this.inputNum.slice(0, 10)).toLocaleString()}
        return parseFloat(this.inputNum.slice(0, 9)).toLocaleString()
      }

      if(this.priorityCalcResult){
        if(this.priorityCalcResult.match(/\d/g).length > 10){ return parseFloat(this.priorityCalcResult).toExponential(0) }
        if(this.priorityCalcResult.includes('e')){ return parseFloat(this.priorityCalcResult).toExponential(0)}
        if(parseFloat(this.priorityCalcResult) < 1 && parseFloat(this.priorityCalcResult) > -1){ return this.priorityCalcResult }
        if(this.priorityCalcResult[0] === '-'){ return parseFloat(this.priorityCalcResult.slice(0, 10)).toLocaleString()}
        return parseFloat(this.priorityCalcResult.slice(0, 9)).toLocaleString()
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
    width: 220px;
    padding: 0 10px;
    height: 60px;
    background: black;
    opacity: 0.9;
    color: white;
    text-align: right
  }
</style>
