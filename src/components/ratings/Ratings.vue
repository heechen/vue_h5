<template>
  <div>
      i am ratings{{current}}
  </div>
</template>

<script>

export default {
    data(){
        return {
            
        }
    },
    computed:{
     current(){
       console.log("computed-current")   //undefind      
       return "computed-current"
        
     }
  },
     beforeCreate() {   
      console.log("beforeCreate")   //undefind      
    },
            
    created() {    
      console.log("created")   //undefind
     },
        beforeMount() {  
               console.log("beforeMount")   //undefind
            }, 

    mounted() {
     console.log("Mount")   //undefind
    },
     beforeUpdate() { 
               console.log('beforeUpdate')   //No
            },
   
     updated() {
        
        console.log('updated')   //No
     },
    methods: {
      
    },
    
}
</script>

<style lang="stylus">

      
</style>
