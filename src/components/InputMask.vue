<template>
    <div>
        <div v-if="masks[this.type]">
            <input type="text" :placeholder="masks[this.type].placeholder" @input="format" @keydown="isBackspace" v-model="inputText">
        </div>
        <div
                v-if="error"
                class="error"
        >
            <h2>The transmitted mask type is not supported</h2>
        </div>
    </div>
</template>

<script>

    export default {
        name: 'InputMask',

        props: {
            type: {type: String, default: 'phone'},
        },
        data() {
            return {
                masks: {
                    'date': {'accept': '\\d', 'pattern': '__/__/____', 'placeholder': '00/00/0000', 'slots': '_'},
                    'time': {'accept': '\\d', 'pattern': '__:__:__', 'placeholder': '00:00:00', 'slots': '_'},
                    'date_time': {'accept': '\\d', 'pattern': '__/__/____ __:__:__', 'placeholder': '00/00/0000 00:00:00', 'slots': '_'},
                    'cep': {'accept': '\\d', 'pattern': '_____-___', 'placeholder': '00000-000', 'slots': '_'},
                    'phone': {'accept': '\\d', 'pattern': '____-____', 'placeholder': '00000-0000', 'slots': '_'},
                    'phone_with_ddd': {'accept': '\\d', 'pattern': '(__) ____-____', 'placeholder': '(00) 0000-0000', 'slots': '_'},
                    'phone_us': {'accept': '\\d', 'pattern': '(___) ___-____', 'placeholder': '(000) 000-0000', 'slots': '_'},
                    'ip_address': {'accept': '\\d', 'pattern': '___.___.___.___', 'placeholder': '099.099.099.099', 'slots': '_'},
                },
                inputText: '',
                pattern: "+1 (___) ___-____",
                j: 0,
                slots: new Set("_"),
                accept: new RegExp(false || "\\d", "g"),
                back: false
            }
        },
        computed: {
            prev() {
                return Array.from(this.pattern, (c, i) => this.slots.has(c) ? this.j = i + 1 : this.j)
            },
            first() {
                return [...this.pattern].findIndex(c => this.slots.has(c))
            },
            error () {
                return this.masks[this.type]  ? false : true
            }
        },
        methods: {
            clean(input) {
                input = input.match(this.accept) || [];
                return Array.from(this.pattern, c => {
                        return input[0] === c || this.slots.has(c) ? input.shift() || c : c
                    }
                );
            },
            format(el) {
                const [i] = [el.target.selectionStart].map(i => {
                    i = this.clean(this.inputText).findIndex(c => this.slots.has(c));
                    return this.prev[i - 1] || this.first;
                });
                if (this.back) {
                    this.inputText = this.clean(this.inputText.slice(0, i - 1)).join``;
                } else {
                    this.inputText = this.clean(this.inputText).join``;
                }
                if (this.inputText == this.pattern) {
                    this.inputText = null
                }
                this.back = false;
                this.$emit('input', this.inputText)
            },
            isBackspace(e) {
                this.back = e.key === "Backspace"
            },
        },
        mounted() {
            if (this.masks[this.type]) {
                this.pattern = this.masks[this.type].pattern
                this.slots = new Set(this.masks[this.type].slots)
                this.accept = new RegExp(this.masks[this.type].accept || "\d", "g")
            }
        }
    }
</script>