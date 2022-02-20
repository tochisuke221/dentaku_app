<template>
  <div class="display-result">
    <h3 style="margin: 0">
      {{ displayNumber }}
    </h3>
  </div>
</template>

<script>
export default{
  props:["result", "inputNum", "priorityCalcResult"],
  computed: {
    displayNumber(){
      return fixedInputNum(this.inputNum) || fixedPriorityCalcResult(this.priorityCalcResult) || fixedResult(this.result)
    },
  }
}

//結果を表示
function fixedResult(num){
  if(!isFinite(num)){ return  "Error..." }
  //小数点対応
  if(Math.abs(num) < 1){ return fixSmallNum(num)  }    
  // 大きい数字対応
  if(Math.abs(num) > 999999999){ return parseFloat(num).toExponential(0) }
 
//  const numLength = num[0] === '-' ? 10 : 9
//  return parseFloat(num).toLocaleString( undefined, { maximumFractionDigits: [8] })
  return fixNormalNum(num)
}

// 入力値を表示
function fixedInputNum(num){
  if(!num) return 
  // 小数点対応
  if(Math.abs(num) < 1 ){ return num }
  if(num.includes('.')){ return joinIntegerToSmallNum(num) }

  return parseFloat(num).toLocaleString()
}

function joinIntegerToSmallNum(num){
  const numParts = num.split('.')

  return parseFloat(numParts[0]).toLocaleString() + '.' + numParts[1]
}

// 乗除の途中結果を表示
function fixedPriorityCalcResult(num){
  if(!isFinite(num)){ return  "0" }
  if(num){
    //小数点対応
    if(Math.abs(num) < 1){ return fixSmallNum(num)  }
    //大きい数字対応
    if(Math.abs(num) > 999999999){ return parseFloat(num).toExponential(0) }
    
    // return parseFloat(num).toLocaleString( undefined, { maximumFractionDigits: [8] })
    return fixNormalNum(num)
  }
}

// 9桁に収まるように加工する ex. 992,324,911.023456 → 992,324,911
function fixNormalNum(num){
  const numPart = num.split('.')
  const smallLen =  9 - numPart[0].length

  // 整数または整数部分しか表示出来ない場合
  if(smallLen === 0 || !numPart[1]) return parseFloat(numPart[0]).toLocaleString()

  return parseFloat(numPart[0]).toLocaleString() + '.' + numPart[1].slice(0, smallLen)
}

// 0以外で絶対値が0.00000001より小さいものを指数表示に変化する
function fixSmallNum(num){
  if(Math.abs(num) === 0){ return 0}
  if(Math.abs(num) < 0.00000001){ return  parseFloat(num).toExponential(0) }

  return trimmingZero(num)
}

// ex. 0.0012300 → 0.00123
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
    width: 200px;
    padding: 0 10px;
    height: 60px;
    background: black;
    opacity: 0.9;
    color: white;
    text-align: right
  }
</style>
