<template>
    <v-menu offset-y v-model="showKeyboard" :close-on-content-click="false" transition="slide-y-transition" max-width="400" >
        <template v-slot:activator="{ on }">
            <v-text-field ref="textfield" v-model="textValue" v-bind="$attrs" v-on="on" class="align-right" readonly />
        </template>
        <v-list>
            <v-list-item class="pa-0">
                <div style="display:flex; width:100%; justify-content:center; align-items:center">
                    <table>
                        <tr v-for="(row, r) in buttons" :key="r">
                            <td v-for="(button, c) in row" :key="r+'-'+c" class="pa-0" :colspan="button.cols"  :rowspan="button.rows">
                                <v-btn
                                    elevation="0"
                                    :color="button.color == null ? 'teal lighten-4 black--text' : button.color"
                                    block 
                                    @click="onClickButton(button)" 
                                    width="80"
                                    large>
                                    <span v-if="button.text != null">{{button.text}}</span>
                                    <v-icon v-if="button.icon != null">{{button.icon}}</v-icon>
                                </v-btn>
                                <!-- :color="button.color != null ? button.color : 'white black--text'" -->
                            </td>
                        </tr>
                    </table>
                </div>
            </v-list-item>            
        </v-list>
    </v-menu>
</template>

<script>
    export default {
        inheritAttrs: false,
        props: {
            value: {
                type: String,
                default: "0"
            }
        },
        computed: {
            textValue: {
                get: function () {
                    return this.$props.value
                },
                set: function (v) {
                    this.$emit("input", v)
                }
            },
        },
        data: function () {
            return {
                showKeyboard: false,
                buttons:[
                    [
                        {value: "7", text: "7", cols: 1, rows: 1},
                        {value: "8", text: "8", cols: 1, rows: 1},
                        {value: "9", text: "9", cols: 1, rows: 1},
                        {value: "Backspace", icon: "mdi-keyboard-backspace", cols:1, rows: 1, color:"warning white--text"},
                    ],
                    [
                        {value: "4", text: "4", cols:1, rows: 1},
                        {value: "5", text: "5", cols:1, rows: 1},
                        {value: "6", text: "6", cols:1, rows: 1},
                        {value: "Clear", icon: "mdi-close", cols:1, rows: 1, color:"warning white--text"},
                    ],
                    [
                        {value: "1", text: "1", cols:1, rows: 1},
                        {value: "2", text: "2", cols:1, rows: 1},
                        {value: "3", text: "3", cols:1, rows: 1},
                        {value: "Enter", text:"ENTER", cols:1, rows: 1, color:"primary"},
                    ],
                    [
                        {value: "0", text: "0", cols:1, rows: 1},
                        {value: ".", text: ".", cols:1, rows: 1},
                        {value: "-", icon: "mdi-minus", cols:1, rows: 1},
                        {value: "Escape", text:"CANCEL", cols:1, rows: 1, color:"grey lighten-1 black--text"},
                    ],
                ],
                startValue: null,
                pressedEnter: false,
                pressedCancel: false
            }
        },
        // ===============================================
        // 監視
        watch: {
            // ===============================================
            // キーボード表示状態の監視
            showKeyboard: function (v, old) {
                if (v) {
                    // ===========================
                    // 表示開始した時は、keyupのイベントリスナーを登録、各フラグを初期化して現在の値を退避
                    window.addEventListener("keyup", this.onKeyUp)
                    this.startValue = this.value
                    this.pressedEnter = false
                    this.pressedCancel = false
                } else {
                    if (this.pressedCancel) {
                        // ===========================
                        // キャンセル時は、元の値に戻す
                        this.textValue = this.startValue
                    } else {
                        // ===========================
                        // 確定時は、入力値をチェック
                        if (this.textValue == "-") {
                            // "-" のみの時は、"0"
                            this.textValue = "0"
                        } else if (this.textValue.substr(this.textValue.length - 1, 1) == ".") {
                            // 末尾が "." の時は、"0" を付与
                            this.textValue += "0"
                        }
                    }
                    // ===========================
                    // keyupのイベントリスナーを削除
                    window.removeEventListener("keyup", this.onKeyUp)
                }
            },
        },
        created: function () {
        },
        beforeDestroy: function () {
        },
        // ===============================================
        // マウント時
        mounted: function () {
            this.textValue = "" + this.value
        },
        // ===============================================
        // メソッド
        methods: {
            onKeyUp: function (event) {
                this.onClickButton ({value:event.key})
            },
            // ===============================================
            // ボタン押下時
            onClickButton: function (button) {
                switch (button.value) {
                    case "Clear":
                        this.textValue = "0"
                        break;
                    case ".":
                        const i = this.textValue.indexOf(".")
                        if (i < 0) {
                            this.textValue += "."
                        } else {
                            return
                        }
                        break;
                    case "-":
                        if (this.textValue == "0") {
                            this.textValue = "-"
                        }
                        break;
                    case "Backspace":
                        if (this.textValue.length == 1) {
                            this.textValue = "0"
                        } else {
                            this.textValue = this.textValue.substr(0, this.textValue.length - 1)
                        }
                        break;
                    case "Enter":
                        this.pressedEnter = true
                        this.showKeyboard = false
                        break;
                    case "Escape":
                        console.log("escape")
                        this.pressedCancel = true
                        this.showKeyboard = false
                        break;
                    case "0":
                    case "1":
                    case "2":
                    case "3":
                    case "4":
                    case "5":
                    case "6":
                    case "7":
                    case "8":
                    case "9":
                        const v = this.textValue + button.value
                        if (isNaN(v)) {
                            return
                        }
                        this.textValue = ""+(v * 1)
                        break;
                }
            }
        }
    }
</script>

<style scoped>
    .align-right >>> input {
        text-align: right
    }
</style>
