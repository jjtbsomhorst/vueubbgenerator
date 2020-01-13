<template>
    <div>
        <div class="ui active dimmer" v-show="viewmode == 'loading'">
                <div class="ui loader"></div>
    </div>
    <div class="ui secondary menu row">
    <div class="right menu">
        <div class="ui icon" v-on:click="sortNumeric()"><i v-bind:class="sortOrderNumeric"></i></div>
        <div class="ui icon" v-on:click="sortAlfebetical()"><i v-bind:class="sortOrderAlfabetic"></i></div></div>
    </div>

    <div class="ui link six cards">
        <div class="card" v-for="poster in posters" v-bind:key="poster.imdbid" v-on:click="onSelectEntry(poster.imdbid)">
            <div class="image">
            <img style="max-height: 270px;max-width: 173px;" v-bind:src="'http://www.jeroensomhorst.eu/ubbgenerator/images/'+poster.image+'.jpg'">
            </div>
            <div class="content">
            <div class="header"></div>
            <div class="meta">
                <b>Tweakers:</b> {{formatRating(poster.ubbscore)}}
                <b>imdb:</b>{{formatRating(poster.imdbscore)}}
            </div>
            <div class="description">
             
            </div>
            </div>
        </div>
  </div></div>
</template>
<script type="text/javascript">
 export default{
     name: 'Gallery',
     methods: {
         retrievePosters: function(){
             this.viewmode = 'loading';
             console.log('Retrieving posters from the api');
             if(this.posters.length > 0){
                 this.viewmode = 'gallery'
             }else{
                 axios.get('api.php/posters').then((response)=>{
                     this.posters =response.data;
                     this.retrievePosters();
                 });
             }
         },
         onSelectEntry: function(imdbid){
             window.open('https://www.imdb.com/title/'+imdbid+'/','_blank');
         },
         formatRating(rating){
            if(!(typeof rating === 'undefined')){
                let flt = parseFloat(rating);
                if(flt >= 0){
                    return flt.toFixed(2);
                }
            }
            return "";
        },
        sortAlfebetical: function(){},
        sortNumeric: function(){}
     },
     data: function(){
         return {
            viewmode:'loading',
            posters: [],
            sortOrderNumeric: "sort numeric down link icon",
            sortOrderAlfabetic: "sort alphabet down link icon"
         }
     },
     created: function(){
         console.log('created!');
         this.retrievePosters();
     }
     

 }
</script>