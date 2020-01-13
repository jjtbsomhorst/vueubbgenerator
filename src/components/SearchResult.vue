<template>
 <div class="item" v-on:click="onSelectEntry">
    <img class="avatar image" v-bind:src="entry.Poster">
    <div class="content">
      <a class="header">{{entry.Title}}</a>
      <div class="meta">
        <b>Tweakers:</b>{{formatRating(entry.tweakscore)}}
        <b>imdb:</b>{{entry.imdbRating}}
        <b>Genre:</b>{{entry.Genre}}
        <b>Released:</b>{{entry.Year}}
      </div>
      <div class="description">{{entry.Plot}}</div>
    </div>
  </div>
</template>
<script type="text/javascript">
/* eslint-disable */

export default{
    methods: {
      onSelectEntry(){
        axios.get('api.php/movie/'+this.entry.imdbID).then((response)=>{
          this.$emit('select-entry',response.data);
        });
      },
      formatRating(rating){
        if(!(typeof rating === 'undefined')){
          let flt = parseFloat(rating);
          return flt.toFixed(2);
        }
        return "";
      }
    },
    props: ['entry'],

}

</script>