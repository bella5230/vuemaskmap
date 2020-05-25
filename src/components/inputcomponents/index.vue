<script>
export default {
    name:'input-component',
    props:{
      text:{
        type: String,
        reqireds:true

      }  
    },
    computed:{
      inputText:{
        get(){
          return this.text
        },
        set(val){
          // console.log(val)
          this.$emit('update:text',val)
        }

      }
    }
}
</script>
<template src="./template.html"></template>
<style src="./style.css" scoped></style>