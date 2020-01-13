<template>
    <div>
        <div class="ui borderless main menu">
            <div class="left menu">
                <div class="ui icon item" v-on:click='goHome'>
                    <i class="home link icon"/>
                </div>
            </div>
            <div class="left menu">
                <div class="ui category search item">
                    <div class="equal width grid"><div class="column"/>
                    
                    <div class="ui transparent icon input column">
                        <input class="prompt" type="text" v-model="searchTerm" placeholder="Search for movies or series">
                            <i class="search link icon"></i>
                    </div>
                    <div class="column"/>
                    </div>

                </div>
            </div>

            <div class="right menu">
                <div class="ui icon item" v-on:click='showGallery'>
                    <i class="images link icon"/>
                </div>
            </div>


        </div>
        <div class="ui container">
            <div class="ui active dimmer" v-show="viewmode == 'loading'">
                <div class="ui loader"></div>
            </div>

        <div class="ui two column stackable grid" v-show="viewmode == 'search'">      
            <div class="ui column relaxed">
                <h1>Laatst gereviewed</h1>
                <div class="ui container items">
                    <SearchResult v-for="result in searchResults"  v-bind:key="result.imdbID" v-bind:entry="result" v-on:select-entry="selectResult"/>
                </div>
            </div>
            <div class="ui column relaxed divided">
                <h1>Nu in de bioscoop</h1>
                <div class="ui container items">
                <SearchResult v-for="result in nowplaying"  v-bind:key="result.imdbID" v-bind:entry="result" v-on:select-entry="selectResult"/>
                </div>
            </div>
        </div>
        
        <div v-show="viewmode == 'write'" id="reviewPanel">
            <div class="ui items">
                <SearchResult v-bind:entry="selectedResult"/>
            </div>

            <div class="row">
                <ReviewEditorButtonBar v-on:align='onAlignEvent' v-on:decorate='onDecorateEvent'></ReviewEditorButtonBar>
            </div>
            <div class="ui form">
                <textarea id="ReviewEditor" v-model="reviewText"></textarea>
                <input type="number" min="1" max="10" step="0.5" v-model="reviewScore"/>
                <button class="ui button" id="generateReview" v-on:click='generateReview'>Generate</button>
            </div>
        </div>


        <div v-show="viewmode == 'generate'" id="ubbcode" class="ui form">
            <textarea id="ubbcodearea">[table bgcolor=transparent width=100% cellpadding=6][tr][td fontsize=14][url="http://www.imdb.com/title/{{selectedResult.imdbID}}","IMDb -- {{selectedResult.Title}}"][b]{{selectedResult.Title}}[/b] ({{selectedResult.Year}})[/url][/td][td align=right valign=top rowspan=2 fontsize=9][url="http://www.jeroensomhorst.eu/ubbgenerator/{{selectedResult.Poster}}"][img=110,163,,,"{{selectedResult.Title}} ({{selectedResult.Year}})",1]http://www.jeroensomhorst.eu/ubbgenerator/{{selectedResult.Poster}}[/][/url][/td][/tr][tr][td valign=top][small][b]Tweakers:[/b] {{selectedResult.tweakscore}} | [b]IMDb:[/b] {{selectedResult.imdbRating}} | [b]Genre:[/b] {{selectedResult.Genre}} | [b]Runtime:[/b] {{selectedResult.Runtime}} | [b][abbr="Motion Picture Association of America"]MPAA[/abbr]:[/b] {{selectedResult.Rated}}[/small][hr=noshade][justify]{{reviewText}}[/justify][/td][/tr][tr][td colspan=2 align=right][small][url="http://www.imdb.com/title/{{selectedResult.imdbID}}"][img=16,16,,left,""]http://www.jeroensomhorst.eu/ubbgenerator/assets/imdb.png[/img][/url][url="https://en.wikipedia.org/w/index.php?search={{selectedResult.Title}} (film)"][img=16,16,,left,""]http://www.jeroensomhorst.eu/ubbgenerator/assets/wikipedia.png[/img][/url][url="https://www.youtube.com/results?search_query=trailer+{{selectedResult.Title}}"][img=16,16,,left,""]http://www.jeroensomhorst.eu/ubbgenerator/assets/youtube.png[/img][/url][img=,24,,,,]http://www.jeroensomhorst.eu/ubbgenerator/assets/stars/{{reviewScore}}.png[/img][b]{{reviewScore}}[/b] / 10[/small][/td][/tr][/table][sub][url=http://www.jeroensomhorst.eu/ubbgenerator/#?format=ubb]Genereer je eigen UBB code review[/url][/sub]</textarea>
            <button class="ui button" id="ctc" v-on:click='copytoclipboard'>Copy to clipboard</button>
        </div>

        <Gallery v-if="viewmode == 'gallery'" id="gallery"/>

        </div>
        </div>

        </div>
        

        

    </div>
