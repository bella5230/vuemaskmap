<template>
    <h1 class="title">{{ title }}</h1>
</template>>

<script>
export default {
    name:'Titlecomponent',
    props:{
      title:{
        type: String,
        reqireds:true
        // default:"w1sss233?w"
      }  
    }
    // data(){
    //     return{
    //         title : "hihihihhihihi title"
    //     }
    // }
}
</script>
<style scoped>
h1.title {
color :blue
}
</style>