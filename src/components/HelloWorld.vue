<template>
  <div>
    <div v-if="reset" class="alert">
      <div class="alert-block1">
        <p>Вы выиграли! Повторить?</p>
        <button @click="reset = !reset, next()">Конечно!</button> 
        <button @click="dont = !dont, done()">Я уже устал</button>
      </div>
    </div>
    <h1>Word game Example 2 with Vue.js</h1>
    <h2>Время: {{min}}:{{sec}}</h2>
    <button @click="dont = !dont, done()">Я уже устал</button>
    <div v-if="dont" class="done">
      <div class="done-block1">
        <p>номер по порядка: {{continuation+1}}</p>
        <p>слово: {{words}}</p>
        <p>время выполнения-  {{min}}:{{sec}}</p>
      </div>
    </div>
    <div class="content">
      <div class="content-block1">
        <div 
          v-for="(item, index) in all" 
          :key="index" 
          class="box"
          :class="{'active': item.isActive}"
        >
          {{item.word}}
        </div>
      </div>
      <div class="content-block2">
        <div 
          v-for="(item, index) in len" 
          :key="index" 
          :class="{'act': item.isAct}"
          @click="btn(index)"
        >
          <button 
            class="block2-btn"
          >{{item.word}}</button>
        </div>
        <button @click="resets()">Reset</button>
      </div>
    </div>
    <img :src="img" alt="">
    <!-- {{answer}} -->
  </div>
</template>

<script>
  import answer from '../answer.json'
  import words from '../words';
  export default {
    data() {
      return {
        words: 'питер',
        len: [],
        word: '',
        rand: '',
        count: 0,
        all: [],
        reset: false,
        continuation: 0,
        timer: 0,
        sec: 0,
        min: 0,
        dont: false,
        img: '',
        answer: {}
      }
    },
    methods: {
      lenghts() {
        console.log(answer)
        this.words = words.words[this.continuation].word
        this.img = words.words[this.continuation].img
        function makeRandomArr(a, b) {
          return Math.random() - 0.5;
        }
        let word = this.words.split('')
        this.rand = word.sort(makeRandomArr)
        for(let i=0, l=this.rand.length; i<l; i++){
          this.all.push({ isActive: false})
          this.len.push({isAct: false, word: this.rand[i]})
        }
      },
      btn(index){
        if(this.len[index].isAct !== true){
          this.count++
        }
        this.all[this.count-1].isActive = true
        this.all[this.count-1].word = this.rand[index]
        this.word+=this.all[this.count-1].word
        this.len[index].isAct = true
        if(this.word === this.words){
          this.reset = true
        }
        else if(this.word !== this.words && this.count === this.all.length){
          alert("Не верно")
          this.resets()
        }
      },
      resets(){
        for(let i=0, l=this.all.length; i<l; i++){
          this.all[i].isActive = false
          this.all[i].word = ''
          this.len[i].isAct = false
          this.count = 0
          this.word = ''
        }
      },
      next(){
        this.all = []
        this.len = []
        this.word = ''
        if(this.continuation < 6){
          ++this.continuation
        }
        else{
          alert("Молодцы вы прошли")
        }
        this.count = 0
        this.lenghts()
      },
      time(){
        if(!this.dont){
          this.timer++;
        }
        this.sec = this.timer%60;
        this.min = parseInt(this.timer/60)
      },
      done(){
        answer.data.id = this.continuation;
        answer.data.name = this.words;
        answer.data.img_src = this.img;
        this.answer = answer
        console.log(answer)
        
      }
    },
    mounted () {
      this.lenghts()
      this.time()
      if(localStorage.answer){
        this.answer = localStorage.answer
      }
      setInterval(this.time.bind(this) , 1000)
    },
    watch: {
      answer(newValue) {
        localStorage.answer = newValue
      }
    },
  }
</script>

<style scoped>
  .content{
    width: 700px;
    margin: 0 auto;
    text-transform: uppercase;
  }
  .alert, .done{
    position: absolute;
    background: rgba(0, 0, 0, .3);
    bottom: 0;
    left: 0;
    right: 0;
    top: 0;
  }
  img{
    width: 400px;
    height: 300px;
  }
  .alert-block1, .done-block1{
    top: 50%;
    left: 50%;
    position: absolute;
    transform: translate(-50%, -50%);
    width: 500px;
    height: 300px;
    background: #fff;
  }
  .alert-block1 p{
    margin-top: 15%;
  }
  .box{
    width: 50px;
    height: 50px;
    background: #cdffe9;
    font-size: 0;
  }
  .box:not(:first-child){
    border-left: 1px solid #000;
  }
  .content-block1{
    margin-bottom: 3%;
  }
  .content-block1, .content-block2{
    display: flex;
    justify-content: center;
  }
  .active{
    font-size: 20px;
  }
  .block2-btn{
    background: #00dd7a;
    width: 40px;
    height: 40px;
    border: none;
    margin: 1px;
    color: #fff;
    font-weight: bolder;
    font-size: 24px;
    text-transform: uppercase;
  }
  .act button{
    background: #c0edd8;
  }
</style>