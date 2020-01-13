<template>
    <div class="ui buttons">
        <button class="ui button" v-on:click="align('left')"><i class="align left icon"></i></button>
        <button class="ui button" v-on:click="align('center')"><i class="align center icon"></i></button>
        <button class="ui button" v-on:click="align('right')"><i class="align right icon"></i></button>
        <button class="ui button" v-on:click="decorate('spoiler')"><i class="low vision icon"></i></button>
        <button class="ui button" v-on:click="decorate('b')"><i class="bold icon"></i></button>
        <button class="ui button" v-on:click="decorate('i')"><i class="italic icon"></i></button>
        <button class="ui button" v-on:click="decorate('underline')"><i class="underline icon"></i></button>
    </div>
</template>
<script type="text/javascript">
 export default{
     methods:{
         align(order){
            this.$emit('align',order);
         },
         decorate(decoration){
             // receiving end should decide what to do with it
            this.$emit('decorate',decoration);
         }
     }
 }
</script>