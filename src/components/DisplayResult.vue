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
        //小数点対応
        if(Math.abs(this.result) < 1){ return fixSmallNum(this.result)  }
        
        // -があるかないかで数値の方をどうするか決める
        if(this.result[0] === '-'){ return parseFloat(this.result.slice(0, 10)).toLocaleString()}
        return parseFloat(this.result.slice(0, 9)).toLocaleString()
      }else{
        return "Error..(;_;)"
      }
    },
    displayNumber(){
      if(this.inputNum){
        if(Math.abs(this.inputNum) < 1 ){ return this.inputNum }

        const numLength = this.inputNum[0] === '-' ? 10 : 9
        return parseFloat(this.inputNum.slice(0, numLength)).toLocaleString()
      }

      if(this.priorityCalcResult){
        //小数点対応
        if(Math.abs(this.priorityCalcResult) < 1){ return fixSmallNum(this.priorityCalcResult)  }

        if(this.priorityCalcResult[0] === '-'){ return parseFloat(this.priorityCalcResult.slice(0, 10)).toLocaleString()}
        return parseFloat(this.priorityCalcResult.slice(0, 9)).toLocaleString()
      }

      return this.displayResult
    },
  }
}

function fixSmallNum(num){
  if(Math.abs(num) === 0){ return 0}
  if(Math.abs(num) < 0.00000001){ return  parseFloat(num).toExponential(0) }

  return trimmingZero(num)
}

function trimmingZero(str){
  const num = String(parseFloat(str).toFixed(8))
  let end = num.length - 1;
  while (num.charAt(end)==='0') end--;

  return num.slice(0, end + 1);
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
