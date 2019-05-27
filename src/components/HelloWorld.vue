<template>
  <div>
      <div class="contanier">
         {{person1.name}}:{{person1.hp}}<br/><br/>
         {{person2.name}}:{{person2.hp}}<br/><br/>
         <button @click="restart()">开始对战</button>
         <button @click="beginGame()" :disabled='autoTimer||!person1||gameOver'>下一步</button>
         <button @click="autoPlay()" v-if="!autoTimer" :disabled='!person1||gameOver'>自动进行</button>
         <button @click="stopPlay()" v-else :disabled='!autoTimer||gameOver'>停止</button>
         
      </div>
      <div class="tips">
        <p v-for="(item,index) in tipArr" :key="index">
          <span :class="item.style">{{item.tips}}</span> 
        </p>
        <p v-if="gameOver">游戏结束,获胜者:{{winner}}</p>

      </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      person1:'',
      person2:'',
      ifFirstRound:true,
      whoBeat:0,
      gameOver:false,
      winner:'',
      autoTimer:null,
      tipArr:[]
    }
  },
  methods: {
    restart(){
      this.initData()
    },
    initData() {
      this.person1=''
      this.person2=''
      this.ifFirstRound=true
      this.whoBeat=0
      this.gameOver=false
      this.winner=''
      this.stopPlay()
      this.autoTimer=null
      this.tipArr=[]
      var  person=[
        {  
           level:11,
           name:'张飞',
           hp:1120,
           atk:240,
           def:180,
           boom:0.3,
           speed:200,
           skill:[

           ]
        },
        {
           level:11,
           name:'许褚',
           hp:1380,
           atk:231,
           def:200,
           boom:0.2,
           speed:150,
        }
      ]
      var playersNum=[]
      var count=0
      while(count<2){
        let x=Math.floor(Math.random()*person.length)
        if(playersNum.indexOf(x)==-1){
          count++;
          playersNum.push(x)
        }
      }
      this.person1=person[playersNum[0]]
      this.person2=person[playersNum[1]]
    },
    beginGame(){
      if(this.gameOver){
        console.log('游戏结束')
        return
      }
      //处理第一回合攻击顺序
      if(this.ifFirstRound){
        if(this.person1.speed<this.person2.speed){
          this.whoBeat=1
        }
        this.ifFirstRound=false
      }
      if(this.whoBeat==0){
        this.beat(this.person1,this.person2)
        this.whoBeat=1
        return
      }
      if(this.whoBeat==1){
        this.beat(this.person2,this.person1)
        this.whoBeat=0
        return
      }
    },
    autoPlay(){
      if(this.gameOver){
        return
      }
      clearInterval(this.autoTimer)
      let _this=this
      _this.autoTimer=setInterval(function(){
        if(_this.gameOver){
          clearInterval(_this.autoTimer)
          return
        }
        _this.beginGame()
      }, 1200);
    },
    stopPlay(){
      clearInterval(this.autoTimer)
      this.autoTimer=null
    },
    beat(palyer1,palyer2){
      this.tipArr=[]
      let ranNum=Math.ceil(Math.random()*4)
      let atkNum=Math.ceil((palyer1.atk*palyer1.atk)/(palyer1.atk+palyer2.def)*(palyer1.level/palyer2.level/2)) + ranNum
      this.tipArr.push({
        tips:palyer1.name+'攻击了'+palyer2.name,
        style:'normal1'
      })
      if(Math.random()<palyer1.boom){
        this.tipArr.push({
          tips:palyer1.name+'打出了暴击',
          style:'lightHeight'
        })
        atkNum=Math.ceil(atkNum*2.5)
      }
      palyer2.hp-=atkNum
      if(palyer1.hp<=0){
        palyer1.hp=0
        this.gameOver=true
        this.winner=palyer2.name
      }
      if(palyer2.hp<=0){
        palyer2.hp=0
        this.gameOver=true
        this.winner=palyer1.name
      }
      this.tipArr.push({
        tips:palyer2.name+'最终受到'+atkNum+'点伤害',
        style:'normal2'
      })
    }
  },
  mounted(){
    
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.tips{
  width: 500px;
  min-height: 300px;
  border:1px slateblue dashed;
  margin:30px  auto; 
}
.normal1{
  color: cornflowerblue
}
.normal2{
  color: olive
}
.lightHeight{
  color: rebeccapurple
}



</style>
