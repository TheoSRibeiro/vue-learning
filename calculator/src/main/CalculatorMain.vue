<template>
    <div class="calculator">
        <DisplayComponent :value="displayValue"/>
        <ButtonComponente label="AC" triple @onClick="clearMemory"/>
        <ButtonComponente label="/" operation @onClick="setOperation"/>
        <ButtonComponente label="7" @onClick="addDigit"/>
        <ButtonComponente label="8" @onClick="addDigit"/>
        <ButtonComponente label="9" @onClick="addDigit"/>
        <ButtonComponente label="*" operation @onClick="setOperation"/>
        <ButtonComponente label="4" @onClick="addDigit"/>
        <ButtonComponente label="5" @onClick="addDigit"/>
        <ButtonComponente label="6" @onClick="addDigit"/>
        <ButtonComponente label="-" operation @onClick="setOperation"/>
        <ButtonComponente label="1" @onClick="addDigit"/>
        <ButtonComponente label="2" @onClick="addDigit"/>
        <ButtonComponente label="3" @onClick="addDigit"/>
        <ButtonComponente label="+" operation @onClick="setOperation"/>
        <ButtonComponente label="0" double @onClick="addDigit"/>
        <ButtonComponente label="." @onClick="addDigit"/>
        <ButtonComponente label="=" operation @onClick="setOperation"/>

    </div>
</template>

<script>
import DisplayComponent from "../components/DisplayComponent.vue"
import ButtonComponente from "../components/ButtonComponent.vue"

export default {
    components: {ButtonComponente, DisplayComponent},
    data: function(){
        return {
            displayValue: "0",
            clearDisplay: false,
            operation: null,
            values: [0,0],
            current: 0
        }
    },
    methods: {
        clearMemory(){
            // voltar para  o estado inicial
            Object.assign(this.$data, this.$options.data())
        },
        setOperation(operation){
            if(this.current == 0){
              this.operation = operation
              this.current = 1
              this.clearDisplay = true   
            }else{
                const equals = operation === "="
                const currentOperation = this.operation

                try{
                    // fazer uma operacao matematica nos 2 indices do array
                    this.values[0] = eval(
                        `${this.values[0]} ${currentOperation} ${this.values[1]}`
                    )

                    //tratamento para se o usr inserir um numero invalido ou infinito
                    if(isNaN(this.values[0]) || !isFinite(this.values[0])){
                        this.clearMemory()
                        return
                    }
                }catch (e){
                    this.$emit('onError', e)
                }

                this.values[1] = 0

                this.displayValue = this.values[0]
                this.operation = equals ? null : operation
                this.current = equals ? 0 : 1
                this.clearDisplay = !equals
            }
        },
        addDigit(digito){
            if(digito === "." && this.displayValue.includes(".")){
                return
            }

            const clearDisplay = this.displayValue === "0" || this.clearDisplay

            const currentValue = clearDisplay ? "" : this.displayValue
            const displayValue = currentValue + digito

            this.displayValue = displayValue
            this.clearDisplay = false

            // this.values[this.current] = displayValue
            // ou se quiser os valores como float
            if(digito !== "."){
                const i = this.current
                const newValue = parseFloat(displayValue)
                this.values[i] = newValue
               
            }
        }
    }
}
</script>

<style>
    .calculator{
        height: 320px;
        width: 235px;
        border-radius: 5px;
        overflow: hidden;

        display: grid;
        grid-template-columns: repeat(4, 25%);
        /* grid-template-columns: auto auto auto auto; */
        grid-template-rows: 1fr 48px 48px 48px 48px 48px;
    }
</style>