</template>
<script type="text/javascript">
/* eslint-disable */
import ReviewEditorButtonBar from './ReviewEditorButtonBar'
import SearchResult from './SearchResult'
import Gallery from './Gallery'
export default {
    name: 'ReviewEditor',
    components: {
        ReviewEditorButtonBar,SearchResult,Gallery
    },
    methods: {
        copytoclipboard(){
            let copyText = document.getElementById("ubbcodearea");
            /* Select the text field */
            copyText.select();
            copyText.setSelectionRange(0, 99999); /*For mobile devices*/
            document.execCommand("copy");

            /* Alert the copied text */
            alert("Review succesfully copied to clipboard");
        },
        generateReview(){
          this.viewmode = 'generate'
          let data= {};
          
          data.reviewScore = this.reviewScore;
          data.reviewText = this.reviewText;
          data.movie = this.selectedResult;
          data.movie.reviewtext = this.reviewText;
          data.movie.reviewscore = this.reviewScore;
          data.movie.Title = this.selectedResult.Title;
          axios.post('api.php/review',data).then((response)=>{});
        },
        showGallery(){
            this.viewmode = 'gallery';
        },

        onDecorateEvent(decoration){
            let node = document.getElementById('ReviewEditor');
            let content = node.value;
            if(!(content == ""|| content == null)){
                let startTag = "["+decoration+"]";
                let endTag = "[/]";
                let beforeSelection = this.reviewText.substring(0,node.selectionStart);
                let afterSelection = this.reviewText.substring(node.selectionEnd);
                let selectedContent = this.reviewText.substring(node.selectionStart,node.selectionEnd);
                selectedContent = startTag+selectedContent+endTag;
                this.reviewText = beforeSelection+selectedContent+afterSelection;
            }
        },
        onAlignEvent(alignment){
            this.onDecorateEvent(alignment);
        },

        selectResult(entry){
            this.selectedResult = entry;
            this.viewmode = 'write';
        },
        lookupImdbResult(searchTerm){
            if(searchTerm.length > 3){
               axios.get('api.php/movies/'+searchTerm).then((response)=>{this.searchResults = response.data.Search});
            }
        },
        goHome: async function(){
            this.viewmode = 'loading';
            this.nowplaying = []; // reset the list
            let latestReviewsResponse = await axios.get('api.php/reviews/latest');
            let nowRunningResponse = await axios.get('api.php/nowplaying');
            this.latestReviews = latestReviewsResponse.data.Search;
            this.searchResults = this.latestReviews;
            this.nowplaying = nowRunningResponse.data.splice(0,20);
            this.viewmode = 'search';
    },
    },
    data: function(){
        return {
            history: [],
            viewmode: 'loading',
            searchTerm: '',
            searchResults: [],
            nowplaying: [],
            selectedResult: {},
            reviewText: '',
            ubbReview:'',
            reviewScore: 0,
            latestReviews: []
            }
    },

    created: function(){
        this.goHome();
    },
    watch:{
        searchTerm: function(newTerm,oldTerm){
            
            if(newTerm.length > 3){
                // reset everything
                this.reviewText = '';
                this.viewmode = 'search';
                this.reviewScore = 0;
                this.ubbReview = '';
                this.selectedResult = {};

                this.lookupImdbResult(newTerm)
            }
        },
        reviewText: function(newTerm,oldTerm){
            this.history.push(newTerm);
        }
    }
}
</script>