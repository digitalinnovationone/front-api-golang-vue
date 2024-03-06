<template>
  <div class="container mt-5">
    <h1 class="mb-4">Quiz de Tradução</h1>

    <div v-if="loading">
      <div class="text-center">
        <div class="spinner-border text-primary" role="status">
          <span class="sr-only">Carregando...</span>
        </div>
      </div>
    </div>

    <template v-else>
      <div v-if="currentQuestion < palavras.length">
        <p class="lead">
          Qual é a tradução correta para a palavra "<strong>{{ palavras[currentQuestion].palavra }}</strong>"?
        </p>
        <ul class="list-unstyled">
          <li v-for="(opcao, index) in palavras[currentQuestion].opcoes" :key="index" class="mb-2">
            <label class="btn btn-outline-primary d-block text-left">
              <input type="radio" :value="opcao" v-model="selectedOption" autocomplete="off"> {{ opcao }}
            </label>
          </li>
        </ul>
        <button @click="checkAnswer" class="btn btn-primary mt-3">Próximo</button>
      </div>
      <div v-else class="text-center">
        <p class="lead">
          Jogo concluído! Você acertou <strong>{{ correctAnswers }}</strong> de <strong>{{ palavras.length }}</strong>.
        </p>
        <button @click="restartGame" class="btn btn-success mt-3">Recomeçar</button>
      </div>
    </template>
    
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'JogarPage',
  data() {
    return {
      palavras: [],
      currentQuestion: 0,
      selectedOption: '',
      correctAnswers: 0,
      loading: false
    }
  },
  mounted() {
    this.fetchPalavras()
  },
  methods: {
    fetchPalavras() {
      this.loading = true
      axios.get('http://localhost:8080/palavras')
        .then(response => {
          this.palavras = response.data
          this.currentQuestion = 0
          this.selectedOption = ''
          this.correctAnswers = 0
        })
        .catch(error => {
          console.error(error)
        })
        .finally(() => {
          this.loading = false
        })
    },
    checkAnswer() {
      if(this.selectedOption === this.palavras[this.currentQuestion].traducao) {
        this.correctAnswers++
      }
      this.currentQuestion++
      this.selectedOption = ''
      if (this.currentQuestion >= this.palavras.length) {
        this.loading = true
        setTimeout(() => {
          this.loading = false
        }, 1000)
      }
    },
    restartGame() {
      this.fetchPalavras()
    }
  }
}
</script>
