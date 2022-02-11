<template>
  <div id="container">
      <div class="title">
        <a href="https://docs.google.com/presentation/d/1DCIWgj-jHTwUs4oTEZxnt5dEYvJvpRh_XWSOhqBVw90/edit?usp=sharing" target="_blank" rel="noopener noreferrer">
          電卓
        </a>
      </div>
      <DisplayResult :result="result" :inputNum="inputNum" :funcMode="funcMode" :priorityCalcResult="priorityCalcResult"></DisplayResult>
    <div class="wrapper">
      <!-- 1行目 -->
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'C'" @func-click="clearInputNum($event)"></FunctionButton></div>
      <div class="btn" id="ac"><FunctionButton  :inputNum="inputNum" :func="'AC'" @func-click="clearAll($event)"></FunctionButton></div>
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'+/-'" @func-click="reverseCode($event)"></FunctionButton></div>
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'÷'" @func-click="updateResult($event)"></FunctionButton></div>
      <!-- 2行目 -->
      <div class="btn"><NumberButton :number="number[7]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><NumberButton :number="number[8]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><NumberButton :number="number[9]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'×'" @func-click="updateResult($event)"></FunctionButton></div>
      <!-- 3行目 -->
      <div class="btn"><NumberButton :number="number[4]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><NumberButton :number="number[5]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><NumberButton :number="number[6]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'-'" @func-click="updateResult($event)"></FunctionButton></div>
      <!-- 4行目 -->
      <div class="btn"><NumberButton :number="number[1]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><NumberButton :number="number[2]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><NumberButton :number="number[3]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'+'" @func-click="updateResult($event)"></FunctionButton></div>
      <!-- 5行目 -->
      <div class="btn" id="zero"><NumberButton :number="number[0]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><NumberButton :number="number[10]" @num-click="updateVariableNums($event)"></NumberButton></div>
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'='" @func-click="updateResult($event)"></FunctionButton></div>
    </div>
  </div>
</template>

<script>
import NumberButton from "./components/NumberButton.vue"
import DisplayResult from "./components/DisplayResult.vue"
import FunctionButton from "./components/FunctionButton.vue"

// 変数管理
function initialState(){
  return {
    number: [
      "0","1","2","3","4","5","6","7","8","9","."
    ],
    result: "0",
    inputNum:"",
    priorityCalcResult:"", // 乗除の計算結果保管用   
    priorityFuncMode: "", // 乗除用の計算符号
    funcMode: "+",
  }
}

