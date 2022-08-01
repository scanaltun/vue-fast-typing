<template>
  <div class="container jumbotron bg-info">
    <h1 class="display-4">Typing Speed Test</h1>
    <p class="lead">How fast can you type?</p>
  

    <hr class="my-4" />

    <div v-if="isFinish" class="alert alert-primary">
        <h3>Time is up!</h3>
        <p class="display-4">{{wpm}} WPM </p>    
        <span>Accuracy Percent : % {{accuracyPercent}}</span> <br>
        <span>Correct Word : {{trueCount}}</span> <br>
        <span>Wrong  Word :  {{falseCount}}</span> <br>
        
        <button @click="newTest" class="btn btn-success mt-5 btn-lg btn-black">New Test</button>
    </div>

    <div v-else>

    <div  class="card">
      <div class="card-body">
        <span v-for="(word,key) in words.filter((data,index)=>index<15)" :key="key"  v-bind:class="key!=0 || writingWordControl " class="words ml-10 ">{{word}}</span>
        </div>
    </div>

    <div class="card">
      <div class="card-body  bg-danger">
        <div class="input-group input-group-lg">
          <input
            type="text"
            class="form-control"
            v-model="writingWord"
          />
          
          <div class="input-group-append" id="button-addon4">
            <button class="btn btn-dark" disabled type="button">
              {{timer}} sec.
            </button>
            <button :disabled="isRunning" class="btn btn-dark" type="button" @click="getWords">
              Yenile
            </button>
          </div>
        </div>
      </div>
    </div>
    </div>
  </div>
</template>

<script>

import wordList from '@/assets/word.json'

export default {
    data() {
        return {
            words:[],
            writingWord:null,
            isTrue:true, 
            trueCount:0,
            falseCount:0,
            timer: 60,
            interval:false,
            isRunning:false,
            isFinish:false,
            wordList:wordList,

        }
    },

watch:{
    writingWord(val){
         if(!val|| val== ' '){
            this.writingWord=''
            return
            //space e bastığımızda çalışmamasını sağlar.
         }
        if(!this.isRunning) this.toggleTimer()
        const word = this.words[0].slice(0, val.length)
        const userWord = val.replace(' ','')
        //.toUpperCase() büyük küçük harf sorunu olmasın istenirse 

        this.isTrue = word=== userWord

        if(val.indexOf(' ') !== -1) {
            this.isTrue ? this.trueCount++ :this.falseCount++
            this.words.splice(0,1)
            this.writingWord=''
            this.isTrue=true

        }
    }
},


computed:{
    writingWordControl(){
        return this.isTrue ? 'writing-word' : 'writing-word bg-danger'
    },
    wpm(){
        return 300 - this.words.length
    },
    accuracyPercent(){
        const percent = ( 100 / this.wpm)
        const val =(percent *this.trueCount)
        return isNaN(val) ? 0 :val 
    }
},

mounted(){
    this.getWords()

},

methods:{

    newTest(){
        this.getWords()
        this.isFinish=false
        this.timer=60
        this.isTrue=true
        this.isRunning=false
    },

    getWords(){
        this.words=this.wordList.sort(()=>Math.random()-0.5).splice(0,300)

    },

    toggleTimer(){
        this.isRunning = true,
        this.interval = setInterval(this.timeProcess,1000)
    },
    timeProcess(){
          if(this.timer===0){
            clearInterval(this.interval)
            this.isFinish=true
            return;
          }
        this.timer --   
    }

}

};
</script>

<style>

    .words{
        font-size:30px;
        font-weight: 400;
        margin-left: 10px;

    }

    .writing-word{
        background-color: lightgray;
        color:black;
        padding: 4px;
        border-radius:5px;
    }
</style>