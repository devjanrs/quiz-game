<template>

  <div>

    <Placartela :winCount="this.winCount" :loseCount="this.loseCount" />
    
    <h1 v-html="this.questao">
    </h1>

    <template v-for="(answer, index) in this.resposta" :key="index">
      <input
      :disabled="this.resposta_enviada" 
      type="radio" 
      name="options" 
      :value="answer"
      v-model="this.resposta_escolhida"
      >

      <label v-html="answer"></label><br>
    </template>
    
        
    <button v-if="!this.resposta_enviada" @click="this.submitAnswer()" class="send" type="button">Enviar</button>

    <section v-if="this.resposta_enviada" class="result">
        <h4 v-if="this.resposta_escolhida == this.respostacorreta"
        v-html="'&#9989; Parabéns! A resposta ' + this.respostacorreta + ' é a correta.'">    
        </h4>
        <h4 v-else
        v-html="'&#10060; Que pena, você errou =/  A resposta correta é ' + this.respostacorreta +'.'">       
        </h4>
        <button @click="this.getNewQuestion()" class="send" type="button">Próxima pergunta</button>
    </section>

  </div>
</template>

<script>
import Placartela from '@/components/PlacarTela.vue'


export default {
  name: 'App',
  components: {
    Placartela
  },

  data() {
    return {
      questao: undefined,
      respostaincorreta: undefined,
      respostacorreta: undefined,
      resposta_escolhida: undefined,
      resposta_enviada: false,
      winCount: 0,
      loseCount: 0,
    }
  },
  
  computed: {
    resposta() {
      var respostas = [...this.respostaincorreta];
      respostas.splice( Math.round(Math.random() * respostas.length), 0, this.respostacorreta);
      return respostas;
    }
  },
  methods: {

    submitAnswer() {
      if (!this.resposta_escolhida) {
        alert("Escolha uma das opções")
      } else {
        this.resposta_enviada = true
        if (this.resposta_escolhida == this.respostacorreta) {
          this.winCount++
        } else {
          this.loseCount++
        }
      }
    },

    getNewQuestion() {

      this.resposta_enviada = false;
      this.resposta_escolhida = undefined;
      this.questao = undefined;

      this.axios
      .get('https://opentdb.com/api.php?amount=1&category=21&difficulty=easy')
      .then((response) => {
        this.questao = response.data.results[0].question;
        this.respostaincorreta = response.data.results[0].incorrect_answers;
        this.respostacorreta = response.data.results[0].correct_answer;  
      })
    }
  },
  created() {
    this.getNewQuestion()
  }
}

// https://opentdb.com/api.php?amount=1&category=21&difficulty=easy
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #2c3e50;
    border: 1px solid #2c3e50;
    border-radius: 15px;
    cursor: pointer;
    transition: 0.5s;
  }

  button.send:hover {
    background-color: #2cccc4;
    border: 1px solid #2cccc4;
    border-radius: 30px;
    cursor: pointer;
  }
}
</style>
