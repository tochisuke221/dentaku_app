<template>
  <div>
    <button :id="'button' + number" @click="checkNumber">{{number}}</button>
  </div>
</template>

<script>
export default{
  props: ["number"],
  methods:{
    checkNumber() {
      this.$emit("num-click", this.number)
    },
  },
}
</script>

<style scoped>
  button{
    color: white;
    background: #C0C0C0;
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
  div{
    display: flex;
  }

  #button0{
    border-radius: 50%;
    width: 90px;
  }
</style>
