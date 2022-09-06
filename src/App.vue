<template>
  <div id="app">
   <div class="container">
      <h1>VueJS Currency Exchange App</h1>
      <div class="main">
      <div class="container-first">
        <div class="first-block">
          <div class="image">
            <img v-show="flag1=='ukr'" src='./assets/img/Flag_of_Ukraine_3.svg' alt="ukr" >
            <img v-show="flag1=='usd'" src='./assets/img/Flag_of_the_United_States_(Pantone).svg' alt="usd" >
            <img v-show="flag1=='eur'" src='./assets/img/Flag_of_Europe.svg' alt="eur" >
          </div>
          <select name="first_currency" id="first_currency" v-model="first_currency" @change="fetchData()">
            <option v-show="choose=='false'" value="UAH">UAH</option>
            <option v-show="choose=='true'" value="USD">USD</option>
            <option v-show="choose=='true'" value="EUR">EUR</option>
          </select>
        </div>
       
        <input type="number" name="value-first" id="value-first" v-model="amountFirst" @change="fetchData()">
      </div>
      <button @click="switchData()"></button>
      <div class="container-third">
        <div class="third-block">
          <select name="second_currency" id="second_currency" v-model="second_currency" @change="fetchData()">
              <option class="ua" v-show="choose=='true'" value="UAH">UAH</option>
              <option class="usd" v-show="choose=='false'" value="USD">USD</option>
              <option class="eur" v-show="choose=='false'" value="EUR">EUR</option>
          </select>
          <div class="image">
            <img v-show="flag2=='ukr'" src='./assets/img/Flag_of_Ukraine_3.svg' alt="ukr" >
            <img v-show="flag2=='usd'" src='./assets/img/Flag_of_the_United_States_(Pantone).svg' alt="usd" >
            <img v-show="flag2=='eur'" src='./assets/img/Flag_of_Europe.svg' alt="eur" >
          </div>  
        </div>  
            <input type="number" name="value-second" id="value-second" disabled  v-model="amountSecond" @change="fetchData()">       
      </div>
     
      </div>
       <div class="container-second">
           <h2 id ="baseValue">1 {{first_currency}} = {{this.rate}} {{second_currency}} </h2>
      </div>
      <div class="container-fourth">
        <h2 id="date">Date and time</h2>
        <p>{{currentDateTime()}}</p>
      </div>
    </div>
  </div>
</template>

<script>


export default {
  data(){
    return{
      data:[],
      first_currency:"UAH",
      second_currency: "USD",
      rate:"",
      amountFirst: 1,
      amountSecond: 0,
      choose:'false', 
      flag2: 'usd',
      flag1: 'ukr'
    }
  },
  methods:{
    fetchData(){
      fetch('https://api.privatbank.ua/p24api/pubinfo?json&exchange&coursid=5')
      .then(res=>res.json())
      .then(data=>{
        this.data=data;
        if (this.first_currency == "UAH" && this.second_currency=="USD") {
          this.rate = parseFloat(1/data[0].buy).toFixed(4);
          this.choose='false';
          this.flag1='ukr';
          this.flag2='usd'
        } else if (this.first_currency == "UAH" && this.second_currency=="EUR"){
          this.rate = parseFloat(1/data[1].buy).toFixed(4);
          this.choose='false';
          this.flag1='ukr'
          this.flag2='eur'
        } else if (this.first_currency =="USD"){
          this.rate = parseFloat(data[0].sale).toFixed(4);
          this.choose ='true';
          this.flag1='usd';
          this.flag2='ukr';
        } else if (this.first_currency =="EUR"){
          this.rate = parseFloat(data[1].sale).toFixed(4);
          this.choose ='true';
          this.flag1='eur';
          this.flag2='ukr'
        }
        console.log('flag1='+this.flag1);
        console.log('flag2='+this.flag2);
        this.amountSecond=this.amountFirst*this.rate;
        
      });
    },
    switchData(){
      let temp =this.first_currency;
      this.first_currency=this.second_currency;
      this.second_currency=temp;
      let temp_flag = this.flag1;
      this.flag1 = this.flag2;
      this.flag2 = temp_flag;
      this.fetchData();
    },
     currentDateTime() {
      const current = new Date();
      const date = current.getDate() + '/'+ (current.getMonth()+1) + '/'+ current.getFullYear();
      const time = current.getHours() + ":" + current.getMinutes() + ":" + current.getSeconds();
      const dateTime = date +' '+ time;
      return dateTime;
    }
  },
  mounted(){
    this.fetchData();
    
  }
}
</script>

<style>
#app {
  text-align: center;
}
.container{
      box-shadow: 0 10px 20px 0 rgb(180 190 200 / 100%);
      padding: 2vw;
      width: 70%;
      margin: auto;
}
.main{
    display: flex;
    justify-content: center;
  }
  .main input{
    font-size: 2vw;
    padding: 2vw;
  }
  .main select{
    font-size: 2vw;
    padding: 1.8vw;
    width: 40%
  }
  .container-first{
    display: block;
    text-align: end;
  }
  .container-third {
    display: block;
    text-align: start;
   }
  .container-first select{
    width: 40%;
  }
  .container-first input{
    width: 40%;
  }
  .container-third input{
    width: 40%;
  }
  .container-third select{
    width: 40%;
  }
  #first_currency,
  #second_currency{
    margin-bottom: 3vw;
  }
  .main button{
    background-image: url("./assets/img/code_16.svg");
    background-repeat: no-repeat ;
    width: 6vw;
    border: none;
    margin: 2vw;
    background-position: center;
    background-color: transparent;
  }
  .container-fourth{
    font-size: 25px;
  }
  .first-block{
    display: flex;
    justify-content: flex-end;
  }
  .third-block{
    display: flex;
    justify-content: flex-start;
  }
  .image img{
    height: 6vw;
  }
</style>
