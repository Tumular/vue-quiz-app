<script setup>
import { ref, computed } from 'vue';
import arquivoPerguntas from './perguntas.json';

const questions = ref(arquivoPerguntas);

const quizCompleted = ref(false)
const currentQuestion = ref(0)
const score = computed(() => {
	let value = 0
	questions.value.forEach(q => {
		if (q.selected != null && q.answer == q.selected) {
			value++
		}
	})
	return value
})

const getCurrentQuestion = computed(() => {
	let question = questions.value[currentQuestion.value]
	question.index = currentQuestion.value
	return question
})

const SetAnswer = (e) => {
	questions.value[currentQuestion.value].selected = e.target.value
	e.target.value = null
}

const NextQuestion = () => {
	if (currentQuestion.value < questions.value.length - 1) {
		currentQuestion.value++
		return
	}

	quizCompleted.value = true
}

const NewQuiz = () => {
	if (quizCompleted.value == true) {
		currentQuestion.value = 0;
		questions.value.forEach(q => q.selected = null);
		quizCompleted.value = false;
	}
}
</script>

<template>
	<main class="app">
		<h1>Perguntas</h1>
		<section class="quiz" v-if="!quizCompleted">
			<div class="quiz-info">
				<div class="score">Pontuação {{ score }}/{{ questions.length }}</div>
				<div class="question">{{ getCurrentQuestion.question }}</div>
			</div>

			<div class="options">
				<label v-for="(option, index) in getCurrentQuestion.options" :for="'option' + index" :class="`option ${getCurrentQuestion.selected == index
					? index == getCurrentQuestion.answer
						? 'correct'
						: 'wrong'
					: ''
					} ${getCurrentQuestion.selected != null &&
						index != getCurrentQuestion.selected
						? 'disabled'
						: ''
					}`">
					<input type="radio" :id="'option' + index" :name="getCurrentQuestion.index" :value="index"
						v-model="getCurrentQuestion.selected" :disabled="getCurrentQuestion.selected" @change="SetAnswer" />
					<span>{{ option }}</span>
				</label>
			</div>

			<button @click="NextQuestion" :disabled="!getCurrentQuestion.selected">
				{{
					getCurrentQuestion.index == questions.length - 1
					? 'Finalizada'
					: getCurrentQuestion.selected == null
						? 'Escolha uma resposta'
						: 'Próxima pergunta'
				}}
			</button>
		</section>

		<section v-else>
			<h2>Você terminou o quiz!</h2>
			<p>Sua pontuação final é {{ score }}/{{ questions.length }}</p>
			<button @click="NewQuiz">Refazer</button>
		</section>
	</main>
</template>

<style scoped>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'Montserrat', sans-serif;
}

body {
	background-color: #271c36;
	color: #FFF;
}

.app {
	display: flex;
	flex-direction: column;
	align-items: center;
	padding: 2rem;
	height: 100vh;
}

h1 {
	font-size: 2rem;
	margin-bottom: 2rem;
}

.quiz {
	background-color: #382a4b;
	padding: 1rem;
	width: 460px;
	max-width: 640px;
}

.quiz-info {
	justify-content: space-between;
	margin-bottom: 1rem;
}

.quiz-info .question {
	color: #8F8F8F;
	font-size: 1.25rem;
}

.quiz-info .score {
	color: #FFF;
	font-size: 1.25rem;
	padding-bottom: 10px;
}

.options {
	margin-bottom: 1rem;
}

.option {
	padding: 1rem;
	display: block;
	background-color: #271c36;
	margin-bottom: 0.5rem;
	border-radius: 0.5rem;
	cursor: pointer;
}

.option:hover {
	background-color: #2d213f;
}

.option.correct {
	background-color: #2cce7d;
}

.option.wrong {
	background-color: #ff5a5f;
}

.option:last-of-type {
	margin-bottom: 0;
}

.option.disabled {
	opacity: 0.5;
}

.option input {
	display: none;
}

button {
	appearance: none;
	outline: none;
	border: none;
	cursor: pointer;
	padding: 0.5rem 1rem;
	background-color: #2cce7d;
	color: #2d213f;
	font-weight: 700;
	text-transform: uppercase;
	font-size: 1.2rem;
	border-radius: 0.5rem;
}

button:disabled {
	opacity: 0.5;
}

h2 {
	font-size: 2rem;
	margin-bottom: 2rem;
	text-align: center;
}

p {
	color: #8F8F8F;
	font-size: 1.5rem;
	text-align: center;
}
</style>
