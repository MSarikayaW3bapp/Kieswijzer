<template>
<div id="containerBuiten">
  <div id="links"></div>
  <div id="midden">
    <div id="containerBinnen">
      <div id="vraag">
        <h1>Vraag {{iteratie + 1}}:</h1>
        <p>{{vraagData.vraag}}</p>
      </div>
      <div id="foto">
        <img id="img" v-bind:src="image">
      </div>
      <div id="buttons">
        <div v-on:click="trueGeklikt()" class="button groen">&#10003;</div>
        <div v-on:click="falseGeklikt()" class="button rood">&#9587;</div>
      </div>
      <div id="progresionBar">
        <div id="bar">
          <div id="point"></div>
        </div>
        <div id="progesieMobile">
          {{iteratie + 1 +` van `+ vragenData.length}}
        </div>
      </div>
    </div>
  </div>
  <div id="rechts"></div>
</div>
</template>
<script>
import router from "../router";

export default {
  name: "VraagComponent",
  beforeCreate() {
    fetch(`${process.env.VUE_APP_BACKEND_URL}/getAllQuestions`)
        .then((response) => {
          return response.json();
        })
        .then((myJson) => {
          this.vragenData = myJson;
          this.lengten = 500/myJson.length
        });
  },
  data(){
    return{
      image: null,
      vragenData: null,
      lengten:0,
      iteratie:0,
      geklikteButton:true,
      allPoints:[
        {id: 0, name: "Ict", points: 0},
        {id: 1, name: "AenM", points: 0},
        {id: 2, name: "TenI", points: 0},
        {id: 3, name: "Mei", points: 0},
        {id: 4, name: "BenI", points: 0}
      ],
      nummer1:"",
      nummer2:"",
      nummer3:"",
      progesie:""
    }
  },
  mounted(){
    this.image = `${process.env.VUE_APP_BACKEND_URL}/uploads/` + this.vraagData.image
  },
  computed:{
    vraagData(){
      return{
        ...this.vragenData[this.iteratie]
      }
    }
  },
  methods:{
    //functie om naar de volgende vraag te gaan
    next(){
      this.iteratie++
      let x = this.iteratie * this.lengten.toFixed()
      this.progesie = x + 'px'
      document.getElementById("point").style.marginLeft = this.progesie
      this.image = `${process.env.VUE_APP_BACKEND_URL}/uploads/` + this.vraagData.image
    },
    //functcdcie om punten te controleren en verdelen
    controle() {
      //controleert of er nog vragen zijn
      if (this.iteratie < this.vragenData.length -1) {
        //controleert of de juiste button is geklikt
         if(this.geklikteButton === this.vraagData.juisteAntwoord){
           //punten worden verdeeld
           this.allPoints[0].points += this.vraagData.puntenIct
           this.allPoints[1].points += this.vraagData.puntenAenM
           this.allPoints[2].points += this.vraagData.puntenTenI
           this.allPoints[3].points += this.vraagData.puntenMei
           this.allPoints[4].points += this.vraagData.puntenBenI
         }
         //functie next word aangeroepen
         this.next()
      }
      else{
         //alle vragen zijn beantwoord
         //sorteert de lijst met punten
         this.allPoints.sort((a, b) => {return a.points - b.points})
         // nummer 4 is de grootste
         this.nummer1 = this.allPoints[4].id
         this.nummer2 = this.allPoints[3].id
         this.nummer3 = this.allPoints[2].id
         //de top 3 word opgeslagen in de html session
         sessionStorage.setItem("n1",this.nummer1)
         sessionStorage.setItem("n2",this.nummer2)
         sessionStorage.setItem("n3",this.nummer3)
         router.push('resultaten')//hiermee ga je naar de resultaaten pagina
      }
    },
    //button voor als je op vals geklikt hebt
    falseGeklikt(){
      //staat geklikteButton veranderd
      this.geklikteButton = false
      // roept controle functie aan
      this.controle()
    },
    //button voor als je op true geklikt hebt
    trueGeklikt(){
       this.geklikteButton = true
      //roept controle functie aan
       this.controle()
    }
  }
}
</script>
<style scoped>
*{
  font-family: ff-scala-sans-pro,sans-serif;
  padding: 0px;
  margin-left: 0px;
  margin-right: 0px;
}
#foto{
  display: flex;
  justify-content: center;
}
#img{

  height: 250px;
}
#containerBuiten{
  display: flex;
  width: 100%;
  height: 100%;
}
#links{
  width: 5%;
}
#midden{
  width: 90%;
}
#containerBinnen{
  display: flex;
  flex-direction: column;
  width: 100%;
}
#vraag{
  width: 100%;
}
#vraag h1{
  margin-top: 25px;
  text-align: center;
}
#vraag p{
  text-align: center;
  font-size: 1.5em;
}
#buttons{
  width: 100%;
  display: flex;
  justify-content: center;
}
.button{
  color: white;
  padding: 10px 65px 10px 65px;
  margin: 10px;
  border-radius: 15px;
  cursor: pointer;
}
.rood{
  font-size: 45px;
  background-color: #E30513;
}
.groen{
  font-size: 55px;
  background-color: green;
}
#progresionBar{
  display: flex;
  flex-direction: column;
  align-content: center;
  height: 100px;
}
#bar{
  display: none;
  align-self: center;
  margin-top: 30px;
  border: 2px solid darkgray;
  width: 500px;
  height: 0px;
}
#point{
  margin-left: 0px;
  margin-top: -7px;
  width: 15px;
  height: 15px;
  border: 1px cornflowerblue solid;
  border-radius: 10px;
  background-color: cornflowerblue;
}
#progesieMobile{
  align-self: center;
  font-size: 1.5em;
  margin-top: 30px;
}
#rechts{
  width: 5%;
}
@media (min-width: 800px) {
  .button{
    color: white;
    padding: 15px 90px 15px 90px;
    margin: 10px;
    border-radius: 15px;
    cursor: pointer;
  }
  .rood{
    font-size: 55px;
    background-color: #E20D18;
  }
  .groen{
    font-size: 65px;
    background-color: green;
  }
  #progresionBar{
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 100px;
  }
  #bar{
    display: inline-block;
    margin-top: 30px;
    border: 2px solid darkgray;
    width: 480px;
    height: 0px;
  }
  #point{
    margin-left:-3px;
    margin-top: -7px;
    width: 15px;
    height: 15px;
    border: 1px cornflowerblue solid;
    border-radius: 10px;
    background-color: cornflowerblue;
  }
  #progesieMobile{
    display: none;
  }
}

</style>