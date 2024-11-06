<template>
    <div class="col-sm-5 mx-auto align-middle">
        <div class="card text-center">
            <div class="card-header">
                <div>
                    Ne Kadar Hızlı Yazabilirsin?
                </div>
            </div>
            <div class="card-body">
                <span id="content" class="mx-1" v-for="(words, key) in words.filter((data, index) => index < 20)"
                    :key="key" v-bind:class="key != 0 || control">{{ words }}</span>
            </div>
            <div class="card-footer">
                <div v-if="isFinish">
                    <h1>Skorunuz</h1>
                    <button class="btn btn-outline-secondary" type="button" @click="newWords">Yenile</button>
                    <h4>Dakika Kelime: {{ dks }}</h4>
                    <h4>Doğluk Yüzdesi: %{{ truePercent }}</h4>
                </div>
                <div v-else class="input-group">
                    <input type="text" class="form-control" aria-label="Recipient's username with two button addons"
                        v-model="userWrite">
                    <button class="btn btn-outline-secondary" type="button" @click="getwords">Yenile</button>
                </div>
                <p class="my-auto">Zaman:{{ timerCount }}</p>
                <div class="my-auto btn-group">
                    <p class="mx-2">Doğru:{{ trueWord }}</p>
                    <p>Yanlış: {{ falseWord }}</p>
                </div>
            </div>
        </div>
    </div>
</template>
<script>

import wordList from '@/assets/words.json'

export default {
    data() {
        return {
            words: [],
            userWrite: null,
            wordTrue: true,
            trueWord: 0,
            falseWord: 0,
            timerCount: 60,
            running: false,
            interval: false,
            isFinish: false,
            wordList: wordList
        }
    },

    watch: {
        userWrite(val) {
            if (!val || val === ' ') {
                this.userWrite = ''
                return
            }
            if (!this.running) this.toggleTimer()
            const word = this.words[0].slice(0, val.length).toUpperCase()
            const userControl = val.replace(' ', '').toUpperCase()

            this.wordTrue = word === userControl

            if (val.indexOf(' ') !== -1) {
                this.wordTrue ? this.trueWord++ : this.falseWord++
                this.words.splice(0, 1)
                this.userWrite = ''
                this.wordTrue = true
            }
        }
    },

    methods: {
        getwords() {
            this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300)
        },
        toggleTimer() {
            this.running = true
            this.interval = setInterval(this.timeProcess, 1000)
        },
        timeProcess() {
            if (this.timerCount === 0) {
                clearInterval(this.interval)
                this.isFinish = true
                return
            }
            this.timerCount--
        },
        newWords() {
            this.getwords()
            this.isFinish = false
            this.timerCount = 60
            this.wordTrue = true
            this.running = false
        },
    },

    computed: {
        control() {
            return this.wordTrue ? 'writing-word' : 'writing-word bg-danger'
        },
        dks() {
            return 300 - this.words.length
        },
        truePercent() {
            const percent = (100 / this.dks)
            const val = (percent * this.trueWord)
            return isNaN(val) ? 0 : val
        },
    },

    mounted() {
        this.getwords()
    }
}

</script>
<style scoped>
.writing-word {
    background-color: #7d9dad;
    color: white;
    border-radius: 20%;
    padding: 3px;
}

#content {
    word-break: break-word;
    display: inline-block;
}

.card-header {
    background-color: #9db2ed;
    color: white;
}

.card-body {
    background-color: #a8d4ff;
    color: black;
}

.card-footer {
    background-color: #9db2ed;
    color: white;
}

.btn {
    background-color: #7d9dad;
    color: white
}
</style>