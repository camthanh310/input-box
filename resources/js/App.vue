<script>
export default {
    name: 'App',
    mounted() {
        this.$refs['itemInputs'][0].focus()
    },
    data() {
        return {
            success: false,
            isSecret: false,
            currentIndex: -1
        }
    },
    methods: {
        setSecretMode() {
            this.isSecret = !this.isSecret
            this.$refs['itemInputs'][this.currentIndex].focus()

            for (let index = 0; index < 5; index++) {
                if (this.isSecret) {
                    if (this.$refs['itemInputs'][index].value) {
                        this.$refs['itemInputs'][index].value = '*'
                    }
                } else {
                    this.$refs['itemInputs'][index].value = this.$refs['itemInputs'][index].dataset.mask
                }

            }


        },
        onInputField(event, index) {
            event.preventDefault()

            if ((event.keyCode >= 48 && event.keyCode <= 57) || (event.keyCode >= 96 && event.keyCode <= 105))  {
                this.currentIndex = index + 1

                event.target.dataset.mask = event.target.value
                if (this.isSecret) {
                    this.$refs['itemInputs'][index].value = '*'
                } else {
                    this.$refs['itemInputs'][index].value = event.target.dataset.mask
                }
                if (index < 4) {
                    setTimeout(() => {
                        this.$refs['itemInputs'][index + 1].focus()
                    }, 1)
                }

                if (index === 4) {
                    axios.post('/api/success')
                        .then(response => {
                            if (response.data === 'success') {
                                this.success = true
                                setTimeout(() => {
                                    for (let index = 0; index < 5; index++) {
                                        this.$refs['itemInputs'][index].value = ''
                                    }
                                    this.$refs['itemInputs'][0].focus()
                                    this.success = false
                                }, 5000);
                            }
                        })
                }
            } else {
                event.target.value = ''
            }
        }
    }
}

</script>

<template>
    <div class="flex items-center space-x-2 justify-center">
        <input
            ref="itemInputs"
            type="text"
            class="text-center rounded-md ring-1 ring-gray-200 py-px w-20 h-20"
            v-for="(n, index) in 5"
            :key="n"
            @keyup="onInputField($event, index)"
            maxlength="1"
            data-mask=""
        />

        <button v-if="!success" type="button" @click="setSecretMode" class="py-2 px-4 bg-blue-500 text-white">
            {{ isSecret ? 'Secret' : 'No Secret' }} Mode
        </button>
    </div>

    <div class="text-green-600 font-bold text-center" v-if="success">
        Your input is successfully
    </div>
</template>
