<template>
  <div id="container">
      <div class="title">
        <a href="https://docs.google.com/presentation/d/1DCIWgj-jHTwUs4oTEZxnt5dEYvJvpRh_XWSOhqBVw90/edit?usp=sharing" target="_blank" rel="noopener noreferrer">
          スーパーコンピュータ スマとち計算機!
        </a>
      </div>
      <DisplayResult :result="result" :inputNum="inputNum" :funcMode="funcMode" :priorityCalcResult="priorityCalcResult"></DisplayResult>
    <div class="wrapper">
      <!-- 1行目 -->
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'C'" @func-click="updateResult($event)"></FunctionButton></div>
      <div class="btn" id="ac"><FunctionButton  :inputNum="inputNum" :func="'AC'" @func-click="updateResult($event)"></FunctionButton></div>
      <div class="btn"><FunctionButton :inputNum="inputNum" :func="'+/-'" @func-click="updateResult($event)"></FunctionButton></div>
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
    lastInputNum: "",
    priorityCalcResult:"", // 乗除の計算結果保管用   
    priorityFuncMode: "", // 乗除用の計算符号
    funcMode: "+",
    lastFuncMode: "",
  }
}

export default {
  name: 'App',
  data(){
    return initialState()
  },
  methods: {
    updateVariableNums(event){
      // 最終計算符号が = で計算が終了状態のときは、全変数を初期化
      if(this.funcMode === "="){ this.resetAllVariables(); }
      if(this.funcMode === 'AC'){ return this.resetAllVariables(); }
      

      this.updateInputNumber(event);
    },
    resetAllVariables(){
      Object.assign(this.$data, initialState());
    },
    updateInputNumber(event){
      //桁数制限
      if(this.inputNum.replace(/[^0-9]/g, '').length === 9) return
      if(this.inputNum === "0" && event === "0") return 
      if(this.inputNum.includes(".") && event === ".") return 

      if(this.inputNum === "" && event === "."){
        event = "0."
      }

      if(this.inputNum === "0" && event !== "."){
        this.inputNum = "";
      }

      this.inputNum += event;
    },
    updateResult(event){
      // 最終計算符号が = で計算が終了状態のときは、全変数を初期化
      if(this.funcMode === "="){ this.resetAllVariables(); }

      // +/-ボタン機能
      if(event === '+/-'){
        if(this.inputNum){
          return this.inputNum = this.inputNum[0] ==='-' ? this.inputNum.slice(1) : '-' + this.inputNum
        }
        if(this.priorityCalcResult){
          return this.priorityCalcResult = this.priorityCalcResult[0] ==='-' ? this.priorityCalcResult.slice(1) : '-' + this.priorityCalcResult
        }
        return this.result = this.result[0] ==='-'? this.result.slice(1) : '-' + this.result
      }

      // AllClear機能
      if(event === 'AC'){
        return this.resetAllVariables();
      }
      
      // Clear機能
      if(event === 'C'){
        return this.inputNum = "";
      }

      // = を連打した場合は最後の入力値と符号で計算を続ける。
      if(!this.inputNum && event === "="){
        this.result = this.calcNum(this.result, this.lastInputNum, this.lastFuncMode);
        return 
      }

      // 符号連打対応
      if(!this.inputNum){
        if((event === "×" || event === "÷") && !this.priorityCalcResult){
          this.priorityCalcResult = "0"
          this.priorityFuncMode = event
          this.lastInputNum = "0"
          this.lastFuncMode = event
          return
        }
        if(event === '×' || event === '÷') {
          return this.priorityFuncMode = event
        }
        return this.funcMode = event
      }

      //次の計算が×・÷は先にそっちを計算する。
      if(event === "×" || event === "÷"){
        if(!this.priorityCalcResult){
          // 符号が押される前まで計算を決定
          this.priorityCalcResult = this.inputNum
          // 最後の入力値と符号を保管
          this.lastInputNum = this.inputNum
          this.lastFuncMode = this.priorityFuncMode
          // 初期化と符号更新
          this.priorityFuncMode = event
          this.inputNum = ""
        }else{
          // 符号が押される前まで計算を決定
          this.priorityCalcResult= this.calcNum(this.priorityCalcResult, this.inputNum, this.priorityFuncMode);
          // 最後の入力値と符号を保管
          this.lastInputNum = this.inputNum 
          this.lastFuncMode = this.priorityFuncMode
          // 初期化と符号更新
          this.priorityFuncMode = event
          this.inputNum = ""
        }
      }else{
        if(!this.priorityCalcResult){
          // 符号が押される前まで計算を決定
          this.result=this.calcNum(this.result, this.inputNum, this.funcMode)
          // 最後の入力値と符号を保管
          this.lastInputNum = this.inputNum
          this.lastFuncMode = this.funcMode
          // 初期化と符号更新
          this.inputNum = ""
          this.funcMode = event
        }else{
          // 符号が押される前まで計算を決定
          this.priorityCalcResult = this.calcNum(this.priorityCalcResult, this.inputNum, this.priorityFuncMode); 
          this.result = this.calcNum(this.result, this.priorityCalcResult, this.funcMode); 
          // 最後の入力値と符号を保管
          this.lastInputNum = this.inputNum
          this.lastFuncMode = this.priorityFuncMode
          // 初期化と符号更新
          this.priorityCalcResult = ""
          this.priorityFuncMode = ""
          this.inputNum = ""
          this.funcMode = event
        }
      }
    },
    calcNum(sum, num,func){
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
      return String(val);
    }
  },
  components: {
    NumberButton,
    FunctionButton,
    DisplayResult,
  }
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
