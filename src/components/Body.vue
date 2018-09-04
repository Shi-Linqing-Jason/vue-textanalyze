<template>
    <div class="container">
        <div class="row">
            <div class="col-lg-6  col-md-6 col-xs-6 container">
                <div class="col-sm">
                    <br>
                    <br>
                    <br>
                    <select v-model="language" class="form-group form-control" id="source">
                        <option selected="selected" value="en">英文</option>
                        <option value="zh">中文</option>
                        <option value="ti">藏语</option>
                        <option value="ug">维吾尔语</option>
                    </select>
                    <Uploader></Uploader>
                    <br>
                    <br>
                    <div class="form-group col-sm">
                        <textarea v-model="sourceText" class="form-control" name="" id="text-input" cols="60" rows="20"></textarea>
                    </div>
                </div>
            </div>
            <div class="col-lg-6 container">
                <div class="col">
                    <div>
                        <button type="button" class="btn btn-primary btn-md" id="button1" v-on:click="analyzeButton">开始分析</button>        
                        <br><br><br><br><br>
                        <div v-show="showTime"><p>关键词列表</p></div>
                        <div>
                            <ul id="example-1">
                                <li v-for="item in keyWords">
                                    {{ item.word }}
                                </li>
                            </ul>
                        </div>
                        <div v-show="showTime"><p>文本摘要</p></div>
                        <div>
                            <ul id="example-2">
                                <li v-for="item in keySentences">
                                    {{ item.sentence }}
                                </li>
                            </ul>
                        </div>
                        
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Uploader from './Uploader'
import axios from 'axios'
import config from '../config'
import qs from 'qs'
export default {
    components: {
        Uploader
    },
    data() {
        return {
            sourceText: "",
            language: "en",
            keyWords: [],
            keySentences: [],
            showTime: false
        }
    },
    methods: {
        // analyzeButton() {
        //     console.log(this.sourceText)
        //     console.log(this.language)
        //     var sentences_url = config.baseurl + '/analyze/keywords'
        //     console.log({lang:this.language, text:this.sourceText})
        //     //var Info = 'text='+this.sourceText+'& lang='+this.language
        //     var data = qs.stringify({"lang": this.language, "text": this.sourceText})
        //     axios({
        //         method:'post',
        //         url: sentences_url,
        //         headers:{
        //             'Content-type' : "application/x-www-form-urlencoded"
        //         },
        //         data: data
        //         // params:{
        //         //     'lang': this.language, 
        //         //     'text': this.sourceText
        //         // }
        //     }).then(function(response){
        //         console.log(response)
        //     }).catch(function(err){
        //         console.log(err)
        //     })
        // }
        analyzeButton() {
            this.showTime = true
            var resultKeywords = this.keyWords
            var resultSentences = this.keySentences
            resultKeywords.splice(0,resultKeywords.length);
            resultSentences.splice(0,resultSentences.length);
            axios.post(config.baseurl + '/analyze/keywords', {'lang': this.language, 'text': this.sourceText})
                  .then(function (response) {
                    for(var i = 0; i < 8; i++) {
                        resultKeywords.push(response.data[i])
                    }
                  })
                  .catch(function (error) {
                    console.log(error);
                  });
            
            axios.post(config.baseurl + '/analyze/keysentences', {'lang': this.language, 'text': this.sourceText})
                  .then(function (response) {
                    for(var i = 0; i < response.data.length; i++) {
                        resultSentences.push(response.data[i])
                    }
                  })
                  .catch(function (error) {
                    console.log(error);
                  });
            
        }
    }
}
</script>

<style>
    
    #example-2 li{
        list-style-type:none;
        display:inline;
    }
</style>
