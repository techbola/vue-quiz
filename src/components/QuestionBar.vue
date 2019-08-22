<template>
    <div class="question-box-container">
        <b-jumbotron header="Quiz App" lead="techBola Quiz App">

            <template slot="lead">
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group class="mb-4">
                <b-list-group-item 
                    v-for="(answer, index) in answers" 
                    :key="index"
                    @click="selectAnswer(index)" 
                    :class="answerClass(index)">
                        {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button 
                variant="primary" 
                class="mr-3"
                @click="submitAnswer"
                :disabled="selectedIndex === null || answered">
                    Submit
                </b-button>
            <b-button @click="next" variant="success" href="#">Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>

import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function,
    },
    data(){
        return {
            selectedIndex: null,
            shuffledAnswers: [],
            correctIndex: null,
            answered: false
        }
    },
    computed: {
        answers(){

            let answers = [...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)

            return answers
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler () {
                this.selectedIndex = null
                this.answered = false
                this.shuffleAnswers()
            }
        }
    },
    methods: {
        selectAnswer(index){
            this.selectedIndex = index
        },
        shuffleAnswers(){
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers);
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        submitAnswer(){
            let isCorrect = false
            if(this.correctIndex === this.selectedIndex){
                isCorrect = true
            }

            this.answered = true

            this.increment(isCorrect)
        },
        answerClass(index){
            let answerClass = ''

            if(!this.answered && this.selectedIndex === index){
                answerClass = 'selectedAnswer'
            } else if(this.answered && this.correctIndex === index){
                answerClass = 'correctAnswer'
            } else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                answerClass = 'incorrectAnswer'
            }
            return answerClass
        }
    }
}
</script>

<style scoped>
    .list-group-item{
        cursor: pointer;
    }
    .list-group-item:hover{
        background: #666;
        color: #fff;
    }
    .selectedAnswer{
        background: lightblue;
    }
    .correctAnswer{
        background: lightgreen;
    }
    .incorrectAnswer{
        background: red;
    }
</style>