export default {
  name: 'App',
  data(){
    return initialState()
  },
  methods: {
    updateVariableNums(e){
      // 最終計算符号が = または ACの時は全変数を初期化
      if(this.funcMode === "=" || this.funcMode === 'AC' ){ this.resetAllVariables(); }

      this.updateInputNumber(e);
    },
    resetAllVariables(){
      Object.assign(this.$data, initialState());
    },
    updateInputNumber(e){
      if(!isCorrectInputNum(this.inputNum, e)) return
      // 特殊パターン：最初にいきなり.押した場合
      if(this.inputNum === "" && e === "."){ e = "0." }

      this.inputNum += e;
    },
    // Clear機能
    clearInputNum(){
      this.inputNum = "";
    },
    // AllClear機能
    clearAll(){
      this.resetAllVariables();
    },
    // +/-ボタン機能
    reverseCode(){
      if(!this.inputNum) return this.inputNum = "-0"
      
      this.inputNum = this.inputNum[0] ==='-' ? this.inputNum.slice(1) : '-' + this.inputNum
    },
    // 符号連打対応(入力値がない状態での符号変換)
    switchFuncMode(e){
      if(this.inputNum) return
      if(this.priorityCalcResult) return 

      // 加減モードに移行
      if(!isPriorityCalc(e)){ return this.funcMode = e}

      //乗除モードに移行
      this.priorityCalcResult = this.result
      this.priorityFuncMode = e
      // 初期化
      this.result = "0"
      this.funcMode = "+"

    // if(!this.inputNum){
    //     if((isPriorityCalc(e)) && !this.priorityCalcResult){
    //       if(this.result){
    //         this.priorityCalcResult = this.result
    //         this.result = "0"
    //       }else{
    //         this.priorityCalcResult = "0"
    //       }
    //       this.priorityFuncMode = e
    //       return
    //     }
    //     if(e === '×' || e === '÷') {
    //       return this.priorityFuncMode = e
    //     }
    //     return this.funcMode = e
    //   }
    },


    updateResult(e){
      // 最終計算符号が = で計算が終了状態のときは、全変数を初期化
      if(this.funcMode === "="){ this.resetAllVariables(); }

      // 符号連打時の符号切り替え
      this.switchFuncMode(e);

      //次の計算が×・÷は先にそっちを計算する。
      isPriorityCalc(e) ? this.executePriorityCalc(e) : this.executeCalc(e)
    },
    executePriorityCalc(e){
      if(!this.inputNum) return 
      if(!this.priorityCalcResult){
        // 乗除計算の最初は、入力値 = 現在の結果とする
        this.priorityCalcResult = this.inputNum
        this.priorityFuncMode = e

        //初期化
        this.inputNum = ""
      }else{
        // 符号が押される前まで計算を決定
        this.priorityCalcResult= calcNum(this.priorityCalcResult, this.inputNum, this.priorityFuncMode);
        this.priorityFuncMode = e
        
        // 初期化
        this.inputNum = ""
      }
    },
    executeCalc(e){
      if(!this.inputNum) return 
      if(!this.priorityCalcResult){
        // 乗除計算の結果が存在していなければ、現在の結果に単純に加減計算
        this.result=calcNum(this.result, this.inputNum, this.funcMode)
        this.funcMode = e
        // 初期化
        this.inputNum = ""
      }else{
        // 乗除計算の結果が存在すれば、最新の結果 = 乗除計算結果 + 現在の加減結果
        this.priorityCalcResult = calcNum(this.priorityCalcResult, this.inputNum, this.priorityFuncMode); 
        this.result = calcNum(this.result, this.priorityCalcResult, this.funcMode); 
        this.funcMode = e
        // 初期化と符号更新
        this.priorityCalcResult = ""
        this.priorityFuncMode = ""
        this.inputNum = ""
      }
    },
  },
  components: {
    NumberButton,
    FunctionButton,
    DisplayResult,
  }
}

// 計算を行う
function calcNum(sum, num,func){
  let val;
  switch(func){
    case "+":
      val = parseFloat(sum) + parseFloat(num)
      break;
    case "-":
      val = parseFloat(sum) - parseFloat(num)
      break;
    case "×":
      val = parseFloat(sum) * parseFloat(num)
      break;
    case "÷":
      val = parseFloat(sum) / parseFloat(num)
      break;
    case "=":
      break;
  }
  //誤差を切る
  if(String(val).match(/e/)){ return String(val)}
  return String(Number(val.toFixed(9)));
}

// 入力値が正しい形かチェック
function isCorrectInputNum(num, input){
  if(num.replace(/[^0-9]/g, '').length === 9) return false
  if((num === "0" || num === "-0") && input === "0") return false
  if(num.includes(".") && input === ".") return false

  return true
}

function isPriorityCalc(code){
  return code === "×" || code === "÷"
}
</script>

<style>
#container > div{
  margin: 0 auto;
}
.title{
  text-align: center;
  font-weight: bold;
}
button {
  background: green;
}
.wrapper {
  background: black;
  opacity: 0.9;
  width:230px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  row-gap: 10px;
  align-items: center;
  padding: 5px;
}

#zero{
  grid-column: span 2;
}

#explain{
  color: white;
  background: orange;
  border-radius: 50%;
  border:none;
  width: 40px;
  height: 40px;
  margin-left:5px;
  cursor:pointer;
}
</style>
