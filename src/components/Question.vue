<template >
    <div class=" card w-1/2 bg-base-content text-base-300 overflow-hidden" :class="animate_class">
        <div class="card-body items-center text-center">
            <div class="justify-between flex align-center w-full">
                <span>Question {{ index + 1 }}/10</span>
                <span :class="class_message">{{ message }}</span>
                <span>Temps restant 00:{{ temp_restant }}</span>
            </div>
            <div class="divider bg-base-300 " style="height: 0.1rem;"></div>
            <h2 class="card-title">{{ question['question'] }}</h2>
            <div class="grid grid-rows-2 grid-cols-2 gap-4 mt-2">
                <div v-for="proposition in question['propositions'] " :data-proposition="proposition" class="btn w-36"
                    :class="{ 'btn-disabled': btn_desactive, 'btn-error': error_class == proposition, 'btn-success': success_class == proposition }"
                    @click="evalAnswer">{{ proposition }}</div>
            </div>

            <div v-show="isContinueShowed" class="card-actions justify-end mt-5">
                <button @click="this.$emit('next')" class="btn btn-neutral">Continuer <i
                        class="bi bi-arrow-right"></i></button>
            </div>
        </div>
        <div class="w-full h-2">
            <div class="bg-secondary h-2 transition" :style="{ 'width': (((index + 1) / 10) * 100) + '%' }"></div>
        </div>
    </div>
</template>
<script >

export default {
    props: ['index', 'question'],
    data() {
        return {
            temp_restant: 15,
            isTimerActive: true,
            btn_desactive: false,
            error_class: '',
            success_class: '',
            // commentaire lié a la réponse de l'utilisateur
            message: '',
            class_message: '',
            // bruitage
            error_sound: new Audio('sound/error.wav'),
            success_sound: new Audio('sound/success.wav'),
            // animation
            animate_class: '',
            // boutton continuer
            isContinueShowed: false
        }
    },
    methods: {
        // méthode du timer
        decrementTimer() {
            if (this.temp_restant > 0 && this.isTimerActive) {
                this.temp_restant--
                this.timer()
            } else if (this.isTimerActive) {
                this.timeOver()
            }
        },
        timer() {
            setTimeout(this.decrementTimer, 1000)
        },
        // methode d'évalution de la réponse
        evalAnswer(e) {
            // 1.desactiver les boutons
            this.btn_desactive = true
            // arrête le compte à rebour
            this.isTimerActive = false
            // 2.vérifier la réponse
            if (e.target.getAttribute('data-proposition') == this.question['réponse']) {
                this.goodAnswer()
            } else {
                this.badAnswer(e.target.getAttribute('data-proposition'))
            }
        },

        goodAnswer() {
            this.success_class = this.question['réponse']
            // afficher un message d'encouragement
            this.message = "Bravo , vous avez trouvé la bonne réponse"
            this.class_message = 'text-green-500'
            // joue le son
            this.success_sound.play();
            // joue une animation 
            this.animate_class = "pulse";
            // affiche continuer
            this.isContinueShowed = true
            // incrémenter le score
            this.$emit('score');

        },

        badAnswer(rep) {
            this.error_class = rep
            this.success_class = this.question['réponse']
            // afficher un message 
            this.message = "Dommage , c'est une mauvaise réponse"
            this.class_message = 'text-red-500'
            // joue le son
            this.error_sound.play();
            // joue une animation 
            this.animate_class = "shake";
            // affiche continuer
            this.isContinueShowed = true
        },

        timeOver() {
            this.btn_desactive = true
            this.success_class = this.question['réponse']
            // afficher un message 
            this.message = "Dommage , le temps s'est écoulé"
            this.class_message = 'text-red-500'
            // joue le son
            this.error_sound.play();
            // joue une animation 
            this.animate_class = "shake";
            // affiche continuer
            this.isContinueShowed = true

        }

    },
    mounted() {
        this.timer();
    },
    emits: ['next', 'score'],
}
</script>
<style ></style>
