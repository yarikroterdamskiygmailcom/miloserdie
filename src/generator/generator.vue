<template>
    <div class="generator">
       <div class="containerGenerator">
           <h3>Подобрать пароль</h3>
           <div class="resultGenerator">
               <div class="res">{{result}}</div>
               <div class="buttonRes">
                   <ion-icon name="refresh" @click="change"></ion-icon>
                   <ion-icon name="copy" @click="copy" ></ion-icon>
               </div>
               <div class="massage" :class="{activeMassage:massage}">{{textMassage}}</div>
           </div>
           <div class="params">
               <div class="paramsInf">
                   <label id="length">Длина</label>
                   {{length}}
               </div>
               <input type="range" for="length" v-model="length" @click="change" @change="sbor" min="7" max="30" >
           </div>
           <div class="params">
               <div class="paramsInf">
                   <label id="wordLength" @click="change">Буквы</label>
                   {{wordLength}}
               </div>
               <input type="range" for="wordLength" v-model="wordLength"  @change="sbor" min="1" max="10" >
           </div>
           <div class="params">
               <div class="paramsInf">
                   <label id="numberLength">Цыфры</label>
                   {{numberLength}}
               </div>
               <input type="range" for="numberLength" v-model="numberLength" @click="change" @change="sbor" min="1" max="10" >
           </div>
       </div>
        <slides></slides>
    </div>
</template>
<style>
    .generator{
        display: flex;
        align-items: center;
        flex-direction: column;
        justify-content: center;
    }
    .containerGenerator{
        width: 40vw;
        padding: 20px;
        background: skyblue;
    }
    .resultGenerator{
        border-radius: 10px;
        padding: 10px;
        background: deepskyblue;
        display: flex;
        justify-content: space-between;
        position: relative;
    }
    .res{
        width: 100%;
        word-break: break-all;
        text-align: center;
        align-self: center;
    }
    .buttonRes{
        display: flex;
        align-self: center;
        flex-direction: column;
    }
    .params{
        width: 100%;
        display: flex;
        flex-direction: column;
    }
    .params input[type='range']{
        -webkit-appearance: none;
        height: 3px;
        /*padding: 10px;*/
    }
    input[type=range]:focus {
        outline: none;
    }
    input[type=range]::-webkit-slider-runnable-track {
        width: 100%;
        height: 8.4px;
        cursor: pointer;
        animate: 0.2s;
        /*box-shadow: 1px 1px 1px #000000, 0px 0px 1px #0d0d0d;*/
        /*background: #3071a9;*/
        border-radius: 1.3px;
        /*border: 0.2px solid #010101;*/
    }
    input[type=range]::-webkit-slider-thumb {
        box-shadow: 1px 1px 1px #000000, 0px 0px 1px #0d0d0d;
        border: 1px solid #000000;
        height: 20px;
        width: 5px;
        border-radius: 3px;
        background: #ffffff;
        cursor: pointer;
        -webkit-appearance: none;
        margin-top: -8px;
    }
    .paramsInf{
        display: flex;
        justify-content: space-between;
    }
    .massage{
        color:white;
        position: absolute;
        font-size: small;
        bottom: 0;
        opacity: 0;
        transition: all 0.5s;
    }
    .activeMassage{
        opacity: 1;
    }
    @media screen and (max-width: 720px){
        .containerGenerator {
            width: 100vw;
        }
    }
</style>
<script>
    import slides from './slides.vue'
    export default {
        name: 'app',
        data () {
            return {
                alphabet:['A','a', 'E','e', 'I','i', 'O','o', 'U','u', 'Y','y','B','b', 'C','c', 'D','d',
                    'F','f', 'G','g', 'H','h', 'J','j', 'K','k', 'L','l', 'M','m', 'N','n', 'P','p', 'Q','q',
                    'R','r' ,'S','s', 'T','t', 'V','v', 'W','w', 'X','x', 'Z','z'],
                numbers:[1,2,3,4,5,6,7,8,9,0],
                result:null,
                length:7,
                wordLength:5,
                numberLength:5,
                massage:false,
                textMassage:''
            }
        },
        methods:{
            compareRandom:function () {
                return Math.random() - 0.5;
            },
            getRandom:function(min, max){
                return Math.floor(Math.random() * (max - min) + min);
            },
            sorts:function(simvol){
                return simvol.sort(this.compareRandom)
            },
            fors:function(simvols,number){
                let save = []
                for(let i = 0;i<number; i++) {
                    save.push(simvols[this.getRandom(0,simvols.length)])
                }
                return save
            },
            change:function(){
                this.sorts(this.alphabet,this.alphabet.length)
                this.sbor()
            },
            sbor:function(){
                this.result = this.finish
            },
            copy:function(e){
                this.massage = true
                let its = this
                let text = this.result
                let input = document.createElement('input');
                input.setAttribute('value', text);
                document.body.appendChild(input);
                input.select();
                try {
                    let result = document.execCommand('copy');
                    let msg = true? "успешный" : "неудачный" ;
                    this.textMassage = 'Скопировано'
                } catch (err) {
                    this.textMassage = 'Не скопировано'
                }
                document.body.removeChild(input)
                setTimeout(function(){
                    its.massage = false
                },2000)
                return false;
            }
        },
        computed:{
            word:function(){
               return this.fors(this.sorts(this.alphabet),this.wordLength)
            },
            number:function(){
               return this.fors(this.sorts(this.numbers),this.numberLength)
            },
            beforefinish:function(){
                return this.word.concat(this.number).sort(this.compareRandom)
            },
            middlefinish:function(){
                return  this.fors(this.beforefinish,this.length)
            },
            finish:function(){
                let str =  null;
                let result = null
                let arr =this.middlefinish

                for(let i = 0;i<arr.length;i++){
                    i == 0?str = String(arr[i]):str+=String(arr[i])
                }
                return str
            }
        },
        mounted(){
            this.sorts(this.alphabet,this.alphabet.length)
            this.sbor(this.finish)
        },
        components:{
            slides:slides
        }
    }
</script>
